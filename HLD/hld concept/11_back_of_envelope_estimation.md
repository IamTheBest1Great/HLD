# 11 — Back-of-Envelope Estimation
> **Why this matters:** This is the first technical step in every HLD interview. Done in 5 minutes, it tells you whether you need caching, sharding, multiple data centers, or a CDN. Interviewers who skip this produce systems that are either wildly over-engineered or embarrassingly under-powered.

---

## 1. Numbers Every Engineer Must Know Cold

Memorize these. They are the building blocks of every estimation.

### Latency Numbers

```
Operation                          Latency        Notes
─────────────────────────────────────────────────────────────────
L1 cache reference                 0.5 ns
L2 cache reference                 7 ns
RAM reference (main memory)        100 ns
Compress 1KB with Snappy           3,000 ns       3 µs
Read 1MB sequentially from RAM     250,000 ns     250 µs
SSD random read                    150,000 ns     150 µs
Read 1MB sequentially from SSD     1,000,000 ns   1 ms
HDD seek                           10,000,000 ns  10 ms
Read 1MB sequentially from HDD     30,000,000 ns  30 ms
Round trip within same datacenter  500,000 ns     0.5 ms
Round trip US to Europe            150,000,000 ns 150 ms

RULES OF THUMB:
  Memory:     ~100ns   → microseconds
  SSD:        ~150µs   → milliseconds (1000x slower than memory)
  HDD:        ~10ms    → milliseconds (100x slower than SSD)
  Same DC:    ~0.5ms
  Cross-US:   ~40ms
  US-Europe:  ~150ms
  US-Asia:    ~200ms
```

### Storage Units

```
1 Byte   = 8 bits
1 KB     = 1,000 bytes        (kilobyte)
1 MB     = 1,000,000 bytes    (megabyte)
1 GB     = 1,000,000,000      (gigabyte)
1 TB     = 1,000,000,000,000  (terabyte)
1 PB     = 10^15 bytes        (petabyte)

MEMORY AIDS:
  1 character (ASCII) = 1 byte
  1 page of text = ~2 KB
  1 MP3 song = ~3 MB (compressed)
  1 photo (smartphone) = ~3 MB
  1 minute of HD video = ~100 MB
  1 hour of 4K video = ~15 GB
```

### Throughput Numbers

```
Single server (web app):          ~10,000 QPS
PostgreSQL single node:           ~10,000 writes/sec
                                  ~100,000 reads/sec (with index)
Redis single node:                ~100,000-1,000,000 ops/sec
Kafka single partition:           ~1,000,000 msgs/sec
Cassandra single node:            ~100,000 writes/sec
Network bandwidth (modern server): 10 Gbps = 1.25 GB/sec
SSD write throughput:             ~500 MB/sec
SSD read throughput:              ~3,500 MB/sec (NVMe)
```

### Time Conversions

```
1 minute  = 60 seconds
1 hour    = 3,600 seconds
1 day     = 86,400 seconds       ← memorize this
1 month   = 2,500,000 seconds    ≈ 2.5 million
1 year    = 31,000,000 seconds   ≈ 31 million

QUICK APPROXIMATIONS:
  100K req/day  → ~1 req/second
  1M req/day    → ~12 req/second
  10M req/day   → ~115 req/second ≈ 100 req/second
  1B req/day    → ~11,500 req/second ≈ 10K req/second
```

---

## 2. The Estimation Framework (5 Steps)

Do this in the first 5 minutes of every interview. Talk through each step out loud.

```
STEP 1: Users and Activity
  → How many Daily Active Users (DAU)?
  → What actions do they perform and how often?

STEP 2: Compute QPS
  → Read QPS and Write QPS separately

STEP 3: Calculate Storage
  → Per item size × items per day × retention period

STEP 4: Calculate Bandwidth
  → Data in/out per second

STEP 5: Draw Conclusions
  → Do we need sharding? caching? CDN? multiple DCs?
```

---

## 3. Worked Example 1: Twitter-Scale System

Let's estimate fully.

### Step 1: Users and Activity

```
Given:
  Daily Active Users (DAU) = 200 million
  Average user reads feed: 5 times/day
  Average user posts tweet: 0.5 times/day (1 tweet every 2 days)
  Average tweet has: 50% text-only, 50% with media (image/video)
  Read:Write ratio = 10:1
```

### Step 2: Compute QPS

```
WRITE QPS (tweets posted):
  200M users × 0.5 tweets/day = 100M tweets/day
  100M tweets / 86,400 seconds = ~1,160 tweets/second
  Peak (3× average) = ~3,500 tweets/second

READ QPS (feed loads):
  200M users × 5 feed loads/day = 1B feed loads/day
  1B / 86,400 = ~11,600 reads/second
  Peak = ~35,000 reads/second

CONCLUSION:
  Writes: ~3,500/sec → single DB primary handles this
  Reads: ~35,000/sec → need read replicas + caching
```

### Step 3: Storage

```
TWEET STORAGE:
  Tweet metadata (text, user_id, timestamp, likes_count):
    ~500 bytes per tweet (text up to 280 chars + metadata)
  
  100M tweets/day × 500 bytes = 50 GB/day
  50 GB/day × 365 days = ~18 TB/year

MEDIA STORAGE (50% of tweets have media):
  50M tweets/day with media
  Average image: 500 KB (compressed)
  50M × 500 KB = 25 TB/day ← This is the big one!
  25 TB/day × 365 = ~9 PB/year

TOTAL STORAGE NEEDED:
  Text metadata: ~18 TB/year → SQL DB (manageable)
  Media files: ~9 PB/year → Object storage (S3) is the only option

CONCLUSION:
  Text to Cassandra/PostgreSQL
  Media to S3 + CDN (non-negotiable at this scale)
```

### Step 4: Bandwidth

```
WRITE BANDWIDTH:
  Text tweets: 3,500/sec × 500 bytes = 1.75 MB/sec (tiny)
  Media uploads: 50M media/day / 86,400 = 578 media/sec
                 578 × 500 KB = 289 MB/sec upload bandwidth needed

READ BANDWIDTH:
  Text reads: 35,000/sec × 500 bytes = 17.5 MB/sec
  Media reads: CDN handles this (each PoP serves region's load)
               Without CDN: 35,000 reads × 50% media × 500KB = 8.75 GB/sec ← impossible
               With CDN: Origin serves cache misses only (~5% of reads)
               8.75 GB/sec × 0.05 = 437 MB/sec from origin to CDN ← manageable

CONCLUSION:
  CDN is not optional for media. Without it, bandwidth alone kills the system.
```

### Step 5: Conclusions from Estimation

```
✅ Writes (3,500/sec): Single DB primary handles this comfortably
✅ Reads (35,000/sec): Need read replicas + Redis cache (cache hit rate ~95%)
✅ Storage (9 PB/year media): S3 for media, sharded DB for metadata
✅ Bandwidth: CDN mandatory, upload servers with chunking for large media
✅ Scale: 200M DAU → single-region won't cut it → multi-region needed
```

---

## 4. Worked Example 2: URL Shortener

```
ASSUMPTIONS:
  100M shortened URLs created per day
  10B redirects per day (100:1 read:write ratio)

WRITE QPS:
  100M / 86,400 ≈ 1,160 writes/second

READ QPS:
  10B / 86,400 ≈ 115,700 redirects/second ← heavy read load!
  Peak: ~350,000 redirects/second

URL STORAGE:
  Each URL record: short_key (8B) + long_url (512B) + metadata (128B) ≈ 650 bytes
  100M/day × 650 bytes = 65 GB/day
  Retain for 5 years: 65 GB × 365 × 5 = ~119 TB

CACHE SIZE (80/20 rule — 20% of URLs get 80% of traffic):
  20% of daily active URLs cached
  Assume 100M unique URLs stored; 20M hot URLs
  20M × 650 bytes = 13 GB ← fits easily in Redis cluster

BANDWIDTH:
  Write: 1,160 × 650 bytes = 0.75 MB/sec (trivial)
  Read: 115,700 × 650 bytes = 75 MB/sec outbound

CONCLUSIONS:
  115,700 reads/sec → can't hit DB → Redis cache essential
  Cache hit rate target: 99% (only 1% misses go to DB → 1,157 DB reads/sec)
  13 GB cache size → single Redis instance suffices (but add replica for HA)
  119 TB storage → PostgreSQL or Cassandra, sharded if needed
  DB write: 1,160/sec → single primary handles comfortably
```

---

## 5. Worked Example 3: WhatsApp

```
ASSUMPTIONS:
  2B registered users
  500M Daily Active Users
  Each user sends 40 messages/day
  Each message: 1KB average (text + metadata)
  20% of messages have media (images: 500KB avg)

WRITE QPS (messages sent):
  500M users × 40 messages/day = 20B messages/day
  20B / 86,400 ≈ 231,500 messages/second
  Peak: ~700,000 messages/second ← extreme write load

READ QPS:
  Each message delivered to at least 1 recipient (1:1) or many (group)
  Assume average 1.5 recipients per message:
  231,500 × 1.5 = ~347,000 message deliveries/second

MESSAGE STORAGE:
  20B messages/day × 1 KB = 20 TB/day of text messages
  20B × 20% = 4B media messages/day
  4B × 500 KB = 2,000 TB = 2 PB/day of media ← massive!
  
  Retention policy: 30 days on servers (WhatsApp deletes after delivery or 30 days)
  Storage needed: 2 PB/day × 30 days = 60 PB for media
  Text: 20 TB × 30 = 600 TB

CONNECTIONS:
  500M DAU actively connected via WebSocket
  500M concurrent WebSocket connections
  A single server can handle ~65,000 concurrent connections (TCP limit)
  500M / 65,000 = ~7,700 WebSocket servers needed!

CONCLUSIONS:
  WebSocket servers: ~8,000 needed (just for connections)
  Message throughput: 700K/sec → Kafka essential for buffering
  Media: Object storage (S3-equivalent) at PB scale
  Consistency: Each conversation's messages must be ordered → partition by conversation_id in Kafka
```

---

## 6. Worked Example 4: YouTube Upload

```
ASSUMPTIONS:
  2B users, 50M DAU content watchers
  500,000 video uploads per day
  Average video: 30 minutes, 1080p = ~1.5 GB raw
  After transcoding into multiple formats (360p, 720p, 1080p, 4K): ~3× storage
  
UPLOAD QPS:
  500,000 uploads / 86,400 ≈ 6 uploads/second (low QPS, high bandwidth)

UPLOAD BANDWIDTH:
  6 uploads/sec × 1.5 GB = 9 GB/sec incoming bandwidth
  Need multiple upload servers + chunked upload infrastructure

TRANSCODING:
  Each upload → transcoded to ~8 formats → 8 jobs per upload
  6 uploads/sec → 48 transcoding jobs/second
  1 transcoding job takes ~5 minutes
  Concurrent jobs = 48 × 300 seconds = 14,400 concurrent transcoding jobs
  → Large worker pool needed (dedicated transcoding servers or spot instances)

STORAGE:
  500K uploads/day × 1.5 GB × 3 (transcoded formats) = 2.25 PB/day
  After 5 years: enormous (YouTube stores everything)
  Conclusion: S3-class storage, tiered (hot/warm/cold based on view count)

VIEW QPS:
  50M watchers/day × 5 videos each = 250M video starts/day
  250M / 86,400 ≈ 2,900 video starts/second
  Average video streaming: 5 Mbps (1080p)
  2,900 × 5 Mbps = 14.5 Gbps outbound → CDN handles this

CONCLUSIONS:
  CDN is the backbone of video delivery
  Storage: Tiered object storage (hot videos on fast storage, old videos on cold/cheap)
  Transcoding: Async job queue (Kafka) → worker pool (auto-scaled)
  Upload: Chunked multipart upload directly to S3
```

---

## 7. Common Estimation Patterns

### The 80/20 Rule (Pareto Principle for Caching)

```
80% of traffic goes to 20% of content.
→ Cache 20% of your data → serve 80% of requests from cache.

Cache size = 0.2 × daily_data_volume
Example (Twitter):
  100M tweets/day × 500 bytes = 50 GB/day of tweet data
  Cache size = 0.2 × 50 GB = 10 GB
  10 GB fits in a single Redis instance with room to spare.
```

### QPS to Servers

```
Single commodity server: ~10,000 QPS (for typical web app)

Servers needed = Peak QPS / QPS per server

Example: Peak 35,000 reads/sec
  35,000 / 10,000 = 3.5 → need 4 servers minimum
  With N+1 redundancy: 5 servers
  With headroom for growth: 8 servers
```

### Storage with Replication

```
Always multiply raw storage by replication factor.

Database (replication factor 3): 100 TB raw → 300 TB total disk
S3 (3 AZ replication, built-in): You store 1 PB, they manage ~3 PB internally
Kafka (replication factor 2): 10 TB of messages → 20 TB disk

Always quote raw storage in interview, mention replication as a design choice.
```

---

## 8. Interview Cheat Sheet

```
INSTANT APPROXIMATIONS:
─────────────────────────────────────────────────────────────────
DAU 100M, 1 action/day   → 1K QPS
DAU 100M, 10 actions/day → 10K QPS
DAU 1B, 10 actions/day   → 100K QPS

1 KB per record, 1M records → 1 GB
1 KB per record, 1B records → 1 TB

1M req/day / 86,400 ≈ 12 req/second (use 10 for easy math)
1B req/day / 86,400 ≈ 12K req/second (use 10K)

10 bytes × 1B = 10 GB
100 bytes × 1B = 100 GB
1 KB × 1B = 1 TB
1 MB × 1B = 1 PB

WHEN TO USE WHAT:
─────────────────────────────────────────────────────────────────
Peak QPS < 1,000    → Single server, maybe cache
Peak QPS 1K–10K     → Single server + read replicas + cache
Peak QPS 10K–100K   → Multiple servers + aggressive caching
Peak QPS > 100K     → Sharding + CDN + distributed architecture

Storage < 1 TB      → Single DB, simple setup
Storage 1 TB–10 TB  → Single DB with read replicas
Storage 10 TB–1 PB  → Sharded DB + object storage for blobs
Storage > 1 PB      → Object storage only, distributed systems
```

---

## 📝 Key Interview Sentences

> *"Let me do a quick estimation before diving into the design. At 200M DAU, each user making 5 reads and 0.5 writes per day, I get roughly 35,000 reads/sec and 1,200 writes/sec at peak, scaling by 3×. This tells me reads are the bottleneck, so I'll design around a caching layer targeting a 95% cache hit rate — that reduces DB read load to about 1,750 reads/sec, which a single primary with two read replicas handles comfortably."*

> *"The storage estimate is interesting — text metadata is about 18 TB/year, but media is 9 PB/year. That asymmetry completely drives the architecture: text goes to a relational DB, media goes to object storage with CDN, no exceptions."*

> *"At 500M concurrent WebSocket connections we need roughly 8,000 WebSocket gateway servers. That's not a small number — it means the connection layer itself needs its own load balancing and service discovery infrastructure."*

---

## ✅ Self-Test Questions

1. You're designing Instagram. DAU = 500M, each user views 20 photos/day and uploads 1 photo every 10 days. Each photo is 3MB. Calculate: write QPS, read QPS, storage per year.

2. A URL shortener has 100M redirects/day. The cache hit rate is 99%. How many DB reads/second does that translate to? Is that manageable?

3. WhatsApp has 500M DAU. Each user sends 40 messages/day. How many concurrent WebSocket connections are there? How many servers do you need if each handles 50,000 connections?

4. You're told "we have 1B users, each generates 1 KB of data per day." How much storage is that per year? If you replicate 3×, what's the total disk?

5. Your system handles 100,000 QPS. A single server handles 10,000 QPS. How many servers do you need accounting for N+1 redundancy and 50% headroom for growth?
