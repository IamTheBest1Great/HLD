# 09 — Storage Systems
> **Why this matters:** Any system that handles files, images, videos, or large data needs the right storage type. Choosing block storage for media or object storage for a database is a career-ending mistake in an interview. Know which is which and why.

---

## 1. The Four Types of Storage

```
┌────────────────────────────────────────────────────────────────────┐
│                    STORAGE TYPE OVERVIEW                           │
├──────────────────┬──────────────┬──────────────────┬──────────────┤
│  Object/Blob     │    Block     │      File        │   In-Memory  │
│  Storage         │   Storage   │     Storage      │   (Cache)    │
├──────────────────┼──────────────┼──────────────────┼──────────────┤
│ S3, GCS, Azure  │ EBS, SAN    │ EFS, NFS         │ Redis        │
│ Blob            │             │                  │ Memcached    │
├──────────────────┼──────────────┼──────────────────┼──────────────┤
│ Unstructured    │ Raw blocks  │ Hierarchical     │ Key-value    │
│ flat namespace  │ like a disk │ directories      │ in RAM       │
├──────────────────┼──────────────┼──────────────────┼──────────────┤
│ Virtually       │ Limited to  │ Limited to       │ Limited by   │
│ unlimited       │ disk size   │ disk size        │ RAM size     │
├──────────────────┼──────────────┼──────────────────┼──────────────┤
│ Images, videos  │ Databases,  │ Shared configs,  │ Sessions,    │
│ backups, logs   │ OS volumes  │ NFS mounts       │ hot data     │
└──────────────────┴──────────────┴──────────────────┴──────────────┘
```

---

## 2. Object Storage (Blob Storage)

The most important storage type for HLD interviews. Used for images, videos, files, backups.

### How It Works

```
STRUCTURE: Flat namespace — no directories, just buckets + keys.

Bucket: "twitter-media"
  ├── "user/123/avatar.jpg"        ← key (looks like path, but isn't)
  ├── "tweet/456/photo.png"
  ├── "video/789/clip.mp4"
  └── "backup/2024-03-01.tar.gz"

Operations:
  PUT  bucket/key body    → Upload object
  GET  bucket/key         → Download object
  DELETE bucket/key       → Delete object
  LIST bucket/prefix      → List objects with prefix
  HEAD bucket/key         → Get metadata (no body)

Objects are IMMUTABLE — you don't edit them in place, you replace them.
```

### Why Object Storage for Media?

```
Requirements for YouTube/Instagram:
  ✅ Store 500 hours of video uploaded per minute
  ✅ Serve to 2 billion users globally
  ✅ Survive hardware failures
  ✅ Cost-effective at petabyte scale

Object Storage solves all of these:
  Scale: Petabytes to exabytes (S3 stores trillions of objects)
  Durability: 11 nines (99.999999999%) — data replicated across 3+ AZs
  Global: CDN integration built in
  Cost: ~$0.023/GB/month (vs block storage ~$0.10/GB/month)
  No server: Fully managed, no OS, no disk management
```

### Object Storage in System Design

```
UPLOAD FLOW (e.g., Instagram photo upload):
─────────────────────────────────────────────────────────────────
Client
  │
  ├──(1) POST /upload → App Server
  │         │
  │         ├──(2) Generate unique key: "media/user123/photo_abc.jpg"
  │         ├──(3) Request pre-signed URL from S3
  │         │         S3 returns: "https://s3.../media/user123/photo_abc.jpg?sig=..."
  │         └──(4) Return pre-signed URL to client
  │
  ├──(5) PUT <pre-signed URL> [photo bytes] → S3 directly  ← bypasses app server!
  │
  └──(6) POST /upload/complete → App Server
              │
              └── Save metadata to DB: { url, user_id, timestamp }

WHY PRE-SIGNED URLS?
  App server doesn't handle the binary data → saves bandwidth + CPU
  Client uploads directly to S3 → faster (no extra hop)
  URL expires in 15 minutes → security
```

### Object Storage for Downloads (with CDN)

```
DOWNLOAD FLOW:
─────────────────────────────────────────────────────────────────
User in Mumbai requests photo originally stored in US-East S3

WITHOUT CDN:
  Request → US-East S3 → 200ms latency (cross-ocean)

WITH CDN:
  Request → Mumbai CDN edge
    CACHE HIT → served in 5ms ✅
    CACHE MISS → Mumbai edge fetches from US-East S3, caches it
              → served in 210ms (first time), 5ms thereafter ✅

CDN Pull Model (most common):
  Origin: S3
  CDN pulls content from origin on first request
  Subsequent requests served from edge cache
  Cache-Control headers control TTL
```

---

## 3. Block Storage

Block storage is raw storage that appears as a disk to the operating system.

```
WHAT IT IS:
  Like a hard drive attached to your server.
  OS can format it, create a filesystem, use it like a local disk.
  Low-level: data is stored in fixed-size "blocks".

USE CASES:
  ✅ Database storage (PostgreSQL writes to block storage)
  ✅ OS/boot volumes
  ✅ High-performance scratch space
  ✅ Any workload needing random I/O with low latency

WHY DATABASES USE BLOCK STORAGE (not object storage):
  Database needs:
    Random read/write to specific byte offsets ✅ block supports this
    POSIX filesystem semantics (locks, permissions)
    Low latency (sub-millisecond) random I/O

  Object storage:
    Read/write entire objects (no partial updates)
    Higher latency (~10-100ms)
    No POSIX semantics

  → Block storage for DB, Object storage for files/media
```

---

## 4. File Storage (Network File Systems)

```
WHAT IT IS:
  A shared filesystem mounted over a network.
  Multiple servers can mount and access the same filesystem.
  Looks and behaves like a local filesystem.

USE CASES:
  ✅ Shared configuration files across multiple servers
  ✅ Shared logs that multiple services write to
  ✅ Legacy apps that need filesystem semantics
  ✅ Machine learning training data shared across GPU nodes

EXAMPLES: AWS EFS, Azure Files, Google Filestore, NFS

WHY NOT ALWAYS USE FILE STORAGE:
  Slower than block storage (network overhead)
  Concurrency issues (multiple writers need locking)
  Not as scalable as object storage
  Cost: more expensive than object storage for large data
```

---

## 5. CDN — Content Delivery Network

CDNs cache content at the network edge, close to users.

### How a CDN Works

```
GEOGRAPHY:
─────────────────────────────────────────────────────────────────
Origin Server (US-East)
       │
       ├──── CDN PoP (Mumbai)    ← servers for India users
       ├──── CDN PoP (London)    ← servers for UK/Europe users
       ├──── CDN PoP (Singapore) ← servers for SE Asia users
       ├──── CDN PoP (São Paulo) ← servers for Brazil users
       └──── CDN PoP (Tokyo)     ← servers for Japan users

User in India requests "netflix.com/show/stranger-things.mp4"
Without CDN: Round-trip to US-East → ~300ms latency
With CDN:    Served from Mumbai PoP → ~10ms latency  ← 30x faster
```

### Pull CDN vs Push CDN

```
PULL CDN (most common):
─────────────────────────────────────────────────────────────────
Step 1: First user in Mumbai requests "logo.png"
Step 2: Mumbai PoP cache MISS → fetches from origin
Step 3: Mumbai PoP caches "logo.png" locally
Step 4: Returns to user (slow this once)
Step 5: All subsequent Mumbai users get "logo.png" from cache (fast)

Cache expiry: Controlled by Cache-Control header
  "Cache-Control: max-age=86400" → cached for 24 hours

Good for: Most content. Simple setup, no pre-planning.
Bad for: First user after cold start gets slow response.

PUSH CDN:
─────────────────────────────────────────────────────────────────
You proactively upload content to all CDN edge servers.
Step 1: You deploy a new version of your app
Step 2: Push all static assets to CDN edges proactively
Step 3: All users immediately get fast responses from edge

Good for: Known popular content (e.g., software release, movie premiere)
Bad for: Complex to manage; must push to every edge; storage cost at edges.
```

### Cache Invalidation — The Hardest CDN Problem

```
PROBLEM:
  User uploads new avatar → stored in S3
  CDN has cached old avatar with TTL = 24 hours
  User sees old avatar for up to 24 hours ❌

SOLUTIONS:

1. VERSIONED URLS (best practice):
   Old: cdn.example.com/avatars/user123.jpg
   New: cdn.example.com/avatars/user123_v2.jpg   ← different URL → cache miss!
   Or:  cdn.example.com/avatars/user123.jpg?v=1234567890
   
   When you update, change the URL → CDN sees it as new content.
   Old URL eventually expires from cache naturally.

2. API CACHE INVALIDATION (cache purge):
   After upload: call CDN API → "invalidate /avatars/user123.jpg"
   CDN marks key as invalid → next request goes to origin.
   Cost: Some CDNs charge per invalidation request.

3. SHORT TTL:
   Set Cache-Control: max-age=60 (1 minute)
   Stale window is small.
   But: More origin requests (less CDN efficiency).
```

---

## 6. Chunking Large Files

When handling large files (videos, backups), you can't transfer them in one piece.

### Why Chunk Files?

```
PROBLEM: Upload a 2GB video
  Single upload: connection drops at 1.9GB → start over ❌
  Single upload: Can't parallelize (sequential) → slow ❌
  Single upload: Can't resume → frustrating UX ❌

SOLUTION: Split into chunks

VIDEO: 2GB
  ├── chunk_001 (5MB)  ← upload
  ├── chunk_002 (5MB)  ← upload in parallel
  ├── chunk_003 (5MB)  ← upload in parallel
  ...
  └── chunk_400 (5MB)  ← upload

Benefits:
  ✅ Parallel uploads → faster (4 chunks at once = 4x throughput)
  ✅ Resume on failure → only re-upload failed chunks
  ✅ Progress tracking → show upload progress %
  ✅ CDN can serve different chunks from different edges
```

### Chunking Flow (YouTube-style)

```
CLIENT                         APP SERVER              S3
  │                                │                    │
  │──(1) "I want to upload         │                    │
  │       2GB video"──────────────►│                    │
  │                                │                    │
  │◄─(2) Upload ID: "upload_xyz"───│                    │
  │      Chunk size: 5MB           │                    │
  │                                │                    │
  │──(3) Upload chunk_001──────────┼───────────────────►│
  │──(3) Upload chunk_002──────────┼───────────────────►│  (parallel)
  │──(3) Upload chunk_003──────────┼───────────────────►│  (parallel)
  │...                             │                    │
  │                                │                    │
  │──(4) "All chunks done"────────►│                    │
  │                                │──(5) S3 Multipart  │
  │                                │    Complete API───►│
  │                                │                    │  S3 assembles chunks
  │◄─(6) "Upload complete"─────────│                    │
```

---

## 7. Deduplication

Avoid storing the same data twice.

```
WHY IT MATTERS:
  Dropbox: millions of users upload the same file (e.g., "cat.jpg")
  Without dedup: store 1M copies of same data → 1TB wasted
  With dedup: store once, reference it → 1GB used

HOW IT WORKS (Content-Addressed Storage):

  hash(file_content) = "a1b2c3d4..."

  User A uploads "cat.jpg" → hash = "a1b2c3"
    → Check DB: "a1b2c3 exists?" → NO
    → Store file in S3 as "a1b2c3"
    → Store in DB: {user_a, "cat.jpg", hash="a1b2c3"}

  User B uploads the SAME "cat.jpg" → hash = "a1b2c3"
    → Check DB: "a1b2c3 exists?" → YES!
    → Don't upload to S3 (file already there)
    → Store in DB: {user_b, "cat.jpg", hash="a1b2c3"}

  Both users see their file. Only 1 copy in S3. ✅

BLOCK-LEVEL DEDUP:
  Split file into chunks (e.g., 4MB each)
  Hash each chunk
  Only upload chunks that don't already exist
  Two similar files (e.g., same video with one edit) share most chunks
  → Massive space savings for versioned files (Dropbox sync)
```

---

## 8. Storage Decision Framework

```
WHAT ARE YOU STORING?
        │
        ├── User files, images, videos, backups → Object Storage (S3)
        │
        ├── Database data, OS volumes → Block Storage (EBS)
        │
        ├── Shared config, logs, ML datasets → File Storage (EFS/NFS)
        │
        └── Sessions, counters, hot data → In-Memory (Redis)

HOW IS IT ACCESSED?
        │
        ├── By URL globally → Object Storage + CDN
        │
        ├── Sequential (stream bytes) → Object Storage
        │
        ├── Random I/O at byte offsets → Block Storage
        │
        └── Filesystem semantics (ls, mkdir) → File Storage

HOW BIG IS IT?
        │
        ├── Petabytes+ → Object Storage (unlimited, cheap)
        │
        ├── Gigabytes, high IOPS → Block Storage (fast, expensive)
        │
        └── Terabytes, shared → File Storage
```

---

## 📝 Key Interview Sentences

> *"User-uploaded media — images and videos — goes to S3. The app server generates a pre-signed URL and returns it to the client. The client uploads directly to S3, bypassing the app server entirely. That way our app servers don't become I/O bottlenecks. A CDN in front of S3 handles global delivery with low latency."*

> *"For video uploads, I'd chunk the file into 5MB pieces on the client side. Each chunk is uploaded in parallel using S3's multipart upload API. If a chunk fails, only that chunk needs to be retried, not the entire video. This also gives us a real upload progress indicator."*

> *"Deduplication saves significant storage in a Dropbox-like system. We hash each block of each file. If the hash already exists in our storage, we skip the upload and just add a reference. Users who upload the same file — like a company-wide presentation — share the storage cost."*

---

## ✅ Self-Test Questions

1. You're designing YouTube. Where do you store: raw video files, video metadata (title, description), user watch history, video thumbnails? Give a storage type and reason for each.

2. Why can't you use object storage as the underlying storage for a PostgreSQL database?

3. A user updates their profile picture on Instagram. The old picture is cached in the CDN with a 7-day TTL. How do you ensure they see the new picture immediately?

4. A user uploads a 4GB video. The connection drops at 3.5GB. How does chunking help? What does the client need to do?

5. Two Dropbox users upload the exact same 500MB file. How does deduplication work? What's stored on disk vs what's stored in the metadata DB?
