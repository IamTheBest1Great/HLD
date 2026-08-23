# HLD Phase 1 — Complete Foundational Roadmap
> **Goal:** After studying everything in this phase, you should be able to explain every concept in 60 seconds, make confident architectural decisions, and handle 95% of HLD interview questions at any top product company.

---

## 📁 File Index

| # | File | Concepts Covered |
|---|------|-----------------|
| 1 | `01_networking_and_protocols.md` | HTTP/S, TCP vs UDP, DNS, WebSockets, Long Polling, SSE, gRPC, REST, GraphQL |
| 2 | `02_scaling_fundamentals.md` | Vertical vs Horizontal scaling, Stateless vs Stateful, Load Balancers (L4/L7), Auto-scaling |
| 3 | `03_databases_sql_vs_nosql.md` | SQL, NoSQL types, ACID vs BASE, when to use which |
| 4 | `04_cap_theorem_and_consistency.md` | CAP theorem, PACELC, consistency models, strong vs eventual |
| 5 | `05_database_internals.md` | Sharding, Replication, Indexing, B-Tree vs LSM Tree |
| 6 | `06_caching.md` | Cache strategies, eviction policies, multi-layer caching, cache problems |
| 7 | `07_message_queues_and_async.md` | Queues vs Streams, Kafka vs RabbitMQ, Pub/Sub, delivery guarantees |
| 8 | `08_consistent_hashing.md` | Hash rings, virtual nodes, why it matters |
| 9 | `09_storage_systems.md` | Blob/Object/Block/File storage, CDN, chunking, deduplication |
| 10 | `10_reliability_patterns.md` | Rate limiting algorithms, circuit breaker, retry/backoff, idempotency, health checks |
| 11 | `11_back_of_envelope_estimation.md` | Numbers every engineer must know, QPS, storage, bandwidth calculations |

---

## 🗓️ Study Schedule (2 Weeks)

```
Week 1 — Networking, Scaling, Databases
─────────────────────────────────────────
Day 1:  File 01 — Networking & Protocols        (2–3 hrs)
Day 2:  File 02 — Scaling Fundamentals          (2 hrs)
Day 3:  File 03 — Databases SQL vs NoSQL        (2–3 hrs)
Day 4:  File 04 — CAP Theorem & Consistency     (2 hrs)
Day 5:  File 05 — Database Internals            (2–3 hrs)
Day 6:  Review Week 1 + self-quiz               (2 hrs)
Day 7:  Rest / light review

Week 2 — Caching, Queues, Patterns, Math
─────────────────────────────────────────
Day 8:  File 06 — Caching                       (2 hrs)
Day 9:  File 07 — Message Queues & Async        (2–3 hrs)
Day 10: File 08 — Consistent Hashing            (1.5 hrs)
Day 11: File 09 — Storage Systems               (2 hrs)
Day 12: File 10 — Reliability Patterns          (2 hrs)
Day 13: File 11 — Back-of-Envelope Estimation   (2 hrs)
Day 14: Full review + practice estimation problems
```

---

## 🧠 The Complete Concept List

### Networking & Protocols
- [ ] HTTP vs HTTPS — TLS handshake, certificates
- [ ] HTTP/1.1 vs HTTP/2 vs HTTP/3 — multiplexing, head-of-line blocking
- [ ] TCP vs UDP — reliability tradeoffs, use cases
- [ ] DNS — resolution chain, TTL, types of records
- [ ] WebSockets — handshake, full-duplex, use cases
- [ ] Long Polling — how it differs from regular polling
- [ ] Server-Sent Events (SSE) — one-directional streaming
- [ ] gRPC — protobuf, streaming types, when to use over REST
- [ ] REST vs GraphQL — query flexibility vs over/under-fetching
- [ ] Reverse proxy vs Forward proxy

### Scaling
- [ ] Vertical scaling — limits, when to use
- [ ] Horizontal scaling — stateless requirement, complexity
- [ ] Stateless vs Stateful services — session handling implications
- [ ] Load balancer algorithms — Round Robin, Least Connections, IP Hash, Weighted
- [ ] Layer 4 vs Layer 7 load balancing — what each can see and do
- [ ] Auto-scaling — metric triggers, warm-up time, scale-in protection
- [ ] Session stickiness — sticky sessions, external session store

### Databases
- [ ] SQL databases — tables, joins, indexes, ACID
- [ ] NoSQL — Key-Value, Document, Wide-Column, Graph — use cases for each
- [ ] ACID properties — Atomicity, Consistency, Isolation, Durability
- [ ] BASE properties — Basically Available, Soft state, Eventual consistency
- [ ] When to choose SQL vs NoSQL — decision matrix

### CAP & Consistency
- [ ] CAP Theorem — definition, why CA is impossible in distributed systems
- [ ] CP vs AP systems — examples of each
- [ ] PACELC — extends CAP with latency tradeoff
- [ ] Strong consistency — linearizability
- [ ] Eventual consistency — what "eventually" means
- [ ] Read-your-writes, monotonic reads, causal consistency

### Database Internals
- [ ] Sharding — range, hash, directory sharding; hotspot problems
- [ ] Replication — leader-follower, multi-leader, leaderless
- [ ] Sync vs async replication — durability vs performance tradeoff
- [ ] B-Tree indexes — how they work, when they help
- [ ] LSM Tree — write-optimized, compaction, Cassandra/RocksDB
- [ ] Secondary indexes in distributed DBs
- [ ] Read replicas — how they help, replication lag issues

### Caching
- [ ] Cache-aside (lazy loading) — miss penalty, stale data risk
- [ ] Read-through — cache handles DB interaction
- [ ] Write-through — write to cache + DB simultaneously
- [ ] Write-back (write-behind) — async DB write, data loss risk
- [ ] Write-around — bypass cache on write
- [ ] LRU eviction — least recently used
- [ ] LFU eviction — least frequently used
- [ ] TTL — time-based expiry
- [ ] Cache stampede / thundering herd — and how to prevent it
- [ ] Cache penetration, cache breakdown — and solutions
- [ ] Multi-layer caching — CDN → Application cache → DB cache

### Message Queues & Async
- [ ] Queue vs Stream — message consumed once vs persistent log
- [ ] Pub/Sub pattern — producers, consumers, topics
- [ ] Kafka architecture — brokers, topics, partitions, consumer groups
- [ ] RabbitMQ vs Kafka — when to use each
- [ ] At-most-once delivery — may lose messages
- [ ] At-least-once delivery — may duplicate messages
- [ ] Exactly-once delivery — hard, requires idempotent consumers
- [ ] Backpressure — producer faster than consumer
- [ ] Message ordering guarantees

### Consistent Hashing
- [ ] Why naive modulo hashing fails with node changes
- [ ] Hash ring concept — mapping keys and nodes
- [ ] Virtual nodes — why and how many
- [ ] Consistent hashing in practice (Redis Cluster, Cassandra, CDNs)

### Storage Systems
- [ ] Object/Blob storage — S3, immutable, unlimited scale
- [ ] Block storage — EBS, raw blocks, databases use this
- [ ] File storage — NFS, shared filesystem
- [ ] CDN — pull vs push, cache invalidation, GeoDNS
- [ ] Chunking large files — why and how
- [ ] Deduplication — hash-based, block-level

### Reliability Patterns
- [ ] Token bucket algorithm — rate limiting with bursts
- [ ] Leaky bucket — smooth output rate
- [ ] Sliding window counter — accurate rate limiting
- [ ] Circuit breaker — open/closed/half-open states
- [ ] Retry with exponential backoff + jitter
- [ ] Idempotency — same request, same result
- [ ] Bulkhead pattern — isolate failures
- [ ] Health checks — heartbeat, liveness, readiness probes
- [ ] SPOF analysis — single point of failure identification

### Back-of-Envelope Estimation
- [ ] Numbers every engineer must know (latency, storage units)
- [ ] DAU → QPS conversion formula
- [ ] Peak QPS estimation
- [ ] Storage per year calculation
- [ ] Bandwidth estimation
- [ ] Cache size estimation (20% rule)

---

## ✅ Self-Quiz After Each File

After reading each file, close it and answer:
1. Explain the concept in 60 seconds as if to a junior engineer
2. Give one real-world system that uses this concept
3. What is the key trade-off this concept involves?
4. When would you NOT use this approach?

If you can't answer all four, re-read the relevant section.

---

## 🔗 How These Connect to Interview Problems

```
Networking  ─────────────────► WhatsApp, Zoom, Twitter, any real-time system
Scaling     ─────────────────► Every single problem (it's always asked)
Databases   ─────────────────► Payment systems, booking, social feeds
CAP Theorem ─────────────────► Justifying DB choice in any problem
Sharding    ─────────────────► Twitter, Uber, YouTube, any large-scale DB
Caching     ─────────────────► Instagram, YouTube, URL shortener, feeds
Queues      ─────────────────► Notification service, Uber, payment flows
Cons. Hash  ─────────────────► Cache design, distributed DB partitioning
Storage     ─────────────────► YouTube, Dropbox, S3, any media system
Reliability ─────────────────► Payment, booking, any production system
Estimation  ─────────────────► Every interview — done in first 5 minutes
```
