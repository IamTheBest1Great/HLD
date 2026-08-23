Here's the complete HLD for Dropbox / Google Drive:

---

# Complete High-Level Design for Cloud File Storage (Dropbox / Google Drive)

## Table of Contents
1. Introduction
2. Requirements
3. Traffic Estimation and Capacity Planning
4. API Design
5. Database Design
6. High-Level Architecture
7. Detailed Component Design
8. Multi-Region Deployment
9. Trade-offs and Discussion
10. Failure Scenarios and Mitigations
11. Security Considerations
12. Conclusion and Interview Summary

---

## 1. Introduction

Dropbox/Google Drive is a cloud file storage and synchronization service. Users can upload files from any device, access them from anywhere, share them with others, and have changes automatically synced across all their devices. When a user edits a file on their laptop, it should appear updated on their phone within seconds — without them thinking about it.

Key differences from simpler storage systems: files can be **arbitrarily large** (up to 5 GB per file), **the same file byte-range may be uploaded by millions of users** (deduplication opportunity), **sync requires detecting diffs** rather than re-uploading entire files, and **collaborative editing** introduces real-time conflict resolution. The system is fundamentally about **reliable, consistent state synchronization across heterogeneous devices**.

---

## 2. Requirements

### Functional Requirements
- **Upload**: Upload files and folders of any size (up to 5 GB per file).
- **Download**: Retrieve files at any time from any device.
- **Sync**: Automatically detect and propagate changes across all user devices.
- **Share**: Share files/folders with others via link or direct invite (view / comment / edit permissions).
- **Version History**: Keep previous versions of files for at least 30 days (Dropbox-style) or indefinitely (Drive-style).
- **Conflict Resolution**: Handle concurrent edits to the same file from multiple devices.
- **Folder Operations**: Create, rename, move, delete folders; recursive folder operations.
- **Search**: Full-text search across file names and (optionally) file content.
- **Quota Management**: Per-user storage quota (e.g., 15 GB free for Drive).
- **Offline Access**: Files marked for offline access are synced locally and available without internet.

### Non-Functional Requirements
- **High Durability**: 99.999999999% (11 nines) — files must never be lost.
- **High Availability**: 99.99% uptime for reads; uploads may have brief degraded periods.
- **Low Latency Sync**: File change visible on other devices within 5 seconds.
- **Scalability**: 1 billion users, 10 billion files, petabytes of storage.
- **Deduplication**: Identical file content stored only once globally (content-addressable storage).
- **Bandwidth Efficiency**: Only upload/download changed portions of files (delta sync).

---

## 3. Traffic Estimation and Capacity Planning

**Assumptions**
- Total users: 500 million; DAU: 100 million
- Files per user: 1,000 avg; total files: 500 billion
- Average file size: 500 KB (mix of docs, photos, videos)
- Daily uploads: 50 million files
- Daily downloads: 200 million files (4:1 read:write)
- Average file change: 10% of content changes per edit (delta upload)
- Deduplication ratio: 30% (many users have same photos, docs, installers)
- Metadata record size: ~300 bytes per file
- Replication factor: 3× for storage

**Calculations**

- Uploads/second (avg): 50M / 86,400 ≈ **580 uploads/sec**
- Downloads/second (avg): 200M / 86,400 ≈ **2,315 downloads/sec**
- Daily upload volume: 50M × 500 KB = **25 TB/day**
- After dedup (30%): **~17.5 TB/day new unique data**
- 10-year storage (with replication): 17.5 TB × 365 × 10 × 3 = **~192 PB**
- Metadata DB size: 500B files × 300 bytes = **150 TB** (metadata alone — needs sharding)
- Peak sync events: During business hours, 10× average → **~5,800 sync ops/sec**
- Bandwidth: 2,315 downloads/sec × 500 KB = **~1.1 GB/sec egress**

The fundamental insight: **storage at petabyte scale demands content-addressable chunked storage with global deduplication.** Metadata is itself a big-data problem.

---

## 4. API Design

**Style**: REST for file operations, WebSocket/SSE for sync events. Chunked upload via resumable upload protocol.

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/v1/files` | `POST` | Initiate file upload (returns upload session) |
| `/v1/files/{fileId}` | `GET` | Download file or get metadata |
| `/v1/files/{fileId}` | `PATCH` | Update file metadata (rename, move) |
| `/v1/files/{fileId}` | `DELETE` | Delete file (soft delete) |
| `/v1/files/{fileId}/versions` | `GET` | List version history |
| `/v1/files/{fileId}/share` | `POST` | Create share link or invite collaborator |
| `/v1/folders` | `POST` | Create folder |
| `/v1/folders/{folderId}` | `GET` | List folder contents |
| `/v1/upload/sessions` | `POST` | Start resumable chunked upload |
| `/v1/upload/sessions/{sessionId}` | `PUT` | Upload a chunk (with byte range header) |
| `/v1/upload/sessions/{sessionId}/complete` | `POST` | Finalize upload after all chunks sent |
| `/v1/sync/changes` | `GET` | Poll for changes since a cursor (delta sync) |
| `wss://sync.drive.com/v1/devices/{deviceId}` | WebSocket | Push sync notifications |

**Resumable Chunked Upload Flow**:
```
POST /v1/upload/sessions          → { session_id, chunk_size: 4MB }
PUT /v1/upload/sessions/{id}      → Content-Range: bytes 0-4194303/20971520
PUT /v1/upload/sessions/{id}      → Content-Range: bytes 4194304-8388607/20971520
... (5 chunks for a 20 MB file)
POST /v1/upload/sessions/{id}/complete → { file_id, version_id }
```

**Delta Sync API** (Dropbox-style cursor):
```
GET /v1/sync/changes?cursor=<opaque_cursor>
→ {
    "changes": [
      { "type": "modified", "file_id": "f123", "path": "/docs/notes.txt", "version": 42 },
      { "type": "deleted",  "file_id": "f456", "path": "/pics/old.jpg" }
    ],
    "cursor": "<new_cursor>",
    "has_more": false
  }
```

The cursor encodes the client's last-known sync state. The server computes a diff — only changes since that cursor are returned. This is the core of efficient sync.

**Rate Limiting**: 1,000 API calls/minute per user. Upload bandwidth throttled to fair-share during peak. Chunked uploads can be paused and resumed indefinitely (session valid 7 days).

---

## 5. Database Design

Three distinct storage tiers with very different access patterns.

### File Content — Object Storage (S3-Compatible) with Chunking

Files are **never stored whole**. Every file is split into **4 MB fixed-size chunks**. Each chunk is **content-addressed** by its SHA-256 hash.

```
file: "presentation.pptx" (20 MB)
→ chunk_1: SHA256(bytes 0–4MB)    = "a3f2..."  → s3://chunks/a3/f2/a3f2...
→ chunk_2: SHA256(bytes 4–8MB)    = "bb91..."  → s3://chunks/bb/91/bb91...
→ chunk_3: SHA256(bytes 8–12MB)   = "a3f2..."  → same as chunk_1! (dedup)
→ chunk_4: SHA256(bytes 12–16MB)  = "cc04..."  → s3://chunks/cc/04/cc04...
→ chunk_5: SHA256(bytes 16–20MB)  = "d71a..."  → s3://chunks/d7/1a/d71a...
```

The **file manifest** (stored in metadata DB) is just a list of chunk hashes in order. To reconstruct the file, fetch each chunk by its hash and concatenate.

**Why chunking + content-addressing?**
- **Dedup**: If two users upload the same 4 MB chunk (same photo, same installer, same library), it's stored once. Reference count incremented.
- **Delta sync**: When a file changes, only modified chunks need uploading. A 1 GB video with a new thumbnail only uploads the 4 MB chunk that changed.
- **Resumable uploads**: Each chunk is independently verifiable. Network failure → resume from last successful chunk.
- **Parallel upload/download**: Multiple chunks uploaded simultaneously (5 threads, each handling one chunk).

### File Metadata — Sharded PostgreSQL / Spanner

**Table: `files`**
- `file_id` (UUID, PK)
- `owner_id` (user_id, FK, shard key)
- `parent_folder_id` (FK, nullable)
- `name` (string)
- `mime_type`
- `size_bytes`
- `chunk_hashes` (text[], ordered list of SHA256 hashes)
- `current_version_id` (FK)
- `created_at`, `modified_at`
- `is_deleted` (soft delete), `deleted_at`

**Table: `file_versions`**
- `version_id` (UUID, PK)
- `file_id` (FK)
- `version_number`
- `chunk_hashes` (snapshot of chunk list at this version)
- `size_bytes`, `created_at`, `created_by_device_id`

**Table: `chunks`**
- `chunk_hash` (SHA256, PK — content address)
- `s3_object_key`
- `size_bytes`
- `ref_count` (how many file versions reference this chunk)

**Table: `shares`**
- `share_id`, `file_id`, `shared_by`, `shared_with` (user or null for link)
- `permission` (VIEW / COMMENT / EDIT), `expires_at`

**Table: `sync_cursors`**
- `device_id`, `user_id`, `last_sync_seq` (monotonic sequence number per user's change log)

**Sharding**: Files table sharded by `owner_id` (user_id). All of a user's file metadata lives on the same shard — queries like "list my files" are single-shard. Cross-user queries (shared files) use a secondary index table.

**Change Log** (for delta sync): An append-only `change_log` table per user shard — every file create/modify/delete appends a row with a monotonic sequence number. The delta sync API scans from the client's cursor sequence number to the latest, returning only those rows.

### Chunk Reference Tracking — Redis + PostgreSQL

`chunk_hash → ref_count` maintained in Redis for fast increment/decrement. Flushed to PostgreSQL periodically. When ref_count drops to 0, chunk is eligible for garbage collection (S3 object deletion scheduled after 24-hour grace period).

### Search Index — Elasticsearch

File names, paths, and (for Drive-style) extracted text content indexed in Elasticsearch. Updated asynchronously via Kafka when files are created/renamed/deleted.

---

## 6. High-Level Architecture**Component Roles**
- **CDN**: Caches frequently accessed files at edge. Files downloaded directly from CDN for high cache-hit reads — no origin load.
- **API Gateway**: Auth (OAuth2 / JWT), quota enforcement, rate limiting. Routes upload traffic to Upload Service, download to Download Service.
- **Sync Service**: Persistent WebSocket connections to client devices. Consumes Kafka `file-changes` events and pushes deltas to the relevant device connections. The real-time "magic" of Dropbox sync lives here.
- **Upload Service**: The most complex service. Manages chunked upload sessions, deduplication check per chunk, and parallel chunk ingestion.
- **File Service**: All file/folder metadata CRUD. Version history management. Share link generation.
- **Download Service**: Verifies access permissions, generates pre-signed S3 URLs for direct chunk downloads.
- **Kafka**: Decouples sync notifications, search indexing, thumbnail generation, and GC from the upload critical path.
- **GC Worker**: Tracks `chunk.ref_count`. When it hits zero (last file version deleted), schedules chunk for S3 deletion after a 24-hour grace period.

---

## 7. Detailed Component Design

### 7.1 Chunking and Deduplication — The Core Insight

This is what makes Dropbox-style sync efficient. Before uploading any chunk, the client computes SHA-256 of that chunk and asks the server: "Do you already have this chunk?"

**Check-before-upload protocol** (also called "block existence check"):
```
Client → POST /v1/chunks/check
Body: { "hashes": ["a3f2...", "bb91...", "a3f2...", "cc04...", "d71a..."] }

Server → { "missing": ["bb91...", "cc04...", "d71a..."] }
```

The server returns only the hashes it doesn't have. The client uploads only those missing chunks. In the example above, `a3f2...` appears twice (same content in chunk 1 and chunk 3) — uploaded only once. If another user already uploaded the same chunk, it's already on S3 — zero upload needed.

The chunk existence check runs against a Redis set of all chunk hashes (or a Bloom filter for probabilistic near-zero-miss check). The actual upload of missing chunks then happens in parallel threads.

**Variable-length chunking** (advanced): Instead of fixed 4 MB chunks, Content-Defined Chunking (CDC / Rabin fingerprinting) splits at content boundaries. This means a file insertion at byte 0 doesn't invalidate all subsequent chunks — only the affected chunks change. Used by Dropbox in production; fixed-size is simpler to explain in interviews.

### 7.2 Upload Flow (Detailed)

The upload flow is shown below — this is the most interview-critical sequence for this system.

The upload service receives chunks uploaded in parallel, stores each in S3 under `chunks/{hash[:2]}/{hash[2:4]}/{hash}`. Once all chunks for a file are uploaded, the client calls `/complete`, which atomically creates the file metadata record with the ordered chunk list as the manifest.

**Atomicity**: If the upload session expires (7 days) without completion, orphaned chunks are cleaned up by the GC worker. Partial uploads never create visible file records.

**Write ordering**: Metadata DB write (`INSERT INTO files`) happens only after all chunk S3 writes are confirmed. No metadata row = file doesn't exist from client's perspective. This makes the upload naturally crash-safe.

### 7.3 Delta Sync — The Cursor Mechanism

Every file operation (create, modify, rename, delete) appends an entry to the user's `change_log` table with a monotonically increasing `seq` number.

The sync client on each device maintains a local cursor = the last `seq` it processed. When the device comes online (or reconnects after sleep), it calls:

```
GET /v1/sync/changes?cursor=9041
→ changes since seq 9041:
  - seq 9042: file "report.pdf" modified
  - seq 9043: folder "archive" created
  - seq 9044: file "old.txt" deleted
  cursor: 9044
```

The client downloads only these 3 changes and applies them locally. For the modified file, it then calls chunk-check to see which of the new chunk hashes it already has locally (from its local cache) — and downloads only the missing chunks.

**Push sync via WebSocket**: The Sync Service holds a WebSocket connection open for each active device. When Kafka delivers a `file-changed` event (published by File Service on every mutation), Sync Service pushes a lightweight notification: `{ "type": "file_changed", "hint_cursor": 9042 }`. The device then calls the delta API with its current cursor. The WebSocket doesn't carry file content — it carries a nudge to poll. This prevents the WebSocket server from needing to know cursor state; it just delivers nudges.

**Long-poll fallback**: Mobile clients in background mode use long-polling instead of WebSocket to conserve battery. Same delta API, but request waits up to 60 seconds for a change before returning empty.

### 7.4 Conflict Resolution

When two devices edit the same file concurrently (both offline, then both come online):

**Strategy: Last-write-wins with conflict copy** (Dropbox approach)

1. Device A uploads version 5 (based on version 4). Server accepts → file is now at version 5.
2. Device B tries to upload its version 5 (also based on version 4). Server detects base version mismatch (expected v4 as parent, but v5 already exists).
3. Server creates a **conflict copy**: `report (Bob's conflicted copy 2025-04-11).pdf` — version 5 from Device B is saved as a new file.
4. Device B is notified of the conflict copy. Both versions are visible; user manually resolves.

**For Google Drive collaborative docs**: Real-time CRDT (Conflict-free Replicated Data Types) or OT (Operational Transformation) applied at the document editor layer — every keystroke is an operation, operations are merged deterministically. This is a separate system (Google Docs / real-time collab engine) layered on top of Drive storage.

### 7.5 Versioning and Garbage Collection

Every successful file upload creates a new `file_version` row. The `files` table's `current_version_id` pointer advances to the latest version. Old versions remain queryable via `/v1/files/{fileId}/versions`.

**Storage accounting**: Each version holds its own chunk_hash list. A chunk referenced by multiple versions has `ref_count > 1`. Deleting a version decrements ref_count for each of its chunks. When ref_count hits 0, the GC Worker schedules physical deletion from S3 after 24 hours (grace period for accidental deletes).

**Quota calculation**: User's quota consumed = sum of `size_bytes` across all *current versions* of non-deleted files. Old versions don't count against quota (Drive behavior) or count after 30 days (Dropbox behavior for non-paying users).

**Trash / soft delete**: Deleted files move to Trash. Trash is purged after 30 days. File metadata persists in DB with `is_deleted=true`. S3 chunks are not immediately affected — only purged when the last version referencing them is permanently deleted.

### 7.6 Sharing and Permissions

**Link sharing**: `POST /v1/files/{fileId}/share` generates a unique token stored in the `shares` table with `shared_with=null` (public link). Anyone with the token URL can access the file. Token is encoded in the download URL; Download Service verifies it against the shares table.

**User-to-user sharing**: `shared_with=user_id`. A `shared_files` index table maps `(shared_with_user_id, file_id)` for efficient "files shared with me" queries.

**Permission hierarchy**: OWNER > EDITOR > COMMENTER > VIEWER. Permissions are checked in File Service for every metadata mutation and in Download Service for every download. Shared folder permissions cascade to children (fetched via recursive CTE or materialized path on folder tree).

### 7.7 Quota Management

Quota is checked synchronously on every upload initiation. The current usage is tracked in a Redis counter (`quota:{user_id}` = bytes_used). On upload complete, the counter is incremented. On file delete (permanent), decremented. Periodically reconciled against DB-computed quota for accuracy.

If `bytes_used + upload_size > quota_limit`: upload rejected with 402 Payment Required (upgrade prompt). The Redis counter provides sub-millisecond quota checks without hitting the DB on every chunk upload.

---

## 8. Multi-Region Deployment

**Active-Active with home-region affinity**:
- Each user is assigned a home region (US-East, EU-West, AP-South) based on signup location.
- All file metadata writes for a user go to their home region's PostgreSQL primary.
- S3 content is stored in the home region and **asynchronously cross-region replicated** via S3 CRR.

**Read routing**: Downloads can be served from any region with S3 CRR. The CDN caches hot files globally. Metadata reads (file listings, share checks) go to a read replica in the nearest region, with sub-second replication lag from home region primary.

**Sync events**: Change log is home-region-primary-only. Sync Service instances in all regions subscribe to Kafka, which is replicated across regions via MirrorMaker2. A device in Singapore syncing a file owned in US-East gets the event via inter-region Kafka replication within 1-2 seconds.

**Regional failover**: If US-East degrades, new uploads for US-East users are temporarily queued (resumable upload sessions persist in Redis with cross-region backup). Downloads continue via CDN edge cache + S3 CRR replica. Metadata reads fall back to the cross-region read replica (stale by seconds — acceptable for browsing). No data loss because S3 CRR and PostgreSQL streaming replication are continuous.

---

## 9. Trade-offs and Discussion

| Decision | Trade-off |
|----------|-----------|
| **Fixed 4 MB chunking vs CDC** | Fixed chunks are simple, stateless, and predictable. CDC gives better dedup for file insertions/deletions but requires stateful chunking on client. Dropbox uses CDC; simpler to start with fixed in design interviews. |
| **Content-addressed chunk store vs file-as-object** | Content addressing enables global dedup and delta sync. Trade-off: manifest complexity (chunk list must be maintained per version), and reassembly cost on download. File-as-object is simpler but wastes storage and bandwidth. |
| **SHA-256 for chunk keys vs random UUIDs** | SHA-256 guarantees dedup is correctness-based, not luck. Trade-off: SHA-256 computation on client (cheap, ~100ms for 4 MB). Also: hash collisions are cryptographically impossible at our scale. |
| **Sharded PostgreSQL vs DynamoDB for metadata** | PostgreSQL allows rich queries (recursive folder trees, complex permission joins). Trade-off: harder to scale beyond a few TB per shard without Vitess/Citus. DynamoDB gives infinite horizontal scale but loses SQL expressiveness. |
| **WebSocket push + delta poll vs pure polling** | WebSocket gives 5-second sync latency with zero wasted polls. Trade-off: stateful connections are expensive to scale (each WS server holds O(N) connections). Pure polling at 30-second intervals is simpler but gives poor UX. |
| **Pre-signed S3 URLs vs proxy through app** | Pre-signed URLs offload all download bandwidth to S3/CDN. Trade-off: permission revocation is not instant (URL valid for 15 min even if share is revoked). Proxy-through-app is always-current but saturates app server bandwidth. |
| **LWW + conflict copy vs CRDT** | LWW+conflict copy is simple to implement and correct (no data loss). Trade-off: users must manually resolve conflicts. CRDT/OT gives seamless collaboration but is extremely complex to implement for binary files; only practical for text/structured docs. |

**Scaling to 100×**:
- **Bottleneck 1 — metadata DB**: Shard more aggressively by `user_id` using consistent hashing. Migrate to Google Spanner or CockroachDB for globally distributed SQL without manual sharding.
- **Bottleneck 2 — chunk dedup check**: Bloom filter of all chunk hashes (100B entries × 10 bits/entry = ~125 GB — fits on a large Redis cluster). False positives trigger an unnecessary upload; false negatives are impossible. Reduces dedup check from DB lookup to in-memory bit operation.
- **Bottleneck 3 — sync fan-out**: For users sharing a folder with 10,000 collaborators, one file change triggers 10,000 sync notifications. Use a fan-out service that batches Kafka events and shards delivery by user_id ranges across Sync Service instances.

---

## 10. Failure Scenarios and Mitigations

| Failure | Impact | Mitigation |
|---------|--------|------------|
| **S3 outage** | Downloads fail; uploads fail | S3 cross-region replication; CDN edge cache serves recent files; uploads paused with client-side retry (resumable sessions preserved in Redis) |
| **PostgreSQL primary failure** | Metadata writes fail | RDS Multi-AZ auto-failover (~30s); reads continue on replica; upload sessions queued in Redis during failover window |
| **Upload session lost (Redis failure)** | Partial upload can't resume | Redis AOF + RDB; Sentinel failover; upload sessions also checkpointed to S3 as a safety net every 60 seconds |
| **Chunk corruption** | Silently wrong file content | SHA-256 verified on upload AND on download. Corrupt chunk → S3 checksum mismatch → retry from other S3 region or replica |
| **Sync loop** | File oscillates between two versions indefinitely | Version vector / base version tracking prevents echo: device only uploads if its base matches server's current version |
| **Runaway quota** | User fills 15 GB in seconds (malicious or bug) | Redis quota counter checked before every upload; hard stop at quota limit; alerting on anomalous upload rates |
| **GC deletes live chunk** | Active file becomes unreadable | Grace period (24h); ref_count must be 0; periodic reconciliation of ref_count vs DB scan; S3 versioning allows recovery |
| **Large file upload interrupted** | User loses 4 GB of upload progress | Resumable chunked upload; each chunk independently confirmed; resume from last successful chunk |
| **Conflict copy explosion** | Heavy offline user generates dozens of conflict copies | Conflict detection on client before upload; UI surfaces conflicts immediately; auto-cleanup of unresolved conflicts after 30 days |
| **Regional outage** | Users in that region lose access | GeoDNS redirects to nearest healthy region; reads serve from CDN + S3 CRR; writes queued; users notified of degraded mode |
| **Ransomware / mass delete** | User deletes all files | Soft delete (30-day trash); version history; admin recovery tool; S3 versioning preserves all prior chunk states |

---

## 11. Security Considerations

- **Authentication**: OAuth2 (Google SSO / Apple SSO) + API tokens for desktop clients. JWT with 1-hour expiry; refresh token rotation.
- **Encryption at rest**: All S3 chunks encrypted with AES-256 (SSE-S3 or SSE-KMS). Each user's data encrypted with a per-user key derived from a master key (envelope encryption). Key revocation = instant access termination even if chunks are exfiltrated.
- **Encryption in transit**: TLS 1.3 for all API calls. Pre-signed S3 URLs use HTTPS. WebSocket over `wss://`.
- **End-to-end encryption** (Mega.nz / ProtonDrive model): Client encrypts file before chunking; server never sees plaintext. Trade-off: server cannot generate thumbnails, index content, or scan for malware. Optional feature for high-security users.
- **Malware scanning**: Chunks scanned by virus scanner worker (ClamAV / third-party API) on upload completion. Infected files flagged, quarantined, user notified. Scanning is per-chunk (chunks are content-addressed, so a known-bad chunk hash can be blocklisted globally).
- **Sharing abuse prevention**: Shared links rate-limited per IP (prevent mass public data exfil). Links can be password-protected. Admin can revoke all shares for a user instantly. Download audit log for shared files.
- **Pre-signed URL security**: URLs expire in 15 minutes. Signed with HMAC to prevent forgery. URL includes `user_agent` binding (optional) to prevent link sharing.
- **CORS**: Download endpoints only accept requests from known app origins. S3 bucket CORS policy restricts origins.
- **Data residency**: EU users' data stays in EU region (GDPR compliance). Metadata and chunks for EU users never replicated to US-East. Enforced via user home-region assignment at signup.
- **Account takeover protection**: All active sessions listed in account settings. Suspicious login (new country, new device) triggers email alert + optional step-up auth. All sessions revocable with one click.

---

## 12. Conclusion and Interview Summary

### Key Design Decisions

- **Chunk-based content-addressed storage**: The foundational Dropbox insight. Files are lists of SHA-256-keyed 4 MB chunks. Global dedup, delta sync, and resumable uploads all fall out naturally from this design.
- **Check-before-upload (block existence check)**: Client asks server which chunks are missing before uploading anything. Eliminates redundant uploads of duplicate content across users and devices.
- **Cursor-based delta sync**: Each device tracks a monotonic cursor into the user's change log. Sync = "give me changes since my cursor." Efficient, stateless (server doesn't track per-device state beyond the cursor value).
- **WebSocket nudge + delta poll**: WebSocket delivers a lightweight "something changed" signal; the delta API delivers the actual changes. Decouples real-time push infrastructure from change delivery logic.
- **Sharded PostgreSQL for metadata**: Rich SQL (folder trees, permission cascades, version history) with horizontal sharding by `user_id`. Correct tool for structured, relational metadata at scale.
- **LWW + conflict copy for concurrent edits**: Simple, correct, and transparent. No data loss. Users see both versions and resolve manually.
- **Soft delete + 30-day trash + GC worker**: Deletion is never immediate. Protects against accidental and malicious mass-delete. GC worker is the only place physical S3 deletion happens.

### Comparison to Other Systems

| Aspect | Pastebin | BookMyShow | Dropbox/Drive |
|--------|----------|------------|---------------|
| Core challenge | Blob storage + short URL | Seat contention | Delta sync + dedup |
| Consistency model | Eventual | Strong (bookings) | Eventual + conflict copy |
| Primary bottleneck | Storage bandwidth | Write concurrency | Bandwidth + metadata scale |
| Key data store | S3 + DynamoDB | Redis + PostgreSQL | S3 (chunks) + sharded PG |
| Real-time requirement | None | WebSocket (seat map) | WebSocket (sync nudge) |
| Client intelligence | None | None | High (chunking, dedup, cursor) |
| Scale dimension | Storage (TB) | Concurrent users | Users × files (PB) |

### Perfect Interview Answer (30-second version)

> "Dropbox's core insight is chunked content-addressed storage. Every file is split into 4 MB chunks, each identified by its SHA-256 hash and stored once globally — this gives you dedup, delta sync, and resumable uploads for free. Before uploading, the client checks which chunks the server is missing, so only changed chunks travel over the wire. File metadata — the ordered list of chunk hashes — lives in a sharded PostgreSQL DB. Sync works via cursor: each device tracks a monotonic sequence number, calls the delta API on wake-up, and a WebSocket delivers a nudge when something changes. Conflicts get a 'conflict copy' — both versions survive, user resolves manually. Storage is S3 with cross-region replication for 11-nines durability. The tricky parts are garbage collecting orphaned chunks when ref_count hits zero, and scaling the WebSocket fan-out when 10,000 users share a heavily-edited folder."

---

### Mock Interview Follow-up Questions

**Q1 — How do you handle a 5 GB video file upload on a flaky mobile connection?**
Chunked upload + resumable sessions. The 5 GB file becomes 1,280 chunks of 4 MB each. Each chunk is independently uploaded and acknowledged. On connection drop, the client resumes from the last unacknowledged chunk — it already knows which chunks the server has from the initial check-before-upload response. The upload session is valid for 7 days (stored in Redis, checkpointed to S3). The user can put their phone in a bag, come back 3 days later, and resume. Progress is shown as (chunks confirmed / total chunks).

**Q2 — How do you implement "Files shared with me" efficiently?**
A separate `shared_files` index table with `(shared_with_user_id, file_id, permission, shared_at)` — sharded by `shared_with_user_id`. When listing "shared with me", it's a single-shard scan on this table, returning file_ids. Metadata for those files is fetched via cross-shard lookup (batch fetch by file_id, using the shard key encoded in the file_id). For folder sharing, permission inheritance is computed via recursive CTE on the folder tree, with results cached in Redis for 60 seconds to avoid repeated recursive queries.

**Q3 — How do you prevent a user from filling S3 with duplicate chunks across their own files?**
Global dedup already handles this at the chunk level — if the same 4 MB block appears in 100 of the user's files, it's stored once with `ref_count=100`. For quota accounting, we charge the user only for *unique bytes they contribute* — i.e., only chunks where they were the first uploader (`ref_count` went from 0 to 1 on their upload). If their 4 MB chunk was already in the system from another user, it costs them zero quota. This is Google Drive's "smart storage" behavior and requires tracking `first_uploaded_by` on the chunks table.
