# Durability — Consolidated HLD Notes

## Table of Contents

- [1. Durability Fundamentals](#1-durability-fundamentals)
  - [1.1 What Is Durability?](#11-what-is-durability)
    - [1.1.1 Persistence of Data](#111-persistence-of-data)
    - [1.1.2 Surviving Crashes & Power Failures](#112-surviving-crashes--power-failures)
    - [1.1.3 Durable Storage Guarantees](#113-durable-storage-guarantees)
    - [1.1.4 Why Durability Matters in HLD](#114-why-durability-matters-in-hld)
  - [1.2 Core Principle of Durability](#12-core-principle-of-durability)
    - [1.2.1 Redundant Non-Volatile Storage](#121-redundant-non-volatile-storage)
    - [1.2.2 Write-Ahead Logging (WAL)](#122-write-ahead-logging-wal)
    - [1.2.3 Replication](#123-replication)
    - [1.2.4 Data Integrity Verification](#124-data-integrity-verification)
    - [1.2.5 Recovery Mechanisms](#125-recovery-mechanisms)
  - [1.3 Durability vs Availability vs Scalability](#13-durability-vs-availability-vs-scalability)
    - [1.3.1 Durability](#131-durability)
    - [1.3.2 Availability](#132-availability)
    - [1.3.3 Scalability](#133-scalability)
    - [1.3.4 Key Differences](#134-key-differences)
    - [1.3.5 Real-World Tradeoffs](#135-real-world-tradeoffs)
- [2. Core Durability Techniques](#2-core-durability-techniques)
  - [2.1 Write-Ahead Logging (WAL)](#21-write-ahead-logging-wal)
    - [2.1.1 Why WAL Exists](#211-why-wal-exists)
    - [2.1.2 Sequential Log Writes](#212-sequential-log-writes)
    - [2.1.3 Crash Recovery](#213-crash-recovery)
    - [2.1.4 WAL Replay](#214-wal-replay)
    - [2.1.5 Databases Using WAL](#215-databases-using-wal)
  - [2.2 Replication](#22-replication)
    - [2.2.1 Why Replication Matters](#221-why-replication-matters)
    - [2.2.2 Synchronous Replication](#222-synchronous-replication)
    - [2.2.3 Asynchronous Replication](#223-asynchronous-replication)
    - [2.2.4 Multi-Region Replication](#224-multi-region-replication)
    - [2.2.5 Replication Tradeoffs](#225-replication-tradeoffs)
  - [2.3 Distributed Consensus](#23-distributed-consensus)
    - [2.3.1 Why Consensus Is Needed](#231-why-consensus-is-needed)
    - [2.3.2 Raft](#232-raft)
    - [2.3.3 Paxos](#233-paxos)
    - [2.3.4 Quorum-Based Systems](#234-quorum-based-systems)
    - [2.3.5 Leader Election](#235-leader-election)
  - [2.4 Durable Commit Logs](#24-durable-commit-logs)
    - [2.4.1 Append-Only Logs](#241-append-only-logs)
    - [2.4.2 Kafka Commit Logs](#242-kafka-commit-logs)
    - [2.4.3 Event Sourcing](#243-event-sourcing)
    - [2.4.4 Replayability](#244-replayability)
  - [2.5 Checksums & Data Integrity](#25-checksums--data-integrity)
    - [2.5.1 Detecting Corruption](#251-detecting-corruption)
    - [2.5.2 Hash Verification](#252-hash-verification)
    - [2.5.3 End-to-End Integrity Checks](#253-end-to-end-integrity-checks)
    - [2.5.4 Silent Data Corruption](#254-silent-data-corruption)
  - [2.6 Erasure Coding](#26-erasure-coding)
    - [2.6.1 What Erasure Coding Is](#261-what-erasure-coding-is)
    - [2.6.2 Replication vs Erasure Coding](#262-replication-vs-erasure-coding)
    - [2.6.3 Storage Efficiency](#263-storage-efficiency)
    - [2.6.4 Recovery Tradeoffs](#264-recovery-tradeoffs)
  - [2.7 RAID (Redundant Array of Independent Disks)](#27-raid-redundant-array-of-independent-disks)
    - [2.7.1 RAID 0](#271-raid-0)
    - [2.7.2 RAID 1](#272-raid-1)
    - [2.7.3 RAID 5](#273-raid-5)
    - [2.7.4 RAID 6](#274-raid-6)
    - [2.7.5 RAID 10](#275-raid-10)
    - [2.7.6 RAID Tradeoffs](#276-raid-tradeoffs)
  - [2.8 Non-Volatile Memory](#28-non-volatile-memory)
    - [2.8.1 NVDIMM](#281-nvdimm)
    - [2.8.2 Battery-Backed RAM](#282-battery-backed-ram)
    - [2.8.3 Persistent Memory](#283-persistent-memory)
    - [2.8.4 Performance Benefits](#284-performance-benefits)
- [3. Backup & Recovery](#3-backup--recovery)
  - [3.1 Backup Strategies](#31-backup-strategies)
    - [3.1.1 Full Backup](#311-full-backup)
    - [3.1.2 Incremental Backup](#312-incremental-backup)
    - [3.1.3 Differential Backup](#313-differential-backup)
    - [3.1.4 Snapshot-Based Backup](#314-snapshot-based-backup)
  - [3.2 Point-in-Time Recovery (PITR)](#32-point-in-time-recovery-pitr)
    - [3.2.1 WAL-Based Recovery](#321-wal-based-recovery)
    - [3.2.2 Time-Based Recovery](#322-time-based-recovery)
    - [3.2.3 Disaster Recovery Use Cases](#323-disaster-recovery-use-cases)
  - [3.3 Disaster Recovery](#33-disaster-recovery)
    - [3.3.1 Regional Failures](#331-regional-failures)
    - [3.3.2 Cross-Region Replication](#332-cross-region-replication)
    - [3.3.3 Recovery Time Objective (RTO)](#333-recovery-time-objective-rto)
    - [3.3.4 Recovery Point Objective (RPO)](#334-recovery-point-objective-rpo)
  - [3.4 Immutable Infrastructure & Versioning](#34-immutable-infrastructure--versioning)
    - [3.4.1 Immutable Data](#341-immutable-data)
    - [3.4.2 Object Versioning](#342-object-versioning)
    - [3.4.3 Snapshot Immutability](#343-snapshot-immutability)
    - [3.4.4 Ransomware Protection](#344-ransomware-protection)
- [4. Durability in Distributed Systems](#4-durability-in-distributed-systems)
  - [4.1 Storage Durability in Distributed Systems](#41-storage-durability-in-distributed-systems)
    - [4.1.1 Replicated Storage Nodes](#411-replicated-storage-nodes)
    - [4.1.2 Distributed File Systems](#412-distributed-file-systems)
    - [4.1.3 Object Storage Durability](#413-object-storage-durability)
    - [4.1.4 Geo-Redundancy](#414-geo-redundancy)
  - [4.2 Durability in Databases](#42-durability-in-databases)
    - [4.2.1 ACID Durability](#421-acid-durability)
    - [4.2.2 WAL + Replication](#422-wal--replication)
    - [4.2.3 Commit Guarantees](#423-commit-guarantees)
    - [4.2.4 Crash Recovery](#424-crash-recovery)
  - [4.3 Durability in Messaging Systems](#43-durability-in-messaging-systems)
    - [4.3.1 Durable Queues](#431-durable-queues)
    - [4.3.2 Persistent Messages](#432-persistent-messages)
    - [4.3.3 Kafka Replication](#433-kafka-replication)
    - [4.3.4 Replay Mechanisms](#434-replay-mechanisms)
  - [4.4 Durability in Object Storage](#44-durability-in-object-storage)
    - [4.4.1 Multi-Copy Replication](#441-multi-copy-replication)
    - [4.4.2 Erasure Coding](#442-erasure-coding)
    - [4.4.3 Data Integrity Verification](#443-data-integrity-verification)
    - [4.4.4 Durability SLAs](#444-durability-slas)
- [5. Durability Decision Framework](#5-durability-decision-framework)
  - [5.1 Choosing the Right Durability Strategy](#51-choosing-the-right-durability-strategy)
    - [5.1.1 Financial Systems](#511-financial-systems)
    - [5.1.2 Social Media Systems](#512-social-media-systems)
    - [5.1.3 Analytics Systems](#513-analytics-systems)
    - [5.1.4 Messaging Systems](#514-messaging-systems)
    - [5.1.5 Archival Systems](#515-archival-systems)
  - [5.2 Tradeoffs in Durability](#52-tradeoffs-in-durability)
    - [5.2.1 Latency vs Durability](#521-latency-vs-durability)
    - [5.2.2 Cost vs Durability](#522-cost-vs-durability)
    - [5.2.3 Availability vs Consistency](#523-availability-vs-consistency)
    - [5.2.4 Replication vs Storage Efficiency](#524-replication-vs-storage-efficiency)
  - [5.3 Scenario → Durability Technique](#53-scenario--durability-technique)
    - [5.3.1 High Write Throughput Systems](#531-high-write-throughput-systems)
    - [5.3.2 Mission-Critical Systems](#532-mission-critical-systems)
    - [5.3.3 Global Systems](#533-global-systems)
    - [5.3.4 Low-Cost Archival Systems](#534-low-cost-archival-systems)
- [6. Common Anti-Patterns](#6-common-anti-patterns)
  - [6.1 Missing WAL](#61-missing-wal)
  - [6.2 Single Disk Dependency](#62-single-disk-dependency)
  - [6.3 No Backups](#63-no-backups)
  - [6.4 Async Replication Without Understanding Data Loss Risk](#64-async-replication-without-understanding-data-loss-risk)
  - [6.5 No Integrity Verification](#65-no-integrity-verification)
  - [6.6 No Disaster Recovery Plan](#66-no-disaster-recovery-plan)
  - [6.7 Storing Everything in Memory](#67-storing-everything-in-memory)
  - [6.8 Ignoring Recovery Testing](#68-ignoring-recovery-testing)
- [7. Durability Patterns by System Type](#7-durability-patterns-by-system-type)
  - [7.1 Summary: Durability Techniques by Pattern](#71-summary-durability-techniques-by-pattern)
  - [7.2 Pattern A – CRUD & Storage Systems (Durability Focus)](#72-pattern-a---crud--storage-systems-durability-focus)
  - [7.3 Pattern B – Feed / Timeline Systems (Durability Focus)](#73-pattern-b---feed--timeline-systems-durability-focus)
  - [7.4 Pattern C – Real-Time Systems (Durability Focus)](#74-pattern-c---real-time-systems-durability-focus)
  - [7.5 Pattern D – Search Systems (Durability Focus)](#75-pattern-d---search-systems-durability-focus)
  - [7.6 Pattern E – Geospatial Systems (Durability Focus)](#76-pattern-e---geospatial-systems-durability-focus)
  - [7.7 Pattern F – Financial Systems (Durability Focus)](#77-pattern-f---financial-systems-durability-focus)
  - [7.8 Pattern G – Large-Scale Infrastructure (Durability Focus)](#78-pattern-g---large-scale-infrastructure-durability-focus)
  - [7.9 Pattern H – Collaborative Systems (Durability Focus)](#79-pattern-h---collaborative-systems-durability-focus)
- [8. Interview-Oriented HLD Notes](#8-interview-oriented-hld-notes)
  - [8.1 Key Tradeoffs](#81-key-tradeoffs)
    - [8.1.1 Durability vs Latency](#811-durability-vs-latency)
    - [8.1.2 Sync vs Async Replication](#812-sync-vs-async-replication)
    - [8.1.3 Replication vs Erasure Coding](#813-replication-vs-erasure-coding)
    - [8.1.4 Cost vs Reliability](#814-cost-vs-reliability)
  - [8.2 Common Interview Questions](#82-common-interview-questions)
    - [8.2.1 How Does WAL Work?](#821-how-does-wal-work)
    - [8.2.2 Why Use Replication?](#822-why-use-replication)
    - [8.2.3 How Does Kafka Ensure Durability?](#823-how-does-kafka-ensure-durability)
    - [8.2.4 RAID vs Replication?](#824-raid-vs-replication)
    - [8.2.5 What Happens During Crash Recovery?](#825-what-happens-during-crash-recovery)
  - [8.3 Quick Revision Material](#83-quick-revision-material)
    - [8.3.1 Durability Technique Comparison](#831-durability-technique-comparison)
    - [8.3.2 Replication Cheat Sheet](#832-replication-cheat-sheet)
    - [8.3.3 RPO vs RTO](#833-rpo-vs-rto)
    - [8.3.4 Key Interview Sentences](#834-key-interview-sentences)
    - [8.3.5 Self-Test Questions](#835-self-test-questions)

---

## 1. Durability Fundamentals

### 1.1 What Is Durability?

**Durability** is the guarantee that once a write operation (create, update, delete) is acknowledged as successful, the data will persist permanently — even in the face of power failures, crashes, network partitions, or disk corruption. It is the "D" in ACID (Atomicity, Consistency, Isolation, Durability).

In practical terms, durability answers the question: *If the system crashes right now, will my data still be there when it restarts?*

#### 1.1.1 Persistence of Data
Data must be written to non‑volatile storage (disk, SSD, NVRAM) that retains its contents when power is removed. Simply acknowledging a write from memory is not durable; the data must survive a power cycle.

#### 1.1.2 Surviving Crashes & Power Failures
A truly durable system ensures that committed data survives:
- Process crashes (application, database)
- OS panics
- Power loss to the entire machine or data center
- Disk controller failures
- Partial writes (torn pages)

#### 1.1.3 Durable Storage Guarantees
Cloud providers express durability as a percentage. For example, Amazon S3 offers 99.999999999% (11 nines) durability, meaning if you store 10,000,000 objects, you can expect to lose one object every 10,000 years on average.

#### 1.1.4 Why Durability Matters in HLD
Without durability guarantees, data can be silently lost — a catastrophic failure for financial systems, medical records, legal documents, and user‑generated content. In HLD interviews, you must demonstrate that your design does not lose committed data.

### 1.2 Core Principle of Durability

**Redundant, Non‑Volatile Storage + Write-Ahead Logging + Replication + Integrity Verification**

#### 1.2.1 Redundant Non-Volatile Storage
Data must be stored on media that survives power loss (HDD, SSD, NVRAM) and duplicated so that no single hardware failure causes data loss.

#### 1.2.2 Write-Ahead Logging (WAL)
Before modifying the main data store, the system appends the change to a sequential, append‑only log on persistent storage. If a crash occurs, the system replays the log to recover unapplied changes.

#### 1.2.3 Replication
Data is copied to multiple independent nodes, ideally in different physical locations (racks, availability zones, regions). If one node or data center is destroyed, data survives on another.

#### 1.2.4 Data Integrity Verification
Checksums, cryptographic hashes, and periodic scrubbing detect silent data corruption (bit rot) before it propagates. Corrupted data can be repaired from a healthy replica.

#### 1.2.5 Recovery Mechanisms
The system must be able to restore data from backups, replay logs, or rebuild from replicas. Recovery procedures must be tested regularly.

### 1.3 Durability vs Availability vs Scalability

#### 1.3.1 Durability
Guarantee that committed writes will persist. Measured as data loss probability (e.g., 1e-12 per year). Concerned with storage integrity and crash survival.

#### 1.3.2 Availability
Guarantee that the system is reachable and responds to requests. Measured as uptime percentage (e.g., 99.99%). Concerned with redundancy and failover.

#### 1.3.3 Scalability
Ability to handle growing load by adding resources. Measured in requests/sec or data volume. Concerned with throughput and capacity.

#### 1.3.4 Key Differences

| Aspect | Durability | Availability | Scalability |
|--------|-----------|-------------|-------------|
| **Question** | Is my data safe once written? | Is the system up and reachable? | Can it handle more load? |
| **Primary technique** | WAL + replication + checksums | Redundancy + failover | Horizontal scaling |
| **Trade‑off** | Performance / cost | Consistency (CAP) | Consistency (CAP) |
| **Example metric** | Data loss probability (1e-12) | Uptime % (99.99%) | Requests/sec |
| **Failure scenario** | Disk corruption after write ack | Server crash after write ack | Overloaded server |

#### 1.3.5 Real-World Tradeoffs
- A system can be highly available but not durable (e.g., in‑memory cache that loses data on crash but is always reachable).
- A system can be durable but not highly available (e.g., a database that refuses writes during a partition but never loses committed data).
- A system can be scalable but not durable (e.g., a stateless web tier that scales horizontally but stores no persistent state).
- The goal is to achieve the right balance for the use case.

---

## 2. Core Durability Techniques

### 2.1 Write-Ahead Logging (WAL)

#### 2.1.1 Why WAL Exists
Without WAL, a database would need to flush all modified data pages to disk synchronously on every commit — extremely slow due to random I/O. WAL enables fast sequential writes for durability while allowing lazy, batched flushing of data pages.

#### 2.1.2 Sequential Log Writes
WAL writes are append‑only and sequential, which is the fastest possible disk I/O pattern. The log grows continuously; old entries are eventually truncated after data pages are flushed.

#### 2.1.3 Crash Recovery
On restart after a crash, the database reads the WAL from the last checkpoint, identifies which transactions were committed but whose data pages were not yet flushed, and re‑applies those changes. Uncommitted transactions are rolled back.

#### 2.1.4 WAL Replay
The replay process brings the database to a consistent state — exactly as if all committed transactions had been fully persisted before the crash.

#### 2.1.5 Databases Using WAL
- PostgreSQL: Write‑Ahead Log (WAL)
- MySQL/InnoDB: Redo Log
- SQLite: Rollback Journal / WAL mode
- MongoDB: Journal
- etcd: Raft log (serves as WAL)
- Kafka: Partition logs on disk

**Example — PostgreSQL WAL flow:**
```
Client commits transaction
  → PostgreSQL writes changes to WAL on disk (sequential, fsync)
  → Returns "commit successful" to client
  → Later (asynchronously): dirty data pages flushed to disk
  → Crash: on restart, replay WAL from last checkpoint
```

### 2.2 Replication

#### 2.2.1 Why Replication Matters
A single server is a single point of failure. Replication stores copies of data on independent nodes, so the failure of one node (or even an entire data center) does not cause permanent data loss.

#### 2.2.2 Synchronous Replication
The write is acknowledged to the client only after it has been persisted on **all** (or a quorum of) replicas. Zero data loss if the primary fails — the data is already on the replica.

**Pros:** Strongest durability guarantee; no data loss window.  
**Cons:** Higher write latency; write may fail if a replica is unreachable.  
**Use for:** Financial transactions, critical metadata, payment ledgers.

**Example:** Google Spanner — writes synchronously replicated to at least two data centers before commit is acknowledged.

#### 2.2.3 Asynchronous Replication
The primary acknowledges the write immediately after persisting locally. Replicas catch up later. There is a small window where a primary crash could lose data that hasn't yet replicated.

**Pros:** Low write latency; high availability.  
**Cons:** Potential data loss if primary crashes before replication.  
**Use for:** Social media feeds, logs, analytics, CDN configuration.

**Example:** GitHub — repository writes are durable on the primary region instantly, then asynchronously replicated to a secondary region. If primary region is destroyed, you might lose the last few seconds of pushes.

#### 2.2.4 Multi-Region Replication
Data is replicated across geographically distant regions to protect against regional disasters (earthquake, flood, power grid failure). Can be synchronous (for critical data) or asynchronous (for cost/latency reasons).

#### 2.2.5 Replication Tradeoffs
| Mode | Latency | Data Loss Risk | Use Case |
|------|---------|---------------|----------|
| Synchronous (all) | Highest | Zero | Banking, ledgers |
| Synchronous (quorum) | Moderate | Zero (if quorum survives) | Distributed databases |
| Asynchronous | Lowest | Small window | Social media, CDN, logs |

### 2.3 Distributed Consensus

#### 2.3.1 Why Consensus Is Needed
When multiple nodes must agree on the order and durability of writes without a single point of failure, a consensus algorithm ensures that a majority of nodes have persisted each entry before it is considered committed.

#### 2.3.2 Raft
Raft elects a leader that manages a replicated log. A log entry is committed when a majority of nodes have persisted it. If the leader fails, a new one is elected and the log remains consistent.

**Used by:** etcd, Consul, CockroachDB, TiKV.

#### 2.3.3 Paxos
The original consensus algorithm. Ensures safety in the presence of failures. Multi‑Paxos extends it for a sequence of values. Harder to implement than Raft but mathematically proven.

**Used by:** Google Chubby, Apache ZooKeeper (Zab, a Paxos variant).

#### 2.3.4 Quorum-Based Systems
With N replicas, a write must be acknowledged by W nodes, a read by R nodes. If W + R > N, the system guarantees strong consistency. Tunable: trade off between durability (higher W) and availability.

**Used by:** Cassandra, DynamoDB.

#### 2.3.5 Leader Election
Consensus protocols elect a single leader responsible for sequencing writes. This avoids split‑brain scenarios and ensures a consistent, durable log.

### 2.4 Durable Commit Logs

#### 2.4.1 Append-Only Logs
Instead of overwriting state in place, all changes are appended to an immutable, ordered log on disk. The current state is derived by replaying the log. This is the foundation of event sourcing and message brokers.

#### 2.4.2 Kafka Commit Logs
Each Kafka partition is an append‑only log stored on disk. Messages are durable once flushed. With `acks=all` and `min.insync.replicas >= 2`, a message is durable across multiple brokers.

#### 2.4.3 Event Sourcing
Instead of storing the current state, store the sequence of events that led to it. The event log is the system of record and is never deleted. This provides full auditability and point‑in‑time reconstruction.

#### 2.4.4 Replayability
Because the log is immutable and durable, consumers can replay it from any point — enabling new services to build state, fix bugs, or recover from corruption.

### 2.5 Checksums & Data Integrity

#### 2.5.1 Detecting Corruption
Silent data corruption (bit rot) can occur due to faulty RAM, disk controller bugs, cosmic rays, or aging media. Checksums detect such corruption before it propagates.

#### 2.5.2 Hash Verification
Each block of data is stored alongside a checksum (CRC32, SHA‑256). On every read, the checksum is recomputed and compared. A mismatch indicates corruption.

#### 2.5.3 End-to-End Integrity Checks
Ensuring that data is not corrupted in transit (network) or at rest, from the application all the way down to the physical disk. Checksums at each layer validate integrity.

#### 2.5.4 Silent Data Corruption
A real‑world risk: Facebook once lost data from a corrupt RAID controller that wrote garbage silently. Checksums would have caught it. Always enable checksum verification on storage systems.

**Examples:**
- ZFS / Btrfs: checksum every block, auto‑repair from mirror.
- AWS S3: stores ETags (MD5) and periodically scans for bit rot.
- PostgreSQL: page‑level checksums (enabled with `--data-checksums`).

### 2.6 Erasure Coding

#### 2.6.1 What Erasure Coding Is
Data is split into `k` data fragments and encoded into `n` total fragments (n > k). Any `k` fragments can reconstruct the original data. Storage overhead is n/k — much lower than full replication.

**Example:** 17+3 erasure coding (Backblaze): 17 data + 3 parity = 20 fragments. Any 17 can rebuild the file. Storage overhead = 20/17 ≈ 1.18x (vs. 3x for triple replication).

#### 2.6.2 Replication vs Erasure Coding
- **Replication:** Better for hot data, random reads, small objects. Lower CPU overhead, higher storage cost.
- **Erasure coding:** Better for cold data, large sequential reads, cost‑sensitive archival. Higher CPU overhead for encode/decode.

#### 2.6.3 Storage Efficiency
Erasure coding achieves far better storage efficiency than replication while maintaining equivalent or better durability. A (10+4) code can tolerate any 4 failures with only 1.4x storage overhead.

#### 2.6.4 Recovery Tradeoffs
Recovering from a lost fragment requires reading from multiple surviving fragments and recomputing the missing data. This is more I/O and CPU intensive than simply copying from a replica.

### 2.7 RAID (Redundant Array of Independent Disks)

#### 2.7.1 RAID 0
Striping — data split across disks. **Zero redundancy.** Failure of any disk loses all data. Used only for performance, not durability.

#### 2.7.2 RAID 1
Mirroring — identical copies on two disks. Can tolerate one disk failure. 50% storage efficiency.

#### 2.7.3 RAID 5
Block‑level striping with distributed parity. Can tolerate one disk failure. Good read performance, slower writes (parity calculation). Minimum 3 disks.

#### 2.7.4 RAID 6
Like RAID 5 but with dual parity. Can tolerate any two disk failures. Safer for large capacity drives where rebuild time is long.

#### 2.7.5 RAID 10
Mirroring + striping (RAID 1 + RAID 0). Can tolerate multiple disk failures as long as no mirror pair loses both disks. Excellent performance and durability, at 50% storage efficiency.

#### 2.7.6 RAID Tradeoffs
| Level | Min Disks | Fault Tolerance | Storage Efficiency | Use Case |
|-------|-----------|-----------------|-------------------|----------|
| RAID 0 | 2 | None | 100% | Performance only |
| RAID 1 | 2 | 1 disk | 50% | Boot, critical |
| RAID 5 | 3 | 1 disk | (n-1)/n | Budget server |
| RAID 6 | 4 | 2 disks | (n-2)/n | Large disks |
| RAID 10 | 4 | ≥1 (varies) | 50% | DB, high perf |

### 2.8 Non-Volatile Memory

#### 2.8.1 NVDIMM
Non‑Volatile Dual In‑Line Memory Module — RAM that retains data across power cycles. Combines speed of DRAM with persistence of storage.

#### 2.8.2 Battery-Backed RAM
A battery or supercapacitor provides enough power to flush the contents of a write cache to persistent storage during a power failure. Used in RAID controllers and enterprise storage arrays.

#### 2.8.3 Persistent Memory
Intel Optane and similar technologies provide byte‑addressable, persistent storage with near‑DRAM latency. Allows databases to write directly to persistent memory without the overhead of block I/O.

#### 2.8.4 Performance Benefits
NVDIMM and persistent memory allow sub‑microsecond durable writes, compared to ~0.1 ms for SSD and ~5–10 ms for HDD. Critical for high‑frequency trading and real‑time systems.

---

## 3. Backup & Recovery

### 3.1 Backup Strategies

#### 3.1.1 Full Backup
A complete copy of the entire dataset. Simple to restore but time‑consuming and storage‑intensive. Typically performed weekly.

#### 3.1.2 Incremental Backup
Copies only data changed since the last backup (full or incremental). Fast to create and uses less storage. Restoration requires the full backup plus all subsequent incrementals.

#### 3.1.3 Differential Backup
Copies all data changed since the last full backup. Faster restoration than incremental (only full + latest differential needed) but larger than incremental.

#### 3.1.4 Snapshot-Based Backup
Storage snapshots create point‑in‑time copies using copy‑on‑write or redirect‑on‑write. Near‑instantaneous and consistent. Used by cloud providers (AWS RDS, EBS snapshots).

### 3.2 Point-in-Time Recovery (PITR)

#### 3.2.1 WAL-Based Recovery
Continuous archiving of WAL logs allows restoration to any specific point in time — down to the transaction or second. Combine a base backup with WAL replay.

#### 3.2.2 Time-Based Recovery
Specify a target time (e.g., "restore to 5 seconds before the accidental DROP TABLE"). The database replays WAL up to that timestamp.

#### 3.2.3 Disaster Recovery Use Cases
- Accidental data deletion (`DELETE FROM users WHERE ...` without WHERE).
- Ransomware encrypting data.
- Application bug corrupting data over time.
- Audit or compliance requirements to view historical state.

**Example — AWS RDS PITR:**
- Daily automated snapshots + continuous transaction log backups.
- Restore to any second within the retention period (up to 35 days).
- New DB instance created with restored data.

### 3.3 Disaster Recovery

#### 3.3.1 Regional Failures
A natural disaster, power grid failure, or network partition can make an entire region unavailable. Disaster recovery ensures data survives and can be restored in another region.

#### 3.3.2 Cross-Region Replication
Data is continuously replicated to a geographically distant region. In case of a regional outage, failover to the standby region with minimal data loss.

#### 3.3.3 Recovery Time Objective (RTO)
The maximum acceptable time to restore service after a disaster. For critical systems, RTO might be minutes; for archival, hours or days.

#### 3.3.4 Recovery Point Objective (RPO)
The maximum acceptable age of data that can be lost in a disaster. For synchronous replication, RPO = 0 (no data loss). For daily backups, RPO = 24 hours.

```
RTO: How long until we're back online?
RPO: How much data can we afford to lose?

Good: RTO = 1 hour, RPO = 5 minutes
Better: RTO = 5 minutes, RPO = 0 (synchronous replication)
Budget: RTO = 24 hours, RPO = 24 hours (nightly backups to tape)
```

### 3.4 Immutable Infrastructure & Versioning

#### 3.4.1 Immutable Data
Data that is never overwritten or deleted. Each write creates a new version. Deletion is a soft delete (marker) with a retention period before permanent removal.

#### 3.4.2 Object Versioning
Cloud object storage (S3, GCS) supports versioning. If an object is overwritten, the old version is retained. If deleted, a delete marker is added. Objects can be restored to any previous version.

#### 3.4.3 Snapshot Immutability
Some storage systems support immutable snapshots that cannot be modified or deleted, even by administrators, for a configurable retention period. Used for compliance and ransomware protection.

#### 3.4.4 Ransomware Protection
Immutable backups cannot be encrypted or deleted by ransomware. Combined with off‑site replication, they provide a last line of defense against data destruction.

---

## 4. Durability in Distributed Systems

### 4.1 Storage Durability in Distributed Systems

#### 4.1.1 Replicated Storage Nodes
Distributed file systems (HDFS, Ceph) and object stores replicate data across multiple physical nodes. A configurable replication factor (e.g., 3) ensures durability against node failures.

#### 4.1.2 Distributed File Systems
HDFS (Hadoop) stores each block on 3 DataNodes (by default). If a node fails, blocks are re‑replicated to maintain the replication factor. Combined with checksums for integrity.

#### 4.1.3 Object Storage Durability
AWS S3 achieves 11 nines durability by storing data redundantly across multiple availability zones, using erasure coding and continuous integrity verification. Data is automatically repaired if corruption is detected.

#### 4.1.4 Geo-Redundancy
For the highest durability, data is replicated across multiple geographic regions. Even a complete regional outage does not cause data loss. S3 Cross‑Region Replication (CRR) provides this.

### 4.2 Durability in Databases

#### 4.2.1 ACID Durability
The "D" in ACID guarantees that once a transaction is committed, it persists even through crashes. Achieved via WAL + fsync + replication.

#### 4.2.2 WAL + Replication
Databases combine local WAL for single‑node crash recovery with replication for multi‑node durability. The WAL ensures no committed transaction is lost on the local node; replication ensures survival if the entire node is destroyed.

#### 4.2.3 Commit Guarantees
A transaction is only reported as committed when:
- It has been written to the WAL and fsynced to disk (local durability).
- AND (for synchronous replication) the WAL entry has been replicated to the required number of standbys.

#### 4.2.4 Crash Recovery
On restart, the database replays WAL from the last checkpoint, restoring all committed transactions and rolling back uncommitted ones. The database is consistent after recovery.

### 4.3 Durability in Messaging Systems

#### 4.3.1 Durable Queues
Messages in a durable queue (RabbitMQ with persistent messages, SQS) are written to disk before acknowledgment. The message survives broker restarts.

#### 4.3.2 Persistent Messages
Kafka stores all messages on disk with configurable retention. With appropriate settings (`acks=all`, `min.insync.replicas >= 2`), messages are durable across multiple brokers.

#### 4.3.3 Kafka Replication
Each partition has a leader and followers (ISR — in‑sync replicas). When a producer sets `acks=all`, the message is durable only after all ISR members have persisted it. If the leader fails, a follower with the latest data is elected.

#### 4.3.4 Replay Mechanisms
Because messages are retained on disk and not deleted after consumption, consumers can replay the entire log from any offset. This enables recovery, debugging, and new consumer onboarding.

### 4.4 Durability in Object Storage

#### 4.4.1 Multi-Copy Replication
Traditional approach: store 3 full copies of each object across different physical devices/locations. Simple but storage‑intensive.

#### 4.4.2 Erasure Coding
Modern approach: split object into data and parity fragments, distribute across many drives. Achieves equivalent or better durability with much less storage overhead.

#### 4.4.3 Data Integrity Verification
Object stores continuously verify data integrity using checksums (MD5, SHA‑256). Corrupted fragments are detected and repaired from healthy fragments automatically.

#### 4.4.4 Durability SLAs
Cloud providers offer contractual durability guarantees:
- AWS S3 Standard: 99.999999999% (11 nines)
- Azure Blob Storage: 99.9999999999% (12 nines) for GRS
- Google Cloud Storage: 99.999999999% (11 nines)

---

## 5. Durability Decision Framework

### 5.1 Choosing the Right Durability Strategy

#### 5.1.1 Financial Systems
**Requirement:** Zero data loss (RPO = 0).  
**Techniques:** Synchronous replication + WAL + distributed consensus (Raft).  
**Example:** Payment ledger using CockroachDB with synchronous replication across 3+ regions.

#### 5.1.2 Social Media Systems
**Requirement:** Eventual durability acceptable; small data loss tolerable.  
**Techniques:** Asynchronous replication + WAL + periodic snapshots.  
**Example:** Twitter storing tweets in Cassandra with RF=3, quorum writes.

#### 5.1.3 Analytics Systems
**Requirement:** Raw data can be re‑ingested; some loss acceptable.  
**Techniques:** Durable commit log (Kafka) with moderate replication, batched writes to data lake.  
**Example:** Clickstream events in Kafka with `acks=1` for throughput, archived to S3 with erasure coding.

#### 5.1.4 Messaging Systems
**Requirement:** No message loss once acknowledged.  
**Techniques:** `acks=all`, `min.insync.replicas >= 2`, durable disk writes.  
**Example:** Kafka with RF=3, `min.insync.replicas=2`, `acks=all`.

#### 5.1.5 Archival Systems
**Requirement:** Long‑term, low‑cost durability.  
**Techniques:** Erasure coding, geo‑redundancy, immutable versioning.  
**Example:** Backblaze B2 with 17+3 erasure coding across 20 drives, cross‑data center replication.

### 5.2 Tradeoffs in Durability

#### 5.2.1 Latency vs Durability
Synchronous writes (fsync, sync replication) add latency but guarantee durability. Asynchronous writes are faster but have a data loss window. Balance based on business requirements.

#### 5.2.2 Cost vs Durability
Higher durability costs more: more replicas, more regions, more storage overhead. Erasure coding reduces cost while maintaining durability. Cold storage tiers offer cheap durable archival.

#### 5.2.3 Availability vs Consistency
During a partition, a system must choose between being available (accepting writes, risking inconsistency) and being consistent (refusing writes, preserving durability). This is the CAP tradeoff.

#### 5.2.4 Replication vs Storage Efficiency
Triple replication = 3x storage cost, simple, fast recovery. Erasure coding (10+4) = 1.4x storage cost, more CPU, slower recovery. Choose based on access patterns and cost sensitivity.

### 5.3 Scenario → Durability Technique

#### 5.3.1 High Write Throughput Systems
Use asynchronous replication + WAL with group commit. Batching writes reduces per‑operation fsync overhead. Kafka with `linger.ms` and `batch.size` tuning.

#### 5.3.2 Mission-Critical Systems
Synchronous replication to at least 2 nodes + WAL + distributed consensus. Regular PITR backups. Multi‑region deployment. No single point of failure.

#### 5.3.3 Global Systems
Multi‑region synchronous replication for critical metadata; asynchronous for bulk data. Geo‑redundant object storage. Global load balancing with regional failover.

#### 5.3.4 Low-Cost Archival Systems
Erasure coding + object versioning + periodic integrity checks. Use cold storage tiers (Glacier, Archive). Accept higher retrieval latency.

---

## 6. Common Anti-Patterns

### 6.1 Missing WAL
Running a database without WAL (e.g., `fsync=off`) for performance. Any crash can corrupt the entire database or lose committed transactions. Never disable WAL in production.

### 6.2 Single Disk Dependency
Relying on a single disk without RAID or replication. A single disk failure causes permanent data loss. Use at least RAID 1 or distributed replication.

### 6.3 No Backups
Assuming that replication alone is sufficient. Replication does not protect against logical errors (accidental DELETE, application bug) or ransomware. Always maintain separate, tested backups.

### 6.4 Async Replication Without Understanding Data Loss Risk
Using asynchronous replication without monitoring replication lag. If the replica falls far behind, a primary failure can lose significant data. Know your RPO and monitor it.

### 6.5 No Integrity Verification
Storing data without checksums. Silent corruption (bit rot) can go undetected for years, corrupting backups as well. Enable checksums at all layers (filesystem, database, application).

### 6.6 No Disaster Recovery Plan
Assuming the data center will never fail. Without a DR plan, a regional disaster can permanently destroy data. Test failover to a secondary region.

### 6.7 Storing Everything in Memory
In‑memory databases (Redis) without persistence or replication lose all data on restart. Configure RDB/AOF persistence for Redis, or use a disk‑backed database for critical data.

### 6.8 Ignoring Recovery Testing
Backups that have never been restored are not backups — they are hopes. Regularly test restore procedures to verify they work and meet RTO/RPO targets.

---

## 7. Durability Patterns by System Type

### 7.1 Summary: Durability Techniques by Pattern

| Pattern | System Type | Key Durability Techniques | Deep Dive / Rationale |
|---------|-------------|------------------------|------------------------|
| **A: CRUD & Storage Systems** | URL Shortener, Pastebin, Online Bookstore, Ticket Booking, Dropbox / Drive | Database WAL; Replication; Periodic Backups | All SQL databases (PostgreSQL, MySQL) use WAL for local durability. Ensure `fsync` is enabled and WAL is on dedicated fast storage. Use synchronous replication for critical data (inventory, orders). Use asynchronous… |
| **B: Feed / Timeline Systems** | Twitter / X, Instagram Feed, Facebook News Feed, LinkedIn Feed | Event Logs; Multi-Replica Storage; Async Persistence | Tweets, posts, and activities are stored as immutable events in Kafka or Cassandra with RF=3. Events can be replayed to rebuild state. Cassandra with RF=3, quorum writes ensures durability. If a node fails, hinted… |
| **C: Real-Time Systems** | WhatsApp / Messenger, Slack / Discord, Live Comments (YouTube Live), Multiplayer Game Backend | Durable Messaging; Replayable Event Streams; Persistent Session Data | Chat messages stored in Kafka with `acks=all` and RF=3. Messages are durable even if the chat server crashes — they are re‑delivered on reconnect. Kafka retains messages for days, allowing new consumers to catch up and… |
| **D: Search Systems** | Google Search, YouTube Search, Amazon Product Search, Search Autocomplete | Index Replication; Snapshot Recovery; Distributed Storage | Elasticsearch index shards have replicas. If a primary shard fails, a replica is promoted. Snapshots to S3 for disaster recovery. Regular index snapshots to object storage. If the cluster is lost, indices can be… |
| **E: Geospatial Systems** | Uber / Lyft, Google Maps, Food Delivery, Nearby Friends | Regional Replication; Event Logs; Backup Strategies | Driver location streams and trip history are replicated across regions for durability and local access. Ride events (requested, accepted, completed) are durably stored in Kafka. Replayable for analytics and recovery.… |
| **F: Financial Systems** | Payment Gateway, Wallet System, UPI-like System, Distributed Job Scheduler | Synchronous Replication; Consensus Protocols; Immutable Audit Logs | All transactions synchronously replicated to at least 2 nodes. No data loss window. Raft (CockroachDB, etcd) ensures that a majority of nodes have persisted each transaction before commit. Every financial event is… |
| **G: Large-Scale Infrastructure** | Analytics / Clickstream, Distributed Logging (ELK), Metrics Monitoring, Kafka-like System | Distributed Commit Logs; Erasure Coding; Data Lakes | Kafka with RF=3 and `min.insync.replicas=2` provides durable, ordered event storage. Data lakes (S3, HDFS) use erasure coding to reduce storage cost while maintaining high durability. Raw data in object storage with… |
| **H: Collaborative Systems** | Google Docs, Figma / Whiteboard, Distributed Locking Service, Web Crawler | Versioned Documents; Operational Logs; Multi-Region Replication | Every edit creates a new version. Old versions are never deleted, providing durability through immutability. CRDT operations are stored as an append‑only log in Cassandra or Kafka. Documents can be rebuilt by replaying… |

---

### 7.2 Pattern A – CRUD & Storage Systems (Durability Focus)

| System | Key Durability Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **URL Shortener** | Database WAL | All SQL databases (PostgreSQL, MySQL) use WAL for local durability. | Ensure `fsync` is enabled and WAL is on dedicated fast storage. |
| **Pastebin** | Replication | Use synchronous replication for critical data (inventory, orders). | Use asynchronous read replicas for scaling reads, accepting a small data loss window. |
| **Online Bookstore** | Periodic Backups | Automated daily snapshots + continuous WAL archiving for PITR. | Store backups in a separate region or account. |

---

### 7.3 Pattern B – Feed / Timeline Systems (Durability Focus)

| System | Key Durability Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Twitter / X** | Event Logs | Tweets, posts, and activities are stored as immutable events in Kafka or Cassandra with RF=3. | Events can be replayed to rebuild state. |
| **Instagram Feed** | Multi-Replica Storage | Cassandra with RF=3, quorum writes ensures durability. | If a node fails, hinted handoff and read‑repair converge replicas. |
| **Facebook News Feed** | Async Persistence | Feed caches (Redis) can use AOF persistence or be rebuilt from the event log. | Accept eventual consistency in favor of performance. |

---

### 7.4 Pattern C – Real-Time Systems (Durability Focus)

| System | Key Durability Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **WhatsApp / Messenger** | Durable Messaging | Chat messages stored in Kafka with `acks=all` and RF=3. | Messages are durable even if the chat server crashes — they are re‑delivered on reconnect. |
| **Slack / Discord** | Replayable Event Streams | Kafka retains messages for days, allowing new consumers to catch up and services to recover state after a crash. | Kafka retains messages for days, allowing new consumers to catch up and services to recover state after a crash. |
| **Live Comments (YouTube Live)** | Persistent Session Data | Presence and session state in Redis with AOF persistence or replicated Redis Cluster. | Short TTLs limit the durability requirement. |

---

### 7.5 Pattern D – Search Systems (Durability Focus)

| System | Key Durability Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Google Search** | Index Replication | Elasticsearch index shards have replicas. | If a primary shard fails, a replica is promoted. Snapshots to S3 for disaster recovery. |
| **YouTube Search** | Snapshot Recovery | Regular index snapshots to object storage. | If the cluster is lost, indices can be restored from snapshots. |
| **Amazon Product Search** | Distributed Storage | The underlying data (crawled pages, product catalog) is stored in a durable distributed file system (HDFS) or object store (S3). | The underlying data (crawled pages, product catalog) is stored in a durable distributed file system (HDFS) or object store (S3). |

---

### 7.6 Pattern E – Geospatial Systems (Durability Focus)

| System | Key Durability Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Uber / Lyft** | Regional Replication | Driver location streams and trip history are replicated across regions for durability and local access. | Driver location streams and trip history are replicated across regions for durability and local access. |
| **Google Maps** | Event Logs | Ride events (requested, accepted, completed) are durably stored in Kafka. | Replayable for analytics and recovery. |
| **Food Delivery** | Backup Strategies | PostgreSQL with PostGIS for trip history: WAL + daily snapshots to S3. | PostgreSQL with PostGIS for trip history: WAL + daily snapshots to S3. |

---

### 7.7 Pattern F – Financial Systems (Durability Focus)

| System | Key Durability Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Payment Gateway** | Synchronous Replication | All transactions synchronously replicated to at least 2 nodes. | No data loss window. |
| **Wallet System** | Consensus Protocols | Raft (CockroachDB, etcd) ensures that a majority of nodes have persisted each transaction before commit. | Raft (CockroachDB, etcd) ensures that a majority of nodes have persisted each transaction before commit. |
| **UPI-like System** | Immutable Audit Logs | Every financial event is appended to an immutable log (Kafka + WORM storage). | Logs are never deleted and are replicated across regions. |

---

### 7.8 Pattern G – Large-Scale Infrastructure (Durability Focus)

| System | Key Durability Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Analytics / Clickstream** | Distributed Commit Logs | Kafka with RF=3 and `min.insync.replicas=2` provides durable, ordered event storage. | Kafka with RF=3 and `min.insync.replicas=2` provides durable, ordered event storage. |
| **Distributed Logging (ELK)** | Erasure Coding | Data lakes (S3, HDFS) use erasure coding to reduce storage cost while maintaining high durability. | Data lakes (S3, HDFS) use erasure coding to reduce storage cost while maintaining high durability. |
| **Metrics Monitoring** | Data Lakes | Raw data in object storage with erasure coding, lifecycle policies, and cross‑region replication. | Raw data in object storage with erasure coding, lifecycle policies, and cross‑region replication. |

---

### 7.9 Pattern H – Collaborative Systems (Durability Focus)

| System | Key Durability Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Google Docs** | Versioned Documents | Every edit creates a new version. | Old versions are never deleted, providing durability through immutability. |
| **Figma / Whiteboard** | Operational Logs | CRDT operations are stored as an append‑only log in Cassandra or Kafka. | Documents can be rebuilt by replaying the log. |
| **Distributed Locking Service** | Multi-Region Replication | Document state replicated across regions for durability and local access. | --- |

---

---

## 8. Interview-Oriented HLD Notes

### 8.1 Key Tradeoffs

#### 8.1.1 Durability vs Latency
Stronger durability (synchronous writes, fsync) increases latency. Weaker durability (async, memory‑only) is faster but risks data loss.

#### 8.1.2 Sync vs Async Replication
Sync: zero data loss, higher latency. Async: low latency, potential data loss. Choose based on business requirements.

#### 8.1.3 Replication vs Erasure Coding
Replication: simpler, faster recovery, higher storage cost. Erasure coding: storage efficient, higher CPU, slower recovery. Best for cold data.

#### 8.1.4 Cost vs Reliability
More durability costs more: more replicas, more regions, faster storage. Balance with business impact of data loss.

### 8.2 Common Interview Questions

#### 8.2.1 How Does WAL Work?
WAL appends changes to a sequential log on disk before modifying data pages. On crash, the log is replayed to recover committed transactions. This enables fast sequential writes for durability.

#### 8.2.2 Why Use Replication?
A single server is a single point of failure. Replication stores copies on independent nodes, ensuring data survives hardware failures, disk corruption, and data center outages.

#### 8.2.3 How Does Kafka Ensure Durability?
Kafka stores messages on disk in partition logs. With `acks=all` and `min.insync.replicas >= 2`, messages are persisted on multiple brokers before acknowledgment. Logs are retained even after consumption.

#### 8.2.4 RAID vs Replication?
RAID protects against local disk failure but not server failure, fire, or logical errors. Replication protects against node failure and can span data centers. Use RAID for local durability and replication for distributed durability.

#### 8.2.5 What Happens During Crash Recovery?
1. System restarts. 2. Reads WAL from last checkpoint. 3. Replays committed transactions that were not yet flushed to data pages. 4. Rolls back uncommitted transactions. 5. Database is consistent and accepts new connections.

### 8.3 Quick Revision Material

#### 8.3.1 Durability Technique Comparison

| Technique | Mechanism | Use Case | Tradeoff |
|-----------|-----------|----------|----------|
| WAL | Sequential log before data update | All databases | Write latency (fsync) |
| Synchronous Replication | Wait for replica ack | Financial, critical data | Higher latency |
| Asynchronous Replication | Background sync | Social, logs | Small data loss window |
| Erasure Coding | k data + m parity fragments | Cold storage, archives | CPU overhead, slower recovery |
| Checksums | Hash verification per block | All storage | CPU overhead |
| Raft Consensus | Majority ack for each log entry | Distributed DBs, coordination | Network latency |
| PITR | Snapshots + WAL archiving | Disaster recovery | Storage cost |
| Immutable Versioning | Never overwrite; soft delete | Object storage, compliance | Storage growth |

#### 8.3.2 Replication Cheat Sheet

| Replication Mode | Data Loss Risk | Latency Impact | When to Use |
|-----------------|---------------|----------------|-------------|
| Synchronous (all) | Zero | High | Banking, ledgers |
| Synchronous (quorum) | Zero (if quorum survives) | Moderate | Distributed DBs |
| Asynchronous | Small window | Low | Social, CDN, logs |
| None | 100% on failure | Lowest | Cache, ephemeral |

#### 8.3.3 RPO vs RTO
- **RPO (Recovery Point Objective):** Maximum acceptable data loss measured in time. Zero for sync replication; hours for daily backups.
- **RTO (Recovery Time Objective):** Maximum acceptable time to restore service. Minutes for automated failover; days for manual restore from tape.
- **Goal:** Minimize both within budget constraints.

#### 8.3.4 Key Interview Sentences
> *“To ensure durability, I start with write‑ahead logging so no committed change is lost on crash. I use synchronous replication to at least two nodes for critical data that cannot tolerate any loss, and asynchronous replication for cross‑region backups. I add checksums to detect silent corruption, and regular backups with point‑in‑time recovery to guard against human error.”*

> *“For large‑scale blob storage, I use erasure coding instead of full replication to reduce overhead while maintaining high durability. A 17+3 code can tolerate any 3 failures with only ~1.18x storage overhead versus 3x for triple replication.”*

> *“I verify that every 'durable' write is acknowledged only after it's on at least two physically independent persistent media. I also regularly test restores because a backup is not durable until you've proven you can recover from it.”*

#### 8.3.5 Self-Test Questions
1. What is the difference between durability and availability? Can a system be durable but not available?
2. How does write‑ahead logging ensure durability? What happens during crash recovery?
3. When would you choose synchronous replication over asynchronous? Give a concrete example.
4. Why is erasure coding preferred over triple replication for cold storage?
5. What are RPO and RTO? A system does daily backups at midnight. What is its RPO? What if it also has synchronous replication?
6. What are two ways to detect silent data corruption?
7. Why are backups necessary even if you have replication?

*End of consolidated notes.*
