# Estimation Skills (Back of Envelope) — Consolidated HLD Notes

## Table of Contents

- [1. Estimation Fundamentals](#1-estimation-fundamentals)
  - [1.1 Why Estimation Matters](#11-why-estimation-matters)
    - [1.1.1 Capacity Planning](#111-capacity-planning)
    - [1.1.2 Infrastructure Sizing](#112-infrastructure-sizing)
    - [1.1.3 Cost Estimation](#113-cost-estimation)
    - [1.1.4 Architecture Decisions](#114-architecture-decisions)
    - [1.1.5 Interview Problem Solving](#115-interview-problem-solving)
  - [1.2 Core Estimation Principles](#12-core-estimation-principles)
    - [1.2.1 Start with Assumptions](#121-start-with-assumptions)
    - [1.2.2 Use Round Numbers](#122-use-round-numbers)
    - [1.2.3 Focus on Order of Magnitude](#123-focus-on-order-of-magnitude)
    - [1.2.4 Clearly State Assumptions](#124-clearly-state-assumptions)
    - [1.2.5 Validate Sanity of Results](#125-validate-sanity-of-results)
  - [1.3 Key Metrics & Units](#13-key-metrics--units)
    - [1.3.1 DAU (Daily Active Users)](#131-dau-daily-active-users)
    - [1.3.2 MAU (Monthly Active Users)](#132-mau-monthly-active-users)
    - [1.3.3 QPS (Queries Per Second)](#133-qps-queries-per-second)
    - [1.3.4 TPS (Transactions Per Second)](#134-tps-transactions-per-second)
    - [1.3.5 Storage Units](#135-storage-units)
    - [1.3.6 Bandwidth Units](#136-bandwidth-units)
    - [1.3.7 Latency Units](#137-latency-units)
  - [1.4 Common Conversions](#14-common-conversions)
    - [1.4.1 KB → MB → GB → TB → PB](#141-kb--mb--gb--tb--pb)
    - [1.4.2 Seconds → Minutes → Hours → Days](#142-seconds--minutes--hours--days)
    - [1.4.3 Bits vs Bytes](#143-bits-vs-bytes)
    - [1.4.4 Requests per Day → QPS](#144-requests-per-day--qps)
- [2. Numbers Every Engineer Should Know](#2-numbers-every-engineer-should-know)
  - [2.1 Latency Numbers](#21-latency-numbers)
    - [2.1.1 Memory Access](#211-memory-access)
    - [2.1.2 SSD Access](#212-ssd-access)
    - [2.1.3 HDD Access](#213-hdd-access)
    - [2.1.4 Network Round Trip](#214-network-round-trip)
    - [2.1.5 Cross-Region Latency](#215-cross-region-latency)
  - [2.2 Throughput Numbers](#22-throughput-numbers)
    - [2.2.1 Disk Throughput](#221-disk-throughput)
    - [2.2.2 Network Bandwidth](#222-network-bandwidth)
    - [2.2.3 Database Throughput](#223-database-throughput)
    - [2.2.4 Cache Throughput](#224-cache-throughput)
  - [2.3 Time Conversions](#23-time-conversions)
    - [2.3.1 Seconds per Day](#231-seconds-per-day)
    - [2.3.2 Seconds per Month](#232-seconds-per-month)
    - [2.3.3 Seconds per Year](#233-seconds-per-year)
  - [2.4 Storage Reference Numbers](#24-storage-reference-numbers)
    - [2.4.1 Tweet Size](#241-tweet-size)
    - [2.4.2 Image Size](#242-image-size)
    - [2.4.3 Video Size](#243-video-size)
    - [2.4.4 Log Entry Size](#244-log-entry-size)
- [3. Traffic Estimation](#3-traffic-estimation)
  - [3.1 DAU to QPS](#31-dau-to-qps)
    - [3.1.1 Basic Formula](#311-basic-formula)
    - [3.1.2 Peak Traffic Estimation](#312-peak-traffic-estimation)
    - [3.1.3 Read vs Write QPS](#313-read-vs-write-qps)
    - [3.1.4 Peak Multiplier Assumptions](#314-peak-multiplier-assumptions)
  - [3.2 Request Distribution](#32-request-distribution)
    - [3.2.1 Read-Heavy Systems](#321-read-heavy-systems)
    - [3.2.2 Write-Heavy Systems](#322-write-heavy-systems)
    - [3.2.3 Fan-Out Systems](#323-fan-out-systems)
  - [3.3 Example Calculations](#33-example-calculations)
    - [3.3.1 Twitter Timeline Requests](#331-twitter-timeline-requests)
    - [3.3.2 Instagram Feed Refreshes](#332-instagram-feed-refreshes)
    - [3.3.3 Search Query Traffic](#333-search-query-traffic)
  - [3.4 Estimating Server Count](#34-estimating-server-count)
    - [3.4.1 QPS per Server](#341-qps-per-server)
    - [3.4.2 Horizontal Scaling](#342-horizontal-scaling)
    - [3.4.3 Redundancy Buffer](#343-redundancy-buffer)
- [4. Storage Estimation](#4-storage-estimation)
  - [4.1 Storage Estimation Formula](#41-storage-estimation-formula)
    - [4.1.1 Data Size per Object](#411-data-size-per-object)
    - [4.1.2 Objects per Day](#412-objects-per-day)
    - [4.1.3 Retention Period](#413-retention-period)
    - [4.1.4 Replication Factor](#414-replication-factor)
  - [4.2 Database Storage](#42-database-storage)
    - [4.2.1 Row Size Estimation](#421-row-size-estimation)
    - [4.2.2 Index Storage](#422-index-storage)
    - [4.2.3 Metadata Overhead](#423-metadata-overhead)
  - [4.3 Media Storage](#43-media-storage)
    - [4.3.1 Image Storage](#431-image-storage)
    - [4.3.2 Video Storage](#432-video-storage)
    - [4.3.3 Compression Effects](#433-compression-effects)
  - [4.4 Example Calculations](#44-example-calculations)
    - [4.4.1 Twitter Tweet Storage](#441-twitter-tweet-storage)
    - [4.4.2 YouTube Video Storage](#442-youtube-video-storage)
    - [4.4.3 WhatsApp Media Storage](#443-whatsapp-media-storage)
    - [4.4.4 Log Storage](#444-log-storage)
  - [4.5 Storage with Replication](#45-storage-with-replication)
    - [4.5.1 3x Replication](#451-3x-replication)
    - [4.5.2 Erasure Coding](#452-erasure-coding)
    - [4.5.3 Backup Storage](#453-backup-storage)
- [5. Bandwidth Estimation](#5-bandwidth-estimation)
  - [5.1 Bandwidth Fundamentals](#51-bandwidth-fundamentals)
    - [5.1.1 Ingress Traffic](#511-ingress-traffic)
    - [5.1.2 Egress Traffic](#512-egress-traffic)
    - [5.1.3 Internal Network Traffic](#513-internal-network-traffic)
  - [5.2 Bandwidth Estimation Formula](#52-bandwidth-estimation-formula)
    - [5.2.1 Request Size](#521-request-size)
    - [5.2.2 Response Size](#522-response-size)
    - [5.2.3 Requests per Second](#523-requests-per-second)
  - [5.3 Example Calculations](#53-example-calculations)
    - [5.3.1 Twitter Timeline Bandwidth](#531-twitter-timeline-bandwidth)
    - [5.3.2 WhatsApp Message Traffic](#532-whatsapp-message-traffic)
    - [5.3.3 Netflix Streaming Bandwidth](#533-netflix-streaming-bandwidth)
    - [5.3.4 CDN Traffic Estimation](#534-cdn-traffic-estimation)
  - [5.4 Compression Impact](#54-compression-impact)
    - [5.4.1 Gzip Compression](#541-gzip-compression)
    - [5.4.2 Video Compression](#542-video-compression)
    - [5.4.3 Bandwidth Savings](#543-bandwidth-savings)
- [6. Caching Estimation](#6-caching-estimation)
  - [6.1 Cache Sizing](#61-cache-sizing)
    - [6.1.1 Hot Data Percentage](#611-hot-data-percentage)
    - [6.1.2 Cache Object Size](#612-cache-object-size)
    - [6.1.3 Memory Requirements](#613-memory-requirements)
  - [6.2 Cache Hit Ratio](#62-cache-hit-ratio)
    - [6.2.1 Why Hit Ratio Matters](#621-why-hit-ratio-matters)
    - [6.2.2 Read Reduction](#622-read-reduction)
    - [6.2.3 Latency Reduction](#623-latency-reduction)
  - [6.3 Cache Traffic Reduction](#63-cache-traffic-reduction)
    - [6.3.1 Database Offloading](#631-database-offloading)
    - [6.3.2 CDN Cache Savings](#632-cdn-cache-savings)
    - [6.3.3 Hot Key Distribution](#633-hot-key-distribution)
  - [6.4 Example Calculations](#64-example-calculations)
    - [6.4.1 Twitter Timeline Cache](#641-twitter-timeline-cache)
    - [6.4.2 Product Catalog Cache](#642-product-catalog-cache)
    - [6.4.3 Search Query Cache](#643-search-query-cache)
  - [6.5 The 80/20 Rule](#65-the-8020-rule)
    - [6.5.1 Pareto Principle](#651-pareto-principle)
    - [6.5.2 Hot Data Patterns](#652-hot-data-patterns)
    - [6.5.3 Cache Optimization](#653-cache-optimization)
- [7. Estimation Framework](#7-estimation-framework)
  - [7.1 The 5-Step Estimation Process](#71-the-5-step-estimation-process)
    - [7.1.1 Understand Requirements](#711-understand-requirements)
    - [7.1.2 Estimate Users & Traffic](#712-estimate-users--traffic)
    - [7.1.3 Estimate Storage](#713-estimate-storage)
    - [7.1.4 Estimate Bandwidth](#714-estimate-bandwidth)
    - [7.1.5 Estimate Infrastructure](#715-estimate-infrastructure)
  - [7.2 Identifying Bottlenecks](#72-identifying-bottlenecks)
    - [7.2.1 CPU Bottlenecks](#721-cpu-bottlenecks)
    - [7.2.2 Memory Bottlenecks](#722-memory-bottlenecks)
    - [7.2.3 Disk Bottlenecks](#723-disk-bottlenecks)
    - [7.2.4 Network Bottlenecks](#724-network-bottlenecks)
  - [7.3 Sanity Checking](#73-sanity-checking)
    - [7.3.1 Compare with Real Systems](#731-compare-with-real-systems)
    - [7.3.2 Check Order of Magnitude](#732-check-order-of-magnitude)
    - [7.3.3 Validate Peak Estimates](#733-validate-peak-estimates)
- [8. Worked Examples](#8-worked-examples)
  - [8.1 Twitter-Scale System](#81-twitter-scale-system)
    - [8.1.1 User Activity Estimation](#811-user-activity-estimation)
    - [8.1.2 Timeline QPS](#812-timeline-qps)
    - [8.1.3 Tweet Storage](#813-tweet-storage)
    - [8.1.4 Feed Cache Estimation](#814-feed-cache-estimation)
    - [8.1.5 Bandwidth Estimation](#815-bandwidth-estimation)
  - [8.2 URL Shortener](#82-url-shortener)
    - [8.2.1 URL Creation QPS](#821-url-creation-qps)
    - [8.2.2 Redirect Traffic](#822-redirect-traffic)
    - [8.2.3 Database Storage](#823-database-storage)
    - [8.2.4 Cache Requirements](#824-cache-requirements)
  - [8.3 WhatsApp Messaging](#83-whatsapp-messaging)
    - [8.3.1 Message Throughput](#831-message-throughput)
    - [8.3.2 Concurrent Connections](#832-concurrent-connections)
    - [8.3.3 Media Storage](#833-media-storage)
    - [8.3.4 Real-Time Bandwidth](#834-real-time-bandwidth)
  - [8.4 YouTube / Netflix](#84-youtube--netflix)
    - [8.4.1 Video Upload Storage](#841-video-upload-storage)
    - [8.4.2 Streaming Bandwidth](#842-streaming-bandwidth)
    - [8.4.3 CDN Sizing](#843-cdn-sizing)
    - [8.4.4 Transcoding Costs](#844-transcoding-costs)
  - [8.5 Search Engine](#85-search-engine)
    - [8.5.1 Query QPS](#851-query-qps)
    - [8.5.2 Index Storage](#852-index-storage)
    - [8.5.3 Cache Layers](#853-cache-layers)
    - [8.5.4 Ranking Computation](#854-ranking-computation)
- [9. Common Estimation Patterns](#9-common-estimation-patterns)
  - [9.1 Read vs Write Ratios](#91-read-vs-write-ratios)
    - [9.1.1 Social Media Systems](#911-social-media-systems)
    - [9.1.2 Financial Systems](#912-financial-systems)
    - [9.1.3 Search Systems](#913-search-systems)
  - [9.2 QPS to Server Estimation](#92-qps-to-server-estimation)
    - [9.2.1 API Servers](#921-api-servers)
    - [9.2.2 Database Servers](#922-database-servers)
    - [9.2.3 Cache Servers](#923-cache-servers)
  - [9.3 Replication Overhead](#93-replication-overhead)
    - [9.3.1 Database Replication](#931-database-replication)
    - [9.3.2 Storage Replication](#932-storage-replication)
    - [9.3.3 Multi-Region Traffic](#933-multi-region-traffic)
  - [9.4 Peak vs Average Traffic](#94-peak-vs-average-traffic)
    - [9.4.1 Traffic Spikes](#941-traffic-spikes)
    - [9.4.2 Seasonal Scaling](#942-seasonal-scaling)
    - [9.4.3 Viral Traffic Patterns](#943-viral-traffic-patterns)
- [10. Common Pitfalls](#10-common-pitfalls)
  - [10.1 Ignoring Peak Traffic](#101-ignoring-peak-traffic)
  - [10.2 Forgetting Replication Costs](#102-forgetting-replication-costs)
  - [10.3 Underestimating Metadata](#103-underestimating-metadata)
  - [10.4 Ignoring CDN Savings](#104-ignoring-cdn-savings)
  - [10.5 Confusing Bits vs Bytes](#105-confusing-bits-vs-bytes)
  - [10.6 Ignoring Read/Write Ratios](#106-ignoring-readwrite-ratios)
  - [10.7 Overcomplicated Calculations](#107-overcomplicated-calculations)
  - [10.8 Unrealistic Assumptions](#108-unrealistic-assumptions)
- [11. Interview-Oriented HLD Notes](#11-interview-oriented-hld-notes)
  - [11.1 Estimation Best Practices](#111-estimation-best-practices)
    - [11.1.1 Talk Through Assumptions](#1111-talk-through-assumptions)
    - [11.1.2 Use Round Numbers](#1112-use-round-numbers)
    - [11.1.3 Focus on Scale](#1113-focus-on-scale)
    - [11.1.4 Prioritize Bottlenecks](#1114-prioritize-bottlenecks)
  - [11.2 Common Interview Questions](#112-common-interview-questions)
    - [11.2.1 Estimate QPS for Twitter](#1121-estimate-qps-for-twitter)
    - [11.2.2 Estimate Storage for YouTube](#1122-estimate-storage-for-youtube)
    - [11.2.3 Estimate CDN Bandwidth](#1123-estimate-cdn-bandwidth)
    - [11.2.4 Estimate Cache Size](#1124-estimate-cache-size)
    - [11.2.5 Estimate Number of Servers](#1125-estimate-number-of-servers)
  - [11.3 Quick Revision Material](#113-quick-revision-material)
    - [11.3.1 Common Formulas Cheat Sheet](#1131-common-formulas-cheat-sheet)
    - [11.3.2 Important Numbers to Memorize](#1132-important-numbers-to-memorize)
    - [11.3.3 Estimation Framework Summary](#1133-estimation-framework-summary)
    - [11.3.4 Key Interview Sentences](#1134-key-interview-sentences)
    - [11.3.5 Self-Test Questions](#1135-self-test-questions)

- [12. Quick Numbers by System Type](#12-quick-numbers-by-system-type)
  - [12.1 Summary: Key Metrics to Estimate by Pattern](#121-summary-key-metrics-to-estimate-by-pattern)
  - [12.2 Pattern A – CRUD & Storage Systems](#122-pattern-a---crud--storage-systems)
  - [12.3 Pattern B – Feed / Timeline Systems](#123-pattern-b---feed--timeline-systems)
  - [12.4 Pattern C – Real-Time Systems](#124-pattern-c---real-time-systems)
  - [12.5 Pattern D – Search Systems](#125-pattern-d---search-systems)
  - [12.6 Pattern E – Geospatial & Location-Based Systems](#126-pattern-e---geospatial--location-based-systems)
  - [12.7 Pattern F – Financial & Critical Consistency Systems](#127-pattern-f---financial--critical-consistency-systems)
  - [12.8 Pattern G – Large-Scale Processing & Infrastructure](#128-pattern-g---large-scale-processing--infrastructure)
  - [12.9 Pattern H – Collaborative & Infrastructure Utilities](#129-pattern-h---collaborative--infrastructure-utilities)
---

## 1. Estimation Fundamentals

### 1.1 Why Estimation Matters

Back‑of‑the‑envelope estimation is a critical skill in system design. It demonstrates your ability to think at scale, make reasonable assumptions, and translate user activity into concrete infrastructure requirements.

#### 1.1.1 Capacity Planning
Estimates reveal how much computing power, memory, storage, and network capacity you need before building the system. They prevent under‑provisioning (which leads to crashes) and over‑provisioning (which wastes money).

#### 1.1.2 Infrastructure Sizing
Knowing the expected QPS and storage helps you decide the number and type of servers, database instances, cache nodes, and CDN requirements.

#### 1.1.3 Cost Estimation
Rough cost estimates from bandwidth, storage, and compute allow stakeholders to budget appropriately and compare alternative architectures.

#### 1.1.4 Architecture Decisions
Estimation directly drives key architectural choices: whether you need a single database or sharding, caching or not, CDN or not, synchronous vs. asynchronous replication.

#### 1.1.5 Interview Problem Solving
In HLD interviews, estimation is often the first step after understanding requirements. It sets the stage for the entire design discussion and shows structured thinking.

### 1.2 Core Estimation Principles

#### 1.2.1 Start with Assumptions
Begin by clearly stating your assumptions about users, activity, data sizes, and retention periods. Reasonable assumptions are more important than perfect precision.

#### 1.2.2 Use Round Numbers
Simplify math by using round numbers: 1 million = 10⁶, 1 billion = 10⁹, 86,400 seconds/day ≈ 10⁵. Avoid getting bogged down in exact calculations.

#### 1.2.3 Focus on Order of Magnitude
Being off by a factor of 2 or 3 is acceptable; being off by a factor of 1000 is not. Always check if your result is roughly in the right ballpark.

#### 1.2.4 Clearly State Assumptions
Walk through each assumption out loud: "Let's assume 200M DAU, each user posting 0.5 tweets/day, and reading their feed 5 times/day." This shows the interviewer your reasoning.

#### 1.2.5 Validate Sanity of Results
After estimating, check against known real‑world systems. If you calculate that Twitter needs 1 PB of text storage per year, compare it to public numbers and see if it's plausible.

### 1.3 Key Metrics & Units

#### 1.3.1 DAU (Daily Active Users)
The number of unique users who interact with the system in a day. Typically a fraction of MAU (e.g., DAU ≈ 0.5 × MAU for many consumer apps).

#### 1.3.2 MAU (Monthly Active Users)
Unique users per month. Used for longer‑term trends and business metrics. Conversion to DAU depends on engagement frequency.

#### 1.3.3 QPS (Queries Per Second)
The rate of requests hitting the system. Often split into read QPS and write QPS. Peak QPS is usually 2‑3× the average, sometimes 5‑10× for special events.

#### 1.3.4 TPS (Transactions Per Second)
Similar to QPS but typically refers to database transactions or business‑level operations that might involve multiple queries.

#### 1.3.5 Storage Units
Bytes (B), Kilobytes (KB = 10³ B), Megabytes (MB = 10⁶ B), Gigabytes (GB = 10⁹ B), Terabytes (TB = 10¹² B), Petabytes (PB = 10¹⁵ B), Exabytes (EB = 10¹⁸ B).

#### 1.3.6 Bandwidth Units
Bits per second (bps), Megabits per second (Mbps), Gigabits per second (Gbps). Network bandwidth is usually measured in bits per second; storage in bytes. 1 Gbps = 125 MB/s.

#### 1.3.7 Latency Units
Nanoseconds (ns), microseconds (µs), milliseconds (ms). Memory access ~100 ns, SSD ~150 µs, cross‑continent network ~150 ms.

### 1.4 Common Conversions

#### 1.4.1 KB → MB → GB → TB → PB
1 KB = 1,000 bytes, 1 MB = 1,000 KB, 1 GB = 1,000 MB, 1 TB = 1,000 GB, 1 PB = 1,000 TB. Use powers of 10 for simplicity in estimation, even though technically 1 KiB = 1024 bytes. The difference is negligible at scale.

#### 1.4.2 Seconds → Minutes → Hours → Days
1 minute = 60 seconds, 1 hour = 3,600 seconds, 1 day = 86,400 seconds (memorize this). 1 month ≈ 2.5 million seconds (2,592,000 to be precise). 1 year ≈ 31.5 million seconds.

#### 1.4.3 Bits vs Bytes
1 byte = 8 bits. Bandwidth is usually in bits per second (Mbps, Gbps). Storage is in bytes. When converting bandwidth to data volume, divide by 8: 100 Mbps = 12.5 MB/s.

#### 1.4.4 Requests per Day → QPS
```
QPS = total_requests_per_day / 86,400
```
Quick approximations: 100K requests/day ≈ 1.16 QPS, 1 million requests/day ≈ 12 QPS, 1 billion requests/day ≈ 11,600 QPS.

---

## 2. Numbers Every Engineer Should Know

### 2.1 Latency Numbers

```
Operation                          Latency
─────────────────────────────────────────────────
L1 cache reference                 0.5 ns
L2 cache reference                 7 ns
RAM reference (main memory)        100 ns
Compress 1KB with Snappy           3,000 ns (3 µs)
Read 1MB sequentially from RAM     250,000 ns (250 µs)
SSD random read                    150,000 ns (150 µs)
Read 1MB sequentially from SSD     1,000,000 ns (1 ms)
HDD seek                           10,000,000 ns (10 ms)
Read 1MB sequentially from HDD     30,000,000 ns (30 ms)
Round trip within same datacenter  500,000 ns (0.5 ms)
Round trip US to Europe            150,000,000 ns (150 ms)
```

#### 2.1.1 Memory Access
~100 ns. Main memory is about 1000× slower than L1 cache, but still far faster than any disk or network operation.

#### 2.1.2 SSD Access
~150 µs for a random read. About 1000× slower than memory. Sequential reads are faster (1 MB/ms).

#### 2.1.3 HDD Access
~10 ms seek time. About 100× slower than SSD. Avoid HDDs for latency‑sensitive workloads.

#### 2.1.4 Network Round Trip
Within same datacenter: ~0.5 ms. Across the US: ~40 ms. The network is often the bottleneck in distributed systems.

#### 2.1.5 Cross-Region Latency
US to Europe: ~150 ms. US to Asia: ~200 ms. This is why geo‑replication and CDNs exist.

### 2.2 Throughput Numbers

#### 2.2.1 Disk Throughput
NVMe SSD: read up to 3,500 MB/s, write up to 500 MB/s. SATA SSD: ~500 MB/s read/write. HDD: ~150 MB/s.

#### 2.2.2 Network Bandwidth
Modern cloud instances: 10 Gbps (1.25 GB/s). Can be higher with multiple network interfaces. CDN edges have massive aggregate bandwidth.

#### 2.2.3 Database Throughput
PostgreSQL single node: ~10,000 writes/sec, ~100,000 reads/sec (with indexes). Cassandra single node: ~100,000 writes/sec.

#### 2.2.4 Cache Throughput
Redis single node: 100,000 to 1,000,000 operations per second. Memcached similar. Caches are incredibly fast compared to databases.

### 2.3 Time Conversions

#### 2.3.1 Seconds per Day
86,400 seconds. This is the most important number to memorize for QPS calculations.

#### 2.3.2 Seconds per Month
Approximately 2.5 million (2,592,000 for 30‑day months). Use 2.5 × 10⁶ for quick estimates.

#### 2.3.3 Seconds per Year
Approximately 31.5 million (31,536,000). Use 3.15 × 10⁷ for quick estimates.

### 2.4 Storage Reference Numbers

#### 2.4.1 Tweet Size
Text: 280 characters → 280 bytes. With metadata (user ID, timestamp, etc.): ~500 bytes to 1 KB.

#### 2.4.2 Image Size
Smartphone photo: ~3 MB (uncompressed). After compression (JPEG, WebP): ~200‑500 KB. Thumbnails: ~10‑50 KB.

#### 2.4.3 Video Size
1080p H.264: ~1.5 GB per hour. 4K: ~15 GB per hour. After adaptive bitrate encoding, multiple versions stored (3‑8× overhead).

#### 2.4.4 Log Entry Size
A typical structured log line: ~200 bytes to 1 KB. With verbose stack traces: up to several KB.

---

## 3. Traffic Estimation

### 3.1 DAU to QPS

#### 3.1.1 Basic Formula
```
Average QPS = (DAU × actions_per_user_per_day) / 86,400
Peak QPS = Average QPS × Peak Factor (usually 2–3×)
```

#### 3.1.2 Peak Traffic Estimation
For consumer apps, peak hours often see 2‑3× the average load. For special events (Super Bowl, Black Friday), peak can be 5‑10×. Always design for peak, not average.

#### 3.1.3 Read vs Write QPS
Separate reads and writes because they scale differently. Reads can be cached and replicated; writes often need strong consistency and careful partitioning.

#### 3.1.4 Peak Multiplier Assumptions
- Daily pattern: 2–3× average.
- Weekly pattern: Monday morning / Friday evening spikes.
- Annual: holiday season, product launches.
- Viral: unpredictable, design with auto‑scaling.

### 3.2 Request Distribution

#### 3.2.1 Read-Heavy Systems
Social media feeds, content sites, e‑commerce catalogs. Read:write ratio often 10:1 to 100:1. Reads dominate infrastructure costs.

#### 3.2.2 Write-Heavy Systems
Logging, IoT data ingestion, financial transactions. Writes may equal or exceed reads. Sharding and write‑optimized databases (Cassandra) are essential.

#### 3.2.3 Fan-Out Systems
Twitter, Instagram: one write (a tweet) triggers reads by many followers. The write may be cheap, but the fan‑out multiplies load. Caching and push/pull models mitigate this.

### 3.3 Example Calculations

#### 3.3.1 Twitter Timeline Requests
- DAU: 200 million.
- Feeds viewed per user per day: 5.
- Total reads/day: 1 billion.
- Average read QPS: 1B / 86,400 ≈ 11,600.
- Peak (3×): ~35,000.
- Writes (tweets): 200M × 0.5 = 100M/day → 1,160 write QPS, peak ~3,500.

#### 3.3.2 Instagram Feed Refreshes
- DAU: 500M.
- Feed refreshes/user: 20.
- Reads/day: 10B.
- Read QPS average: 10B / 86,400 ≈ 115,700.
- Peak: ~350,000.
- Write (photo uploads): users upload once every 10 days → 50M uploads/day → ~580 write QPS.

#### 3.3.3 Search Query Traffic
- DAU: 100M (search engine).
- Queries/user: 3 per day.
- Total queries/day: 300M.
- Read QPS average: 300M / 86,400 ≈ 3,470.
- Peak: ~10,000.

### 3.4 Estimating Server Count

#### 3.4.1 QPS per Server
A typical commodity web server can handle ~10,000 QPS for simple business logic. For complex, CPU‑bound tasks, it may be only 1,000 QPS.

#### 3.4.2 Horizontal Scaling
Servers needed = Peak QPS / QPS_per_server. Example: 35,000 peak reads / 10,000 per server = 3.5 → 4 servers.

#### 3.4.3 Redundancy Buffer
Add N+1 redundancy (at least one extra server) for fault tolerance. Add growth headroom (e.g., 50%) to handle future load increases. 4 servers + 1 redundancy + 50% growth = 8 servers.

---

## 4. Storage Estimation

### 4.1 Storage Estimation Formula

#### 4.1.1 Data Size per Object
Estimate the average size of a record or object. Include metadata (timestamps, IDs, indexes). Round up to account for overhead.

#### 4.1.2 Objects per Day
Calculate how many new records are created daily. Multiply by days of retention.

#### 4.1.3 Retention Period
How long must data be kept? Legal requirements, business needs, and cost dictate this. Example: 5 years for financial transactions, 30 days for messages.

#### 4.1.4 Replication Factor
Data is replicated for durability. Common factors: 3× for databases, 2–3× for object storage, 2× for Kafka. Always multiply raw storage by replication factor.

### 4.2 Database Storage

#### 4.2.1 Row Size Estimation
Sum the size of each column, plus overhead for row headers, null bitmaps, etc. Use database documentation for accurate overhead per row.

#### 4.2.2 Index Storage
B‑tree indexes add ~50‑100% overhead on the indexed columns. Full‑text indexes add even more. Factor this into total storage.

#### 4.2.3 Metadata Overhead
Logs, WAL, replication metadata, and system tables can add 10‑30% overhead. Round up to be safe.

### 4.3 Media Storage

#### 4.3.1 Image Storage
User‑uploaded images: ~200‑500 KB after compression. Thumbnails: 10‑50 KB. Store multiple resolutions for different devices.

#### 4.3.2 Video Storage
Raw video is huge. Transcoding into multiple bitrates (360p, 720p, 1080p, 4K) multiplies storage by 3–8×. Use cold storage for infrequently accessed content.

#### 4.3.3 Compression Effects
Modern codecs (H.265, AV1) compress 30‑50% more efficiently than H.264. Image formats like WebP and AVIF are smaller than JPEG. Always consider compression in estimates.

### 4.4 Example Calculations

#### 4.4.1 Twitter Tweet Storage
- Tweets/day: 500M.
- Size per tweet: 500 bytes (text + metadata).
- Raw daily: 500M × 500 B = 250 GB/day.
- 5 years: 250 GB × 1825 ≈ 456 TB.
- With 3× replication: 1.37 PB.
- Media (images, videos) stored separately in object storage, adding far more.

#### 4.4.2 YouTube Video Storage
- Uploads/day: 500,000 hours.
- Bitrate: 5 Mbps (1080p).
- Size per hour: (5 × 10⁶ bits/s × 3600 s) / 8 = 2.25 GB/hour.
- Raw daily: 500K × 2.25 GB = 1.125 PB.
- After transcoding (3×): 3.375 PB/day.
- 5 years: ~6 EB.
- Stored across hot/warm/cold tiers with erasure coding.

#### 4.4.3 WhatsApp Media Storage
- Messages/day: 20B text (100 B each) = 2 TB/day.
- Media: 4B images (200 KB) = 800 TB/day; 0.5B videos (2 MB) = 1 PB/day.
- 30‑day retention: ~55 PB raw.
- 3× replication: 165 PB.

#### 4.4.4 Log Storage
- Log lines/sec: 10,000.
- Size per line: 500 bytes.
- Per day: 10K × 86,400 × 500 B ≈ 432 GB/day.
- Retain 7 days: ~3 TB (compressed with gzip, ~1 TB).
- Use hot SSDs for recent logs, cold storage for archives.

### 4.5 Storage with Replication

#### 4.5.1 3x Replication
Standard for many distributed databases (Cassandra, HDFS). Triple the raw storage estimate.

#### 4.5.2 Erasure Coding
Instead of full replication, use erasure coding (e.g., 10+4 = 1.4× overhead). Reduces storage cost significantly but adds CPU overhead for recovery. Used in object stores (S3, Backblaze).

#### 4.5.3 Backup Storage
Additional copies for disaster recovery. Often stored in separate region/account. Add 1–2× of critical data for backups.

---

## 5. Bandwidth Estimation

### 5.1 Bandwidth Fundamentals

#### 5.1.1 Ingress Traffic
Data coming into your system (uploads, API requests). Usually free or cheap in cloud environments, but can be significant for media platforms.

#### 5.1.2 Egress Traffic
Data leaving your system (responses, downloads, streaming). This is the most expensive part of cloud bandwidth. CDNs reduce egress from origin servers.

#### 5.1.3 Internal Network Traffic
Communication between services within the same datacenter. Usually high‑bandwidth, low‑latency. Still must be considered for capacity planning.

### 5.2 Bandwidth Estimation Formula

#### 5.2.1 Request Size
Estimate the average payload size of a request. For uploads, it's the file size; for API calls, it's the JSON/Protobuf body.

#### 5.2.2 Response Size
Estimate the average response size. For text‑based APIs, it might be a few KB. For media, it's the file size.

#### 5.2.3 Requests per Second
Multiply request size by QPS for ingress; response size by QPS for egress.

```
Bandwidth (B/s) = QPS × Average request/response size (bytes)
Bandwidth (bps) = Bandwidth (B/s) × 8
```

### 5.3 Example Calculations

#### 5.3.1 Twitter Timeline Bandwidth
- Reads: 35,000 QPS × 20 KB (20 tweets, 1 KB each) = 700 MB/s egress.
- Media reads via CDN, so origin egress from CDN misses is ~5% of that → 35 MB/s.

#### 5.3.2 WhatsApp Message Traffic
- Text egress: 347K deliveries/sec × 100 B = 34.7 MB/s.
- Media egress: ~4B media deliveries/day (assume 1.5 recipients each) → 4B/86,400 × 200 KB ≈ 9.26 GB/s.
- CDN reduces origin egress to ~5% → 463 MB/s.

#### 5.3.3 Netflix Streaming Bandwidth
- 200M subscribers, 2 hours/day average.
- Streaming seconds/day: 200M × 2 × 3600 = 1.44 × 10¹².
- Average bitrate: 5 Mbps.
- Data/day: (1.44e12 × 5e6) / 8 = 9 × 10¹⁷ bytes = 900 PB/day.
- Average egress: 900 PB / 86,400 ≈ 10.4 TB/s = 83 Tbps.
- Peak: 3× → 250 Tbps. This is why Netflix uses their own CDN (Open Connect).

#### 5.3.4 CDN Traffic Estimation
CDN egress cost is lower than origin egress. Offload as much as possible to CDN. Cache hit ratio of 95% reduces origin traffic by 20×.

### 5.4 Compression Impact

#### 5.4.1 Gzip Compression
Text‑based responses (JSON, HTML, CSS) compress by 60–80%. Enable gzip on API responses to cut bandwidth significantly.

#### 5.4.2 Video Compression
Modern codecs (H.265, AV1) halve bitrate for the same quality compared to H.264. This directly reduces storage and bandwidth costs.

#### 5.4.3 Bandwidth Savings
With gzip, a 20 KB response becomes ~5 KB, reducing egress to 25% of the original. Always factor compression into bandwidth estimates.

---

## 6. Caching Estimation

### 6.1 Cache Sizing

#### 6.1.1 Hot Data Percentage
Apply the 80/20 rule: 80% of traffic goes to 20% of the data. Cache that 20% to serve most requests from memory.

#### 6.1.2 Cache Object Size
Each cached object (e.g., user profile, tweet feed) has a size. Include serialization overhead (Redis hash overhead, JSON structure).

#### 6.1.3 Memory Requirements
Cache memory = number_of_hot_items × item_size × overhead_factor. Overhead for Redis is ~1.5‑2× due to data structure overhead.

### 6.2 Cache Hit Ratio

#### 6.2.1 Why Hit Ratio Matters
A higher hit ratio means fewer requests reach the database. Moving from 95% to 99% hit ratio reduces database load by 5× (from 5% of requests to 1%).

#### 6.2.2 Read Reduction
If read QPS is 100,000 and cache hit ratio is 98%, database sees only 2,000 QPS. This allows a much smaller database cluster.

#### 6.2.3 Latency Reduction
Cache hits are served in microseconds (Redis) instead of milliseconds (database). This improves p99 latency dramatically.

### 6.3 Cache Traffic Reduction

#### 6.3.1 Database Offloading
With 99% cache hit rate, 1% of reads hit the database. A system with 1M read QPS would need only 10K database reads – easily handled by a single instance.

#### 6.3.2 CDN Cache Savings
CDN caches static and semi‑static content at the edge. Content served from CDN doesn't touch your origin at all, saving bandwidth and compute.

#### 6.3.3 Hot Key Distribution
A single very popular key (e.g., viral tweet) can create a hotspot. Replicate it across multiple cache nodes or use client‑side caching.

### 6.4 Example Calculations

#### 6.4.1 Twitter Timeline Cache
- Active users: 200M.
- Timeline cache per user: 100 tweet IDs × 16 bytes = 1.6 KB.
- Total cache: 200M × 1.6 KB = 320 GB raw.
- With Redis overhead (2×): ~640 GB.
- A Redis cluster of 10–12 nodes (64 GB each) handles this.

#### 6.4.2 Product Catalog Cache
- Products: 10 million.
- Product JSON size: 2 KB.
- Hot products (20%): 2M × 2 KB = 4 GB.
- Cache fits easily in a single Redis instance.

#### 6.4.3 Search Query Cache
- Unique queries/day: 100M.
- Cache top 20% (20M queries).
- Each cached result: 5 KB.
- Cache size: 20M × 5 KB = 100 GB.
- Expire after 5 minutes to maintain freshness.

### 6.5 The 80/20 Rule

#### 6.5.1 Pareto Principle
80% of effects come from 20% of causes. In caching, 80% of reads come from 20% of keys. Use this to size caches.

#### 6.5.2 Hot Data Patterns
Identify frequently accessed items (popular posts, viral content) and ensure they stay in cache. Evict cold items quickly.

#### 6.5.3 Cache Optimization
Use LFU or LRU eviction policies to keep hot data in cache. Monitor hit ratio and adjust cache size accordingly.

---

## 7. Estimation Framework

### 7.1 The 5-Step Estimation Process

#### 7.1.1 Understand Requirements
Clarify the system's scale: number of users, types of operations, data volumes, and latency requirements. Write down assumptions.

#### 7.1.2 Estimate Users & Traffic
Convert DAU into read and write QPS using the formula. Identify peak loads with a multiplier.

#### 7.1.3 Estimate Storage
Calculate daily data generation, retention period, and replication factor. Decide storage type (SQL, NoSQL, object storage) based on size.

#### 7.1.4 Estimate Bandwidth
Compute ingress and egress bandwidth from QPS and payload sizes. Determine if a CDN is needed to handle media traffic.

#### 7.1.5 Estimate Infrastructure
Translate QPS and storage into server counts, database instances, cache nodes, and CDN configuration. Add redundancy and growth headroom.

### 7.2 Identifying Bottlenecks

#### 7.2.1 CPU Bottlenecks
Heavy computation, encryption, or serialization can saturate CPU. Estimate CPU cores needed from processing time per request × QPS.

#### 7.2.2 Memory Bottlenecks
Insufficient RAM causes swapping or cache evictions. Ensure cache servers have enough memory; application servers have enough for connection pools and local caches.

#### 7.2.3 Disk Bottlenecks
Slow I/O limits database performance. Use SSDs for high‑throughput databases. Estimate IOPS needed: QPS × average disk operations per request.

#### 7.2.4 Network Bottlenecks
Bandwidth limits cause high latency. Use multiple network interfaces, compress data, and place CDNs strategically.

### 7.3 Sanity Checking

#### 7.3.1 Compare with Real Systems
Check your estimates against publicly known numbers (e.g., Twitter has ~500M tweets/day, YouTube uploads 500 hours/min). Adjust assumptions if your numbers are wildly different.

#### 7.3.2 Check Order of Magnitude
Does a single server handle your estimated load? If you need 10,000 servers for 1,000 QPS, something is wrong (10 QPS per server is extremely low). Ask yourself if the result is physically possible.

#### 7.3.3 Validate Peak Estimates
Peak traffic should not exceed hardware limits. A single 10 Gbps NIC can handle 1.25 GB/s; your peak egress per server should be below that.

---

## 8. Worked Examples

### 8.1 Twitter-Scale System

#### 8.1.1 User Activity Estimation
- DAU: 200M.
- Each user reads feed 5 times/day, posts 0.5 tweets/day.
- 50% of tweets have media.

#### 8.1.2 Timeline QPS
- Reads: 200M × 5 = 1B reads/day → 11,600 QPS average, 35,000 peak.
- Writes: 200M × 0.5 = 100M tweets/day → 1,160 QPS average, 3,500 peak.

#### 8.1.3 Tweet Storage
- Text tweets: 100M/day × 500 B = 50 GB/day. 5 years: ~18 TB raw, 54 TB with 3× replication.
- Media: 50M media tweets/day × 500 KB = 25 TB/day. 5 years: ~9 PB raw, 27 PB replicated.

#### 8.1.4 Feed Cache Estimation
- 200M users × 1.6 KB timeline = 320 GB raw, ~640 GB with Redis overhead.
- 95% cache hit rate reduces DB reads to ~1,750 QPS.

#### 8.1.5 Bandwidth Estimation
- Text reads: 35,000 QPS × 20 KB = 700 MB/s egress.
- Media via CDN: 35,000 × 0.5 (media fraction) × 500 KB = 8.75 GB/s, origin sees ~437 MB/s at 95% cache hit rate.

### 8.2 URL Shortener

#### 8.2.1 URL Creation QPS
- 100M new URLs/month → 3.3M/day → ~38 writes/sec.
- Peak: ~115 writes/sec.

#### 8.2.2 Redirect Traffic
- 10:1 read:write → 33M redirects/day → ~385 redirects/sec.
- Peak: ~1,155 redirects/sec.

#### 8.2.3 Database Storage
- Each mapping: ~650 bytes.
- Daily: 3.3M × 650 B = 2.15 GB.
- 5 years: ~4 TB raw, 12 TB with 3× replication.

#### 8.2.4 Cache Requirements
- 20% hot URLs: 20M URLs × 650 B = 13 GB.
- Cache hit rate 99%: DB reads drop to ~12/sec.

### 8.3 WhatsApp Messaging

#### 8.3.1 Message Throughput
- DAU: 500M, 40 messages/user/day → 20B messages/day.
- Writes: 20B / 86,400 ≈ 231,500 msg/sec.
- Deliveries: 1.5× = 347,250 deliveries/sec.

#### 8.3.2 Concurrent Connections
- 500M DAU actively connected via WebSocket.
- 500M / 65,000 connections per server ≈ 7,700 servers.

#### 8.3.3 Media Storage
- Text: 20B × 100 B = 2 TB/day.
- Media: 4B images × 200 KB = 800 TB/day; 0.5B videos × 2 MB = 1 PB/day.
- 30‑day retention: 55 PB raw, 165 PB replicated.

#### 8.3.4 Real-Time Bandwidth
- Text egress: 347K × 100 B = 34.7 MB/s.
- Media via CDN: origin egress ~5% of ~9 GB/s.

### 8.4 YouTube / Netflix

#### 8.4.1 Video Upload Storage
- 500K hours/day, 5 Mbps → 2.25 GB/hour → 1.125 PB raw daily.
- Transcoding 3× → 3.375 PB/day.

#### 8.4.2 Streaming Bandwidth
- 200M users × 2 h/day × 5 Mbps → 900 PB/day.
- Average egress: 83 Tbps; peak 250 Tbps.

#### 8.4.3 CDN Sizing
- CDN handles essentially all streaming traffic.
- Thousands of edge servers worldwide.

#### 8.4.4 Transcoding Costs
- 500K uploads/day → ~6 uploads/sec.
- 48 transcoding jobs/sec, ~14,400 concurrent jobs.
- Requires a large auto‑scaled worker pool.

### 8.5 Search Engine

#### 8.5.1 Query QPS
- 100M DAU, 3 queries/day → 300M queries/day.
- Average: 3,470 QPS, peak ~10,000.

#### 8.5.2 Index Storage
- Web pages indexed: 50 billion.
- Average page size (compressed text): 20 KB.
- Raw index: 50B × 20 KB = 1 EB. With compression and sharding, manageable across thousands of servers.

#### 8.5.3 Cache Layers
- Top 20% queries cached: ~60M queries × 5 KB = 300 GB.
- CDN caches static search UI elements.

#### 8.5.4 Ranking Computation
- Real‑time ranking requires significant CPU. Pre‑compute rankings where possible; use ML models optimized for inference speed.

---

## 9. Common Estimation Patterns

### 9.1 Read vs Write Ratios

#### 9.1.1 Social Media Systems
Very read‑heavy. Typical ratio: 10:1 to 100:1. Reads dominate and drive caching and CDN requirements.

#### 9.1.2 Financial Systems
More balanced. Reads may be only 2‑5× writes. Strong consistency is critical; caching is limited to non‑transactional data.

#### 9.1.3 Search Systems
Extremely read‑heavy. Millions of queries per second, relatively few index updates (crawl/index cycles). Heavy caching of results.

### 9.2 QPS to Server Estimation

#### 9.2.1 API Servers
~10,000 QPS per commodity server (Node.js, Go, Java with simple logic). For CPU‑intensive tasks, ~1,000 QPS.

#### 9.2.2 Database Servers
PostgreSQL: ~10K writes/sec, 100K reads/sec. Cassandra: 100K writes/sec. Redis: 100K‑1M ops/sec.

#### 9.2.3 Cache Servers
Redis can handle 100K+ ops/sec per node. For 1M reads/sec, you'd need ~10 Redis nodes, or fewer with pipelining.

### 9.3 Replication Overhead

#### 9.3.1 Database Replication
3× replication triples storage and write network traffic. Use synchronous replication for critical data, asynchronous for less critical.

#### 9.3.2 Storage Replication
Object stores replicate automatically. S3's 3 AZ replication is built into the cost. On‑prem HDFS typically uses 3× replication.

#### 9.3.3 Multi-Region Traffic
Replicating data across regions adds inter‑region bandwidth costs. Only replicate what's necessary for disaster recovery or regional access.

### 9.4 Peak vs Average Traffic

#### 9.4.1 Traffic Spikes
Daily peak is often 2‑3× average. Plan capacity for peak, but use auto‑scaling to avoid over‑provisioning during low periods.

#### 9.4.2 Seasonal Scaling
E‑commerce sees 5‑10× traffic during Black Friday. Pre‑warm infrastructure and use reserved instances for baseline.

#### 9.4.3 Viral Traffic Patterns
Sudden, unpredictable spikes (celebrity tweets, breaking news). Use aggressive caching, rate limiting, and auto‑scaling to absorb.

---

## 10. Common Pitfalls

### 10.1 Ignoring Peak Traffic
Designing for average load leads to under‑provisioning during peak hours, causing outages. Always size for peak.

### 10.2 Forgetting Replication Costs
Raw storage must be multiplied by replication factor (typically 3×). Forgetting this underestimates total disk by 3×.

### 10.3 Underestimating Metadata
Indexes, logs, WAL, and system tables can add 50‑200% overhead to raw data size. Round up storage estimates.

### 10.4 Ignoring CDN Savings
Without CDN, all media traffic hits your origin, requiring massive bandwidth. CDN reduces origin traffic by 20× or more.

### 10.5 Confusing Bits vs Bytes
Bandwidth is in bits per second; storage is in bytes. 1 Gbps = 125 MB/s. Mixing them up leads to 8× errors.

### 10.6 Ignoring Read/Write Ratios
Systems with 100:1 read:write need vastly different architectures than balanced ones. Always estimate reads and writes separately.

### 10.7 Overcomplicated Calculations
Use round numbers and approximate. Spending 5 minutes calculating exact numbers to the third decimal is unnecessary. Focus on order of magnitude.

### 10.8 Unrealistic Assumptions
Assuming every user is active every second, or that every action creates megabytes of data, leads to absurd results. Use realistic, grounded assumptions.

---

## 11. Interview-Oriented HLD Notes

### 11.1 Estimation Best Practices

#### 11.1.1 Talk Through Assumptions
Verbally state every assumption: "Let's assume 200M DAU, each viewing their timeline 5 times per day, and posting once every 2 days." This shows structured thinking.

#### 11.1.2 Use Round Numbers
1 day = 86,400 seconds → use 10⁵ for quick mental math. 1 month ≈ 2.5 million seconds. 1 year ≈ 31 million seconds.

#### 11.1.3 Focus on Scale
Don't get lost in tiny details. The interviewer wants to see that you can estimate at scale and draw meaningful conclusions (e.g., "We need a CDN because media bandwidth would be 8 GB/s").

#### 11.1.4 Prioritize Bottlenecks
After estimating, identify the biggest constraints (storage, bandwidth, QPS) and explain how your architecture addresses them.

### 11.2 Common Interview Questions

#### 11.2.1 Estimate QPS for Twitter
Start with DAU, actions per user, separate reads/writes, apply peak multiplier. Conclude with required server count and caching strategy.

#### 11.2.2 Estimate Storage for YouTube
Calculate upload volume, average bitrate, transcoding multiplier, and retention. Show that storage must be tiered and use erasure coding.

#### 11.2.3 Estimate CDN Bandwidth
Calculate total egress, apply cache hit ratio to determine origin vs. CDN traffic. Explain cost savings and latency improvements.

#### 11.2.4 Estimate Cache Size
Use the 80/20 rule: cache 20% of active data. Calculate size from object count × size × overhead. Show that a Redis cluster of N nodes handles it.

#### 11.2.5 Estimate Number of Servers
Peak QPS / QPS per server = base count. Add N+1 redundancy and growth buffer. Verify that this fits within reasonable hardware limits.

### 11.3 Quick Revision Material

#### 11.3.1 Common Formulas Cheat Sheet
- QPS = (DAU × actions_per_day) / 86,400
- Peak QPS = Average QPS × 2–3
- Storage = daily_items × item_size × retention_days × replication_factor
- Bandwidth = QPS × response_size (for egress)
- Servers = Peak QPS / QPS_per_server + redundancy

#### 11.3.2 Important Numbers to Memorize
- 1 day = 86,400 seconds
- 1 million seconds ≈ 11.6 days
- 1 Gbps = 125 MB/s
- 1 byte = 8 bits
- Single server QPS: ~10,000 (web), ~100,000 (Redis reads), ~10,000 (DB writes)
- Memory latency: 100 ns, SSD: 150 µs, cross‑continent: 150 ms

#### 11.3.3 Estimation Framework Summary
1. Understand requirements → 2. Estimate users & traffic → 3. Estimate storage → 4. Estimate bandwidth → 5. Derive architecture implications.

#### 11.3.4 Key Interview Sentences
> *"Let me do a quick estimation first. At 200M DAU, each user making 5 reads and 0.5 writes per day, I get roughly 11,600 reads/sec and 1,160 writes/sec. Peak at 3× gives 35,000 and 3,500. This tells me reads are the bottleneck, so I'll focus on caching and read replicas."*

> *"The storage estimate is interesting — text metadata is about 18 TB/year, but media is 9 PB/year. That asymmetry completely drives the architecture: text goes to a relational DB, media goes to object storage with CDN."*

> *"At 500M concurrent WebSocket connections, we need roughly 8,000 gateway servers. That's a significant infrastructure footprint, so the connection layer needs its own load balancing and auto‑scaling."*

#### 11.3.5 Self-Test Questions
1. You're designing Instagram. DAU = 500M, each user views 20 photos/day and uploads 1 photo every 10 days. Each photo is 3MB. Calculate: write QPS, read QPS, storage per year.
2. A URL shortener has 100M redirects/day. The cache hit rate is 99%. How many DB reads/second does that translate to? Is that manageable?
3. WhatsApp has 500M DAU. Each user sends 40 messages/day. How many concurrent WebSocket connections are there? How many servers do you need if each handles 50,000 connections?
4. You're told "we have 1B users, each generates 1 KB of data per day." How much storage is that per year? If you replicate 3×, what's the total disk?
5. Your system handles 100,000 QPS. A single server handles 10,000 QPS. How many servers do you need accounting for N+1 redundancy and 50% headroom for growth?

## 12. Quick Numbers by System Type

### 12.1 Summary: Key Metrics to Estimate by Pattern

| Pattern | System Type | Primary Key Metrics to Estimate | Typical Bottleneck & Solution |
|---------|-------------|-------------------------------|-------------------------------|
| **A: CRUD & Storage Systems** | URL shortener, Pastebin, Bookstore, Ticket booking, Dropbox | Write QPS, read:write ratio, storage growth per record | Read-heavy 100:1 ratios dominate → cache sizing matters more than write throughput |
| **B: Feed / Timeline Systems** | Twitter, Instagram, Facebook, LinkedIn | DAU, posts/user/day, fan-out multiplier, media storage | Fan-out multiplier is the key driver of both write amplification and cache size |
| **C: Real-Time Systems** | WhatsApp, Slack, Live Comments, Game backend | Messages/user/day, concurrent connections, peak-to-average ratio | Concurrent WebSocket connections drive server-count sizing more than message throughput |
| **D: Search Systems** | Google Search, YouTube Search, Amazon Search, Autocomplete | Queries/user/day, index size, cache hit ratio | Index size and shard count dominate infra sizing more than query QPS itself |
| **E: Geospatial & Location-Based Systems** | Uber, Google Maps, Food Delivery, Nearby Friends | Active drivers/riders, location-ping frequency, match latency budget | Location-ping frequency (not ride count) drives write throughput |
| **F: Financial & Critical Consistency Systems** | Payment gateway, Wallet, UPI, Job scheduler | Transactions/sec, peak:average ratio, audit-log retention | Peak:average ratio matters most — financial systems must be provisioned for spikes, not averages |
| **G: Large-Scale Processing & Infrastructure** | Analytics, Clickstream, Logging, Metrics, Kafka-like | Events/sec, retention period, compression ratio | Retention period × ingestion rate dominates total storage cost |
| **H: Collaborative & Infrastructure Utilities** | Google Docs, Figma, Distributed locks, Snowflake, Web Crawler | Concurrent editors/session, ops/sec per document, ID generation rate | Ops/sec per active document, not total documents, determines real-time infra load |

---

### 12.2 Pattern A – CRUD & Storage Systems

| Question | Key Metrics to Estimate | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------|--------------------|-----------------------------|
| **URL Shortener** | Write QPS (new URLs), read QPS (redirects, ~100:1 vs writes), storage per mapping (~500B-1KB) | This is a canonical read-heavy system: sizing the cache and read path matters far more than the write path. | 100M new URLs/month → ~38 writes/sec avg; 100:1 read ratio → ~3,800 redirects/sec avg, ~11,000 peak; 5-year storage ~4-12 TB replicated. |
| **Pastebin** | Paste creation rate, read:write ratio, average paste size, retention/TTL distribution | Storage growth is driven by paste size × creation rate × retention period, which varies hugely by TTL policy. | 1M pastes/day at 10 KB avg → 10 GB/day raw; with a 30-day average retention, steady-state storage is ~300 GB. |
| **Online Bookstore** | Catalog size, order QPS at peak (e.g., sales events), inventory-check QPS | Peak-to-average ratio during sales events (e.g., Black Friday) is often the real sizing driver, not steady-state traffic. | 10M SKUs cataloged; normal order QPS ~50/sec, sale-event peak 20-50x normal → provision for ~1,000-2,500 QPS bursts. |
| **Ticket Booking** | Concurrent booking attempts during on-sale moments, seat count per event, contention rate | The interesting number isn't average traffic but the instantaneous spike when a popular event goes on sale. | A 50,000-seat stadium event selling out in 10 minutes → up to ~85 successful bookings/sec, with 10-100x that in failed/retry attempts. |
| **Dropbox / Google Drive** | Storage per user (GB), upload/download bandwidth, file count per user | Total storage is simply users × average storage per user, but bandwidth peaks (sync storms) need separate estimation. | 500M users × 2 GB average → ~1 EB total storage (before dedup/compression, which typically saves 30-50%). |

---

### 12.3 Pattern B – Feed / Timeline Systems

| Question | Key Metrics to Estimate | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------|--------------------|-----------------------------|
| **Twitter / X** | DAU, tweets/user/day, average follower count, media fraction | Fan-out cost = tweets/day × average follower count, which can dwarf the raw tweet-write volume itself. | 200M DAU × 0.5 tweets/day = 100M tweets/day; at avg 200 followers/user, fan-out writes ~20B/day (200x the raw tweet volume). |
| **Instagram** | DAU, posts/user/day, average media size, feed-view rate | Media storage dominates total footprint by orders of magnitude compared to text/metadata. | 500M DAU × 1 post/day × 2 MB avg (photo+video mix) = ~1 PB/day raw upload before CDN/compression optimization. |
| **Facebook News Feed** | DAU, feed-refresh rate, ranking-compute cost per feed generation | Feed-generation compute cost (CPU-seconds per feed) is often the binding constraint, not storage or network. | 2B DAU × 5 feed refreshes/day = 10B feed generations/day → ~115,700 QPS average just for feed computation. |
| **LinkedIn Feed** | DAU, average connections/user, post frequency | Because LinkedIn pulls feed data at read time, per-request aggregation cost (not write fan-out) is the key metric. | 50M DAU × 3 feed loads/day, each aggregating ~500 connections' recent posts → significant read amplification per request. |

---

### 12.4 Pattern C – Real-Time Systems

| Question | Key Metrics to Estimate | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------|--------------------|-----------------------------|
| **WhatsApp / Messenger** | DAU, messages/user/day, concurrent WebSocket connections, delivery multiplier (1:1 vs group) | Concurrent connection count directly determines the number of chat servers needed, independent of message volume. | 500M DAU connected concurrently, ~65,000 connections/server → ~7,700 servers; 40 messages/user/day → ~231,500 msg writes/sec. |
| **Slack / Discord** | Concurrent active users per workspace, messages/channel/day, presence-update frequency | Presence updates (heartbeats) can generate more total traffic than actual messages in a chat-heavy platform. | 10M concurrent users sending a presence heartbeat every 30s → ~333,000 presence updates/sec alone. |
| **Live Comments (YouTube Live)** | Concurrent viewers, comments/viewer/minute, peak-to-average ratio during viral moments | The peak-to-average ratio during a viral live event can be 100x or more, which must drive capacity planning, not the average. | 1M concurrent viewers on a viral stream, 2% commenting per minute → ~333 comments/sec sustained, with short bursts far higher. |
| **Multiplayer Game Backend** | Concurrent players per room, state-update frequency (ticks/sec), room count | Tick rate × concurrent rooms determines server CPU load far more directly than total player count alone. | 10,000 concurrent rooms × 20 players × 20 ticks/sec → 4M state updates/sec across the fleet, requiring careful server sharding. |

---

### 12.5 Pattern D – Search Systems

| Question | Key Metrics to Estimate | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------|--------------------|-----------------------------|
| **Google Search** | Queries/user/day, index size (pages × avg size), query-cache hit ratio | Index storage size is driven by total pages crawled, while query latency is driven by cache hit ratio on popular queries. | 100M DAU × 3 queries/day = 300M queries/day (~3,500 QPS avg, ~10,000 peak); 50B pages × 20 KB index footprint ≈ 1 EB raw index. |
| **YouTube Search** | Query rate, video corpus size, metadata update frequency | Metadata (view counts) updates far more frequently than the search index itself, requiring separate throughput estimation. | 500M videos indexed; metadata updates (views) at ~1M events/sec globally, batched into periodic index refreshes. |
| **Amazon Product Search** | Query rate, catalog size, inventory-check QPS layered on top of search QPS | Search QPS and inventory-check QPS are separate numbers since inventory is checked per result, not per query. | 10,000 search QPS at peak, each returning ~20 results → up to 200,000 inventory checks/sec if not batched/cached aggressively. |
| **Search Autocomplete** | Keystrokes/query (avg ~5-8), suggestion-cache hit ratio, trie size | Because every keystroke is a request, autocomplete QPS is typically 5-10x higher than the underlying search QPS. | 3,500 search QPS → roughly 17,500-28,000 autocomplete requests/sec, most served from a cache with >95% hit ratio. |

---

### 12.6 Pattern E – Geospatial & Location-Based Systems

| Question | Key Metrics to Estimate | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------|--------------------|-----------------------------|
| **Uber / Lyft** | Active drivers, location-ping frequency, match latency budget | Location-ping volume (drivers × pings/sec), not completed-ride count, is what stresses the ingestion pipeline. | 5M active drivers pinging every 4 seconds → ~1.25M location updates/sec sustained across the platform. |
| **Google Maps / Nearby Places** | DAU, tile requests/session, place-search QPS | Tile requests vastly outnumber place searches per session and are the dominant traffic driver for map products. | 1B DAU × 50 tile requests/session average → tens of billions of tile requests/day, overwhelmingly served from CDN cache. |
| **Food Delivery (DoorDash)** | Active drivers, orders/hour at peak (dinner rush), location-ping frequency | Dinner-rush peak-to-average ratios (often 5-10x) must drive matching-service capacity, not daily averages. | 500K active drivers pinging every 5s (~100,000 pings/sec) plus a dinner-rush order-matching peak of 5-10x the daily average. |
| **Nearby Friends** | Active users sharing location, update frequency, radius-query rate | This feature has a far lower update frequency requirement than ride-hailing, keeping its numbers comparatively modest. | 100M users updating location every 60s → ~1.7M updates/sec spread evenly, far lower intensity than ride-hailing dispatch. |

---

### 12.7 Pattern F – Financial & Critical Consistency Systems

| Question | Key Metrics to Estimate | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------|--------------------|-----------------------------|
| **Payment Gateway (Stripe)** | Transactions/sec average and peak, audit-log retention period, PCI-scoped data volume | Financial systems must be sized for worst-case peaks (e.g., a flash sale) since undercapacity directly costs revenue. | 10,000 avg TPS with 5-10x peak during major sale events → provision for 50,000-100,000 TPS; audit logs retained 7+ years per compliance. |
| **Wallet System** | Balance-check QPS vs transaction QPS (often 10:1 or higher), consistency-check latency budget | Balance checks (reads) vastly outnumber actual balance-changing transactions and can often be served from a fast read path. | 1M active wallets, ~10 balance checks per actual transaction → read QPS roughly 10x the write QPS. |
| **UPI-like System** | Transactions/sec across the national network, saga-step latency budget, bank-participant count | National-scale payment rails must be sized for aggregate cross-bank volume, which can reach extreme peaks during festivals/sales. | India's UPI handles billions of transactions/month, with peak TPS in the tens of thousands during high-demand periods (e.g., festival sales). |
| **Distributed Job Scheduler** | Jobs scheduled/sec, job-execution duration distribution, concurrent-job ceiling | The scheduler's own throughput requirement is usually modest; the real capacity question is how many jobs execute concurrently. | 100,000 jobs/day → ~1.2 jobs/sec average dispatch rate, but concurrent execution count depends heavily on job duration distribution. |

---

### 12.8 Pattern G – Large-Scale Processing & Infrastructure

| Question | Key Metrics to Estimate | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------|--------------------|-----------------------------|
| **Google Analytics** | Events/sec ingested, retention period, query fan-out per dashboard load | Total storage is ingestion rate × average event size × retention period, often the largest cost driver in the whole system. | 1M events/sec × 500 B/event = 500 MB/sec ingestion → ~43 TB/day raw; with 90-day retention, ~3.9 PB before compression. |
| **Clickstream Processing** | Events/sec, session-window duration, state size per active session | Total in-memory state = concurrent active sessions × state size per session, which bounds the processing cluster's memory needs. | 10M concurrent sessions × 2 KB state each → ~20 GB of active windowed state to hold in the stream processor's memory. |
| **Distributed Logging (ELK)** | Log lines/sec, average line size, retention period per tier (hot/warm/cold) | Log volume can be enormous, so tiered retention (hot/warm/cold) is essential to keeping storage cost estimates reasonable. | 1M log lines/sec × 200 B avg = 200 MB/sec → ~17 TB/day; a 7-day hot tier plus 90-day cold tier balances cost and queryability. |
| **Metrics Monitoring (Prometheus)** | Time series count (cardinality), scrape interval, sample size | High cardinality (unique label combinations) is usually the real bottleneck, not raw sample volume. | 1M active time series scraped every 15s at ~2 bytes/sample → ~133 KB/sec per Prometheus instance, but cardinality can blow this up fast. |
| **Design Kafka-like System** | Messages/sec, average message size, replication factor, retention period | Total disk footprint = messages/sec × avg size × retention period × replication factor, often the dominant cost. | 1M msg/sec × 1 KB avg × 7-day retention × RF=3 → roughly 1.8 EB of total disk footprint across the cluster before compaction/compression. |

---

### 12.9 Pattern H – Collaborative & Infrastructure Utilities

| Question | Key Metrics to Estimate | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------|--------------------|-----------------------------|
| **Google Docs** | Concurrent editors per document, ops/sec per active document, document count | Real-time infra load scales with ops/sec on actively-edited documents, not the total number of documents stored. | A heavily-edited document with 20 concurrent editors typing might generate ~50-100 ops/sec, which the collaboration server must merge in real time. |
| **Figma / Collaborative Whiteboard** | Concurrent editors per file, ops/sec (higher than text due to drag/resize events) | Canvas interactions generate more frequent, smaller ops than text editing, so op/sec estimates run higher for the same editor count. | 20 concurrent editors dragging/resizing objects can generate several hundred ops/sec on a single actively-edited file. |
| **Distributed Locking Service** | Lock acquisitions/sec, average lock hold duration, contention rate per resource | High contention on a small number of hot resources (not total lock volume) is what typically stresses this kind of service. | 100,000 lock acquisitions/sec system-wide is very achievable, but a single hot resource with heavy contention can bottleneck independently of total volume. |
| **Unique ID Generator (Snowflake)** | IDs generated/sec system-wide, number of workers, clock-drift tolerance | Since ID generation is local per worker, the real capacity question is simply worker_id space (bits available) versus worker count. | A 10-bit worker_id field supports 1,024 workers, each capable of generating thousands of IDs/sec locally with no coordination overhead. |
| **Web Crawler** | Pages crawled/sec, frontier queue size, Bloom filter size (for N URLs at target false-positive rate) | Bloom filter size is a direct function of how many URLs need tracking and the acceptable false-positive rate, both estimable upfront. | 10 billion URLs tracked at a 1% false-positive rate requires roughly 12 GB of Bloom filter memory (using standard sizing formulas). |

---

---

*End of consolidated notes.*
