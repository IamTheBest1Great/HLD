# HLD Architecture & Frameworks — Consolidated HLD Notes

> How to build any system: the mental model, the 6-step interview framework, the universal component template, decision trees for every major choice point, seven complete system archetypes, the concept-to-system map, common anti-patterns, an interview execution checklist, clarifying-questions template, and the master map of all 70 classic HLD interview problems.

---

## Table of Contents

- [1. The Mental Model Before You Draw Anything](#1-the-mental-model-before-you-draw-anything)
  - [1.1 The Four Questions That Drive Every Architecture](#11-the-four-questions-that-drive-every-architecture)
  - [1.2 The System Design Thought Process (How to Think, Not Just What to Draw)](#12-the-system-design-thought-process-how-to-think-not-just-what-to-draw)
- [2. The 6-Step Interview Framework](#2-the-6-step-interview-framework)
- [3. The Universal Architecture Template](#3-the-universal-architecture-template)
  - [3.1 The Complete Generic Template](#31-the-complete-generic-template)
  - [3.2 Component Placement Rules — The Complete Reference](#32-component-placement-rules---the-complete-reference)
- [4. Decision Trees for Every Choice Point](#4-decision-trees-for-every-choice-point)
  - [4.1 Decision 1: Which Database?](#41-decision-1-which-database)
  - [4.2 Decision 2: Which Cache Strategy?](#42-decision-2-which-cache-strategy)
  - [4.3 Decision 3: Which Message Queue?](#43-decision-3-which-message-queue)
  - [4.4 Decision 4: Which Protocol?](#44-decision-4-which-protocol)
  - [4.5 Decision 5: Which Scaling Strategy?](#45-decision-5-which-scaling-strategy)
- [5. Complete System Archetypes](#5-complete-system-archetypes)
  - [5.1 Archetype 1: Read-Heavy Systems](#51-archetype-1-read-heavy-systems)
  - [5.2 Archetype 2: Write-Heavy Systems](#52-archetype-2-write-heavy-systems)
  - [5.3 Archetype 3: Real-time Systems](#53-archetype-3-real-time-systems)
  - [5.4 Archetype 4: ACID / Fintech Systems](#54-archetype-4-acid--fintech-systems)
  - [5.5 Archetype 5: Media / Storage Systems](#55-archetype-5-media--storage-systems)
  - [5.6 Archetype 6: Search Systems](#56-archetype-6-search-systems)
  - [5.7 Archetype 7: Geo / Location Systems](#57-archetype-7-geo--location-systems)
- [6. How All Concepts Connect in One View](#6-how-all-concepts-connect-in-one-view)
  - [6.1 The Concept-to-System Mapping](#61-the-concept-to-system-mapping)
  - [6.2 The Component Decision Summary](#62-the-component-decision-summary)
- [7. Anti-Patterns to Avoid](#7-anti-patterns-to-avoid)
  - [7.1 The 10 Most Common HLD Mistakes](#71-the-10-most-common-hld-mistakes)
- [8. Interview Execution Checklist](#8-interview-execution-checklist)
  - [8.1 Complete Pre-Drawing Checklist](#81-complete-pre-drawing-checklist)
  - [8.2 While Drawing](#82-while-drawing)
  - [8.3 Deep Dive Checklist](#83-deep-dive-checklist)
  - [8.4 Closing Checklist](#84-closing-checklist)
  - [8.5 The Trade-off Sentence Template](#85-the-trade-off-sentence-template)
- [9. The Mental Walkthrough for Any Problem](#9-the-mental-walkthrough-for-any-problem)
- [10. Universal Clarifying-Questions Checklist](#10-universal-clarifying-questions-checklist)
  - [10.1 Requirements Gathering](#101-requirements-gathering)
  - [10.2 Traffic Estimation and Capacity Planning](#102-traffic-estimation-and-capacity-planning)
  - [10.3 API Design](#103-api-design)
  - [10.4 Database Design](#104-database-design)
  - [10.5 High‑Level Architecture](#105-highlevel-architecture)
  - [10.6 Detailed Component Design](#106-detailed-component-design)
  - [10.7 Trade‑offs and Discussion](#107-tradeoffs-and-discussion)
  - [10.8 Failure Scenarios and Mitigations](#108-failure-scenarios-and-mitigations)
  - [10.9 Scaling to the Next Level (if time permits)](#109-scaling-to-the-next-level-if-time-permits)
- [11. The 5-Step Rapid Interview Framework (Quick Reference)](#11-the-5-step-rapid-interview-framework-quick-reference)
- [12. The 70-Problem HLD Master Map](#12-the-70-problem-hld-master-map)

- [13. The Generic Seven-Layer Architecture Template (Layer-by-Layer)](#13-the-generic-seven-layer-architecture-template-layer-by-layer)
  - [13.1 USER & CLIENT EDGE](#131-user--client-edge)
  - [13.2 ENTRY POINT & LOAD BALANCING](#132-entry-point--load-balancing)
  - [13.3 APPLICATION LOGIC LAYER](#133-application-logic-layer)
  - [13.4 CACHING LAYER](#134-caching-layer)
  - [13.5 PERSISTENCE LAYER](#135-persistence-layer)
  - [13.6 ASYNCHRONOUS PROCESSING LAYER](#136-asynchronous-processing-layer)
  - [13.7 ANALYTICS & MONITORING LAYER](#137-analytics--monitoring-layer)
- [14. Worked Reference Numbers: A Feed System at 500M DAU](#14-worked-reference-numbers-a-feed-system-at-500m-dau)
---

## 1. The Mental Model Before You Draw Anything

### 1.1 The Four Questions That Drive Every Architecture



Before touching a component, answer these four questions. Your answers determine 80% of the architecture.

```
┌─────────────────────────────────────────────────────────────────────┐
│            THE FOUR ARCHITECTURE DRIVER QUESTIONS                    │
├─────────────────────────┬───────────────────────────────────────────┤
│  QUESTION               │  WHAT IT DRIVES                           │
├─────────────────────────┼───────────────────────────────────────────┤
│ 1. Read/Write ratio?    │ Read-heavy → cache aggressively           │
│                         │ Write-heavy → async queues, sharding      │
├─────────────────────────┼───────────────────────────────────────────┤
│ 2. Consistency needed?  │ Strong (money/booking) → SQL + ACID       │
│                         │ Eventual (feeds/social) → NoSQL + AP      │
├─────────────────────────┼───────────────────────────────────────────┤
│ 3. Real-time needed?    │ Yes → WebSockets, fan-out, presence       │
│                         │ No  → REST APIs, polling is fine          │
├─────────────────────────┼───────────────────────────────────────────┤
│ 4. Scale?               │ Estimation tells you: sharding needed?    │
│                         │ CDN needed? Multi-region? Or just a DB?   │
└─────────────────────────┴───────────────────────────────────────────┘
```

> **The Golden Rule:** Every component you place must be justified by a specific answer to one of these questions. Never place Kafka because it "sounds good." Place it because "the fan-out to 50M followers must be async, and multiple downstream services (notification, search, analytics) all need to consume the same event."

---

### 1.2 The System Design Thought Process (How to Think, Not Just What to Draw)



```
STEP 1: Read the problem
         │
         ▼
STEP 2: Identify the ARCHETYPE
         │
         ├── Feed/Social    → Fan-out + Cache + NoSQL
         ├── Messaging      → WebSockets + Queue + Ordering
         ├── Financial      → ACID + Idempotency + SAGA
         ├── Media/Storage  → CDN + Object Storage + Transcoding
         ├── Search         → Inverted Index + Trie + Elasticsearch
         ├── Geo/Location   → Geohash + Redis + Matching
         └── Infrastructure → Consistent Hashing + HA patterns
         │
         ▼
STEP 3: Apply the generic template
         │
         ▼
STEP 4: Customize per archetype
         │
         ▼
STEP 5: Deep dive the hardest component
         │
         ▼
STEP 6: Articulate every tradeoff
```

---

## 2. The 6-Step Interview Framework



This is the structure for every 45-minute HLD interview. Internalize it until it's automatic.

```
TOTAL TIME: 45 minutes
─────────────────────────────────────────────────────────────────────
Step 1: Clarify Requirements        5–7 min
Step 2: Estimate Scale              3–5 min
Step 3: API Design                  3–5 min
Step 4: High-Level Architecture    8–10 min
Step 5: Deep Dive                  12–15 min
Step 6: Bottlenecks & Trade-offs    5 min
─────────────────────────────────────────────────────────────────────
```

### Step 1 — Clarify Requirements (5–7 min)

Never start drawing. Ask these questions first and write the answers visibly.

```
FUNCTIONAL REQUIREMENTS (what the system does):
  "What are the 3 core features we must support?"
  "What is explicitly out of scope?"
  "Is this B2C or B2B? Web, mobile, or both?"

NON-FUNCTIONAL REQUIREMENTS (how well it does it):
  "How many Daily Active Users (DAU)?"
  "What is the read-to-write ratio?"
  "What is the acceptable latency? (e.g., feed load < 500ms)"
  "Do we need strong or eventual consistency?"
  "What is the availability SLA? (99.9%? 99.99%?)"
  "Global or single-region users?"
  "How long do we retain data?"
```

**Why this matters:** Without scope, you might design a global multi-region system for a 10K-user internal tool. Requirements calibrate everything.

---

### Step 2 — Estimate Scale (3–5 min)

Estimation tells you what your architecture actually needs. Talk through every number.

```
FORMULA CHAIN:
  DAU
   │
   ├── × actions per user per day
   │         = total actions per day
   │
   ├── ÷ 86,400 seconds
   │         = average QPS
   │
   └── × 3 (peak multiplier)
             = peak QPS

STORAGE:
  Records per day × record size in bytes × retention days = total storage

BANDWIDTH:
  Peak QPS × average response size = outbound bandwidth/second

CACHE SIZE:
  20% of daily hot data × record size = Redis memory needed
```

**Worked Example — Twitter:**
```
DAU: 200M users
Writes: 200M × 0.5 tweets/day = 100M tweets/day
Write QPS: 100M ÷ 86,400 ≈ 1,160/s   →  Peak ≈ 3,500/s

Reads: 200M × 5 feed loads/day = 1B reads/day
Read QPS: 1B ÷ 86,400 ≈ 11,600/s   →  Peak ≈ 35,000/s

Storage (text): 100M tweets × 500 bytes = 50 GB/day → 18 TB/year
Storage (media): 50M media × 500 KB = 25 TB/day → 9 PB/year

CONCLUSION:
  Reads are 10× writes → cache aggressively
  9 PB media → S3 + CDN non-negotiable
  35K read QPS → read replicas + Redis required
  3.5K write QPS → single primary DB handles this fine
```

---

### Step 3 — API Design (3–5 min)

Define the contract. This reveals your data model before you draw a box.

```
TEMPLATE FOR EACH ENDPOINT:
  METHOD /resource/{id}
  Auth: Bearer JWT / API Key / None
  Request: { field: type, field: type }
  Response 2xx: { field: type }
  Response 4xx: { error: { code, message } }
  Pagination: cursor-based or offset

EXAMPLE — Twitter:
  POST /tweets
  Auth: Bearer JWT
  Body: { content: string, media_ids?: string[] }
  Response 201: { tweet_id, content, author, created_at, ... }

  GET /timeline
  Auth: Bearer JWT
  Query: ?limit=20&cursor=<opaque_string>
  Response 200: { tweets: [...], next_cursor, has_more }

  POST /tweets/{tweet_id}/likes
  Auth: Bearer JWT
  Response 200: { like_count: number }
  Response 409: { error: { code: "ALREADY_LIKED" } }
```

**Pagination Decision:**
```
Infinite scroll / feed / real-time data → Cursor-based pagination
  (stable, O(log N), no duplicate issues on new inserts)

Admin panel / search results / "page X of Y" → Offset pagination
  (allows jumping to page, shows total count)
```

---

### Step 4 — High-Level Architecture (8–10 min)

Start with the generic template. Fill in specifics. Draw the happy path.

```
EVERY SYSTEM FOLLOWS THIS SKELETON:

Client (Web/Mobile/API)
        │
        ▼
    DNS / GeoDNS                    ← routes to nearest DC
        │
        ├──────────────────────────► CDN (static assets, media)
        │                              only for read-heavy / media systems
        ▼
    Load Balancer (L7)              ← SSL termination, health checks, routing
        │
        ▼
    [API Gateway]                   ← add if microservices: auth, rate limit, routing
        │
        ▼
    App Servers (×N, stateless)     ← horizontally scaled, auto-scaling group
        │
        ├─────────────────────────► Cache (Redis)      ← READ path: check first
        │                            check before DB
        │
        ├─────────────────────────► Message Queue      ← ASYNC path: side effects
        │                            (Kafka/SQS)
        │
        └─────────────────────────► Primary Database   ← WRITE path: source of truth
                                     (SQL or NoSQL)
                                          │
                                     Read Replicas (×2-4)  ← READ path: cache misses
                                          │
                                     Object Storage (S3)   ← MEDIA path: files/blobs

Message Queue
        │
        ▼
    Worker Services                 ← async consumers: notifications, indexing,
    (×N, auto-scaled)                 fan-out, transcoding, etc.
```

---

### Step 5 — Deep Dive (12–15 min)

The interviewer guides this. Common areas:

```
1. Database schema + indexes
2. Sharding strategy + shard key choice
3. Caching strategy + invalidation
4. Fan-out mechanism (push vs pull vs hybrid)
5. Real-time delivery (WebSocket routing)
6. Distributed transaction handling (SAGA, 2PC)
7. Search/indexing pipeline
8. Rate limiting implementation
```

---

### Step 6 — Bottlenecks & Trade-offs (5 min)

Always proactively identify these:

```
QUESTIONS TO ANSWER:
  "Where is the single point of failure in this design?"
  "What breaks first when traffic spikes 10×?"
  "What did we trade away? (consistency, latency, cost)"
  "What would you change with 3 more months of engineering time?"

COMMON ANSWERS:
  SPOF:       The primary DB → fix with leader election + auto-failover
  10× spike:  Cache layer → pre-warm, increase capacity; Queue → add consumers
  Traded:     "We chose eventual consistency for the feed to get write throughput.
               Payments still use strong consistency."
```

---

## 3. The Universal Architecture Template

### 3.1 The Complete Generic Template



```
╔══════════════════════════════════════════════════════════════════════╗
║                        TIER 0: CLIENTS                               ║
║   Web Browser    Mobile App    3rd-Party API    Internal Services    ║
╚══════════════════════════════════════════════════════════════════════╝
                              │
                    ┌─────────▼──────────┐
                    │   DNS / GeoDNS     │  → routes user to nearest DC
                    └────────┬──────┬───┘
                             │      │
                    ┌────────▼──┐  ┌▼───────────────────┐
                    │    CDN    │  │   Load Balancer (L7) │
                    │ (static   │  │   SSL termination    │
                    │  assets,  │  │   health checks      │
                    │  media)   │  │   path-based routing │
                    └───────────┘  └──────────┬───────────┘
                                              │
                                  ┌───────────▼──────────┐
                                  │     API Gateway       │  (optional)
                                  │  auth · rate limit    │
                                  │  routing · logging    │
                                  └──────────┬────────────┘
                                             │
                    ┌────────────────────────┼───────────────────────────┐
                    │                        │                           │
           ┌────────▼───────┐     ┌──────────▼──────┐     ┌────────────▼────┐
           │  App Server 1  │     │  App Server 2   │     │  App Server N   │
           │  (stateless)   │     │  (stateless)    │     │  (stateless)    │
           └────────┬───────┘     └──────────┬──────┘     └────────────┬────┘
                    │                        │                           │
                    └────────────────────────┼───────────────────────────┘
                                             │
              ┌──────────────────────────────┼────────────────────────────┐
              │                              │                            │
    ┌─────────▼─────────┐       ┌────────────▼──────────┐    ┌──────────▼──────┐
    │   Cache Layer     │       │   Message Queue        │    │  Object Storage  │
    │   Redis Cluster   │       │   Kafka / SQS          │    │  S3 / Blob       │
    │                   │       │                        │    │                  │
    │  • Sessions       │       │  • Fan-out events      │    │  • Images        │
    │  • Hot data       │       │  • Async side effects  │    │  • Videos        │
    │  • Rate counters  │       │  • Background jobs     │    │  • Files         │
    │  • Leaderboards   │       │  • Decoupling          │    │  • Backups       │
    └───────────────────┘       └────────────┬───────────┘    └──────────────────┘
                                             │
                              ┌──────────────▼──────────────┐
                              │       Worker Services        │
                              │    (async consumers, ×N)     │
                              │  notification · indexing     │
                              │  fan-out · transcoding       │
                              └──────────────────────────────┘
    ┌──────────────────────────────────────────────────────────────────────┐
    │                        DATA LAYER                                    │
    │                                                                      │
    │   Primary DB (writes)        Read Replicas (×2-4, reads)            │
    │   ┌──────────────────┐       ┌──────────────────────────────┐       │
    │   │  PostgreSQL /    │──────►│  Replica 1  Replica 2  ...   │       │
    │   │  Cassandra /     │ repl  │  (cache misses route here)   │       │
    │   │  MongoDB         │       └──────────────────────────────┘       │
    │   └──────────────────┘                                               │
    │                                                                      │
    │   [Elasticsearch]            [Graph DB]          [Time-series DB]   │
    │   full-text search           relationships        metrics/IoT        │
    └──────────────────────────────────────────────────────────────────────┘

═══════════════════════════════════════════════════════════════════════════
REAL-TIME LAYER (add only when bidirectional real-time is required)
═══════════════════════════════════════════════════════════════════════════
    Client ←──── WebSocket ────► WS Gateway ←──── Redis Pub/Sub ←──── Workers
                                      │
                             Connection Registry
                               (Redis: user→server)
```

---

### 3.2 Component Placement Rules — The Complete Reference



### What Goes Where and Why

```
╔══════════════════════════════════════════════════════════════════════╗
║  COMPONENT          WHERE IT GOES         WHY / RULE                ║
╠══════════════════════════════════════════════════════════════════════╣
║  CDN                Before LB             Static assets ONLY.        ║
║                                           Never for dynamic,         ║
║                                           user-specific data.        ║
╠══════════════════════════════════════════════════════════════════════╣
║  Load Balancer      Between DNS and       First dynamic component.   ║
║                     App Servers           Handles SSL, health checks.║
║                                           L7 for path routing.       ║
╠══════════════════════════════════════════════════════════════════════╣
║  API Gateway        Between LB and        Add only for microservices.║
║                     Services              Handles: auth, rate limit, ║
║                                           routing, aggregation.       ║
╠══════════════════════════════════════════════════════════════════════╣
║  Redis Cache        Between App and       App checks Redis BEFORE DB.║
║                     Database              Reduces DB load by 20×.    ║
║                                           Also: sessions, counters.  ║
╠══════════════════════════════════════════════════════════════════════╣
║  Kafka/Queue        Between App and       For ALL async operations.  ║
║                     Workers               User doesn't wait for it.  ║
║                                           Multiple consumers needed. ║
╠══════════════════════════════════════════════════════════════════════╣
║  Primary DB         End of write path     Source of truth. ALL       ║
║                                           writes go here ONLY.       ║
╠══════════════════════════════════════════════════════════════════════╣
║  Read Replicas      After Primary DB      For reads that miss cache. ║
║                                           Add when primary read      ║
║                                           QPS is too high.           ║
╠══════════════════════════════════════════════════════════════════════╣
║  Object Storage     Separate from DB      ALL binary files.          ║
║  (S3)               tier, behind CDN      Never store blobs in DB.   ║
║                                           Client uploads directly.   ║
╠══════════════════════════════════════════════════════════════════════╣
║  WS Gateway         Separate from HTTP    ONLY for bidirectional     ║
║                     servers               real-time. Has connection  ║
║                                           registry in Redis.         ║
╠══════════════════════════════════════════════════════════════════════╣
║  Elasticsearch      Separate read         ONLY for full-text search. ║
║                     index, synced via     Synced from primary DB via ║
║                     CDC/Kafka             CDC (Debezium + Kafka).    ║
╚══════════════════════════════════════════════════════════════════════╝
```

### Connection Rules (Never Violate These)

```
✅ ALWAYS:
  Client → LB → App Servers         (never client directly to app)
  App → Cache first, then DB         (cache-aside: check before querying)
  Write → DB → Queue → Workers       (DB is source of truth, queue after)
  Media uploads → App gets pre-signed URL → Client → S3 directly
  Object Storage → CDN → Client      (never app server proxies media)

❌ NEVER:
  Worker → Client directly            (workers update DB/cache; client polls
                                       or receives via WS gateway)
  App Server → S3 for streaming media (bandwidth killer, use CDN)
  Two microservices → same DB         (each service owns its own DB)
  Client → DB directly                (always through app layer)
  Sessions stored on App Server       (makes app stateful, breaks scaling)
```

---

## 4. Decision Trees for Every Choice Point

### 4.1 Decision 1: Which Database?



```
QUESTION: What is the nature of your data and access pattern?
                              │
              ┌───────────────▼───────────────┐
              │  Do you need ACID transactions? │
              │  (money, inventory, bookings)   │
              └───────────┬───────────┬────────┘
                         YES          NO
                          │           │
              ┌───────────▼──┐   ┌────▼─────────────────────────────────┐
              │  SQL Database │   │  What is your access pattern?        │
              │  PostgreSQL   │   └──┬─────────┬──────────┬─────────────┘
              │  MySQL        │      │         │          │              │
              └───────────────┘   key-value  massive   flexible     complex
                                     │       writes    schema(JSON) relations
                                     │         │          │              │
                                 ┌───▼──┐  ┌───▼───┐  ┌──▼─────┐  ┌────▼────┐
                                 │Redis │  │Cassan-│  │MongoDB │  │ Neo4j   │
                                 │Dynamo│  │dra    │  │Firestore│  │Neptune  │
                                 │DB    │  │HBase  │  │         │  │         │
                                 └──────┘  └───────┘  └────────┘  └─────────┘

USE EACH FOR:
─────────────────────────────────────────────────────────────────────────
PostgreSQL / MySQL    → Payments, orders, user accounts, inventory
                        Anything with complex JOINs or ACID needed

Redis / DynamoDB      → Sessions, rate limit counters, leaderboards
                        Shopping cart, feature flags, distributed locks

Cassandra / HBase     → Message history, activity feeds, time-series
                        IoT sensor data, analytics events, write-heavy logs

MongoDB / Firestore   → Product catalogs, user profiles (variable fields)
                        CMS content, config storage, flexible schemas

Neo4j / Neptune       → Social graph (friend-of-friend), fraud detection
                        Knowledge graphs, recommendation engines

Elasticsearch         → Full-text search, log analysis, faceted filters
                        Autocomplete (secondary index, synced from primary)
```

**The Polyglot Rule:** Most systems use 3–5 databases. Each for what it does best.

```
Example — Instagram:
  PostgreSQL   → user accounts, auth           (ACID needed)
  Cassandra    → photo metadata, feed events   (massive write scale)
  Redis        → sessions, cache, rate limits  (fast key-value)
  S3           → actual image/video files      (blob storage)
  Elasticsearch→ hashtag + user search         (full-text)
```

---

### 4.2 Decision 2: Which Cache Strategy?



```
CHOOSE BASED ON: How often does data change? Can you tolerate stale reads?
─────────────────────────────────────────────────────────────────────────

CACHE-ASIDE (Lazy Loading)           → Most common. Use for most systems.
  Read:  App checks cache → miss → App reads DB → App writes to cache
  Write: App writes to DB → invalidates cache key
  WHEN:  Data changes infrequently. User profiles, tweet content, product pages.
  RISK:  Cache miss penalty (3 hops: cache + DB + cache write).
         Stale data if invalidation missed.
  TTL:   5–60 minutes typical.

WRITE-THROUGH                        → Use when reads follow writes immediately.
  Write: App writes to cache AND DB simultaneously (both must succeed)
  Read:  Always cache hit (data always there after write)
  WHEN:  User sessions, shopping cart, config that's read right after write.
  RISK:  Write latency higher. Cache pollution (write data that's never read).
  TTL:   Match session lifetime.

WRITE-BACK (Write-Behind)            → Use for high-volume approximate counters.
  Write: App writes to cache ONLY (fast). Background job flushes to DB.
  Read:  Cache always has latest.
  WHEN:  Like counts, view counters, analytics. Approximate values acceptable.
  RISK:  Data loss if cache crashes before flush. Not for financial data.
  TTL:   Flush interval (e.g., every 30 seconds).

WRITE-AROUND                         → Use for write-once, read-later data.
  Write: App writes directly to DB. Cache not updated.
  Read:  First read is cache miss → loads from DB → populates cache.
  WHEN:  Logs, audit trails, media uploads (never re-read soon after write).
  RISK:  First read after write always slow.
```

**Eviction Policy:**
```
LRU (Least Recently Used)  → Default for most cases. Recently accessed = likely needed.
                              Use for: user profiles, tweet content, API responses.

LFU (Least Frequently Used) → When popularity matters more than recency.
                               Use for: trending content, viral posts, celebrity data.

TTL (Time-To-Live)          → When data has a known freshness window.
                               Use for: sessions (1hr), rate limits (1min), prices (5s).
                               Combine with LRU: TTL for freshness, LRU for capacity.
```

---

### 4.3 Decision 3: Which Message Queue?



```
CHOOSE BASED ON: Fan-out needed? Message replay needed? Routing complexity?
─────────────────────────────────────────────────────────────────────────

                Do multiple services need the SAME message?
                              │
                     YES      │      NO
                 ┌────────────▼──┐  ┌───────────────────────────────────┐
                 │    KAFKA      │  │  Do you need complex routing?      │
                 │               │  └──────────┬──────────┬─────────────┘
                 └───────────────┘            YES         NO
                                               │           │
                                         ┌─────▼───┐  ┌───▼──────┐
                                         │RabbitMQ │  │ SQS /    │
                                         │         │  │ Simple Q │
                                         └─────────┘  └──────────┘

KAFKA → Use when:
  ✅ Multiple services need to consume the same event (fan-out)
  ✅ You need to replay events (new service catching up, debugging)
  ✅ High throughput (millions of messages/second)
  ✅ Event sourcing / complete audit log
  ✅ Stream processing (Flink, Spark Streaming consuming from Kafka)
  Example: Tweet posted → Notification Service + Search + Analytics + Fan-out
           all consume from same Kafka topic independently

RABBITMQ → Use when:
  ✅ Complex routing rules (route by message content, type, header)
  ✅ Request-reply RPC pattern
  ✅ Dead-letter queues needed
  ✅ Priority queues
  Example: Email routing (promotional vs transactional vs admin)

SQS / Simple Queue → Use when:
  ✅ Single consumer, simple FIFO processing
  ✅ AWS-native, managed (no ops overhead)
  ✅ Background job queue (resize images, send emails)
  Example: Image transcoding queue, email sending queue

ALWAYS WITH QUEUES:
  ✅ At-least-once delivery + idempotent consumers
  ✅ Dead-letter queue for failed messages
  ✅ Backpressure handling (bounded queue + scale consumers)
```

---

### 4.4 Decision 4: Which Protocol?



```
CHOOSE BASED ON: Bidirectional? Server push? Internal or external?
─────────────────────────────────────────────────────────────────

Is it bidirectional real-time?
  YES → WebSockets
        Use for: Chat, gaming, collaborative editing, live trading dashboard
        Remember: Needs connection registry (Redis: user_id → server_id)

Server only needs to PUSH (not receive from client)?
  → Server-Sent Events (SSE)
    Use for: News feed updates, notification badges, live sports scores
    Simpler than WebSockets. Works over HTTP. Auto-reconnects.
    Limitation: Server → Client only. Client sends via separate REST calls.

Client to Server only (standard request-response)?
  Internal (service-to-service)?
    → gRPC
      Use for: Microservice communication, high-performance internal APIs
      Binary (Protobuf), faster than JSON, supports streaming
      NOT for browsers (needs proxy like Envoy)

  External (browser/mobile to server)?
    → REST over HTTP/2
      Use for: Public APIs, mobile apps, web apps
      Well-understood, cacheable (GET), browser-native

Special cases:
  → UDP: Video/audio calls (Zoom, WhatsApp calls) — old frames are useless
  → Long Polling: Fallback when WebSockets/SSE not available
  → GraphQL: Mobile apps needing flexible queries, multiple clients with different data needs
```

---

### 4.5 Decision 5: Which Scaling Strategy?



```
System is slow. Find the bottleneck FIRST, then apply the right fix.
─────────────────────────────────────────────────────────────────────

BOTTLENECK: App Server CPU
  Step 1: Add more stateless app servers (horizontal scale)
  Step 2: Auto-scaling group with CPU trigger (> 70%)
  Step 3: Move sessions to Redis (if not done)
  Step 4: Add CDN for static assets
  DO NOT: Vertically scale app servers (expensive, limited)

BOTTLENECK: Database Reads
  Step 1: Add Redis cache (cache-aside, target 95% hit rate)
  Step 2: Add 2-4 read replicas (route reads there)
  Step 3: Add composite indexes for slow queries
  Step 4: Denormalize hot query paths
  ONLY AFTER ALL ABOVE FAIL: Shard the database

BOTTLENECK: Database Writes
  Step 1: Async writes via Kafka (don't wait for DB in the request path)
  Step 2: Batch writes (accumulate and insert in bulk)
  Step 3: Switch to write-optimized DB (Cassandra's LSM tree)
  Step 4: Shard by shard key (consistent hashing)
  Shard Key Rules:
    High cardinality (many distinct values, no hotspots)
    Even distribution (hash-based > range-based for uniform load)
    Query locality (queries touch as few shards as possible)
    Example: user_id (good), created_at (bad — all new writes → same shard)

BOTTLENECK: Network Latency
  Step 1: CDN for all static/media content (serve from edge)
  Step 2: GeoDNS to route users to nearest DC
  Step 3: Multi-region deployment (replicate across regions)
  Step 4: HTTP/2 multiplexing, response compression
  Step 5: Pre-signed URLs for direct media access

SCALING ORDER OF OPERATIONS (follow this sequence, never skip):
  Cache (Redis) → Read Replicas → Async Queues → Sharding → Multi-region
  Each step is 10–100× cheaper than the next. Most systems stop at step 2.
```

---

## 5. Complete System Archetypes

### 5.1 Archetype 1: Read-Heavy Systems



**Examples:** Twitter timeline, Instagram feed, YouTube watch, product catalog, URL shortener, HackerNews

### Identify It
```
Read:Write ratio > 5:1
Users consume more than they produce
Latency SLA: reads must be fast (< 200ms)
Consistency: eventual OK (2s delay for new content is fine)
```

### The Architecture

```
CLIENT
   │
   ├──────────────────────────────────────────────► CDN
   │                                              (static assets, media,
   │                                               cached public pages)
   ▼
LOAD BALANCER (L7)
   │
   ▼
APP SERVERS (stateless, ×N, auto-scaled)
   │
   │──────────────────────────────────────────────► REDIS CACHE
   │  Step 1: Check cache                          LRU eviction
   │  Hit → return immediately (~1ms)              TTL = 5-15 min
   │  Miss → continue to DB                        Target: 95% hit rate
   │
   │──────────────────────────────────────────────► READ REPLICAS (×3)
      Step 2: If cache miss, query replica          (not primary!)
      Store result in cache for next request
      Primary DB receives writes only
```

### Key Patterns for Read-Heavy

```
1. CACHE-ASIDE (primary pattern):
   Check Redis → hit: return fast
               → miss: query replica → store in cache → return

2. CDN FOR MEDIA (mandatory at scale):
   Client requests media → CDN edge (5ms)
   CDN miss → fetch from S3 → cache → return (210ms once, 5ms after)

3. PRE-COMPUTATION (for feeds):
   Don't compute timeline at read time
   Fan-out worker pre-builds timelines in Redis when content is written
   Read path: just fetch pre-built list from Redis (O(1))

4. READ REPLICAS (for cache misses):
   Route all reads to replicas
   Primary only receives writes (< 10% of traffic)
   Replication lag: 10-100ms (acceptable for feeds)

DESIGN DECISIONS TO ARTICULATE:
  "Cache hit rate of 95% reduces DB reads by 20×"
  "Read replicas handle remaining 5% of requests"
  "CDN handles all media — S3 never serves media directly"
  "Eventual consistency accepted — feed delay of 1-2s is fine"
```

---

### 5.2 Archetype 2: Write-Heavy Systems



**Examples:** Logging pipeline, IoT sensors, ad-click aggregation, activity tracking, metrics ingestion

### Identify It
```
Write:Read ratio > 5:1
High write throughput required (100K+ writes/sec)
Reads are often aggregated (analytics), not individual lookups
Exact real-time read accuracy less important than write durability
```

### The Architecture

```
CLIENT / SERVICE
   │
   ▼
LOAD BALANCER + RATE LIMITER
   │
   ▼
APP SERVERS (validate, enqueue, return 202 immediately)
   │
   │  Return 202 Accepted to client instantly.
   │  Don't wait for DB write.
   │
   ▼
KAFKA (write buffer + fan-out)
   │  Absorbs traffic spikes
   │  Multiple partitions for parallelism
   │  Multiple consumer groups
   │
   ├─────────────────► Consumer Group A (writes to Cassandra/HBase)
   │                   LSM-tree DB: optimized for high write throughput
   │
   ├─────────────────► Consumer Group B (aggregator)
   │                   Count-min sketch for approximate heavy hitters
   │                   Window-based aggregation (Flink)
   │
   └─────────────────► Consumer Group C (alerting/monitoring)
```

### Key Patterns for Write-Heavy

```
1. ASYNC WRITE (core pattern):
   Client sends data → App acknowledges (202) → Queue handles persistence
   User never waits for DB write. Kafka is the buffer.

2. WRITE-OPTIMIZED DB:
   Cassandra / HBase: LSM Tree (all writes are sequential → fast)
   vs B-Tree (random writes → slow at high volume)
   Partition key = entity (user_id, device_id)
   Clustering key = timestamp (time-sorted within partition)

3. BATCHING:
   Don't write one record at a time. Batch 1000 records → one DB write
   Reduces IOPS by 1000×. Kafka naturally enables batching.

4. APPROXIMATE COUNTING:
   Exact count at 100K writes/sec requires locking → slow
   Count-min sketch: probabilistic, 95%+ accurate, O(1) updates
   Use for: heavy hitters (top-K), approximate totals

DESIGN DECISIONS TO ARTICULATE:
  "We return 202 Accepted immediately — the client doesn't need
   to wait for the write to persist"
  "Kafka absorbs traffic spikes: burst of 1M events queues up,
   consumers process at steady 100K/sec"
  "Cassandra's LSM tree gives us 100K+ writes/sec on a single node
   — far better than PostgreSQL's B-tree for this pattern"
```

---

### 5.3 Archetype 3: Real-time Systems



**Examples:** WhatsApp, Discord, Slack, collaborative docs, live sports scores, multiplayer games

### Identify It
```
Users expect data within 1-2 seconds of it being created
Bidirectional communication (or frequent server push)
Server must push to client (client can't poll fast enough)
Many concurrent persistent connections
```

### The Architecture

```
CLIENT A                         CLIENT B
    │                                │
    │ WebSocket                      │ WebSocket
    ▼                                ▼
WS GATEWAY (L4 load balanced, sticky by user_id)
    │                                │
    ▼                                ▼
WS SERVER 1                    WS SERVER 2
(Client A connected here)      (Client B connected here)
    │                                │
    └───────────────┬────────────────┘
                    │
                    ▼
         CONNECTION REGISTRY (Redis)
         user_id → server_id mapping
         "alice" → ws_server_1
         "bob"   → ws_server_2
                    │
                    ▼
         MESSAGE ROUTER (Kafka or Redis Pub/Sub)
         "Message from Alice to Bob" → 
           Look up "bob" → ws_server_2 →
           Route to WS Server 2 →
           Push to Bob's connection
                    │
                    ▼
         MESSAGE STORE (Cassandra)
         Partition: conversation_id
         Cluster: message_timestamp
         (time-sorted messages per conversation)
```

### Message Delivery Flow

```
Alice sends "Hey Bob!" from her browser:
  1. Alice's WS connection → WS Server 1
  2. WS Server 1 → Kafka: { from: alice, to: bob, content: "Hey Bob!" }
  3. Kafka → Message Store Worker → Cassandra (persist)
  4. Kafka → Message Router Worker:
       Look up Redis: "bob" → ws_server_2
       Send to ws_server_2 via internal gRPC or Redis Pub/Sub
  5. WS Server 2 → push to Bob's WebSocket connection
  6. Bob sees "Hey Bob!" within ~100ms

OFFLINE USER HANDLING:
  Bob is offline → Redis has no entry for bob
  Message Router → Push Notification Service
  → APNs (iOS) or FCM (Android)
  When Bob comes online → WS connects → fetches missed messages from Cassandra
```

### Key Patterns for Real-time

```
1. CONNECTION REGISTRY (required):
   Redis: SET ws:user:{user_id} {server_id} EX 300
   On connect: set. On disconnect: delete. TTL for cleanup.
   Without this, WS servers can't route cross-server messages.

2. PRESENCE (online/offline status):
   Redis: SETEX presence:{user_id} "online" 10
   Client heartbeat every 5s → refreshes TTL
   No heartbeat → key expires → user appears offline
   No explicit "logout" needed — TTL handles it.

3. MESSAGE ORDERING:
   Within a conversation, messages must be in order.
   Kafka: partition by conversation_id → ordering guaranteed within partition
   Each message gets a monotonically increasing sequence_number
   Client displays in sequence order, not arrival order.

4. READ RECEIPTS:
   Bob reads message → Client sends "read" event to server
   Server updates DB: message.read_at = now
   Server notifies Alice via Redis Pub/Sub → WS push

DESIGN DECISIONS TO ARTICULATE:
  "WS servers are stateless — they hold connections but connection state
   (which user is on which server) lives in Redis"
  "Kafka partitioned by conversation_id ensures messages within a
   conversation are always in order"
  "Presence uses Redis TTL: no explicit logout mechanism needed"
```

---

### 5.4 Archetype 4: ACID / Fintech Systems



**Examples:** Payment gateway, digital wallet, ticket booking, flash sale, hotel reservation, stock trading

### Identify It
```
Money or real-world value is involved
Double-spending / overselling must be prevented
Correctness > availability (prefer error over wrong data)
Strong consistency required
Distributed transactions may be needed
```

### The Architecture

```
CLIENT
   │
   ▼
API GATEWAY (auth + rate limit)
   │
   ▼
IDEMPOTENCY CHECK (Redis)
   check: has idempotency_key been seen before?
   YES → return cached result (don't process again)
   NO  → proceed and store result after processing
   │
   ▼
SERVICE / SAGA ORCHESTRATOR
   │
   State machine: PENDING → PROCESSING → SUCCESS / FAILED
   │
   SAGA steps (each with compensating transaction):
   T1: Reserve inventory          C1: Release inventory
   T2: Charge payment             C2: Refund payment
   T3: Create order record        C3: Cancel order
   T4: Send confirmation          C4: Send cancellation
   │
   On any step failure → execute compensating transactions in reverse
   │
   ▼
PRIMARY DB (PostgreSQL, ACID)
   Row-level locking for inventory
   Transactions for multi-row updates
   Optimistic locking (version column) for low-contention
   Pessimistic locking (SELECT FOR UPDATE) for high-contention

   ▼ (after success)
KAFKA (async side effects only — notifications, analytics)
```

### Locking Strategy

```
WHEN TO USE OPTIMISTIC LOCKING:
  Low contention. Conflicts are rare.
  Profile updates, document edits.
  Implementation:
    version column in DB
    UPDATE table SET ..., version = version+1
    WHERE id = ? AND version = {expected_version}
    If 0 rows updated → conflict → retry

WHEN TO USE PESSIMISTIC LOCKING:
  High contention. Conflicts are expected.
  Ticket seats, flash sale inventory, wallet balance.
  Implementation:
    BEGIN;
    SELECT balance FROM accounts WHERE id = 'alice' FOR UPDATE;
    -- Row is locked. Other transactions wait.
    UPDATE accounts SET balance = balance - 100 WHERE id = 'alice';
    COMMIT;

WHEN TO USE DISTRIBUTED LOCK (Redis):
  Cross-service coordination. Preventing duplicate processing.
  SET lock:{resource_id} {server_uuid} NX EX 30
  NX = only set if not exists (atomic)
  EX 30 = expires in 30s (prevents deadlock if server crashes)
  Release: Lua script checks value = our UUID before deleting
```

### Key Patterns for ACID Systems

```
1. IDEMPOTENCY (most important pattern):
   Client generates UUID per operation: idempotency_key = uuid4()
   Server checks Redis before processing:
     HIT → return stored result
     MISS → process → store result → return
   Prevents double-charges on network retries.

2. SAGA for distributed transactions:
   Never use 2PC (blocks resources, coordinator SPOF)
   SAGA: local transactions + compensating transactions
   Orchestration: single orchestrator calls each service, handles failures
   Use for: multi-service operations (payment + inventory + shipping)

3. STATE MACHINE for operations:
   Every operation has explicit states:
   PAYMENT: CREATED → PROCESSING → SUCCEEDED / FAILED / REFUNDED
   Store current state in DB. Transitions are explicit + logged.
   Prevents partial states.

4. DOUBLE-ENTRY BOOKKEEPING:
   Every financial transaction creates two entries:
   Debit Alice account: -$100
   Credit Bob account:  +$100
   Sum of all entries always = 0. Makes reconciliation easy.

DESIGN DECISIONS TO ARTICULATE:
  "Idempotency key prevents double-charging on network retries
   — the most critical correctness guarantee in a payment system"
  "SAGA preferred over 2PC: 2PC blocks resources and has a
   coordinator SPOF. SAGA compensates on failure instead."
  "Pessimistic locking for seat reservation — at peak load, thousands
   of users try the same seat. Optimistic locking would cause
   retry storms. Pessimistic ensures one transaction wins cleanly."
```

---

### 5.5 Archetype 5: Media / Storage Systems



**Examples:** YouTube, Netflix, Dropbox, Instagram, TikTok, S3-like service

### Identify It
```
Large binary files (images, videos, documents)
High storage requirements (TB to PB scale)
Bandwidth is the constraint (not CPU or DB)
Global delivery required
Processing pipelines (transcoding, resizing, thumbnailing)
```

### The Architecture

```
UPLOAD PATH:
Client
   │
   ├──(1) POST /upload → App Server
   │       Returns: { upload_id, presigned_url }
   │
   └──(2) PUT {presigned_url} [file bytes] ──────────────────► S3
           Direct upload, bypasses app server!
           Chunked if > 5MB (multipart upload API)
           Each chunk: 5-10MB, upload in parallel
   │
   └──(3) POST /upload/complete → App Server
           App Server → save metadata to DB
           App Server → publish "media.uploaded" to Kafka

Kafka
   │
   └──(4) Transcoding Worker
           Converts: raw video → 360p, 720p, 1080p, 4K
           Stores each version in S3
           Updates DB: video.status = "ready"
           Publisher: "media.ready" event

DELIVERY PATH:
Client requests: GET /videos/abc123/stream?quality=720p
   │
   App Server: fetch metadata → return CDN URL
   │
   └──────────────────────────────────────────────────────► CDN
           CDN edge (user's city): cache hit → 5ms
           CDN miss: fetch from S3 → cache → 200ms (once)
```

### Key Patterns for Media Systems

```
1. DIRECT UPLOAD TO S3 (non-negotiable):
   NEVER proxy binary through app servers.
   Flow: Client → GET presigned URL → Client → PUT to S3 directly
   App server only handles metadata and URL generation.
   Why: 1000 users uploading 1GB files = 1TB/s through app servers → impossible.

2. CHUNKED UPLOAD (for files > 5MB):
   Client splits file into 5MB chunks.
   Upload chunks in parallel (4 at once = 4× speed).
   S3 Multipart Upload API: initiate → upload parts → complete.
   On failure: only re-upload failed chunks (resume support).
   Progress: track uploaded bytes / total bytes.

3. ASYNC TRANSCODING PIPELINE:
   NEVER transcode synchronously in the request path.
   Flow: Upload → S3 → Kafka event → Worker pool → transcode → S3 → DB update.
   Worker pool auto-scales based on queue depth.
   User sees "Processing..." then "Ready" when done.

4. CDN IN FRONT OF EVERYTHING:
   S3 never serves media directly (high latency, bandwidth cost).
   All media reads go through CDN edge nodes.
   CDN pull model: first request fetches from S3, caches at edge.
   TTL: images 7 days, videos 30 days, thumbnails 1 day.
   Cache invalidation: use versioned URLs (/media/v2/...) on update.

5. TIERED STORAGE:
   Hot (0-30 days, frequently viewed): S3 Standard
   Warm (30-90 days): S3 Infrequent Access (cheaper)
   Cold (90+ days, rarely watched): S3 Glacier (very cheap)
   Auto-transition based on view count + age.

DESIGN DECISIONS TO ARTICULATE:
  "Pre-signed URL for direct upload — app server is never in the
   data path for binary content"
  "Transcoding is async — the API returns a processing status.
   The worker pool scales based on queue depth in Kafka"
  "CDN is not optional — without it, S3 would serve media
   at high latency and cost. CDN makes it 30× faster and 10× cheaper"
```

---

### 5.6 Archetype 6: Search Systems



**Examples:** Google Search, Twitter search, autocomplete, Elasticsearch log search, e-commerce search

### Identify It
```
Users query arbitrary text
Results must be ranked by relevance
Low latency required (< 100ms for autocomplete)
Index must stay fresh (new content searchable within seconds/minutes)
```

### The Architecture

```
INDEXING PIPELINE (async):
Primary DB (PostgreSQL/Cassandra)
   │
   ▼
CDC Tool (Debezium) reads WAL/changelog
   │
   ▼
Kafka topic: "db.changes"
   │
   ▼
Elasticsearch Indexer Worker
   Parses change event
   Calls Elasticsearch bulk API
   Index updated within seconds
   │
   ▼
Elasticsearch Cluster (sharded + replicated)
   Inverted index per shard
   10 shards × 2 replicas = 30 total copies

QUERY PATH:
Client types "dist"
   │
   ▼
AUTOCOMPLETE SERVICE
   Redis sorted set: ZREVRANGE prefix:dist 0 4
   Returns top-5 completions by frequency in ~2ms
   (Rebuilt hourly from search log aggregation)

Client submits query "distributed systems"
   │
   ▼
SEARCH SERVICE
   Parse query → tokenize → normalize
   Query Elasticsearch:
     must: { match: { content: "distributed systems" } }
     filter: { range: { created_at: { gte: "now-7d" } } }
     sort: [ { _score: desc }, { like_count: desc } ]
   Elasticsearch fans query to all shards → merges → returns top-K
   Enrich results with user data from cache
   Return in < 100ms
```

### Key Patterns for Search

```
1. INVERTED INDEX (core data structure):
   Maps term → list of (doc_id, positions, frequency)
   "distributed" → [doc1(pos:0,tf:2), doc3(pos:0,tf:1)]
   Query: AND → intersect lists. OR → union. NOT → difference.
   Phrase query: check if positions are adjacent.

2. TRIE FOR AUTOCOMPLETE:
   Each node = one character in a prefix path.
   Each node stores: top-K completions by frequency.
   Query "app" → navigate a→p→p → return stored top-K.
   No need to traverse subtree. O(L) lookup where L = prefix length.
   Storage: Redis sorted sets. ZADD prefix:{prefix} {score} {term}.
   Rebuild: hourly Spark job aggregates search logs → updates Redis.

3. CDC SYNC (keep ES fresh):
   App writes to primary DB only.
   Debezium reads DB changelog → Kafka → ES indexer worker.
   ES is eventually consistent with DB (seconds behind).
   If ES is down: Kafka persists events → replay when ES recovers.
   Never dual-write (app → DB AND app → ES) — inconsistency risk.

4. RELEVANCE RANKING:
   Base: TF-IDF (term frequency × inverse document frequency)
   Signals layered on top:
     Recency: newer content boosted
     Engagement: like/retweet count
     Personalization: user's interests, past searches
     Quality: spam score, verified account boost

DESIGN DECISIONS TO ARTICULATE:
  "CDC sync via Debezium keeps ES eventually consistent without
   dual writes. The primary DB is always the source of truth."
  "Autocomplete uses Redis sorted sets — sub-5ms for prefix lookup.
   We rebuild hourly from aggregated logs so trending terms
   appear within an hour."
  "Elasticsearch fans queries to all shards in parallel. Each returns
   top-K local results. Coordinator merges and re-ranks for global top-K."
```

---

### 5.7 Archetype 7: Geo / Location Systems



**Examples:** Uber, Lyft, Yelp, Google Maps, DoorDash, AirBnB

### Identify It
```
Queries involve proximity ("find things near me")
Location data is dynamic (drivers updating every few seconds)
Low-latency matching (< 500ms for ride requests)
Large number of concurrent location updates
```

### The Architecture

```
DRIVER LOCATION UPDATES (write-heavy):
Driver app → Location Service → Redis GEO
  GEOADD geo:{city} {lng} {lat} "driver:{id}"
  Every 4 seconds per driver
  1M drivers × 1 update/4s = 250K writes/sec
  Redis cluster (sharded by city) handles this easily

RIDER REQUESTS RIDE:
Client POST /rides { location: {lat, lng} }
   │
   ▼
Matching Service
   1. Compute geohash-6 of rider location: "9q8yy4"
   2. Get 9 cells (center + 8 neighbors): ["9q8yy", "9q8yw", ...]
   3. GEORADIUS geo:{city} {lng} {lat} 5 km ASC COUNT 10
      → returns 10 nearest drivers sorted by distance
   4. Filter: only AVAILABLE drivers (check status in Redis hash)
   5. Offer to nearest driver (WebSocket push)
   6. 15-second timeout → if no accept → offer to next driver
   7. Driver accepts → trip starts → update both clients

STATIC PLACE SEARCH (Yelp-style):
Client GET /places?lat=37.77&lng=-122.4&type=restaurant&radius=2km
   │
   ▼
Places Service
   1. Compute geohash-6: "9q8yy"
   2. Get 9 neighboring cells
   3. Query DB: WHERE geohash6 IN ('9q8yy', '9q8yw', ...) AND type='restaurant'
      → Uses B-tree index on geohash6 column
   4. Compute exact distance with Haversine formula
   5. Filter by exact radius, sort by distance + rating
   6. Cache result: Redis SET places:{geohash6}:{type} {results} EX 600
```

### Key Patterns for Geo Systems

```
1. GEOHASH ENCODING:
   lat/lng → string where nearby points share common prefix.
   "9q8yy" = ~1.2km × 0.6km cell in San Francisco.
   Longer prefix = smaller cell (more precise).
   Store in DB: INDEX on geohash6 column (B-tree prefix search).

2. 9-CELL SEARCH (boundary fix):
   Points near cell edge are in neighboring cell.
   Always search center cell + 8 neighbors.
   Then filter by exact Haversine distance.
   This ensures no nearby points missed.

3. REDIS GEO COMMANDS:
   GEOADD: Add/update location
   GEODIST: Distance between two points
   GEORADIUS: Find all points within radius
   These use geohash internally as a ZSET score.
   Shard: one ZSET per city (avoid one global 1M-driver set).

4. DRIVER STATUS MANAGEMENT:
   "driver:{id}:status" → "available" / "on_trip" / "offline"
   TTL = 60s. Driver goes offline if no heartbeat.
   Only available drivers considered for matching.

DESIGN DECISIONS TO ARTICULATE:
  "Geohash enables prefix-based spatial indexing — O(log N) lookup
   instead of scanning all drivers with Haversine distance"
  "Sharding by city prevents one massive geo index. São Paulo and
   Lagos drivers don't need to be in the same Redis key."
  "The 8-neighbor search is critical — a driver 100m away but
   across a cell boundary would be missed without it"
```

---

## 6. How All Concepts Connect in One View

### 6.1 The Concept-to-System Mapping



```
When you see this system...    You need these concepts...
────────────────────────────────────────────────────────────────────
Twitter / Instagram           Fan-out (push/pull/hybrid)
                              Redis timeline cache
                              Cassandra for write-scale
                              CDN + S3 for media
                              WebSockets for real-time updates
                              Geospatial (trending by region)

WhatsApp / Messenger          WebSockets + connection registry
                              Message ordering (Kafka partitions)
                              Cassandra for message history
                              Presence system (Redis TTL)
                              Push notifications (APNs/FCM)
                              End-to-end encryption

Payment / Stripe              Idempotency keys
                              SAGA pattern
                              ACID transactions (PostgreSQL)
                              State machine
                              Double-entry ledger
                              Rate limiting (fraud prevention)

YouTube / Netflix             Pre-signed URL upload
                              Chunked multipart upload
                              Async transcoding pipeline (Kafka + workers)
                              CDN (mandatory, non-negotiable)
                              Adaptive bitrate streaming
                              Tiered storage (S3 lifecycle)

Uber / Lyft                   Geohash + Redis GEO
                              Real-time WebSocket updates
                              Trip state machine
                              Matching algorithm
                              Surge pricing (demand/supply ratio)

Ticketmaster / Flash Sale     Distributed lock (Redis NX EX)
                              ACID DB (no eventual consistency)
                              Queue overflow (Kafka waitlist)
                              Idempotency
                              Optimistic vs pessimistic locking choice

Autocomplete / Search         Trie + Redis sorted sets
                              Inverted index (Elasticsearch)
                              CDC sync (Debezium + Kafka)
                              TF-IDF ranking
                              Offline rebuild (Spark)
```

---

### 6.2 The Component Decision Summary



```
┌────────────────────────────────────────────────────────────────────────┐
│                    COMPLETE DECISION REFERENCE                          │
├──────────────────┬─────────────────────────────────────────────────────┤
│  SITUATION       │  USE THIS                                           │
├──────────────────┼─────────────────────────────────────────────────────┤
│  High read QPS   │  Redis cache (cache-aside, 95% hit rate target)     │
│  (feeds, pages)  │  + Read replicas for cache misses                   │
├──────────────────┼─────────────────────────────────────────────────────┤
│  High write QPS  │  Kafka queue → async workers                        │
│  (events, logs)  │  + Cassandra (LSM tree, write-optimized)            │
├──────────────────┼─────────────────────────────────────────────────────┤
│  Money / ACID    │  PostgreSQL + ACID                                  │
│  transactions    │  Idempotency keys + SAGA pattern                    │
│                  │  Pessimistic locking for high-contention             │
├──────────────────┼─────────────────────────────────────────────────────┤
│  Real-time       │  WebSockets + connection registry (Redis)           │
│  messaging       │  Kafka for routing + Cassandra for history          │
├──────────────────┼─────────────────────────────────────────────────────┤
│  Media / files   │  S3 + CDN (mandatory). Pre-signed URLs.             │
│  (images/video)  │  Async transcoding. Never proxy through app.        │
├──────────────────┼─────────────────────────────────────────────────────┤
│  Location-based  │  Geohash + Redis GEO. 9-cell search.               │
│  (Uber, Yelp)    │  Shard by city. Real-time via WebSocket.            │
├──────────────────┼─────────────────────────────────────────────────────┤
│  Full-text search│  Elasticsearch + inverted index                     │
│  (Twitter, log)  │  CDC sync via Debezium + Kafka (not dual write)     │
├──────────────────┼─────────────────────────────────────────────────────┤
│  Autocomplete    │  Trie + Redis sorted sets (ZADD/ZREVRANGE)          │
│  (typeahead)     │  Hourly offline rebuild from search logs            │
├──────────────────┼─────────────────────────────────────────────────────┤
│  Social fan-out  │  < 10K followers → push (write to timelines)        │
│  (tweet delivery)│  > 10K followers → pull (query at read time)        │
│                  │  Hybrid for most systems                            │
├──────────────────┼─────────────────────────────────────────────────────┤
│  Rate limiting   │  Redis + Sliding window counter                     │
│                  │  Token bucket for burst-friendly APIs               │
├──────────────────┼─────────────────────────────────────────────────────┤
│  Distributed lock│  Redis SETNX EX + fencing token                    │
│  (seat hold)     │  ZooKeeper for strongest guarantee                  │
├──────────────────┼─────────────────────────────────────────────────────┤
│  Session storage │  Redis (SETEX, TTL = session duration)              │
│                  │  Never on app server (breaks horizontal scale)      │
├──────────────────┼─────────────────────────────────────────────────────┤
│  Presence system │  Redis SETEX with short TTL (10s)                  │
│  (online status) │  Client heartbeat every 5s refreshes TTL           │
├──────────────────┼─────────────────────────────────────────────────────┤
│  Notification    │  Kafka fan-out → in-app (WebSocket) +               │
│  delivery        │  push (APNs/FCM) + email (SendGrid)                │
└──────────────────┴─────────────────────────────────────────────────────┘
```

---

## 7. Anti-Patterns to Avoid

### 7.1 The 10 Most Common HLD Mistakes



```
ANTI-PATTERN 1: Designing before estimating
  ❌ "We'll use sharding" (without knowing QPS)
  ✅ Estimate first: if peak QPS < 10K, a single DB with cache is enough.
     Sharding is only needed when writes saturate a sharded primary.

ANTI-PATTERN 2: Sharding before caching
  ❌ Jump to sharding because "the DB will be large"
  ✅ Order: Cache (Redis) → Read Replicas → Async Queue → Sharding
     A Redis cache with 95% hit rate reduces DB reads by 20×.
     Most systems never need sharding once caching is added.

ANTI-PATTERN 3: Eventual consistency for financial data
  ❌ "We'll use Cassandra with eventual consistency for payments"
  ✅ Anything involving money, tickets, inventory reservations = strong consistency.
     Use ACID. Use locks. Prefer error over wrong data.

ANTI-PATTERN 4: Proxying binary data through app servers
  ❌ App server handles video upload/download stream
  ✅ Pre-signed URLs: Client → S3 directly. App server issues URL only.
     1000 users uploading 1GB videos = impossible if routed through app.

ANTI-PATTERN 5: Stateful app servers
  ❌ Session data stored in app server memory
  ✅ Sessions → Redis. App servers are compute-only, stateless, replaceable.
     If an app server dies, the user just hits another server — no data lost.

ANTI-PATTERN 6: Synchronous fan-out
  ❌ POST /tweet waits for fan-out to 50M followers before returning
  ✅ POST /tweet → save to DB → return 200 → async fan-out via Kafka
     User waits for DB save (fast). Fan-out happens async (seconds).

ANTI-PATTERN 7: One database for everything
  ❌ "We'll use PostgreSQL for everything"
  ✅ Right tool for each job:
     PostgreSQL: ACID operations, complex queries
     Redis: sessions, cache, counters, locks
     Cassandra: message history, feeds, high-write time-series
     Elasticsearch: full-text search
     S3: binary files

ANTI-PATTERN 8: Forgetting idempotency
  ❌ POST /payment called twice on network retry → double charge
  ✅ Every write operation: idempotency key in Redis
     Check before processing. Return cached result if seen.

ANTI-PATTERN 9: Not designing for failure
  ❌ Single DB, single server, no replicas
  ✅ N+1 redundancy for every critical component
     Primary DB + 2 replicas with auto-failover
     Multiple app server instances, multiple AZs
     Circuit breakers for all external calls

ANTI-PATTERN 10: Using WebSockets for everything
  ❌ "I'll use WebSockets for notifications/feed updates"
  ✅ WebSockets for bidirectional real-time (chat, gaming)
     SSE for server-push only (notifications, feed updates) — simpler, HTTP-native
     REST for standard request-response
```

---

## 8. Interview Execution Checklist

### 8.1 Complete Pre-Drawing Checklist



```
BEFORE DRAWING ANYTHING:
□ What are exactly 3 core features? (write on board)
□ What is explicitly out of scope?
□ DAU count
□ Read:Write ratio
□ Latency SLA (e.g., feed load < 200ms)
□ Consistency: strong or eventual?
□ Global or single-region?
□ Estimated peak read QPS
□ Estimated peak write QPS
□ Estimated storage per year
□ Does storage require sharding? (> 10TB → consider)
□ Does read QPS require caching? (> 10K QPS → yes)
□ Does read QPS require replicas? (> 50K QPS → likely)
□ Is real-time needed? (WebSocket vs SSE vs polling)
□ Is media involved? (CDN + S3 pattern)
```

### 8.2 While Drawing



```
□ Start with generic template (don't invent from scratch)
□ Label EVERY component (no unnamed boxes)
□ Show request flow direction with arrows
□ Add CDN only if: static content or media exists
□ Add Redis only if: reads are bottleneck OR sessions needed
□ Add Queue only if: async operations exist (justify what's async)
□ Choose DB type and justify the choice
□ State consistency model explicitly
□ Show both read path and write path
```

### 8.3 Deep Dive Checklist



```
□ DB schema: at least 2-3 core tables with columns
□ Index choices and why (B-tree vs GEO vs inverted)
□ Shard key (if sharding): why this key, why not others
□ Cache invalidation: how cache stays fresh
□ Fan-out mechanism (if feed system): push/pull/hybrid threshold
□ Real-time routing (if RT): connection registry design
□ Transaction handling (if ACID): locking strategy
□ Failure handling: what happens when each component fails
```

### 8.4 Closing Checklist



```
□ "Where is the SPOF?" → name it and fix it
□ "What breaks at 10× traffic?" → name it and scale it
□ "What did we trade away?" → consistency, cost, latency
□ "What would you build next?" → shows depth
□ Confirm you answered the original requirements
```

---

### 8.5 The Trade-off Sentence Template



> The most important thing to say in any HLD interview:

```
"I chose [X] over [Y] here because in this system [SPECIFIC PROPERTY]
matters more than [WHAT WE SACRIFICE]. This is acceptable because
[REASON]. Under [DIFFERENT CONDITION], I would switch to [Y] instead."

EXAMPLES:

"I chose Cassandra over PostgreSQL for the message store because
write throughput matters more than complex query flexibility here.
We never need to JOIN messages — we only query by conversation_id
and time range, which Cassandra handles natively. If we needed
ad-hoc reporting across messages, I'd keep a secondary PostgreSQL
replica for analytics queries."

"I chose eventual consistency for the timeline. Seeing a tweet 2
seconds late is imperceptible to users, but strong consistency would
require synchronous fan-out to 50M followers — that would make tweet
post latency unacceptable. Under a financial context I would never
make this tradeoff."

"I chose Redis Redlock over ZooKeeper for the seat hold lock because
our lock duration is only 10 minutes and we're already running Redis.
The added complexity of ZooKeeper isn't justified unless we need
millisecond-precision guarantees, which seat booking doesn't require."
```

---

## 9. The Mental Walkthrough for Any Problem



When you see a new problem, run through this mental checklist in 30 seconds before speaking:

```
1. IDENTIFY THE ARCHETYPE:
   "This is a feed system" → hybrid fan-out, cache, Cassandra
   "This is messaging" → WebSockets, ordering, history
   "This is payment" → ACID, idempotency, SAGA
   "This is media" → S3, CDN, transcoding pipeline
   "This is search" → inverted index, trie, Elasticsearch
   "This is location" → geohash, Redis GEO, matching

2. IDENTIFY THE HARD PROBLEMS:
   Every system has 1-2 genuinely hard subproblems.
   Find them early. They become your deep-dive.
   Twitter: celebrity fan-out problem
   WhatsApp: message ordering + delivery guarantees
   Uber: real-time matching at scale
   Ticketmaster: preventing overselling under load
   YouTube: video upload + transcoding pipeline

3. IDENTIFY THE CONSISTENCY REQUIREMENT:
   Financial/booking → ACID, strong consistency, locking
   Social/feeds → Eventual consistency, Cassandra, AP
   Mixed → strong for writes (ACID), eventual for reads (cache)

4. ESTIMATE IN YOUR HEAD:
   10M DAU × 10 actions = 100M events/day ≈ 1K QPS
   100M DAU × 10 actions = 1B events/day ≈ 10K QPS
   1B DAU × 10 actions = 10B events/day ≈ 100K QPS
   Quick mental math: QPS ≈ DAU × actions / 100,000

5. PICK YOUR TEMPLATE:
   Under 10K QPS: single DB + Redis cache is probably fine
   10K-100K QPS: Read replicas + aggressive caching
   100K+ QPS: Sharding required, CDN mandatory, multi-region likely
```

---

*Complete Phase 1 Foundation · 18 concept files + this architecture guide*
*Total coverage: ~95% of all FAANG HLD interview questions*

## 10. Universal Clarifying-Questions Checklist

Before drawing any architecture, run through this checklist with your interviewer to nail down scope, scale, and constraints.



### 10.1 Requirements Gathering

### Functional Requirements
- **Core features:** What are the absolutely essential features the system must support?
- **Optional features:** Are there nice‑to‑have features we might consider (e.g., user accounts, analytics, customisation)?
- **User roles:** Are there different types of users (e.g., regular users, admins)? Do they have different permissions?
- **Input/Output:** What data does the system accept? What does it return? Are there any size or format constraints?
- **Actions/Workflows:** What are the primary user workflows (e.g., create, read, update, delete)?
- **Out of scope:** What features should we explicitly **not** design for now?

### Non‑Functional Requirements
- **Scale:** How many users (DAU/MAU) or operations per day/month? What is the expected read/write ratio?
- **Traffic patterns:** Are there peak times? Should we handle traffic spikes (e.g., viral events)?
- **Latency:** What are the acceptable response times for critical operations (e.g., <100ms, <1s)?
- **Availability:** What uptime SLA is expected (e.g., 99.9%, 99.99%)?
- **Consistency:** Do we need strong consistency, or is eventual consistency acceptable? Any specific consistency requirements (e.g., read‑after‑write)?
- **Durability:** How long must data be retained? Should we support data archival or deletion?
- **Security:** What security concerns must we address (authentication, encryption, abuse prevention, compliance)?
- **Geographical distribution:** Does the system need to serve a global audience? Multi‑region required?

### 10.2 Traffic Estimation and Capacity Planning

- **Daily active users (DAU):** Rough estimate? (e.g., 1M, 100M)
- **Actions per user:** How many reads/writes per user per day?
- **Peak factor:** What is the expected peak‑to‑average ratio (e.g., 2×, 5×)?
- **Data size:** Average size of each data item (e.g., record, file, message)?
- **Retention period:** How long do we keep data (e.g., 1 year, 5 years, forever)?
- **Replication factor:** For durability, do we need multiple copies (e.g., 3×)?
- **Growth rate:** How fast is the data expected to grow (e.g., 20% per year)?

### 10.3 API Design

- **API style:** REST, GraphQL, gRPC, or custom? Any preference?
- **Endpoints:** What are the main endpoints and their HTTP methods?
- **Request/response format:** JSON, Protocol Buffers, XML? Any specific fields required?
- **Authentication:** Do we need API keys, OAuth, JWT, or is the API public?
- **Rate limiting:** Should we implement rate limiting per user/IP? What limits?
- **Idempotency:** Should some operations be idempotent? Do we need idempotency keys?
- **Pagination:** For list endpoints, do we need pagination (offset/limit, cursor)?
- **Versioning:** How should we handle API versioning (URL path, header)?

### 10.4 Database Design

- **Data model:** What entities and relationships exist? (e.g., users, posts, orders)
- **Database type:** SQL or NoSQL? Any preference (e.g., PostgreSQL, Cassandra, DynamoDB)?
- **Access patterns:** What are the main queries (e.g., lookup by ID, range scans, joins)?
- **Indexing:** Which fields need indexes? Any composite indexes?
- **Sharding/Partitioning:** Should we design for sharding? What shard key makes sense?
- **Consistency level:** For NoSQL, what read/write consistency levels should we target?
- **Backup and recovery:** Do we need point‑in‑time recovery, replication, or snapshots?

### 10.5 High‑Level Architecture

- **Deployment environment:** Cloud (AWS/GCP/Azure), on‑premises, or hybrid? Any specific services we can leverage?
- **Components:** What major building blocks are needed (load balancers, web servers, caches, databases, queues)?
- **Communication:** Synchronous (HTTP/gRPC) or asynchronous (message queues)? For which parts?
- **Geographical distribution:** Single region or multi‑region? Active‑active or active‑passive?
- **Existing services:** Are there existing components we can reuse (e.g., CDN, identity provider, monitoring)?

### 10.6 Detailed Component Design

### Data Storage and Processing
- **Caching:** Should we use a cache (Redis/Memcached)? What data should be cached? Eviction policy?
- **File/Object storage:** Do we need to store large files (images, videos)? Use S3, blob storage?
- **Search:** Do we need full‑text search (Elasticsearch)? If so, how to index?
- **Stream processing:** Do we need real‑time analytics or aggregation? Use Kafka, Flink?

### Specific Algorithms/Mechanisms
- **ID generation:** How to generate unique IDs (UUID, Snowflake, database sequence)?
- **Concurrency control:** How to handle concurrent writes/updates (optimistic locking, pessimistic locking)?
- **Rate limiting algorithm:** Token bucket, leaky bucket, sliding window? Where to implement (load balancer, API gateway)?
- **Distributed locking:** If needed, how to implement (Redis, ZooKeeper)?

### Analytics and Monitoring
- **Data to collect:** What metrics/logs are important (request count, latency, errors)?
- **Storage for analytics:** Time‑series DB (InfluxDB, Prometheus), data warehouse (BigQuery)?
- **Alerting:** What thresholds should trigger alerts?

### 10.7 Trade‑offs and Discussion

- **Consistency vs. availability:** Where do we stand on the CAP theorem? What trade‑offs are we making?
- **Cost vs. performance:** Are we optimising for lower cost (e.g., fewer replicas, less caching) or higher performance?
- **Simplicity vs. scalability:** Should we start with a simpler design and plan to scale later, or build for extreme scale from day one?
- **Synchronous vs. asynchronous:** Which operations benefit from async processing? What complexity does it add?
- **SQL vs. NoSQL:** Why choose one over the other given the access patterns?
- **Denormalisation vs. normalisation:** When to denormalise for performance?

### 10.8 Failure Scenarios and Mitigations

- **Single points of failure:** Which components could bring down the system? How to eliminate them (redundancy, failover)?
- **Database failure:** What if the primary database goes down? (Replica promotion, multi‑AZ)
- **Cache failure:** Can the system survive a cache outage? Fallback to database?
- **Service overload:** How to handle traffic spikes (auto‑scaling, rate limiting, queueing)?
- **Network partitions:** How does the system behave during a network split? (CAP trade‑off)
- **Data corruption:** How to recover from accidental deletions or corruption? (Backups, point‑in‑time recovery)
- **Regional outage:** If multi‑region, how does failover work? (DNS, global load balancer)
- **DDoS attacks:** What mechanisms protect against DDoS? (Rate limiting, WAF, CDN)

### 10.9 Scaling to the Next Level (if time permits)

- **What if traffic grows 100×?** Which components become bottlenecks first?
- **Database sharding:** Would we need to re‑shard? How to minimise downtime during rebalancing?
- **Multi‑region active‑active:** How to handle cross‑region replication and conflict resolution?
- **Advanced caching:** Would we use CDN for dynamic content, edge computing (CloudFlare Workers)?
- **Microservices vs. monolith:** Would we split into microservices? How would they communicate?


## Tips for Using This Template

- **Adapt to the problem:** Not all questions apply to every system. Pick the most relevant ones.
- **Listen actively:** The interviewer’s answers guide your design. Summarise to confirm understanding.
- **Be concise:** You don’t need to ask everything; focus on clarifying the biggest unknowns.
- **Show your reasoning:** After getting answers, explain how they influence your decisions.

This template will help you navigate any system design interview systematically. Practice by applying it to common problems like designing a chat system, a ride‑hailing service, a video streaming platform, etc.

## 11. The 5-Step Rapid Interview Framework (Quick Reference)

A condensed, five-step checklist to run through out loud in the first two minutes of any HLD interview, before diving into the detailed 6-step / archetype framework above.



| Step | Focus | What To Do |
|------|-------|------------|
| Requirement Clarification | The "What" | Define the scope. Is it a global system? Who are the users? What are the core features vs. "nice-to-haves"? |
| Estimation | The "Scale" | Perform "back-of-the-envelope" math. How many requests per second (RPS)? How much data storage is needed per year? This dictates your database and caching choices. |
| High-Level Diagram | The "Skeleton" | Sketch the main components: Clients, Load Balancers, Web Servers, and Databases. |
| Deep Dive | The "Guts" | Pick 1-2 critical challenges (e.g., "How do we handle a celebrity with 100M followers?") and propose detailed technical solutions. |
| Bottlenecks & Trade-offs | The "Reality Check" | Identify where the system might fail (Single Point of Failure) and discuss how to improve it. |

## 12. The 70-Problem HLD Master Map

Every classic HLD interview question, grouped into 12 sub-categories and mapped to its dominant architectural pillar, primary challenge, difficulty, and interview frequency. Use this as the master index into the pattern tables found throughout files 02–10.



| Sub-Category | Pillar | Primary Challenge | Problem | Diff | Freq |
| --- | --- | --- | --- | --- | --- |
| **1. Traffic Control** 🧱 | **Storage/HA** | Load Balancing | 1. Load Balancer Design | Easy | ⭐ |
|  | **Storage/HA** | Request Routing | 2. API Gateway Service | Easy | ⭐ |
|  | **Write-Heavy** | Request Throttling | 3. Rate Limiter (Distributed) | Med | ⭐ |
|  | **Storage/HA** | Security Filtering | 4. Web App Firewall (WAF) | Med |  |
|  | **Storage/HA** | Traffic Scrubbing | 5. DDOS Protection System | Hard |  |
|  | **Storage/HA** | Secret Management | 6. Secrets Manager (Vault) | Med |  |
| **2. Global Infra** 🔗 | **Storage/HA** | Key Mapping | 7. URL Shortener (TinyURL) | Easy | ⭐ |
|  | **Storage/HA** | Blob Storage | 8. Pastebin (Text Storage) | Easy | ⭐ |
|  | **Storage/HA** | Unique Sequence | 9. Distributed ID (Snowflake) | Med | ⭐ |
|  | **Storage/HA** | Data Partitioning | 10. Consistent Hashing | Med | ⭐ |
|  | **Storage/HA** | Edge Caching | 11. Content Delivery Network | Med |  |
|  | **Storage/HA** | Geo-Routing | 12. Multi-region Router | Hard |  |
| **3. Content Discovery** 📱 | **Read-Heavy** | Feed Generation | 13. News Feed (Push/Pull) | Hard | ⭐ |
|  | **Read-Heavy** | Media Delivery | 14. Instagram (Media Feed) | Med | ⭐ |
|  | **Read-Heavy** | Grid Aggregation | 15. Pinterest (Image Feed) | Med |  |
|  | **Write-Heavy** | Massive Fan-out | 16. Twitter (Write Path) | Hard | ⭐ |
|  | **Read-Heavy** | Vote Aggregation | 17. Reddit (Ranking) | Med |  |
|  | **Read-Heavy** | Time-Decay Math | 18. HackerNews (Ranking) | Med |  |
|  | **Read-Heavy** | Graph Traversal | 19. Friend Suggestions | Med |  |
| **4. Communication** 💬 | **Write-Heavy** | Socket Mgmt | 20. WhatsApp (1-on-1) | Med | ⭐ |
|  | **Write-Heavy** | Msg Ordering | 21. Group Chat System | Hard | ⭐ |
|  | **Read-Heavy** | Heartbeat Mgmt | 22. Presence Platform | Med |  |
|  | **Write-Heavy** | Pub/Sub | 23. Slack (Enterprise) | Hard |  |
|  | **Write-Heavy** | Fan-out | 24. Notification Service | Med | ⭐ |
|  | **Read-Heavy** | Low Latency Video | 25. Zoom Video Conf. | Hard |  |
|  | **Storage/HA** | Mail Retrieval | 26. Email Service (Gmail) | Hard |  |
| **5. Media/Streaming** 🎥 | **Storage/HA** | Large File Upload | 27. YouTube (Storage) | Hard | ⭐ |
|  | **Read-Heavy** | Global Replication | 28. Netflix Distribution | Hard |  |
|  | **Read-Heavy** | Vertical Feed | 29. TikTok (Video Feed) | Hard |  |
|  | **Read-Heavy** | Metadata Sync | 30. Spotify (Streaming) | Med |  |
|  | **Read-Heavy** | Live Buffer Mgmt | 31. Twitch (Live Stream) | Hard |  |
|  | **Write-Heavy** | Compute Pipeline | 32. Video Transcoding | Med |  |
| **6. Geospatial** 🚗 | **Read-Heavy** | Proximity Search | 33. Uber/Lyft (Matching) | Hard | ⭐ |
|  | **Read-Heavy** | Graph Routing | 34. Google Maps (Paths) | Hard | ⭐ |
|  | **Read-Heavy** | Spatial Indexing | 35. Yelp/Nearby | Med | ⭐ |
|  | **Write-Heavy** | Real-time Tracking | 36. Food Delivery Tracker | Med |  |
|  | **Read-Heavy** | Static Map Tiles | 37. Transit Tracker | Med |  |
|  | **ACID/Trans** | Inventory Locking | 38. Hotel Booking | Med |  |
|  | **ACID/Trans** | Double-Booking | 39. AirBnB Reservation | Hard |  |
| **7. Search/Data** 🔍 | **Write-Heavy** | BFS at Scale | 40. Web Crawler | Med | ⭐ |
|  | **Read-Heavy** | Trie Traversal | 41. Autocomplete | Med | ⭐ |
|  | **Read-Heavy** | PageRank/Indexing | 42. Google Search | Hard |  |
|  | **Read-Heavy** | Inverted Index | 43. Elasticsearch (Logs) | Hard |  |
|  | **Read-Heavy** | Dynamic Filters | 44. E-commerce Search | Med |  |
| **8. Fintech** 💸 | **ACID/Trans** | External Sync | 45. Payment Gateway | Hard | ⭐ |
|  | **ACID/Trans** | Double Entry | 46. Digital Wallet | Hard | ⭐ |
|  | **ACID/Trans** | High-Freq Trading | 47. Crypto Exchange | Hard |  |
|  | **Write-Heavy** | Pattern Match | 48. Fraud Detection | Hard |  |
|  | **ACID/Trans** | Order Matching | 49. Stock Brokerage | Hard |  |
|  | **Write-Heavy** | Stream Aggreg. | 50. Ad-Click Aggregator | Hard | ⭐ |
| **9. Concurrency** 🎟️ | **ACID/Trans** | High Contention | 51. Ticketmaster | Hard | ⭐ |
|  | **ACID/Trans** | Inventory Rush | 52. Flash Sale System | Hard | ⭐ |
|  | **ACID/Trans** | Seat Reservation | 53. Flight Reservation | Hard |  |
|  | **Read-Heavy** | Real-time Score | 54. Online Quiz (Kahoot) | Med |  |
|  | **ACID/Trans** | Vote Integrity | 55. Election System | Hard |  |
| **10. Infra/Storage** ⚙️ | **Read-Heavy** | LRU Eviction | 56. Distributed Cache | Med |  |
|  | **Write-Heavy** | Append-only Log | 57. Message Queue (Kafka) | Hard | ⭐ |
|  | **Storage/HA** | Erasure Coding | 58. S3 Object Storage | Hard |  |
|  | **Storage/HA** | Consensus (Paxos) | 59. Key-Value Store | Hard |  |
|  | **Storage/HA** | File Chunking | 60. HDFS (Distributed FS) | Hard |  |
|  | **Write-Heavy** | Time-series Ingest | 61. Logging Pipeline | Med |  |
|  | **Write-Heavy** | Data Collection | 62. Metrics Monitoring | Hard |  |
|  | **Write-Heavy** | Delayed Execution | 63. Task Scheduler | Med | ⭐ |
| **11. Big Data** 📈 | **Write-Heavy** | Counting at Scale | 64. Top K (Heavy Hitters) | Med | ⭐ |
|  | **Read-Heavy** | Bucket Traffic | 65. A/B Testing Platform | Med |  |
|  | **Read-Heavy** | OLAP Queries | 66. Data Warehouse | Hard |  |
| **12. Productivity** ✍️ | **ACID/Trans** | Conflict Resolut. | 67. Google Docs (OT/CRDT) | Hard | ⭐ |
|  | **Write-Heavy** | Event Replay | 68. Whiteboard (Miro) | Hard |  |
|  | **Write-Heavy** | Sandboxed Exec | 69. Code Judge (LeetCode) | Med |  |
|  | **Storage/HA** | Form Schema Mgmt | 70. Google Forms | Easy |  |

---

## 13. The Generic Seven-Layer Architecture Template (Layer-by-Layer)



A complementary way to view the Universal Architecture Template in section 3: instead of components, walk the request path **layer by layer**, from the user's device down to storage, noting exactly what connects to what and along which kind of flow (synchronous, asynchronous, or internal/monitoring).



**How to read a layered diagram:**



| Arrow Style | Flow Type | Meaning |

|---|---|---|

| Solid | Synchronous | Blocking — part of the user's critical request path; determines perceived latency. |

| Dashed | Asynchronous | Non‑critical, decoupled background work; the request returns before this completes. |

| Dotted | Internal / Monitoring | Replication, log collection, metrics ingestion — invisible to the user. |



### 13.1 USER & CLIENT EDGE

- **What components are present?** Mobile App, Web Browser, API Client.
- **Who connects to it?** It is the entry point, resolving DNS queries and serving cached static assets.
- **What/Who does it connect to?**
    - **Upper:** None (it *is* the upper level).
    - **Lower:** It initiates requests and connects to the **DNS & Routing layer** (via DNS queries) and the **CDN** (for cached content). For the primary API request path, it connects directly to the **Load Balancer**.
    - *Arrow: Client → Load Balancer (Synchronous Path)*

### 13.2 ENTRY POINT & LOAD BALANCING

- **What components are present?** Vertically stacked Load Balancer and API Gateway (for redundancy).
- **Who connects to it?** It receives origin requests from the CDN or direct API calls from the Client Edge.
- **What/Who does it connect to?**
    - **Upper:** Connects from the **User & Client Edge** (origin requests).
    - **Lower:** It distributes and routes traffic directly into the **Application Logic Layer**.
    - *Arrow: Entry Point → Application Logic*

### 13.3 APPLICATION LOGIC LAYER

- **What components are present?** Vertically stacked Stateless App Servers or Microservices (containing business rules, redundant), and optional key generation services.
- **Who connects to it?** It processes all incoming business logic requests from the entry point. This is the "brain" of the system.
- **What/Who does it connect to?**
    - **Upper:** It receives the main request stream from the **Entry Point / API Gateway**.
    - **Lower:** It makes synchronous calls to the **Cache Layer** and **Persistence Layer** to fetch and update data.
        - *Arrow: App Servers → Cache (read)*
        - *Arrow: App Servers → Primary Database (read/write)*
    - It also publishes events into the **Asynchronous Processing Layer** for decoupled tasks.
        - *Arrow: App Servers → Message Queue (Async Path)*
    - Finally, it exports logs and metrics to the **Analytics & Monitoring Layer**.
        - *Arrow: App Servers → Analytics*

### 13.4 CACHING LAYER

- **What components are present?** Distributed Cache Cluster (e.g., Redis/Memcached cluster).
- **Who connects to it?** Sits between the application and database to absorb read traffic and reduce latency.
- **What/Who does it connect to?**
    - **Upper:** Connects to the **Application Logic Layer** (receives read/write cache calls).
        - *Arrow: Cache → App Servers (synchronous responses)*
    - **Lower:** If it's a "cache-through" architecture, it could connect to the database, but in most generic designs, the application orchestrates this flow (as shown by the application connecting to both layers).

### 13.5 PERSISTENCE LAYER

- **What components are present?** Vertically stacked Primary Database (Master, for all writes) and Read Replicas.
- **Who connects to it?** The durable store, containing authoritative data for the entire system.
- **What/Who does it connect to?**
    - **Upper:** Receives the read/write request load from the **Application Logic Layer**.
        - *Arrow: DB → App Servers (responses)*
    - **Lower:** It must explicitly manage **Database Replication** to the read replicas.
        - *Arrow: Primary DB → Read Replicas*
    - It also sends data replication logs to the **Analytics & Monitoring Layer**.
        - *Arrow: DB → Analytics*

### 13.6 ASYNCHRONOUS PROCESSING LAYER

- **What components are present?** Message Queue (e.g., Kafka/SQS), leading to vertically stacked Workers or Stream Processors.
- **Who connects to it?** Deconstructs non-critical tasks from the main critical path to maximize throughput and resilience.
- **What/Who does it connect to?**
    - **Upper:** Receives event data and task descriptions published by the **Application Logic Layer**.
        - *Arrow: Message Queue → App Servers (acknowledgments, async flow)*
    - **Lower:** Workers consume tasks from the queue and execute them.
        - *Arrow: Queue → Workers*
    - Upon completion, workers connect to **other systems** (e.g., the Persistence layer to update a status) or publish results, and they send operational data to the **Analytics & Monitoring Layer**.
        - *Arrow: Workers → Analytics*

### 13.7 ANALYTICS & MONITORING LAYER

- **What components are present?** Data Warehouse, Time-Series Databases, Log Ingestion, Reporting services.
- **Who connects to it?** A perpendicular, cross-cutting section that observes the operational health and business metrics of the entire infrastructure. It is a collection point, not a processing point for the main application.
- **What/Who does it connect to?**
    - **Upper/Side:** It is a **passive collector** of data via internal, low-priority flows. It receives logs, metrics, and event data from **App Servers**, replication logs from **Databases**, and operational stats from **Workers**.
        - *Arrows from multiple components → Analytics*
    - It connects to **nothing** at a lower or upper logical layer because it operates as a decoupled observer.

---

## 14. Worked Reference Numbers: A Feed System at 500M DAU

A single, internally-consistent set of back-of-envelope numbers for a large social-feed system (Instagram/Twitter-scale), useful as a sanity-check baseline whenever you need to defend an estimate in an interview.

| Stage | Metric | Value |
|---|---|---|
| Scale | Daily Active Users | 500M DAU |
| Traffic | Read QPS | 100K QPS |
| Traffic | Write QPS | 1K QPS |
| Traffic | Read : Write ratio | 100 : 1 |
| Storage | Growth rate | 73 PB / year |
| Bandwidth | Peak egress | 231 GB / sec |
| Sharding | Strategy | By User ID, ~1,000 shards |
| Feed generation | Celebrity threshold | 10K followers — switches from fan-out-on-write to fan-out-on-read |
| CDN | Cache hit rate target | 80% |
| Media lifecycle | Tiers | Hot → Warm → Cold |
| Cost | Estimated infra spend | ~$57M / month at this scale |
| Cost | Optimization headroom | $40–50M / year via lifecycle policies, denormalization, and right-sized caching |

**Why this matters:** these numbers are what separates a senior candidate from a mid-level one — being able to say "at 100K read QPS with a 100:1 ratio, a single Postgres primary won't survive without read replicas and a cache layer in front of it" is far stronger than describing caching in the abstract. Keep a version of this table memorized for whichever scale tier (10M / 100M / 1B DAU) you're most likely to be asked about, and know how each number changes the architecture: it's the concrete instantiation of the estimation techniques in file 15 (Estimation Skills) and the checklist in section 10 above.

---

*End of consolidated notes.*
