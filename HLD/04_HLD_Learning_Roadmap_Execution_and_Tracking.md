# How to prepare
how to answer URL shortener in 45 minutes

exact questions interviewers ask

deep dive questions

best answers to impress FAANG interviewers

# Your 120-Day High-Level Design (HLD) Mastery Roadmap

## From Pattern-Based Thinker to System Design Expert

You already have the right mindset – treating HLD as a set of recurring patterns, tracking progress, and iterating. This 120-day roadmap is designed to turn you into a top-tier system design candidate for FAANG and other product-based companies. It combines foundational knowledge, pattern-based practice, mock interviews, and continuous refinement.

---

## 📌 How to Use This Roadmap

- **Follow the phases sequentially** – each builds on the previous.
- **Track your progress** using the Excel/Notion template provided.
- **Apply the 6-step interview framework** to every practice problem.
- **Color-code your confidence** after each attempt: Green (solid), Yellow (needs work), Red (weak). Revisit reds after 2 weeks.
- **Schedule weekly reviews** – what you learned, what to improve.

---

## 🧱 Phase 1: Foundation Building (Days 1–30)

### Objective: Master the building blocks – networking, scaling, databases, caching, messaging, and core distributed systems concepts.

### Week 1–2: Core Concepts & Networking
| Day | Topic | Activities |
|-----|-------|------------|
| 1-2 | Networking fundamentals | HTTP/HTTPS, TCP vs UDP, DNS, load balancers (L4 vs L7) |
| 3-4 | Advanced networking | WebSockets, gRPC, HTTP/2, reverse proxies, CDNs |
| 5-6 | Scaling concepts | Vertical vs horizontal scaling, stateless vs stateful, auto-scaling |
| 7-8 | CAP theorem & consistency | CAP, PACELC, strong vs eventual consistency, read-after-write |
| 9-10 | Databases – SQL | ACID, indexing (B-Tree, hash), replication (master-slave, multi-master), sharding |
| 11-12 | Databases – NoSQL | Key-value, document, wide-column, graph; use cases, trade-offs |
| 13-14 | Database deep dive | Choosing between SQL and NoSQL, read replicas, partitioning strategies |
| 15-16 | Caching fundamentals | Cache-aside, read/write-through, write-back, write-around; eviction policies (LRU, LFU, TTL) |
| 17-18 | Distributed caching | Consistent hashing, cache replication, cache stampede prevention |
| 19-20 | Messaging & queues | Message queues vs pub/sub, Kafka vs RabbitMQ, at-least-once vs exactly-once, idempotency |
| 21-22 | Storage systems | Object storage (S3), block storage, file storage, blob storage; use cases |
| 23-24 | Reliability patterns | Circuit breaker, retry with backoff, bulkhead, health checks, rate limiting |
| 25-26 | API design | RESTful principles, pagination, idempotency keys, versioning |
| 27-28 | Estimation skills | DAU, QPS, storage, bandwidth calculations; practice with examples |
| 29-30 | Review & mini-mocks | Revisit weak areas; do 2-3 small design problems (e.g., URL shortener, rate limiter) untimed to apply concepts |

**Resources**: "Designing Data-Intensive Applications" (chapters 1–9), Grokking System Design basics, High Scalability blog, YouTube (Gaurav Sen).

---

## 🏗 Phase 2: Core Patterns (Days 31–60)

### Objective: Solve 2–3 problems from each core pattern, applying the 6-step framework.

### Week 5–6: Pattern A – CRUD & Storage Systems
| Day | Question | Pattern | Focus |
|-----|----------|---------|-------|
| 31-33 | URL Shortener (TinyURL) | Key-Value Store | Hashing, key generation, redirection, caching |
| 34-36 | Pastebin | Key-Value + Expiry | Large text storage, expiry, syntax highlighting |
| 37-39 | Online Bookstore | E-commerce CRUD | Product catalog, search, inventory, sharding |
| 40-42 | Ticket Booking (BookMyShow) | High-concurrency booking | Seat locking, optimistic/pessimistic concurrency, transaction isolation |
| 43-45 | Dropbox / Google Drive | File storage & sync | Chunking, deduplication, delta sync, conflict resolution |
| 46   | Review & mock | Pick one and do a full 45-min timed mock | Self-evaluate |

### Week 7–8: Pattern B – Feed / Timeline Systems
| Day | Question | Sub-pattern | Focus |
|-----|----------|-------------|-------|
| 47-49 | Twitter / X | Social Media Feed | Fan-out (push/pull/hybrid), timeline caching, celebrity problem |
| 50-52 | Instagram Feed | Media-heavy feed | Image storage (CDN), feed ranking, push fan-out |
| 53-55 | Facebook News Feed | Algorithmic feed | Ranking (affinity, weight, time decay), diverse content types, ML integration |
| 56-58 | LinkedIn Feed | Professional network feed | Feed from 2nd/3rd degree connections, relevance scoring, pull-based |
| 59-60 | Review & mock | Timed mock on a feed system | Evaluate bottlenecks & trade-offs |

### Week 9–10: Pattern C – Real-Time Systems
| Day | Question | Sub-pattern | Focus |
|-----|----------|-------------|-------|
| 61-63 | WhatsApp / Messenger | 1:1 and group chat | WebSockets, message ordering, presence, read receipts, multi-device sync |
| 64-66 | Slack / Discord | Group chat with channels | Channels, voice (WebRTC, SFU), permissions, unread counts |
| 67-69 | Live Comments (YouTube Live) | High-volume live updates | Kafka for ingestion, fan-out via Redis streams, rate limiting |
| 70-72 | Multiplayer Game Backend | Game state sync | UDP vs TCP, server-authoritative, client prediction, room management |
| 73-75 | Review & mock | Timed mock on real-time system | Focus on connection handling and scalability |

### Week 11–12: Pattern D – Search Systems
| Day | Question | Sub-pattern | Focus |
|-----|----------|-------------|-------|
| 76-78 | Google Search | Web-scale search | Crawling, inverted index, PageRank, sharding, caching |
| 79-81 | YouTube Search | Video search | Indexing metadata & transcripts, ranking by engagement |
| 82-84 | Amazon Product Search | E-commerce search | Faceted search, inventory-aware results, synonyms |
| 85-87 | Search Autocomplete | Typeahead | Trie, frequency-based ranking, personalization, real-time updates |
| 88-90 | Review & mock | Pick a search problem and do full mock | Evaluate ranking and latency trade-offs |

---

## 🚀 Phase 3: Advanced & Specialized Patterns (Days 61–90)

### Objective: Tackle more complex patterns, including geospatial, financial, infrastructure, and large-scale processing.

### Week 13–14: Pattern E – Geospatial & Location-Based
| Day | Question | Sub-pattern | Focus |
|-----|----------|-------------|-------|
| 91-93 | Uber / Lyft | Ride-sharing | Geohashing/quadtree, real-time matching, state management, surge pricing |
| 94-96 | Google Maps / Nearby Places | Proximity search | Spatial indexing (Geohash, PostGIS), ranking (distance + popularity) |
| 97-99 | Food Delivery (DoorDash/Zomato) | Logistics + Discovery | Restaurant discovery, delivery tracking, assignment optimization |
| 100-102 | Nearby Friends | Real-time location sharing | Privacy, update frequency, geohash-based proximity detection |
| 103-105 | Review & mock | Timed mock on Uber or similar | Focus on location indexing and real-time matching |

### Week 15–16: Pattern F – Financial & Critical Consistency
| Day | Question | Sub-pattern | Focus |
|-----|----------|-------------|-------|
| 106-108 | Payment Gateway (Stripe/PayPal) | Payment processing | Idempotency keys, double-entry ledger, state machine, external calls |
| 109-111 | Wallet System | Digital wallet | Balance consistency (optimistic locking), transaction log, reconciliation |
| 112-114 | UPI-like System | Real-time payment | Two-phase commit across banks, distributed transactions, idempotency |
| 115-117 | Distributed Job Scheduler | Infrastructure | Leader election (ZooKeeper), cron at scale, exactly-once execution |
| 118-120 | Review & mock | Mock on payment system | Focus on ACID, concurrency, and idempotency |

### Week 17–18: Pattern G – Large-Scale Processing & Infrastructure
| Day | Question | Sub-pattern | Focus |
|-----|----------|-------------|-------|
| 121-123 | Google Analytics | Web analytics | Event ingestion (Kafka), stream processing (Flink), batch (Spark), storage (Druid, BigQuery) |
| 124-126 | Clickstream Processing | User behavior pipeline | Sessionization, funnels, real-time dashboards |
| 127-129 | Distributed Logging (Splunk/ELK) | Log aggregation | Log shippers, Kafka buffer, Elasticsearch indexing, hot-warm-cold storage |
| 130-132 | Metrics Monitoring (Prometheus/Datadog) | Time-series metrics | Ingestion, downsampling, query language, alerting |
| 133-135 | Design Kafka-like System | Distributed messaging | Partitioning, replication, consumer offsets, durability |
| 136-138 | Review & mock | Mock on logging or analytics | Focus on high-throughput ingestion and query efficiency |

### Week 19–20: Pattern H – Collaborative & Infrastructure Utilities
| Day | Question | Sub-pattern | Focus |
|-----|----------|-------------|-------|
| 139-141 | Google Docs | Collaborative editing | Operational Transformation vs CRDT, WebSockets, presence, version vectors |
| 142-144 | Figma / Collaborative Whiteboard | Visual collaboration | Scene graph, CRDTs, rendering optimization |
| 145-147 | Distributed Locking Service | Coordination | Redis Redlock, ZooKeeper, fencing tokens, deadlock prevention |
| 148-150 | Unique ID Generator (Snowflake) | ID generation | Snowflake algorithm, worker coordination, clock synchronization |
| 151-153 | Web Crawler | Crawling | URL frontier, politeness, deduplication (Bloom filter), robots.txt |
| 154-156 | Review & mock | Mock on collaborative or infrastructure tool | Focus on consistency and fault tolerance |

---

## 🎯 Phase 4: Integration, Mock Interviews & Deep Dives (Days 91–120)

### Objective: Polish your skills, identify weak areas, and simulate real interview conditions.

### Week 21–22: Advanced Topics & System Integration
| Day | Topic | Activities |
|-----|-------|------------|
| 157-160 | Multi-region architecture | Active-passive vs active-active, geo-sharding, cross-region replication |
| 161-164 | Data migration & schema evolution | Blue-green, parallel runs, backfill, backward/forward compatibility |
| 165-168 | Consensus algorithms | Raft, Paxos, ZooKeeper – understand leader election and replication |
| 169-172 | Real-world case studies | Study architectures of Netflix, Uber, WhatsApp, etc. (High Scalability blog) |
| 173-176 | Revisit weak patterns | Pick your red/yellow patterns and solve 2 more problems each |

### Week 23–24: Mock Interview Marathon
| Day | Activity |
|-----|----------|
| 177-180 | Full mock interviews (1 per day) – use Pramp, interviewing.io, or peer |
| 181-183 | Analyze recordings of your mocks; note improvements |
| 184-186 | Deep dive on specific bottlenecks you missed |
| 187-189 | Practice articulating trade-offs without a diagram |
| 190-192 | Final review of all patterns and core concepts |

### Week 25–26: Buffer & Fine-Tuning
| Day | Activity |
|-----|----------|
| 193-196 | Pick one system and design it with extreme scale (e.g., 1B users) |
| 197-200 | Review tracking sheet, ensure all top-20 questions are at least yellow/green |
| 201-204 | Simulate back-to-back interviews (2 in a day) to build stamina |
| 205-210 | Relax and review – light reading, mental preparation |

---

## 📊 HLD Mastery Tracker (Template)

Create a spreadsheet with these columns. Color-code confidence.

| Priority | Question | Pattern | Sub-pattern | System Type | Core Concepts | Scale Assumption | Attempt 1 | Bottlenecks Missed | Trade-offs Missed | Attempt 2 | Confidence | Status |
|----------|----------|---------|-------------|--------------|----------------|------------------|-----------|---------------------|--------------------|-----------|------------|--------|
| 1 | Design Twitter | Feed | Social Media | Read-heavy | Fan-out, caching, timeline | 200M DAU | 15-Jan | Hotkey partitioning | Push vs pull | 30-Jan | 🟡 | In Progress |

**Confidence Key**: 🟢 Solid, 🟡 Needs work, 🔴 Weak

**Status**: Not Started / In Progress / Done

---

## 🧠 The 6-Step Interview Framework (Practice It Every Time)

1. **Requirements (5 min)** – Functional, non-functional, out of scope.
2. **Estimation (5 min)** – Traffic, storage, bandwidth.
3. **API Design (5 min)** – Endpoints, request/response.
4. **High-Level Architecture (10 min)** – Draw components and data flow.
5. **Deep Dive (15 min)** – Zoom into 1-2 critical components (database, cache, queue).
6. **Bottlenecks & Trade-offs (5 min)** – Identify SPOFs, scaling limits, and justify choices.

---

## 🔥 Final Pro Tips

- **Pattern recognition is key** – After solving 3-4 problems in a pattern, you'll see the common skeleton.
- **Trade-off articulation** – Always compare alternatives: "I chose X over Y because of Z trade-off."
- **Mock with peers** – Explaining to others forces clarity.
- **Stay updated** – Read engineering blogs from FAANG (Netflix TechBlog, Uber Engineering, etc.)
- **Rest & confidence** – The last week is for light review and mental readiness.

You now have a battle-tested 120-day roadmap. Stick to it, track relentlessly, and you will crack any HLD interview. Good luck, Shubham! 🚀

# Comprehensive HLD Mastery Roadmap

# Comprehensive HLD Mastery Roadmap

> Equivalent Markdown version of the interactive roadmap.  
> Collapsible sections are preserved using `<details>`/`<summary>` (works in all modern Markdown renderers).  
> Buttons are shown as clickable prompts or links; no external calls are made.

---

<!-- Phase 1 -->
<details open>
<summary>
<strong>Phase 1 — Foundational bricks</strong>  
<sub>Weeks 1–2 · Zero code. Pure concepts. Non‑negotiable before anything else. <code>Start here</code></sub>
</summary>

> **Goal:** be able to explain every concept below in 60 seconds without notes.  
> If you can't explain it, you can't design with it.

**Week 1 — Networking + Scaling**
- HTTP/S, TCP vs UDP tradeoffs
- DNS, reverse proxy, CDN basics
- WebSockets vs long polling vs SSE
- gRPC vs REST vs GraphQL
- Vertical vs Horizontal scaling
- Stateless vs Stateful services
- Load balancer algorithms (Round‑Robin, Least Connections, IP Hash)
- Layer 4 vs Layer 7 load balancing

**Week 2 — Data + Reliability**
- SQL vs NoSQL decision matrix
- CAP theorem + PACELC
- ACID vs BASE
- Sharding strategies (range, hash, directory)
- Replication (leader‑follower, multi‑leader)
- Caching: cache‑aside, write‑through, write‑back
- Eviction: LRU, LFU, TTL
- Pub/Sub, Kafka vs RabbitMQ
- Blob/Object/Block storage differences
- Circuit breaker, retry backoff, idempotency

**Key concepts to master**
- **Consistent hashing** – Minimize rehashing on node add/remove. Virtual nodes.
- **CAP theorem** – CP vs AP. Why CA is impossible in distributed systems.
- **Back‑of‑envelope math** – DAU → QPS → storage → bandwidth. Always do this.
- **Caching layers** – CDN → App cache → DB cache. Multi‑layer reasoning.
- **Database indexes** – B‑Tree vs LSM Tree. When indexes hurt writes.
- **Message queues** – At‑least‑once vs exactly‑once. Idempotent consumers.

**Resources**
- *Designing Data‑Intensive Applications* (Kleppmann) – replication + partitioning chapters
- *System Design Interview Vol 1* (Alex Xu) – first 5 chapters
- ByteByteGo newsletter + YouTube channel

> 🔗 **Ask Claude:** *“Explain consistent hashing to me from scratch with an example”*

</details>

---

<!-- Phase 2 -->
<details open>
<summary>
<strong>Phase 2 — The interview framework</strong>  
<sub>Week 3 · Lock in the 6‑step template before touching any problem. <code>Critical</code></sub>
</summary>

> Most candidates fail not because they don't know the tech — they fail because they jump straight to drawing boxes without scoping the problem. This framework is your skeleton under pressure.

| # | Step | Description |
|---|------|-------------|
| 1 | **Clarify requirements** | 5–7 min. Functional (what the system does) + Non‑functional (latency, availability, consistency, scale). Define what's out of scope. |
| 2 | **Estimate scale** | 3–5 min. DAU → QPS → storage/year → bandwidth. Calculate peak QPS (avg × 3). This drives every architectural choice. |
| 3 | **Design the API** | 3–5 min. REST endpoints, request/response shape, auth method, pagination strategy. Makes the data model obvious. |
| 4 | **High‑level architecture** | 8–10 min. Client → DNS → LB → App servers → Cache/DB/Queue. Draw the happy path first. |
| 5 | **Deep dive** | 12–15 min. Zoom into 1–2 hard components. Schema, sharding key, caching strategy, queue usage, failure handling. |
| 6 | **Bottlenecks + trade‑offs** | 5 min. Where is the SPOF? What breaks at 10× traffic? What did you sacrifice and why? This is where seniors are separated. |

**Practice problems for the framework** (no deep architecture needed yet)
- **URL shortener** `[HA]` → *“Walk me through the 6‑step framework applied to designing a URL shortener”*
- **Pastebin** `[HA]` → *“Walk me through the 6‑step framework applied to designing a Pastebin”*
- **Rate limiter** `[Write]` → *“Walk me through the 6‑step framework applied to designing a rate limiter”*

**The trade‑off sentence to practice out loud**  
> *“I'm choosing [X] over [Y] because in this system [reason] matters more than [what we sacrifice], and under [condition] I'd switch to Y instead.”*

> 🔗 **Start a mock interview:** *“Do a mock 45 minute HLD session with me on Twitter. Ask me questions and guide me through the 6 steps.”*

</details>

---

<!-- Phase 3 -->
<details open>
<summary>
<strong>Phase 3 — Pattern‑based practice: Tier 1</strong>  
<sub>Weeks 4–5 · CRUD/Storage · Feed/Timeline · Real‑time messaging. Covers ~40% of all FAANG questions. <code>Tier 1</code></sub>
</summary>

> Solve 3 problems per pattern before moving on. Just like DP — recognize the pattern first, then fill in the details.

**Pattern A — CRUD / Storage systems**  
*Core insight:* Master key generation (Base62, UUID, Snowflake), consistent hashing for cache distribution, and choosing SQL vs NoSQL. Every system stores data; this pattern is the foundation.

- **URL shortener ★** `[HA]` – *Deep dive: design TinyURL. Hashing, collision handling, caching, 100M URLs.*
- **Pastebin ★** `[HA]` – *Deep dive: design Pastebin. Blob storage, expiry, unique ID generation.*
- **Dropbox / Drive ★** `[HA]` – *Deep dive: design Dropbox. Chunking, delta sync, deduplication, conflict resolution.*
- **Image hosting** `[Read]` – *Deep dive: design Imgur-like service.*

---

**Pattern B — Feed / Timeline systems (fan‑out)**  
*Core insight:* Push vs pull vs hybrid fan‑out. Push is fast to read but expensive for celebrities. Pull is cheap to write but slow to read. Hybrid is the real answer.

- **Twitter ★** `[Read]` – *Deep dive: fan‑out on write vs read, hybrid for celebrities, Redis timeline cache, Cassandra for tweets.*
- **Instagram ★** `[Read]` – *Deep dive: Instagram feed. Media storage on CDN, hybrid fan‑out, ranking.*
- **Facebook feed ★** `[Read]` – *Deep dive: News Feed. Algorithmic ranking, diverse content, graph storage.*
- **Reddit** `[Read]` – *Deep dive: Reddit. Upvote aggregation, time‑decay ranking, hot/new/top feeds.*

---

**Pattern C — Real‑time messaging**  
*Core insight:* WebSockets for persistent bidirectional connections. Presence via Redis with TTL. Message ordering via sequence IDs. Cassandra for write‑heavy message storage. Read receipts via pub/sub.

- **WhatsApp ★** `[Write]` – *Deep dive: WebSocket gateway, ordering, read receipts, last seen, multi‑device sync.*
- **Group chat ★** `[Write]` – *Deep dive: group chat. Fan‑out to members, ordering guarantees, history storage.*
- **Slack** `[Write]` – *Deep dive: channels, threads, permissions, unread counts, search.*
- **Notification service ★** `[Write]` – *Deep dive: push, email, SMS delivery, fan‑out, retry logic.*

> 🔗 **Compare:** *“What are the key differences between designing WhatsApp vs designing Twitter from an architectural pattern perspective?”*

</details>

---

<!-- Phase 4 -->
<details>
<summary>
<strong>Phase 4 — Pattern‑based practice: Tier 2</strong>  
<sub>Weeks 5–6 · Fintech/ACID · Search · Infrastructure. High‑frequency in senior interviews. <code>Tier 2</code></sub>
</summary>

**Pattern D — Fintech / High‑consistency systems**  
*Core insight:* Correctness over availability. Idempotency keys, double‑entry ledger, optimistic locking, SAGA pattern for distributed transactions. Never use eventual consistency.

- **Payment gateway ★** `[ACID]` – *Deep dive: Stripe. Idempotency, state machine, double‑entry ledger, reconciliation.*
- **Digital wallet ★** `[ACID]` – *Deep dive: balance consistency, concurrent transactions, row‑level locking.*
- **Ticketmaster ★** `[ACID]` – *Deep dive: seat locking, preventing double‑booking, high‑concurrency events.*
- **Flash sale ★** `[ACID]` – *Deep dive: inventory locking, queue‑based demand smoothing, Redis counters.*
- **Google Docs ★** `[ACID]` – *Deep dive: operational transformation vs CRDT, real‑time sync, cursor presence.*
- **Hotel booking** `[ACID]` – *Deep dive: distributed locking, race conditions, reservation expiry.*

---

**Pattern E — Search & indexing**  
*Core insight:* Inverted index for full‑text search. Trie for prefix matching (autocomplete). Elasticsearch. Ranking = TF‑IDF + PageRank + ML signals. Freshness vs quality tradeoff.

- **Autocomplete ★** `[Read]` – *Deep dive: trie, prefix matching, popularity ranking, sub‑100ms response.*
- **Web crawler ★** `[Write]` – *Deep dive: URL frontier, politeness, deduplication via bloom filters, prioritization.*
- **Google Search** `[Read]` – *Deep dive: inverted index, sharding by document, ranking pipeline.*
- **E‑commerce search** `[Read]` – *Deep dive: Elasticsearch, faceted filters, inventory‑aware ranking.*

---

**Pattern F — Infrastructure utilities**  
*Core insight:* Building blocks every system uses. Mastering them lets you answer follow‑up questions anywhere.

- **Rate limiter ★** `[Write]` – *Deep dive: token bucket, sliding window, Redis + Lua atomicity, multi‑server.*
- **Distributed cache ★** `[Read]` – *Deep dive: Redis. Consistent hashing, replication, LRU eviction, persistence.*
- **ID generator (Snowflake) ★** `[Write]` – *Deep dive: 64‑bit structure, timestamp + worker ID + sequence, clock skew.*
- **CDN** `[HA]` – *Deep dive: pull vs push, edge caching, invalidation, GeoDNS routing.*
- **API gateway ★** `[HA]` – *Deep dive: rate limiting, auth, routing, circuit breaking, async logging.*
- **Task scheduler ★** `[Write]` – *Deep dive: exactly‑once execution, leader election, missed job handling.*

> 🔗 **Compare rate limiting algorithms:** *“What are the three rate limiting algorithms (token bucket, leaky bucket, sliding window) and when do you pick each one?”*

</details>

---

<!-- Phase 5 -->
<details>
<summary>
<strong>Phase 5 — Pattern‑based practice: Tier 3</strong>  
<sub>Weeks 7–8 · Geospatial · Streaming/Media · Big Data. Depth problems for senior roles. <code>Tier 3</code></sub>
</summary>

**Pattern G — Geospatial systems**  
*Core insight:* Geohash encodes lat/long – nearby points share prefix. Quadtrees partition 2D space. Real‑time location updates via Redis. Proximity search across geohash cells.

- **Uber / Lyft ★** `[Read]` – *Deep dive: geohash, real‑time driver location, matching, surge pricing, trip state machine.*
- **Yelp / nearby ★** `[Read]` – *Deep dive: geohash indexing, proximity queries, category filters, ranking.*
- **Google Maps ★** `[Read]` – *Deep dive: graph routing (Dijkstra/A★), map tile storage, real‑time traffic, ETAs.*
- **Food delivery tracker** `[Write]` – *Deep dive: real‑time location streaming, ETA updates, assignment optimization.*

---

**Pattern H — Streaming & media**  
*Core insight:* Large files → object storage (S3). Transcoding pipeline → multiple resolutions. CDN for global distribution. Adaptive bitrate (ABR) for network quality. Live streaming needs buffer management.

- **YouTube ★** `[HA]` – *Deep dive: chunked upload, transcoding pipeline, CDN delivery, ABR.*
- **Netflix** `[Read]` – *Deep dive: multi‑region CDN, Open Connect, adaptive streaming, recommendation.*
- **Spotify** `[Read]` – *Deep dive: DRM, adaptive bitrate, offline download, playlist management.*
- **Video transcoding** `[Write]` – *Deep dive: job queue, worker pools, parallelism, failure handling.*
- **S3 object storage** `[HA]` – *Deep dive: chunking, erasure coding, metadata service, consistency model.*

---

**Pattern I — Big data & stream processing**  
*Core insight:* Lambda architecture (batch + speed) vs Kappa (stream‑only). Count‑min sketch for heavy hitters. Time‑series DB for metrics. Kafka as the backbone for decoupling.

- **Kafka system ★** `[Write]` – *Deep dive: append‑only log, partitions, consumer groups, replication, exactly‑once.*
- **Ad‑click aggregator ★** `[Write]` – *Deep dive: high‑throughput ingestion, deduplication, real‑time vs batch.*
- **Top‑K / heavy hitters ★** `[Write]` – *Deep dive: count‑min sketch, heap, time windows, distributed aggregation.*
- **Metrics monitoring** `[Write]` – *Deep dive: time‑series ingestion, downsampling, alerting.*
- **Logging pipeline** `[Write]` – *Deep dive: log shipping, Kafka buffer, Elasticsearch indexing, retention.*

> 🔗 **Stream processing architectures:** *“Explain the lambda architecture vs kappa architecture for stream processing, with tradeoffs.”*

</details>

---

<!-- Phase 6 -->
<details>
<summary>
<strong>Phase 6 — Mock interviews + iteration</strong>  
<sub>Weeks 7–10 · Runs in parallel with Phase 5. The only way to close the knowing/doing gap. <code>Non‑negotiable</code></sub>
</summary>

**Mock rules – How to practice correctly**
- 45‑minute hard cap, no extensions
- Verbalize every decision out loud
- No looking at notes during the mock
- Record yourself if practicing solo
- Use Pramp or interviewing.io for peers
- At least 8 mocks before real interviews

**After each mock – Post‑session debrief**  
- What requirement did I miss or assume?
- What bottleneck did I not identify?
- What trade‑off did I not articulate?
- Did I estimate before designing?
- Did I deep‑dive the right component?
- Mark Red/Yellow/Green in your tracker
- Revisit Red problems after 10 days

**Tracker columns (Excel/Notion)**
- Problem name + pattern
- Read/Write/ACID/HA type
- Attempt 1 date
- Bottlenecks missed
- Trade‑offs skipped
- Confidence: R / Y / G
- Revisit date (if Red)

**Common interview failures (avoid these)**
- **Jumping to design** – Never design before clarifying. 5–7 min on requirements.
- **Skipping estimation** – QPS determines sharding, caching, multiple DCs.
- **Tool name‑dropping** – “I'll use Kafka” is worthless without “because we need durable ordered streams with multiple consumers.”
- **No bottleneck analysis** – Always ask: what breaks first at 10× load?
- **One‑size DB choice** – Justify every DB pick. SQL vs NoSQL vs time‑series vs graph.
- **Silence under pressure** – Say what you’re thinking. Never go silent.

> 🔗 **Start mock interview:** *“Do a 45‑minute mock HLD interview with me on designing YouTube. Play the role of interviewer, ask follow‑up questions, and give feedback at the end.”*

</details>

---

<!-- Phase 7 -->
<details>
<summary>
<strong>Phase 7 — Staff‑level depth (advanced)</strong>  
<sub>Month 3+ · Only needed for senior/staff interviews or complex follow‑up questions. <code>Advanced</code></sub>
</summary>

- **Multi‑region architecture** – Active‑active vs Active‑passive. Cross‑region replication. Geo‑sharding. Regional failover.
- **Consensus protocols** – Raft (etcd), Paxos (ZooKeeper/Chubby). Leader election. Quorum reads/writes.
- **SAGA pattern** – Distributed transactions without 2PC. Choreography vs orchestration. Compensation actions.
- **Schema evolution** – Backward/forward compatibility. Dual writes. Blue‑green migrations. Versioned schemas.
- **Data migration** – Parallel runs, gradual rollout, backfill strategies. Zero‑downtime migrations.
- **Tunable consistency** – Cassandra consistency levels (ONE, QUORUM, ALL). Read repair. Anti‑entropy.
- **CRDTs** – Conflict‑free Replicated Data Types for collaborative editing. G‑Counter, LWW‑Register.
- **Distributed locking** – Redis Redlock. ZooKeeper sequential znodes. Fencing tokens. Lease‑based locks.

> 🔗 **Explain SAGA pattern:** *“Explain the SAGA pattern for distributed transactions with a concrete payment example, comparing choreography vs orchestration.”*

</details>

# Learning Order and System-Design Progression

To gain mastery in High-Level Design (HLD) for top product companies, it is best to move from the **foundational "bricks"** to the **architectural "blueprints"** before finally diving into specific **case studies**. This ensures you don't just memorize solutions but actually understand *why* certain choices are made.

I’ll ask guiding questions along the way to help you build this mental model. Here is the recommended order for your journey:

### 1. Foundations: The "Bricks" 🧱

Before designing a system, you must understand the individual components. This is the **High-Availability (Pillar 4)** phase. You’ll learn how to store and route data reliably.

* **Key Concepts:** Load Balancing, Caching, Scaling (Vertical vs. Horizontal), and CDNs.
* **The Math:** Consistent Hashing and Back-of-the-envelope estimations.

### 2. Communication & Protocols: The "Glue" 📡

Next, you learn how those bricks talk to each other. This bridges **Write-Heavy (Pillar 1)** and **Read-Heavy (Pillar 2)** logic.

* **Key Concepts:** REST vs. GraphQL, WebSockets vs. Long Polling, and gRPC.
* **Message Queues:** Understanding how to decouple systems using tools like Kafka or RabbitMQ.

### 3. Data Mastery: The "Memory" 💾

This is where you tackle **ACID/Transactions (Pillar 3)**. You need to know which database to pick for which problem.

* **Key Concepts:** SQL vs. NoSQL, CAP Theorem, Database Sharding, and Replication (Leader-Follower).
* **Indexes:** How B-Trees and LSM Trees work under the hood.

### 4. Architectural Patterns: The "Blueprints" 🏗️

Once you know the components, you learn the standard ways they are combined.

* **Patterns:** Microservices vs. Monoliths, Event-Driven Architecture, and Fan-out patterns.

### 5. System Case Studies: The "Practice" 🏆

Finally, you apply everything to the 70 problems we categorized. You start with "Anchor" problems that represent each pillar (like TinyURL for Storage or Twitter for Read-Heavy).

---

To see where we should focus first, let's test a bit of foundational knowledge.

Imagine you are designing an app like Instagram, and it suddenly becomes incredibly popular. You have one massive database server that is starting to struggle with the number of people trying to **read** their feeds at the same time.

**Without moving to a completely new database, what is one common "Pillar 4" component you could place in front of the database to speed up those frequent read requests?**

That is a great way to visualize the curriculum! Let's arrange those 70 problems into a structured, step-by-step roadmap. I've organized them so you build foundational knowledge 🧱 before moving into the high-stakes, complex systems 🚀.

I'll help you navigate this list by asking guiding questions as we tackle each section.

### **Phase 1: Foundations & Distributed Building Blocks** 🧱

These problems teach you the "primal patterns" of system design. Master these, and the complex ones become much easier.

1. **Load Balancer Design** ⭐ (Pillar 4: Storage/HA)
2. **API Gateway Service** ⭐ (Pillar 4: Storage/HA)
3. **Consistent Hashing** ⭐ (Pillar 4: Storage/HA)
4. **Distributed ID Generator (Snowflake)** ⭐ (Pillar 4: Storage/HA)
5. **URL Shortener (TinyURL)** ⭐ (Pillar 4: Storage/HA)
6. **Pastebin (Text Storage)** ⭐ (Pillar 4: Storage/HA)
7. **Distributed Cache (Redis-like)** (Pillar 2: Read-Heavy)
8. **Rate Limiter (Distributed)** ⭐ (Pillar 1: Write-Heavy)
9. **Distributed Task Scheduler** ⭐ (Pillar 1: Write-Heavy)
10. **Content Delivery Network (CDN)** (Pillar 4: Storage/HA)

### **Phase 2: High-Consistency & Transactional Systems** 💰

Focus on "Correctness." These are the most common interview questions for Fintech and E-commerce companies.

11. **Payment Gateway (Stripe-like)** ⭐ (Pillar 3: ACID)
12. **Digital Wallet (Ledger System)** ⭐ (Pillar 3: ACID)
13. **Ticketmaster (Booking)** ⭐ (Pillar 3: ACID)
14. **Flash Sale System (Amazon)** ⭐ (Pillar 3: ACID)
15. **Hotel Booking (Expedia)** (Pillar 3: ACID)
16. **Flight Reservation System** (Pillar 3: ACID)
17. **Stock Brokerage (Robinhood)** (Pillar 3: ACID)
18. **Crypto Exchange (Order Book)** (Pillar 3: ACID)
19. **Election/Voting System** (Pillar 3: ACID)
20. **Google Docs (Conflict Resolution/OT)** ⭐ (Pillar 3: ACID)

### **Phase 3: Social, Discovery & Read-Heavy Patterns** 📣

Learn the "Fan-out" pattern—how to push one piece of data to millions of followers.

21. **News Feed (Push/Pull Models)** ⭐ (Pillar 2: Read-Heavy)
22. **Twitter (High-Volume Fan-out)** ⭐ (Pillar 2: Read-Heavy)
23. **Instagram (Media Feed)** ⭐ (Pillar 2: Read-Heavy)
24. **Reddit (Ranking & Upvotes)** (Pillar 2: Read-Heavy)
25. **Pinterest (Image Feed)** (Pillar 2: Read-Heavy)
26. **HackerNews (Time-decay Ranking)** (Pillar 2: Read-Heavy)
27. **Notification Service** ⭐ (Pillar 1: Write-Heavy)
28. **Presence Platform (Online Status)** (Pillar 2: Read-Heavy)
29. **Friend Suggestions/Recommendation** (Pillar 2: Read-Heavy)
30. **A/B Testing Platform** (Pillar 2: Read-Heavy)

### **Phase 4: Real-time Communication & Messaging** 💬

Focus on WebSockets and low-latency data delivery.

31. **WhatsApp/Messenger (1-on-1)** ⭐ (Pillar 1: Write-Heavy)
32. **Group Chat System** ⭐ (Pillar 1: Write-Heavy)
33. **Slack (Enterprise Messaging)** (Pillar 1: Write-Heavy)
34. **Zoom / Video Conferencing** (Pillar 2: Read-Heavy)
35. **Email Service (Gmail-like)** (Pillar 4: Storage/HA)
36. **Online Quiz (Kahoot-like)** (Pillar 2: Read-Heavy)
37. **Collaborative Whiteboard (Miro)** (Pillar 1: Write-Heavy)

### **Phase 5: Geospatial & Location-Based Services** 🚗

This requires learning specialized indexing like Quadtrees or Geohashing.

38. **Yelp/Nearby (Proximity Service)** ⭐ (Pillar 2: Read-Heavy)
39. **Uber/Lyft (Matching Service)** ⭐ (Pillar 2: Read-Heavy)
40. **Google Maps (Pathfinding)** ⭐ (Pillar 2: Read-Heavy)
41. **Food Delivery Tracker (DoorDash)** (Pillar 1: Write-Heavy)
42. **Public Transit Tracker** (Pillar 2: Read-Heavy)
43. **AirBnB (Search & Reservation)** (Pillar 3: ACID)

### **Phase 6: Heavy Media, Streaming & Storage** 🎥

Learn about massive binary data (Blobs), transcoding, and storage efficiency.

44. **YouTube (Upload & Playback)** ⭐ (Pillar 4: Storage/HA)
45. **Netflix (Global Distribution)** (Pillar 2: Read-Heavy)
46. **TikTok (Short-form Feed)** (Pillar 2: Read-Heavy)
47. **Spotify (Audio Streaming)** (Pillar 2: Read-Heavy)
48. **Twitch (Live Stream Buffer)** (Pillar 2: Read-Heavy)
49. **Video Transcoding Pipeline** (Pillar 1: Write-Heavy)
50. **S3-like Object Storage** (Pillar 4: Storage/HA)
51. **HDFS (Distributed File System)** (Pillar 4: Storage/HA)
52. **Google Forms (Survey Tool)** (Pillar 4: Storage/HA)

### **Phase 7: Search, Crawling & Indexing** 🔍

Focus on how to organize the world's information for quick retrieval.

53. **Autocomplete/Typeahead** ⭐ (Pillar 2: Read-Heavy)
54. **Web Crawler** ⭐ (Pillar 1: Write-Heavy)
55. **Google Search (Indexing)** (Pillar 2: Read-Heavy)
56. **Elasticsearch (Log Search)** (Pillar 2: Read-Heavy)
57. **E-commerce Search & Filters** (Pillar 2: Read-Heavy)

### **Phase 8: Data Engineering, Metrics & Big Data** 📈

Focus on processing high-volume streams and complex analysis.

58. **Distributed Message Queue (Kafka)** ⭐ (Pillar 1: Write-Heavy)
59. **Ad-Click Aggregator** ⭐ (Pillar 1: Write-Heavy)
60. **Top K Elements (Heavy Hitters)** ⭐ (Pillar 1: Write-Heavy)
61. **Metrics & Monitoring (Prometheus)** (Pillar 1: Write-Heavy)
62. **Logging Pipeline (ELK)** (Pillar 1: Write-Heavy)
63. **Fraud Detection Service** (Pillar 1: Write-Heavy)
64. **Data Warehouse (BigQuery-like)** (Pillar 2: Read-Heavy)

### **Phase 9: Advanced Infrastructure & Security** ⚙️

The "Hard" tier—systems that manage other systems.

65. **Distributed Key-Value Store** (Pillar 4: Storage/HA)
66. **Distributed Locking Service** (Pillar 4: Storage/HA)
67. **Web Application Firewall (WAF)** (Pillar 4: Storage/HA)
68. **DDOS Protection System** (Pillar 4: Storage/HA)
69. **Secrets Manager (Vault)** (Pillar 4: Storage/HA)
70. **Online Code Judge (LeetCode)** (Pillar 1: Write-Heavy)

---

Now that you have the path, let's start with the very first step in **Phase 1**.

Imagine you are designing a system that needs to handle millions of requests, but you have multiple servers. To make sure no single server gets overwhelmed, you place a **Load Balancer** in front of them.

**What is the primary job of a Load Balancer when a new request comes in?**

# Advanced Execution, Tracking, Resources, and Interview Checklist

# 📅 PHASE 4 — 8-Week Execution Plan

## Week-by-Week Breakdown

| Week | Focus | Key Activities |
|------|-------|----------------|
| **Week 1-2** | Core Fundamentals | Study networking, databases, CAP theorem, caching, messaging |
| **Week 3** | CRUD + Storage Systems | Practice URL Shortener, Pastebin, Dropbox, Ticket Booking |
| **Week 4** | Feed Systems | Practice Twitter, Instagram, Facebook Feed, LinkedIn |
| **Week 5** | Real-Time + Search | Practice WhatsApp, Discord, Google Search, Autocomplete |
| **Week 6** | Rate Limiter + Caching + Logging | Practice Rate Limiter, Distributed Cache, Metrics System |
| **Week 7** | Payments + Recommendation | Practice Payment Gateway, Netflix Recs, Uber |
| **Week 8** | Mock Interviews | Full mock interviews with feedback |

## Daily Practice Routine

| Time | Activity |
|------|----------|
| **30 min** | Review one concept (caching strategies, consistency models) |
| **1 hour** | Solve one design problem using the 6-step framework |
| **15 min** | Document in tracker (what went well, what to improve) |
| **Weekly** | One full mock interview with peer |

---

# 🎯 How to Practice (Like Your DP Excel Tracker)

Since you love structured tracking, create this Excel/Notion tracker:

## HLD Practice Tracker Template

| Question | Pattern | Sub-pattern | Primary Challenge | Read/Write | Consistency | Attempt 1 Date | Bottlenecks Missed | Key Improvements | Status |
|----------|---------|-------------|-------------------|------------|-------------|----------------|-------------------|-------------------|--------|
| Design Twitter | Feed | Social Media | Celebrity fan-out | Heavy Read | Eventual | 2024-01-15 | Hotkey partitioning | Add hybrid fan-out | 🟡 |
| Design WhatsApp | Real-Time | Messaging | Message ordering | Write-heavy | Strong ordering | 2024-01-16 | Last seen scaling | Redis with TTL | 🟢 |

**Color Coding:**
- 🟢 **Green** = Confident (can explain trade-offs, handled follow-ups well)
- 🟡 **Yellow** = Needs improvement (missed key bottlenecks)
- 🔴 **Red** = Weak (struggled with fundamentals)

---

# 🧠 How to Think Like Top Engineers

## The 5 Critical Questions to Always Ask

1. **"Where does this break?"** — Identify single points of failure
2. **"What if traffic becomes 10x?"** — Think about scaling bottlenecks
3. **"What if one region fails?"** — Consider disaster recovery
4. **"What if DB crashes?"** — Plan for data layer failures
5. **"What is the biggest bottleneck?"** — Prioritize improvements

## Trade-off Mindset

At every decision point, ask:
- "Why X over Y?"
- "What do we gain? What do we lose?"
- "Under what conditions would the other choice be better?"

---

# 🥇 Top 20 Must-Do Questions (Most Frequently Asked)

| Rank | Question | Pattern | Why It's Asked |
|------|----------|---------|----------------|
| 1 | Design Twitter/X | Feed | Tests fan-out, caching, timeline generation |
| 2 | Design WhatsApp | Real-Time | Tests WebSockets, message ordering, presence |
| 3 | Design URL Shortener | CRUD | Tests hashing, key generation, redirection |
| 4 | Design YouTube | Streaming | Tests CDN, transcoding, large file storage |
| 5 | Design Uber | Geospatial | Tests location indexing, real-time matching |
| 6 | Design Instagram | Feed + Media | Tests image storage, feed ranking |
| 7 | Design Dropbox | Storage | Tests file sync, deduplication, delta sync |
| 8 | Design Google Drive | Storage | Similar to Dropbox with collaboration |
| 9 | Design API Rate Limiter | Rate Limiter | Tests algorithms, distributed counting |
| 10 | Design Distributed Cache | Caching | Tests consistent hashing, eviction |
| 11 | Design Payment System | Financial | Tests idempotency, ACID, double-entry |
| 12 | Design Ticket Booking | CRUD + Concurrency | Tests locking, concurrency control |
| 13 | Design Netflix | Streaming + Recs | Tests CDN, recommendation systems |
| 14 | Design Google Maps | Geospatial | Tests spatial indexing, routing |
| 15 | Design Facebook News Feed | Feed | Tests algorithmic ranking, diverse content |
| 16 | Design Chat System | Real-Time | Tests WebSockets, message persistence |
| 17 | Design Search Autocomplete | Search | Tests trie, prefix matching, popularity |
| 18 | Design Distributed Logging | Logging | Tests high-volume ingestion, search |
| 19 | Design Metrics Monitoring | Logging | Tests time-series DB, aggregation |
| 20 | Design Kafka-like System | Large-Scale | Tests distributed log, partitioning |

---

# 🔥 Advanced (Staff-Level) Topics

Once you're comfortable with standard designs, dive into these advanced topics:

## Multi-Region Architecture

| Pattern | Description | Trade-offs |
|---------|-------------|------------|
| **Active-Passive** | One region active, other on standby | Simple, but failover takes time |
| **Active-Active** | All regions serving traffic | Complex, needs global consensus |
| **Geo-sharding** | Data partitioned by region | Reduced latency, compliance |

## Data Migration Strategies

| Strategy | When to Use |
|----------|-------------|
| **Blue-Green Deployment** | Zero-downtime migrations |
| **Parallel Runs** | Validate new system before cutover |
| **Gradual Rollout** | Migrate users in batches |
| **Backfill** | Migrate historical data after cutover |

## Schema Evolution

| Technique | Description |
|-----------|-------------|
| **Backward Compatible** | New code can read old data |
| **Forward Compatible** | Old code can read new data |
| **Versioning** | Store schema version with data |
| **Dual Writes** | Write to both old and new schemas during migration |

## Consistent Hashing Internals

- Virtual nodes for better distribution
- Replication factor for fault tolerance
- Read repair for consistency

## Leader Election Algorithms

| Algorithm | How It Works |
|-----------|--------------|
| **Bully Algorithm** | Highest ID node becomes leader |
| **Raft** | Election terms, majority vote |
| **Paxos** | Complex, used in Chubby/ZooKeeper |
| **ZooKeeper** | Sequential znodes for leader election |

## Consensus Protocols

- **Paxos**: Classic but complex
- **Raft**: Understandable, used in etcd
- **Zab**: ZooKeeper's protocol

## Advanced CAP Scenarios

- **Network partition handling** — What does your system do?
- **Tunable consistency** — Cassandra's consistency levels
- **Quorum-based systems** — Read/write quorums

---

# 📚 Complete Resource Guide

## Books (Must Read)

| Book | Why |
|------|-----|
| **Designing Data-Intensive Applications** | The bible — read chapters on replication, partitioning, consistency |
| **System Design Interview — An Insider's Guide (Vol 1)** | Best practical introduction |
| **System Design Interview — An Insider's Guide (Vol 2)** | More advanced problems |
| **Building Microservices** | Microservices architecture patterns |
| **Database Internals** | Deep dive into storage engines |

## Online Courses

| Course | Platform |
|--------|----------|
| **Grokking the System Design Interview** | DesignGurus |
| **System Design Interview** | Exponent |
| **Hello Interview** | HelloInterview (with AI mock interviews) |

## YouTube Channels

| Channel | Best For |
|---------|----------|
| **Gaurav Sen** | System design concepts |
| **CodeKarle** | Detailed walkthroughs |
| **System Design Interview** | Mock interviews |
| **Hussein Nasser** | Networking and databases |

## Blogs and Websites

| Site | Content |
|------|---------|
| **High Scalability** | Real-world architecture case studies |
| **The Architect Elevator** | Architecture patterns |
| **Martin Fowler's Blog** | Microservices, architecture |
| **AWS Architecture Blog** | Cloud-native patterns |

## Practice Platforms

| Platform | Features |
|----------|----------|
| **Pramp** | Free peer mock interviews |
| **Interviewing.io** | Anonymous mock interviews with engineers |
| **HelloInterview** | AI-powered mock interviews |

---

# 🎯 Final Advice for You Specifically

Since you're:

- ✅ Strong in pattern-based learning (DSA + DP)
- ✅ Structured in tracking progress
- ✅ Preparing seriously for interviews

## Your Success Formula

1. **Treat HLD exactly like DP** — Identify patterns, solve 3-5 per pattern, review trade-offs, repeat until fluent

2. **Use your tracker religiously** — Document every attempt, what you missed, and improvements

3. **Focus on trade-offs** — The interviewer cares more about *why* you chose something than *what* you chose

4. **Practice vocalizing** — Design is collaborative; practice explaining your thinking out loud

5. **Mock interviews are non-negotiable** — At least 5 full mocks before the real interview

## Common Pitfalls to Avoid

| Pitfall | How to Avoid |
|---------|--------------|
| Jumping straight to design | Always clarify requirements first |
| Ignoring non-functional requirements | Ask about scale, latency, consistency |
| Memorizing solutions | Learn patterns, not specific designs |
| Not identifying bottlenecks | Always ask "what breaks?" |
| One-size-fits-all database | Justify SQL vs NoSQL choice |

---

# ✅ Quick Reference: HLD Interview Checklist

## Pre-Interview Checklist
- [ ] Review core concepts (CAP, caching, databases)
- [ ] Refresh 5 most common patterns
- [ ] Practice 1 design problem (timed)
- [ ] Prepare questions for interviewer

## During Interview Checklist
- [ ] Step 1: Clarify requirements (5 min)
- [ ] Step 2: Estimate scale (5 min)
- [ ] Step 3: Design APIs (5 min)
- [ ] Step 4: High-level diagram (10 min)
- [ ] Step 5: Deep dive (15 min)
- [ ] Step 6: Trade-offs and bottlenecks (5 min)

## Post-Interview Reflection
- [ ] What requirements did I miss?
- [ ] What bottlenecks did I overlook?
- [ ] What trade-offs could I have discussed better?
- [ ] Update tracker with learnings

---

# 🚀 Your Next Steps

1. **Start with Phase 1** — Build foundations (2 weeks)
2. **Create your tracker** — Excel/Notion with all questions
3. **Pick first pattern (CRUD)** — Solve 3 questions this week
4. **Find a mock partner** — Pramp or study group
5. **Schedule weekly reviews** — Track progress

---

**Remember:** The goal is not to memorize architectures but to develop a **trade-off mindset**. Every design decision is a balance between competing concerns. Show the interviewer you understand these trade-offs and can make reasonable choices under ambiguity.

You've got this, Shubham! Your pattern-based approach from DSA will serve you well here. Just apply the same discipline, and you'll master HLD.

---

**Want to proceed? Tell me:**
1. Do you want the Excel/Notion tracker template?
2. Shall we do a mock deep-dive on one pattern (like Feed systems)?
3. Or start with the first pattern (CRUD) with a guided solution?

# Supplement — Execution Plans, Tracking Templates, and Top Questions

# 🔷 PART 5 — 8-Week Execution Plan

| Week | Focus                        |
| ---- | ---------------------------- |
| 1–2  | Foundations                  |
| 3    | CRUD + URL Shortener + Cache |
| 4    | Feed systems                 |
| 5    | Realtime + Search            |
| 6    | Logging + Rate limiter       |
| 7    | Payments + Booking           |
| 8    | 5 full mock interviews       |

---

# 🔷 PART 6 — Advanced (Staff-Level)

* Multi-region architecture
* Active-active vs Active-passive
* Cross-region replication
* Data migration
* Schema evolution
* Consistent hashing internals
* Raft consensus
* SAGA pattern

---

# 🔷 PART 7 — Tracking Template (Your Style)

Since you love structured Excel tracking:

| Question | Pattern | Sub-pattern | Read/Write Type | Scale | Attempt 1 | Bottlenecks Missed | Trade-offs Missed | Final Confidence |
| -------- | ------- | ----------- | --------------- | ----- | --------- | ------------------ | ----------------- | ---------------- |

Color code:

* Green = Strong
* Yellow = Moderate
* Red = Weak

---

# 🔷 Final Advice (Strategic)

You already master:

* Pattern recognition (from DP)
* Structured learning
* Tracking & iteration

Treat HLD exactly the same way:

1. Solve 3 per pattern
2. Identify common architectural motifs
3. Refine trade-offs
4. Revisit after 2 weeks

---

If you'd like next, I can:

* Build a **ready-to-use HLD Excel tracking template structure**
* Run a **real 45-minute mock interview simulation**
* Deep dive one pattern (e.g., Feed system with full architecture)
* Create a **priority-ordered top 30 question list for FAANG**

Tell me your next move.

Excellent. Since you prefer **structured, pattern-wise tracking (like your DP Excel system)**, below is a **ready-to-use HLD Excel template structure** + a **priority-ordered Top 30 FAANG HLD question list**, categorized by pattern and importance.

This is optimized for:

* Structured learning
* Measurable improvement
* Mock interview readiness
* Pattern recognition

---

# 📊 PART 1 — READY-TO-USE HLD EXCEL TRACKING TEMPLATE

You should create **4 sheets**:

---

# 🟢 SHEET 1 — MASTER DASHBOARD

### Columns:

| Field              | Description                   |
| ------------------ | ----------------------------- |
| Total Questions    | =COUNT(All Questions)         |
| Completed          | COUNTIF(Status, "Done")       |
| Weak Areas         | COUNTIF(Confidence, "Red")    |
| Medium             | COUNTIF(Confidence, "Yellow") |
| Strong             | COUNTIF(Confidence, "Green")  |
| Next Focus Pattern | Manual                        |

Add:

* Conditional formatting (Green / Yellow / Red)
* Completion % bar

---

# 🟢 SHEET 2 — QUESTION TRACKER (Main Sheet)

### Columns (Copy Exactly)

| Column             | Purpose                                                     |
| ------------------ | ----------------------------------------------------------- |
| Priority Rank      | 1–30 (based on FAANG frequency)                             |
| Question Name      | Design Twitter, etc.                                        |
| Pattern            | Feed, CRUD, Payment, etc.                                   |
| Sub-Pattern        | Fan-out, Locking, etc.                                      |
| System Type        | Read-heavy / Write-heavy / Strong Consistency / Low Latency |
| Core Concepts      | Caching, Sharding, MQ, etc.                                 |
| Scale Assumption   | DAU / QPS                                                   |
| Attempt 1 Date     | First try                                                   |
| Attempt 2 Date     | Revision                                                    |
| Bottlenecks Missed | Self-analysis                                               |
| Tradeoffs Missed   | Self-analysis                                               |
| Feedback Notes     | From mock                                                   |
| Confidence         | Red / Yellow / Green                                        |
| Status             | Not Started / In Progress / Done                            |

---

# 🟢 SHEET 3 — PATTERN MASTER SHEET

Group by pattern:

| Pattern | Sub-pattern | Core Focus | Questions Completed | Avg Confidence |
| ------- | ----------- | ---------- | ------------------- | -------------- |

Patterns to include:

* CRUD / Storage
* Feed / Timeline
* Real-time Communication
* Streaming / Media
* Geospatial
* Logging / Analytics
* Critical Consistency
* Infrastructure Utilities
* Recommendation Systems

---

# 🟢 SHEET 4 — MOCK INTERVIEW LOG

| Date | Question | Time Taken | Estimation Done? | API Defined? | Tradeoffs Explained? | Communication Rating (1–5) | Overall Rating |

---

# 📌 HOW YOU SHOULD USE THIS (Your Style)

Because you're structured (like DP tracking):

* Attempt 3 questions per pattern
* Mark bottlenecks
* Revisit weak ones after 10 days
* Improve articulation

---

# 🔥 PART 2 — PRIORITY-ORDERED TOP 30 FAANG HLD QUESTIONS

Ordered by:

* Frequency in Google/Meta/Amazon loops
* Coverage of core distributed patterns
* Interview depth value

---

# 🥇 TIER 1 — ABSOLUTE MUST (Top 10)

| Rank | Question                        | Pattern              | System Type        | Why High Priority         |
| ---- | ------------------------------- | -------------------- | ------------------ | ------------------------- |
| 1    | Design Twitter                  | Feed                 | Read-heavy         | Fan-out tradeoffs         |
| 2    | Design WhatsApp                 | Real-time            | Low latency        | WebSockets + ordering     |
| 3    | Design URL Shortener            | CRUD                 | Write-heavy        | Hashing + scaling         |
| 4    | Design Instagram Feed           | Feed                 | Read-heavy         | Hybrid push/pull          |
| 5    | Design Payment Gateway          | Critical Consistency | Strong ACID        | Idempotency               |
| 6    | Design Uber                     | Geospatial           | Write-heavy        | Matching algorithm        |
| 7    | Design Ticket Booking System    | Locking              | Strong consistency | Double booking prevention |
| 8    | Design Distributed Rate Limiter | Infrastructure       | Write-heavy        | Token bucket              |
| 9    | Design Distributed Cache        | Infra                | Read-heavy         | Consistent hashing        |
| 10   | Design YouTube                  | Streaming            | Read-heavy         | CDN + transcoding         |

---

# 🥈 TIER 2 — VERY HIGH VALUE (11–20)

| Rank | Question                   | Pattern    | System Type   | Focus              |
| ---- | -------------------------- | ---------- | ------------- | ------------------ |
| 11   | Design Facebook News Feed  | Feed       | Read-heavy    | Ranking            |
| 12   | Design Google Drive        | Storage    | Storage-heavy | Chunking           |
| 13   | Design Dropbox             | Storage    | Storage-heavy | Sync + dedupe      |
| 14   | Design Slack               | Real-time  | Low latency   | Group chat         |
| 15   | Design Netflix             | Streaming  | Read-heavy    | Adaptive streaming |
| 16   | Design Google Maps         | Geospatial | Read-heavy    | Proximity search   |
| 17   | Design E-commerce (Amazon) | CRUD       | Balanced      | Cart + sharding    |
| 18   | Design Distributed Logging | Analytics  | Write-heavy   | Kafka pipeline     |
| 19   | Design Metrics System      | Analytics  | Write-heavy   | Time-series        |
| 20   | Design Search Autocomplete | Search     | Read-heavy    | Prefix index       |

---

# 🥉 TIER 3 — ADVANCED DEPTH (21–30)

| Rank | Question                     | Pattern        | System Type           | Focus            |
| ---- | ---------------------------- | -------------- | --------------------- | ---------------- |
| 21   | Design Google Docs           | Collaboration  | Consistency-sensitive | OT / CRDT        |
| 22   | Design Web Crawler           | Infrastructure | Write-heavy           | URL frontier     |
| 23   | Design Spotify               | Streaming      | Read-heavy            | DRM              |
| 24   | Design LinkedIn Connections  | Graph          | Write-heavy           | Graph traversal  |
| 25   | Design Live Scoreboard       | Real-time      | Low latency           | Pub/Sub          |
| 26   | Design Hotel Booking         | Locking        | Strong ACID           | Distributed lock |
| 27   | Design Kafka-like System     | Infra          | High throughput       | Log partitioning |
| 28   | Design Recommendation System | ML pipeline    | Read-heavy            | Feature store    |
| 29   | Design CDN                   | Infra          | Read-heavy            | Edge caching     |
| 30   | Design Unique ID Generator   | Infra          | Write-heavy           | Snowflake        |

---

# 🎯 STRATEGIC EXECUTION PLAN (For You)

Since you're preparing seriously:

### Month 1

* Tier 1 fully
* 3 mock interviews

### Month 2

* Tier 2 fully
* 5 timed mock sessions

### Month 3

* Tier 3 + advanced
* Multi-region variations

---

# 🧠 HOW TO PRACTICE (Optimally)

For each question:

1. 5 mins — Requirements
2. 5 mins — Estimation
3. 10 mins — Architecture
4. 15 mins — Deep dive
5. 5 mins — Bottlenecks

Total: 40–45 minutes

Time yourself.

---

# 🚀 If You Want Next:

I can:

* Generate a **download-ready Excel structure (column exact copy format)**
* Run a **live mock interview simulation (Google-style)**
* Give you **Tier 1 deep-dive architecture templates**
* Create a **30-day structured HLD plan with daily targets**

Tell me which one you want.
