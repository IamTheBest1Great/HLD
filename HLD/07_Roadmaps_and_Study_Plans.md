# HLD Roadmaps & Study Plans — Consolidated HLD Notes

> The complete pattern-based execution roadmap for FAANG-level HLD interview preparation: what interviewers actually test, the 45-minute interview framework, 14 practice patterns with representative questions, an 8-week execution plan, a 120-day day-by-day alternative schedule, the Top-20 must-do questions, Staff-level advanced topics, a curated resource guide, the nine-pillars master reference table, a non-functional-requirements glossary, and the recommended study order.

---

## Table of Contents

- [1. PHASE 0 — Understanding What HLD Interviews Actually Test](#1-phase-0---understanding-what-hld-interviews-actually-test)
  - [1.1 What Interviewers Look For](#11-what-interviewers-look-for)
  - [1.2 What They're NOT Testing](#12-what-theyre-not-testing)
  - [1.3 What They ARE Testing](#13-what-they-are-testing)
- [2. PHASE 1 — Build Strong Foundational Knowledge (Weeks 1-3)](#2-phase-1---build-strong-foundational-knowledge-weeks-1-3)
  - [2.1 Core Concepts You Must Know Cold](#21-core-concepts-you-must-know-cold)
  - [2.2 Recommended Resources for Phase 1](#22-recommended-resources-for-phase-1)
- [3. PHASE 2 — Master the Interview Framework (Your 45-Minute Template)](#3-phase-2---master-the-interview-framework-your-45-minute-template)
  - [3.1 The Complete Interview Framework](#31-the-complete-interview-framework)
- [4. PHASE 3 — Pattern-Based Practice (The Core of Your Preparation)](#4-phase-3---pattern-based-practice-the-core-of-your-preparation)
  - [4.1 Pattern Classification Framework](#41-pattern-classification-framework)
- [5. PATTERN 1 — CRUD / Storage-Based Systems](#5-pattern-1---crud--storage-based-systems)
- [6. PATTERN 2 — Feed / Timeline Systems](#6-pattern-2---feed--timeline-systems)
- [7. PATTERN 3 — Real-Time Systems](#7-pattern-3---real-time-systems)
- [8. PATTERN 4 — Search Systems](#8-pattern-4---search-systems)
- [9. PATTERN 5 — Rate Limiter / Throttling](#9-pattern-5---rate-limiter--throttling)
- [10. PATTERN 6 — Logging / Metrics / Monitoring](#10-pattern-6---logging--metrics--monitoring)
- [11. PATTERN 7 — Distributed Caching Systems](#11-pattern-7---distributed-caching-systems)
- [12. PATTERN 8 — Recommendation Systems](#12-pattern-8---recommendation-systems)
- [13. PATTERN 9 — Payment / Financial Systems](#13-pattern-9---payment--financial-systems)
- [14. PATTERN 10 — Large-Scale Processing](#14-pattern-10---large-scale-processing)
- [15. PATTERN 11 — Geospatial / Location-Based Systems](#15-pattern-11---geospatial--location-based-systems)
- [16. PATTERN 12 — Collaborative Systems](#16-pattern-12---collaborative-systems)
- [17. PATTERN 13 — Infrastructure / System Utilities](#17-pattern-13---infrastructure--system-utilities)
- [18. PATTERN 14 — Web Crawler / Search Indexing](#18-pattern-14---web-crawler--search-indexing)
- [19. PHASE 4 — 8-Week Execution Plan](#19-phase-4---8-week-execution-plan)
  - [19.1 Week-by-Week Breakdown](#191-week-by-week-breakdown)
  - [19.2 Daily Practice Routine](#192-daily-practice-routine)
- [20. How to Practice (Like Your DP Excel Tracker)](#20-how-to-practice-like-your-dp-excel-tracker)
  - [20.1 HLD Practice Tracker Template](#201-hld-practice-tracker-template)
- [21. How to Think Like Top Engineers](#21-how-to-think-like-top-engineers)
  - [21.1 The 5 Critical Questions to Always Ask](#211-the-5-critical-questions-to-always-ask)
  - [21.2 Trade-off Mindset](#212-trade-off-mindset)
- [22. Top 20 Must-Do Questions (Most Frequently Asked)](#22-top-20-must-do-questions-most-frequently-asked)
- [23. Advanced (Staff-Level) Topics](#23-advanced-staff-level-topics)
  - [23.1 Multi-Region Architecture](#231-multi-region-architecture)
  - [23.2 Data Migration Strategies](#232-data-migration-strategies)
  - [23.3 Schema Evolution](#233-schema-evolution)
  - [23.4 Consistent Hashing Internals](#234-consistent-hashing-internals)
  - [23.5 Leader Election Algorithms](#235-leader-election-algorithms)
  - [23.6 Consensus Protocols](#236-consensus-protocols)
  - [23.7 Advanced CAP Scenarios](#237-advanced-cap-scenarios)
- [24. Complete Resource Guide](#24-complete-resource-guide)
  - [24.1 Books (Must Read)](#241-books-must-read)
  - [24.2 Online Courses](#242-online-courses)
  - [24.3 YouTube Channels](#243-youtube-channels)
  - [24.4 Blogs and Websites](#244-blogs-and-websites)
  - [24.5 Practice Platforms](#245-practice-platforms)
- [25. Final Advice for You Specifically](#25-final-advice-for-you-specifically)
  - [25.1 Your Success Formula](#251-your-success-formula)
  - [25.2 Common Pitfalls to Avoid](#252-common-pitfalls-to-avoid)
- [26. Quick Reference: HLD Interview Checklist](#26-quick-reference-hld-interview-checklist)
  - [26.1 Pre-Interview Checklist](#261-pre-interview-checklist)
  - [26.2 During Interview Checklist](#262-during-interview-checklist)
  - [26.3 Post-Interview Reflection](#263-post-interview-reflection)
- [27. Your Next Steps](#27-your-next-steps)
- [28. Master Table: The Nine Pillars of High-Level Design](#28-master-table-the-nine-pillars-of-high-level-design)
- [29. Non-Functional Requirements Glossary](#29-non-functional-requirements-glossary)
- [30. Recommended Study Order](#30-recommended-study-order)
  - [30.1 Foundations: The "Bricks" 🧱](#301-foundations-the-bricks-)
  - [30.2 Communication & Protocols: The "Glue" 📡](#302-communication--protocols-the-glue-)
  - [30.3 Data Mastery: The "Memory" 💾](#303-data-mastery-the-memory-)
  - [30.4 Architectural Patterns: The "Blueprints" 🏗️](#304-architectural-patterns-the-blueprints-)
  - [30.5 System Case Studies: The "Practice" 🏆](#305-system-case-studies-the-practice-)
- [31. The 120-Day Day-by-Day Mastery Schedule](#31-the-120-day-day-by-day-mastery-schedule)
  - [31.1 From Pattern-Based Thinker to System Design Expert](#311-from-pattern-based-thinker-to-system-design-expert)
  - [31.2 How to Use This Roadmap](#312-how-to-use-this-roadmap)
  - [31.3 Phase 1: Foundation Building (Days 1–30)](#313-phase-1-foundation-building-days-1-30)
  - [31.4 Phase 2: Core Patterns (Days 31–60)](#314-phase-2-core-patterns-days-31-60)
  - [31.5 Phase 3: Advanced & Specialized Patterns (Days 61–90)](#315-phase-3-advanced--specialized-patterns-days-61-90)
  - [31.6 Phase 4: Integration, Mock Interviews & Deep Dives (Days 91–120)](#316-phase-4-integration-mock-interviews--deep-dives-days-91-120)
  - [31.7 HLD Mastery Tracker (Template)](#317-hld-mastery-tracker-template)
  - [31.8 The 6-Step Interview Framework (Practice It Every Time)](#318-the-6-step-interview-framework-practice-it-every-time)
  - [31.9 Final Pro Tips](#319-final-pro-tips)

- [32. Ready-to-Use Tracking Template (4-Sheet Spec)](#32-ready-to-use-tracking-template-4-sheet-spec)
  - [32.1 Sheet 1 — Master Dashboard](#321-sheet-1---master-dashboard)
  - [32.2 Sheet 2 — Question Tracker (Main Sheet)](#322-sheet-2---question-tracker-main-sheet)
  - [32.3 Sheet 3 — Pattern Master Sheet](#323-sheet-3---pattern-master-sheet)
  - [32.4 Sheet 4 — Mock Interview Log](#324-sheet-4---mock-interview-log)
- [33. Alternative Tiered Priority List (Top 30, by Tier)](#33-alternative-tiered-priority-list-top-30-by-tier)
  - [33.1 Tier 1 — Absolute Must (Top 10)](#331-tier-1---absolute-must-top-10)
  - [33.2 Tier 2 — Very High Value (11–20)](#332-tier-2---very-high-value-11-20)
  - [33.3 Tier 3 — Advanced Depth (21–30)](#333-tier-3---advanced-depth-21-30)
  - [33.4 Three-Month Strategic Execution Plan](#334-three-month-strategic-execution-plan)
- [34. Cross-Source Notes: CLEAR Method, LB Algorithms & the Trade-off Sentence](#34-cross-source-notes-clear-method-lb-algorithms--the-trade-off-sentence)
---

## 1. PHASE 0 — Understanding What HLD Interviews Actually Test

Before diving into preparation, understand what top companies (Google, Amazon, Meta, Netflix, Microsoft) are actually evaluating.

### 1.1 What Interviewers Look For

| Evaluation Criteria | What They're Testing |
|---------------------|----------------------|
| **Requirement Clarification** | Can you handle ambiguity and scope the problem correctly? |
| **System Decomposition** | Can you break complex systems into manageable components? |
| **Scalability Reasoning** | Do you understand how systems behave under load? |
| **Trade-off Analysis** | Can you justify why you chose one solution over another? |
| **Data Modeling** | Do you understand database design at scale? |
| **Bottleneck Identification** | Can you find and fix weak points in your architecture? |
| **Communication Clarity** | Can you explain complex ideas clearly and collaboratively? |

### 1.2 What They're NOT Testing

- ❌ Perfect architecture with every detail
- ❌ Memorized diagrams from blog posts
- ❌ Framework or tool-specific knowledge
- ❌ Getting the "right answer" (there is none)

### 1.3 What They ARE Testing

- ✅ **Thinking under ambiguity** — Your ability to make reasonable assumptions and defend them
- ✅ **Structured problem-solving** — Following a logical framework
- ✅ **Depth vs. breadth balance** — Knowing when to dive deep vs. stay high-level

---

## 2. PHASE 1 — Build Strong Foundational Knowledge (Weeks 1-3)

Before designing complex systems, master these atomic building blocks. You cannot design a skyscraper if you don't understand bricks.

### 2.1 Core Concepts You Must Know Cold

### 1️⃣ Networking Fundamentals

| Concept | What to Understand |
|---------|---------------------|
| **HTTP/HTTPS** | Request-response model, methods (GET, POST, PUT, DELETE), status codes, headers |
| **TCP vs UDP** | Connection-oriented vs connectionless, reliability vs speed trade-offs |
| **DNS** | Domain resolution process, caching, latency implications |
| **Load Balancers** | Layer 4 (transport) vs Layer 7 (application), algorithms (Round Robin, Least Connections, IP Hash) |
| **CDN** | Edge caching, geographic distribution, pull vs push strategies |
| **WebSockets** | Full-duplex communication, use cases (chat, live updates) |
| **gRPC** | High-performance RPC, protocol buffers, streaming |
| **REST** | Resource-based APIs, statelessness, HTTP methods |

### 2️⃣ Scaling Fundamentals

| Concept | Explanation |
|---------|-------------|
| **Vertical Scaling** | Adding more power (CPU, RAM) to existing servers — simpler but has limits |
| **Horizontal Scaling** | Adding more servers — complex but theoretically infinite |
| **Stateless Services** | No session data stored locally — easily scalable |
| **Stateful Services** | Maintain session state — harder to scale, needs sticky sessions or external storage |
| **Reverse Proxy** | Sits between clients and servers — provides caching, load balancing, security |

### 3️⃣ Database Deep Dive

#### SQL vs NoSQL Decision Matrix

| Factor | Choose SQL | Choose NoSQL |
|--------|------------|--------------|
| **Data Structure** | Structured, fixed schema | Unstructured, semi-structured, flexible schema |
| **Relationships** | Complex relationships, joins | Few relationships, denormalized data |
| **Consistency Needs** | Strong consistency (ACID) | Eventual consistency acceptable (BASE) |
| **Scale** | Vertical scaling (with some horizontal) | Horizontal scaling by design |
| **Query Complexity** | Complex queries, aggregations | Simple key-based lookups |
| **Use Cases** | Financial systems, inventory | Social feeds, real-time analytics |

#### NoSQL Types

| Type | Examples | Use Case |
|------|----------|----------|
| **Key-Value** | Redis, Memcached, DynamoDB | Caching, session storage |
| **Document** | MongoDB, CouchDB | Content management, catalogs |
| **Wide-Column** | Cassandra, HBase | Time-series data, analytics |
| **Graph** | Neo4j, Amazon Neptune | Social networks, recommendations |

#### Critical Database Concepts

| Concept | What to Understand |
|---------|---------------------|
| **Indexing** | B-Tree vs Hash indexes, composite indexes, trade-offs (read speed vs write speed) |
| **Replication** | Master-Slave (read scaling), Master-Master (write scaling), synchronous vs asynchronous |
| **Sharding/Partitioning** | Horizontal partitioning, shard keys, rebalancing challenges |
| **Consistency Models** | Strong, Eventual, Causal, Read-your-writes |
| **ACID vs BASE** | Atomicity, Consistency, Isolation, Durability vs Basically Available, Soft state, Eventual consistency |

### 4️⃣ Caching Strategies

| Strategy | How It Works | Pros | Cons |
|----------|--------------|------|------|
| **Cache-Aside** | Application checks cache first, loads from DB on miss | Simple, cache only what's needed | Cache miss penalty |
| **Read-Through** | Cache sits between app and DB, loads on miss | App code simpler | Cache must understand DB |
| **Write-Through** | Data written to cache and DB simultaneously | Consistency, no stale data | Higher write latency |
| **Write-Back** | Write to cache only, async write to DB | Fast writes | Risk of data loss if cache fails |
| **Write-Around** | Write directly to DB, cache on read | Prevents cache pollution | Cache miss on recent writes |

#### Eviction Policies

| Policy | Description |
|--------|-------------|
| **LRU (Least Recently Used)** | Evict items not accessed for longest time |
| **LFU (Least Frequently Used)** | Evict items accessed least often |
| **FIFO (First In, First Out)** | Evict oldest items regardless of usage |
| **TTL (Time-To-Live)** | Evict after fixed time |

### 5️⃣ Messaging and Queues

| Concept | Explanation |
|---------|-------------|
| **Message Queue** | Point-to-point communication (RabbitMQ, SQS) |
| **Pub/Sub** | One-to-many broadcast (Kafka, SNS) |
| **Queue vs Stream** | Queue: message removed after consumption; Stream: messages persist |
| **At-least-once delivery** | Messages may be duplicated but never lost |
| **Exactly-once delivery** | Hard to achieve, requires idempotent consumers |
| **Message ordering** | FIFO queues vs standard queues |

### 6️⃣ Storage Systems

| Storage Type | Examples | Use Case |
|--------------|----------|----------|
| **Object Storage** | AWS S3, Google Cloud Storage | Files, images, videos — unlimited scale |
| **Block Storage** | AWS EBS, SSD | Databases, high-performance needs |
| **File Storage** | AWS EFS, NFS | Shared file systems |
| **Blob Storage** | Azure Blob | Large unstructured data |

### 7️⃣ Reliability Patterns

| Pattern | Purpose | How It Works |
|---------|---------|--------------|
| **Rate Limiting** | Prevent abuse | Token bucket, Leaky bucket, Sliding window |
| **Circuit Breaker** | Prevent cascading failures | Open (failing fast), Closed (normal), Half-Open (testing) |
| **Retry with Backoff** | Handle transient failures | Exponential backoff, jitter |
| **Idempotency** | Safe retries | Idempotency keys, same request → same response |
| **Bulkhead** | Isolate failures | Partition resources by service/tenant |
| **Health Checks** | Detect failures | Heartbeat endpoints, passive vs active |

### 8️⃣ The CAP Theorem

You cannot have all three. You must choose two.

```
        Consistency (C)
              /\
             /  \
            /    \
           /      \
          /        \
         /          \
        /            \
       /              \
      /                \
Partition Tolerance (P) ——— Availability (A)
```

| Choice | Description | Examples |
|--------|-------------|----------|
| **CP (Consistency + Partition Tolerance)** | Sacrifice availability during partitions | Traditional RDBMS, HBase |
| **AP (Availability + Partition Tolerance)** | Sacrifice consistency during partitions | Cassandra, DynamoDB |
| **CA (Consistency + Availability)** | Not possible in distributed systems | (Single-node systems only) |

#### Modern Extension: PACELC
- **During Partition (P)** — trade-off between Availability and Consistency
- **Else (E)** — trade-off between Latency and Consistency

---

### 2.2 Recommended Resources for Phase 1

| Resource | Why |
|----------|-----|
| **Designing Data-Intensive Applications** | The bible — read replication, partitioning, consistency chapters |
| **System Design Interview — An Insider's Guide (Vol 1 & 2)** | Best practical introduction |
| **Grokking the System Design Interview** | Structured course format |
| **High Scalability Blog** | Real-world architecture case studies |

---

## 3. PHASE 2 — Master the Interview Framework (Your 45-Minute Template)

A structured framework keeps you from rambling or designing the wrong system. Practice applying this exact 6-step framework to every problem.

### 3.1 The Complete Interview Framework

### Step 1 — Clarify Requirements (5-10 minutes)

Never jump straight into designing. Ask questions to define scope.

| Type | Questions to Ask |
|------|------------------|
| **Functional Requirements** | What should the system do? What are the core features? What's in scope vs out of scope? |
| **Non-Functional Requirements** | How many users? Read/write ratio? Expected latency? Consistency needs? Availability SLA? |
| **Scale** | Daily Active Users (DAU)? Total users? Data retention period? Geographic distribution? |

**Example for Twitter:**
- *Functional*: Post tweet, follow users, view timeline, search tweets
- *Non-functional*: Timeline load <500ms, 99.9% availability, 200M DAU, 60:40 read:write ratio
- *Out of scope*: Ads, analytics, recommendations

### Step 2 — Back-of-the-Envelope Estimation (5 minutes)

Calculate scale to inform design choices.

| What to Estimate | How | Example (Twitter-scale) |
|------------------|-----|------------------------|
| **Traffic (QPS)** | DAU × actions per user / seconds in day | 200M DAU × 2 tweets/day / 86400 ≈ 4,600 writes/sec |
| **Peak QPS** | Average × 2-3x | 4,600 × 3 ≈ 13,800 writes/sec peak |
| **Storage** | Daily × retention × size per item | 500M tweets/day × 5 years × 1KB ≈ 912 TB |
| **Bandwidth** | Data transferred per second | 4,600 writes/sec × 1KB = 4.6 MB/s upload |
| **Cache Size** | 20% of daily active data | 20% × 500M tweets = 100M tweets in cache |

### Step 3 — API Design (5 minutes)

Define the contract between clients and servers.

| API Element | What to Include | Example |
|-------------|-----------------|---------|
| **Endpoints** | RESTful resource paths | `POST /tweet`, `GET /timeline/{user_id}` |
| **Request/Response** | JSON structure | `{ "text": "Hello", "media_ids": [] }` |
| **Authentication** | How users are identified | JWT in Authorization header |
| **Pagination** | For list endpoints | `limit`, `offset` or `cursor` params |

### Step 4 — High-Level Architecture (10 minutes)

Draw the "happy path" from client to database.

```
Client (Mobile/Web)
    ↓
DNS (Route53/Cloudflare)
    ↓
CDN (CloudFront) ←───┐ (Static assets)
    ↓                │
Load Balancer (ELB/NGINX)
    ↓
Application Servers (Stateless, auto-scaling)
    ↓                              ↓
Cache (Redis/Memcached)    Message Queue (Kafka)
    ↓                              ↓
Database (Primary)          Worker Servers
    ↓                              ↓
Database (Replicas)         Database (Analytics)
```

### Step 5 — Deep Dive on Key Components (15 minutes)

The interviewer will ask you to zoom in on 1-2 critical areas. This is where you demonstrate depth.

#### Common Deep Dive Areas

| Area | Questions to Address |
|------|---------------------|
| **Database** | Schema design? Sharding key? Indexing strategy? Read replicas? |
| **Caching** | What to cache? Cache strategy? Eviction policy? Cache invalidation? |
| **Data Flow** | End-to-end flow for a key feature? Where are bottlenecks? |
| **Scaling** | How does this component scale horizontally? Single points of failure? |
| **Consistency** | Strong or eventual? How to handle conflicts? |
| **Real-time** | WebSockets? Polling? Server-Sent Events? |

### Step 6 — Identify Bottlenecks and Trade-offs (5 minutes)

Critically evaluate your own design. Show you can find flaws.

| Question to Ask Yourself | Why It Matters |
|--------------------------|----------------|
| Where is the single point of failure? | Shows fault tolerance awareness |
| What happens if traffic spikes 10x? | Shows scalability thinking |
| How do we handle network partitions? | Shows distributed systems knowledge |
| What's the biggest bottleneck? | Shows prioritization |
| Why did you choose X over Y? | Shows trade-off analysis |

---

## 4. PHASE 3 — Pattern-Based Practice (The Core of Your Preparation)

Like Dynamic Programming, HLD has recurring patterns. Master these patterns, and you can solve any variant.

### 4.1 Pattern Classification Framework

Each question is categorized by:
- **Pattern** — The fundamental architectural pattern
- **Sub-pattern** — Specific variant
- **Primary Challenge** — What makes this question hard
- **Read/Write Ratio** — Helps choose database and caching strategy
- **Consistency Needs** — Strong vs Eventual
- **Real-time Requirements** — Yes/No
- **Key Technologies** — What components you'll likely use

---

## 5. PATTERN 1 — CRUD / Storage-Based Systems

### Pattern Overview

| Attribute | Description |
|-----------|-------------|
| **Core Challenge** | Data persistence at scale, efficient retrieval |
| **Typical Ratio** | Variable (read-heavy or write-heavy) |
| **Consistency** | Often strong for critical data |
| **Key Components** | Database, Cache, Object Storage |
| **Sub-patterns** | Basic CRUD, Scalable CRUD, Multi-tenant CRUD |

---

### Question 1.1: Design URL Shortener (TinyURL/Bitly)

| Category | Details |
|----------|---------|
| **Pattern** | CRUD / Storage-Based |
| **Sub-pattern** | Key-Value Store |
| **Primary Challenge** | Generating unique short keys, handling collisions, redirect speed |
| **Read/Write Ratio** | Heavy Read (100:1 reads to writes) |
| **Consistency Needs** | Strong (redirects must go to correct URL) |
| **Real-time** | No |
| **Key Technologies** | Base62 encoding, Hash functions, Redis cache, Relational DB |

**Key Decisions:**
- **Key generation**: Base62 encoding of unique ID, or hash of URL + collision handling
- **Database**: Relational for mapping, NoSQL for analytics
- **Caching**: Cache popular URLs in Redis (LRU eviction)
- **Scaling**: Consistent hashing for cache nodes, read replicas for DB

---

### Question 1.2: Design Pastebin

| Category | Details |
|----------|---------|
| **Pattern** | CRUD / Storage-Based |
| **Sub-pattern** | Key-Value + Expiry |
| **Primary Challenge** | Handling large text content, expiration, syntax highlighting |
| **Read/Write Ratio** | Read-heavy (80:20) |
| **Consistency Needs** | Strong (users expect their paste to be there) |
| **Real-time** | No |
| **Key Technologies** | Object storage for content, Metadata DB, CDN for syntax JS |

**Key Decisions:**
- **Storage**: Store content in S3/Blob storage, metadata in SQL DB
- **Expiry**: Background workers delete expired pastes
- **Caching**: Cache recent pastes in Redis
- **Unique ID**: Similar to URL shortener

---

### Question 1.3: Design Online Bookstore

| Category | Details |
|----------|---------|
| **Pattern** | CRUD / Storage-Based |
| **Sub-pattern** | E-commerce Catalog |
| **Primary Challenge** | Product search, inventory tracking, recommendations |
| **Read/Write Ratio** | Read-heavy (90:10) |
| **Consistency Needs** | Strong for inventory, eventual for catalog |
| **Real-time** | No |
| **Key Technologies** | Search index (Elasticsearch), SQL for inventory, Redis for sessions |

**Key Decisions:**
- **Catalog**: Denormalized document store (MongoDB) for product details
- **Search**: Elasticsearch for full-text search
- **Inventory**: SQL with ACID for accurate counts
- **Caching**: Product pages in Redis

---

### Question 1.4: Design Ticket Booking System (BookMyShow/Ticketmaster)

| Category | Details |
|----------|---------|
| **Pattern** | CRUD / Storage-Based |
| **Sub-pattern** | High-Concurrency Booking |
| **Primary Challenge** | Preventing double-booking, handling seat locks, high traffic for popular events |
| **Read/Write Ratio** | Mixed (read to browse, write to book) |
| **Consistency Needs** | Strong (must prevent overselling) |
| **Real-time** | Yes (seat availability updates) |
| **Key Technologies** | SQL with row-level locking, Redis for temporary holds, Message queues |

**Key Decisions:**
- **Concurrency control**: Optimistic locking with version numbers, or pessimistic row locks
- **Seat holds**: Temporary locks in Redis with TTL
- **Database**: SQL for ACID transactions
- **Scaling**: Shard by event ID, read replicas for browsing

---

### Question 1.5: Design Dropbox / Google Drive

| Category | Details |
|----------|---------|
| **Pattern** | CRUD / Storage-Based |
| **Sub-pattern** | File Storage & Sync |
| **Primary Challenge** | Large file uploads/downloads, sync across devices, conflict resolution |
| **Read/Write Ratio** | Mixed (depends on user behavior) |
| **Consistency Needs** | Strong (files should be consistent across devices) |
| **Real-time** | Yes (sync notifications) |
| **Key Technologies** | Object storage (S3), Metadata DB, Delta sync, WebSockets |

**Key Decisions:**
- **File storage**: Chunk files into blocks, store in S3
- **Metadata**: SQL DB for file hierarchy, permissions
- **Deduplication**: Hash-based block deduplication
- **Delta sync**: Only sync changed blocks
- **Conflict resolution**: Last-write-wins or create copies

---

## 6. PATTERN 2 — Feed / Timeline Systems

### Pattern Overview

| Attribute | Description |
|-----------|-------------|
| **Core Challenge** | Generating personalized timelines efficiently |
| **Typical Ratio** | Read-heavy (timeline views) with write spikes |
| **Consistency** | Eventual (slight delays acceptable) |
| **Key Components** | Message queues, Fan-out services, Cache |
| **Sub-patterns** | Push model, Pull model, Hybrid |

**The Core Concept: Fan-out**

| Model | How It Works | Pros | Cons |
|-------|--------------|------|------|
| **Push (Fan-out on Write)** | When user posts, push to all followers' timelines | Fast reads (pre-computed) | Writes expensive for celebrities |
| **Pull (Fan-out on Read)** | When user views timeline, fetch from followed users | Efficient writes | Slow reads (need to merge) |
| **Hybrid** | Push for regular users, pull for celebrities | Best of both | Complex logic |

---

### Question 2.1: Design Twitter / X

| Category | Details |
|----------|---------|
| **Pattern** | Feed / Timeline |
| **Sub-pattern** | Social Media Feed |
| **Primary Challenge** | Handling celebrity accounts with millions of followers, real-time updates |
| **Read/Write Ratio** | Read-heavy (timeline views) |
| **Consistency Needs** | Eventual |
| **Real-time** | Yes (new tweets appear immediately) |
| **Key Technologies** | Hybrid fan-out, Redis cache, Cassandra for tweets, Kafka |

**Key Decisions:**
- **Fan-out strategy**: Hybrid — push for regular users (<5k followers), pull for celebrities
- **Timeline generation**: Pre-compute timelines in Redis lists
- **Tweet storage**: Cassandra for write scalability
- **Social graph**: Graph DB or MySQL for follows

**Data Models:**
```
User: user_id, name, follower_count
Tweet: tweet_id, user_id, content, timestamp, media_ids
Timeline: user_id, list of (tweet_id, timestamp) (pre-computed in Redis)
Follow: user_id, follower_id
```

---

### Question 2.2: Design Instagram Feed

| Category | Details |
|----------|---------|
| **Pattern** | Feed / Timeline |
| **Sub-pattern** | Media-Heavy Feed |
| **Primary Challenge** | Handling images/videos, ranking feed by relevance |
| **Read/Write Ratio** | Read-heavy |
| **Consistency Needs** | Eventual |
| **Real-time** | Yes |
| **Key Technologies** | CDN for media, Push fan-out, Ranking service |

**Key Decisions:**
- **Media storage**: Images in CDN, metadata in DB
- **Feed generation**: Push-based with ranking algorithm
- **Ranking factors**: Recency, engagement, relationships
- **Caching**: Pre-computed ranked feeds in Redis

---

### Question 2.3: Design Facebook News Feed

| Category | Details |
|----------|---------|
| **Pattern** | Feed / Timeline |
| **Sub-pattern** | Algorithmic Feed |
| **Primary Challenge** | Complex ranking algorithm, diverse content types (posts, photos, links, videos) |
| **Read/Write Ratio** | Read-heavy |
| **Consistency Needs** | Eventual |
| **Real-time** | Yes |
| **Key Technologies** | Ranking service, ML models, Graph DB, Cassandra |

**Key Decisions:**
- **Content types**: Unified feed with mixed content
- **Ranking**: ML model with features (affinity, weight, time decay)
- **Storage**: Graph for social connections, Cassandra for feed items
- **Fan-out**: Push to active users, pull for inactive

---

### Question 2.4: Design LinkedIn Feed

| Category | Details |
|----------|---------|
| **Pattern** | Feed / Timeline |
| **Sub-pattern** | Professional Network Feed |
| **Primary Challenge** | Feed from 2nd/3rd degree connections, company pages, sponsored content |
| **Read/Write Ratio** | Read-heavy |
| **Consistency Needs** | Eventual |
| **Real-time** | No (less real-time pressure) |
| **Key Technologies** | Pull-based feed, Graph DB, Relevance scoring |

**Key Decisions:**
- **Fan-out**: Pull-based due to large number of potential connections
- **Relevance**: Score based on connection strength, content type
- **Storage**: Graph DB for network, document store for posts

---

## 7. PATTERN 3 — Real-Time Systems

### Pattern Overview

| Attribute | Description |
|-----------|-------------|
| **Core Challenge** | Low-latency bidirectional communication, connection management |
| **Typical Ratio** | Variable (many small messages) |
| **Consistency** | Often eventual (message ordering matters) |
| **Key Components** | WebSockets, Pub/Sub, Presence servers |
| **Sub-patterns** | 1:1 Chat, Group Chat, Live Updates |

---

### Question 3.1: Design Chat System / WhatsApp

| Category | Details |
|----------|---------|
| **Pattern** | Real-Time |
| **Sub-pattern** | 1:1 and Group Messaging |
| **Primary Challenge** | Message delivery, ordering, last seen, read receipts, multi-device sync |
| **Read/Write Ratio** | Write-heavy (many messages) |
| **Consistency Needs** | Strong ordering per conversation |
| **Real-time** | Yes |
| **Key Technologies** | WebSockets, Message queues, Cassandra, Redis for presence |

**Key Decisions:**
- **Connection management**: WebSocket servers with sticky sessions
- **Message storage**: Cassandra for write scalability
- **Message ordering**: Client timestamps + server sequence IDs
- **Read receipts**: Pub/sub to notify senders
- **Last seen**: Redis with TTL
- **Multi-device**: Sync via server-stored message history

**Architecture:**
```
Client → WebSocket Gateway → Message Service → Kafka → Persistence
                                       ↓
                                Notification Service
                                       ↓
                                 Push Notifications
```

---

### Question 3.2: Design Discord / Slack

| Category | Details |
|----------|---------|
| **Pattern** | Real-Time |
| **Sub-pattern** | Group Chat with Channels |
| **Primary Challenge** | Large groups, voice channels, permissions, thread support |
| **Read/Write Ratio** | Write-heavy |
| **Consistency Needs** | Eventual for messages, strong for permissions |
| **Real-time** | Yes (voice requires UDP) |
| **Key Technologies** | WebSockets, WebRTC for voice, Cassandra, Redis |

**Key Decisions:**
- **Channels**: Separate message streams per channel
- **Voice**: WebRTC with SFU (Selective Forwarding Unit) architecture
- **Permissions**: Role-based access control in SQL
- **Message history**: Time-series DB for channel messages
- **Unread counts**: Redis counters per user per channel

---

### Question 3.3: Design Live Comments (YouTube Live / Twitch Chat)

| Category | Details |
|----------|---------|
| **Pattern** | Real-Time |
| **Sub-pattern** | High-Volume Live Updates |
| **Primary Challenge** | Massive concurrent writers (thousands of comments per second), fan-out to millions |
| **Read/Write Ratio** | Write-heavy with broadcast reads |
| **Consistency Needs** | Eventual (order approximate) |
| **Real-time** | Yes (sub-second) |
| **Key Technologies** | WebSockets, Kafka, Redis Streams |

**Key Decisions:**
- **Ingestion**: Kafka for buffering high-volume writes
- **Fan-out**: Redis Streams or Kafka per live event
- **Connection management**: WebSocket servers with topic subscriptions
- **Rate limiting**: Per-user rate limiting to prevent spam
- **Moderation**: Real-time filter service

---

### Question 3.4: Design Multiplayer Game Backend

| Category | Details |
|----------|---------|
| **Pattern** | Real-Time |
| **Sub-pattern** | Game State Synchronization |
| **Primary Challenge** | Ultra-low latency (50ms), state consistency, cheating prevention |
| **Read/Write Ratio** | Write-heavy (player actions) |
| **Consistency Needs** | Strong for game state |
| **Real-time** | Yes (UDP often) |
| **Key Technologies** | UDP, Game state servers, Deterministic lockstep, Authority server |

**Key Decisions:**
- **Protocol**: UDP for fast updates, TCP for reliable messages
- **State sync**: Server-authoritative model to prevent cheating
- **Prediction**: Client-side prediction with server reconciliation
- **Room management**: Dedicated game servers per match
- **Matchmaking**: Redis for player queues

---

## 8. PATTERN 4 — Search Systems

### Pattern Overview

| Attribute | Description |
|-----------|-------------|
| **Core Challenge** | Fast full-text search, relevance ranking, typo tolerance |
| **Typical Ratio** | Read-heavy (searches) |
| **Consistency** | Eventual (index freshness trade-offs) |
| **Key Components** | Inverted index, Search cluster, Indexers |
| **Sub-patterns** | Web Search, Product Search, Autocomplete |

---

### Question 4.1: Design Google Search

| Category | Details |
|----------|---------|
| **Pattern** | Search |
| **Sub-pattern** | Web-Scale Search |
| **Primary Challenge** | Crawling entire web, indexing petabytes, ranking, serving under 200ms |
| **Read/Write Ratio** | Read-heavy (searches) |
| **Consistency Needs** | Eventual (freshness vs quality trade-off) |
| **Real-time** | Yes (fast responses) |
| **Key Technologies** | Inverted index, PageRank, MapReduce, Distributed search |

**Key Components:**
1. **Crawler**: Downloads web pages, respects robots.txt
2. **Indexer**: Builds inverted index (word → list of documents)
3. **Storage**: Bigtable for doc metadata, inverted index sharded
4. **Searcher**: Query parsing, ranking, snippet generation

**Key Decisions:**
- **Sharding**: Partition by document ID or term
- **Ranking**: TF-IDF, PageRank, machine learning
- **Freshness**: Incremental updates to index
- **Caching**: Query cache, result cache

---

### Question 4.2: Design YouTube Search

| Category | Details |
|----------|---------|
| **Pattern** | Search |
| **Sub-pattern** | Video Search |
| **Primary Challenge** | Searching video metadata and transcripts, ranking by views/engagement |
| **Read/Write Ratio** | Read-heavy |
| **Consistency Needs** | Eventual |
| **Real-time** | Yes |
| **Key Technologies** | Elasticsearch, Video metadata DB, Transcript index |

**Key Decisions:**
- **Index**: Elasticsearch for video titles, descriptions, tags
- **Transcripts**: Extract and index speech-to-text output
- **Ranking**: Weight by views, likes, recency
- **Filters**: Metadata filters (duration, upload date, quality)

---

### Question 4.3: Design Amazon Product Search

| Category | Details |
|----------|---------|
| **Pattern** | Search |
| **Sub-pattern** | E-commerce Search |
| **Primary Challenge** | Faceted search (filters by category, price, brand), inventory-aware results |
| **Read/Write Ratio** | Read-heavy |
| **Consistency Needs** | Strong for inventory (in-stock status) |
| **Real-time** | Yes |
| **Key Technologies** | Elasticsearch, Faceted indexing, Inventory cache |

**Key Decisions:**
- **Facets**: Store facet counts in index for fast filtering
- **Inventory**: Join with real-time inventory service
- **Ranking**: Relevance + conversion data + personalization
- **Synonyms**: Product synonym expansion

---

### Question 4.4: Design Search Autocomplete

| Category | Details |
|----------|---------|
| **Pattern** | Search |
| **Sub-pattern** | Typeahead Suggestions |
| **Primary Challenge** | Ultra-fast prefix matching, handling popularity, personalization |
| **Read/Write Ratio** | Read-heavy |
| **Consistency Needs** | Eventual |
| **Real-time** | Yes (sub-100ms) |
| **Key Technologies** | Trie (prefix tree), Frequency cache, Redis |

**Key Decisions:**
- **Data structure**: Trie (prefix tree) for fast prefix lookups
- **Ranking**: Weight suggestions by popularity (search frequency)
- **Personalization**: Boost based on user history
- **Freshness**: Update trie daily with recent search data
- **Caching**: Cache top suggestions for frequent prefixes

---

## 9. PATTERN 5 — Rate Limiter / Throttling

### Pattern Overview

| Attribute | Description |
|-----------|-------------|
| **Core Challenge** | Enforcing API usage limits fairly and efficiently |
| **Typical Ratio** | Applied to all requests |
| **Consistency** | Strong (limits must be accurate) |
| **Key Components** | Redis, Token buckets, Distributed counters |
| **Sub-patterns** | Token Bucket, Leaky Bucket, Sliding Window |

---

### Question 5.1: Design API Rate Limiter

| Category | Details |
|----------|---------|
| **Pattern** | Rate Limiter |
| **Sub-pattern** | Distributed Rate Limiting |
| **Primary Challenge** | Accurate counting across multiple servers, low latency overhead |
| **Read/Write Ratio** | Every request is a write (to increment counter) |
| **Consistency Needs** | Strong (limits must be enforced) |
| **Real-time** | Yes (must not add significant latency) |
| **Key Technologies** | Redis, Lua scripts, Token bucket algorithm |

**Algorithms Comparison:**

| Algorithm | How It Works | Pros | Cons |
|-----------|--------------|------|------|
| **Token Bucket** | Tokens added at fixed rate, requests consume tokens | Simple, allows bursts | Memory per user |
| **Leaky Bucket** | Requests processed at fixed rate (queue) | Smooth output | No bursts |
| **Fixed Window** | Count requests in fixed time window | Simple | Edge case spikes |
| **Sliding Window** | Count in rolling time window | Accurate | More complex |
| **Sliding Window Log** | Track timestamps of all requests | Most accurate | Memory heavy |

**Key Decisions:**
- **Storage**: Redis with Lua for atomic operations
- **Rules**: Per-user, per-API, per-tier rate limits
- **Response**: Return headers with remaining quota
- **Distributed**: Consistent hashing to assign users to Redis nodes

---

### Question 5.2: Design API Gateway

| Category | Details |
|----------|---------|
| **Pattern** | Rate Limiter |
| **Sub-pattern** | Gateway with Multiple Features |
| **Primary Challenge** | Combining rate limiting, authentication, routing, logging |
| **Read/Write Ratio** | Every request touches gateway |
| **Consistency Needs** | Strong for auth |
| **Real-time** | Yes |
| **Key Technologies** | Reverse proxy (NGINX), Redis, JWT validation |

**Key Decisions:**
- **Layered approach**: Global rate limits + per-user limits
- **Authentication**: Validate JWT tokens, cache public keys
- **Routing**: Path-based routing to microservices
- **Logging**: Async logging to Kafka
- **Circuit breaking**: Detect failing downstream services

---

## 10. PATTERN 6 — Logging / Metrics / Monitoring

### Pattern Overview

| Attribute | Description |
|-----------|-------------|
| **Core Challenge** | Ingesting high-volume time-series data, efficient querying |
| **Typical Ratio** | Write-heavy (constant data ingestion) |
| **Consistency** | Eventual (loss of some metrics acceptable) |
| **Key Components** | Time-series DB, Stream processor, Aggregator |
| **Sub-patterns** | Metrics, Logs, Tracing |

---

### Question 6.1: Design Metrics Monitoring System (Prometheus/Datadog)

| Category | Details |
|----------|---------|
| **Pattern** | Logging / Metrics |
| **Sub-pattern** | Time-Series Metrics |
| **Primary Challenge** | High-frequency data points, efficient storage, fast queries |
| **Read/Write Ratio** | Write-heavy (ingestion), read-spiky (dashboards) |
| **Consistency Needs** | Eventual |
| **Real-time** | Yes (seconds delay) |
| **Key Technologies** | Time-series DB (InfluxDB/Prometheus), Downsampling, Aggregation |

**Key Decisions:**
- **Data model**: Metric name + labels/tags + timestamp + value
- **Ingestion**: HTTP endpoint, batch writes
- **Storage**: Time-series optimized (LSM tree, compression)
- **Downsampling**: Older data at lower resolution
- **Query**: PromQL-like language for aggregation
- **Alerting**: Rule evaluation engine

---

### Question 6.2: Design Log Aggregator (Splunk/ELK Stack)

| Category | Details |
|----------|---------|
| **Pattern** | Logging / Metrics |
| **Sub-pattern** | Log Ingestion and Search |
| **Primary Challenge** | High-volume log ingestion, full-text search, retention |
| **Read/Write Ratio** | Write-heavy (logs), read-spiky (troubleshooting) |
| **Consistency Needs** | Eventual |
| **Real-time** | Near-real-time (minutes) |
| **Key Technologies** | Log shippers (Fluentd), Kafka, Elasticsearch, Object storage |

**Architecture:**
```
Application → Log Shipper → Kafka → Log Processor → Elasticsearch
                                    ↓
                              Object Storage (S3)
                                    ↓
                              (Cold storage for older logs)
```

**Key Decisions:**
- **Shipping**: Lightweight agents on each server
- **Buffer**: Kafka for durability and backpressure
- **Indexing**: Elasticsearch for search
- **Retention**: Hot (ES), warm (ES with slower storage), cold (S3)
- **Search**: Kibana for visualization

---

### Question 6.3: Design Distributed Tracing (Jaeger/Zipkin)

| Category | Details |
|----------|---------|
| **Pattern** | Logging / Metrics |
| **Sub-pattern** | Request Tracing |
| **Primary Challenge** | Correlating spans across services, sampling strategies |
| **Read/Write Ratio** | Write-heavy |
| **Consistency Needs** | Eventual |
| **Real-time** | Near-real-time |
| **Key Technologies** | Span collectors, Trace storage, Sampling |

**Key Decisions:**
- **Trace context**: Propagate trace ID via headers
- **Sampling**: Head-based (sample at start) vs tail-based
- **Storage**: Cassandra for trace spans
- **UI**: Query by trace ID, service, duration

---

## 11. PATTERN 7 — Distributed Caching Systems

### Pattern Overview

| Attribute | Description |
|-----------|-------------|
| **Core Challenge** | Fast data access, consistency across nodes, high availability |
| **Typical Ratio** | Read-heavy |
| **Consistency** | Configurable (strong to eventual) |
| **Key Components** | Consistent hashing, Replication, Eviction |
| **Sub-patterns** | Key-Value Cache, CDN, Session Store |

---

### Question 7.1: Design Distributed Cache (Redis/Memcached)

| Category | Details |
|----------|---------|
| **Pattern** | Distributed Caching |
| **Sub-pattern** | In-Memory Key-Value Store |
| **Primary Challenge** | Data distribution, fault tolerance, eviction |
| **Read/Write Ratio** | Read-heavy |
| **Consistency Needs** | Configurable (eventual for scalability) |
| **Real-time** | Yes |
| **Key Technologies** | Consistent hashing, Replication, LRU/LFU |

**Key Decisions:**
- **Distribution**: Consistent hashing to minimize rehashing
- **Replication**: Master-slave for read scaling
- **Persistence**: Optional snapshot or AOF (Append-Only File)
- **Eviction**: LRU, LFU, TTL
- **Communication**: TCP with binary protocol

**Consistent Hashing:**
- Maps keys to nodes in a ring
- Adding/removing nodes only affects neighboring keys
- Virtual nodes for better distribution

---

### Question 7.2: Design CDN (Content Delivery Network)

| Category | Details |
|----------|---------|
| **Pattern** | Distributed Caching |
| **Sub-pattern** | Edge Caching |
| **Primary Challenge** | Geographic distribution, cache invalidation, origin offload |
| **Read/Write Ratio** | Read-heavy |
| **Consistency Needs** | Eventual |
| **Real-time** | Yes |
| **Key Technologies** | PoP (Points of Presence), Edge servers, Pull/Push strategies |

**Key Decisions:**
- **Pull CDN**: Edge fetches from origin on cache miss — simpler
- **Push CDN**: Content pushed to edge proactively — better control
- **Cache invalidation**: TTL, API-based purge
- **Routing**: GeoDNS to nearest PoP
- **SSL termination**: At edge for performance

---

### Question 7.3: Design Session Store

| Category | Details |
|----------|---------|
| **Pattern** | Distributed Caching |
| **Sub-pattern** | User Session Management |
| **Primary Challenge** | Session stickiness, expiration, security |
| **Read/Write Ratio** | Read and write on each request |
| **Consistency Needs** | Strong (user shouldn't be logged out unexpectedly) |
| **Real-time** | Yes |
| **Key Technologies** | Redis with TTL, JWT (alternative) |

**Key Decisions:**
- **Storage**: Redis with TTL for automatic expiration
- **Alternatives**: JWT (stateless but can't revoke)
- **Replication**: Redis Cluster for HA
- **Serialization**: JSON or protocol buffers

---

## 12. PATTERN 8 — Recommendation Systems

### Pattern Overview

| Attribute | Description |
|-----------|-------------|
| **Core Challenge** | Personalization, real-time updates, cold start problem |
| **Typical Ratio** | Read-heavy (recommendations) |
| **Consistency** | Eventual |
| **Key Components** | ML models, Feature store, Candidate generation |
| **Sub-patterns** | Collaborative Filtering, Content-Based, Hybrid |

---

### Question 8.1: Design Netflix Recommendation

| Category | Details |
|----------|---------|
| **Pattern** | Recommendation |
| **Sub-pattern** | Video Recommendations |
| **Primary Challenge** | Personalization at scale, new user cold start, diversity |
| **Read/Write Ratio** | Read-heavy |
| **Consistency Needs** | Eventual |
| **Real-time** | Near-real-time |
| **Key Technologies** | Matrix factorization, Spark, Cassandra, Feature store |

**Architecture:**
1. **Offline pipeline**: Train models daily (Spark, Hadoop)
2. **Nearline**: Real-time feature updates
3. **Online serving**: Pre-compute candidate sets
4. **Ranking**: ML model to rank candidates

**Key Decisions:**
- **Candidate generation**: Collaborative filtering, content-based, trending
- **Features**: Watch history, ratings, time of day, device
- **Cold start**: Popular content, demographic-based
- **Diversity**: Algorithm to avoid filter bubbles

---

### Question 8.2: Design YouTube Recommendation

| Category | Details |
|----------|---------|
| **Pattern** | Recommendation |
| **Sub-pattern** | Video Recommendations with Freshness |
| **Primary Challenge** | Balancing relevance with new content, watch time optimization |
| **Read/Write Ratio** | Read-heavy |
| **Consistency Needs** | Eventual |
| **Real-time** | Yes (new videos promoted) |
| **Key Technologies** | Two-stage model (candidate + ranking), TensorFlow, Bigtable |

**Key Decisions:**
- **Two-stage**: Generate hundreds of candidates → rank with deep model
- **Objective**: Optimize for watch time, not clicks
- **Freshness**: Boost recent videos
- **Exploration**: Some randomization to discover new content

---

### Question 8.3: Design Amazon Product Recommendation

| Category | Details |
|----------|---------|
| **Pattern** | Recommendation |
| **Sub-pattern** | "Customers who bought this also bought" |
| **Primary Challenge** | Item-to-item collaborative filtering at scale |
| **Read/Write Ratio** | Read-heavy |
| **Consistency Needs** | Eventual |
| **Real-time** | Near-real-time (after purchase) |
| **Key Technologies** | Item co-occurrence matrix, DynamoDB, Pre-computation |

**Key Decisions:**
- **Algorithm**: Item-based collaborative filtering
- **Pre-computation**: Daily job to compute item similarities
- **Real-time**: Update with recent purchases
- **Personalization**: Also consider user history
- **Storage**: Fast lookup of similar items

---

## 13. PATTERN 9 — Payment / Financial Systems

### Pattern Overview

| Attribute | Description |
|-----------|-------------|
| **Core Challenge** | Transaction integrity, no double-spending, audit trail |
| **Typical Ratio** | Write-heavy (transactions) |
| **Consistency** | Strong (ACID required) |
| **Key Components** | SQL databases, Idempotency keys, Ledger |
| **Sub-patterns** | Payment Gateway, Wallet, UPI |

---

### Question 9.1: Design Payment Gateway (Stripe/PayPal)

| Category | Details |
|----------|---------|
| **Pattern** | Payment / Financial |
| **Sub-pattern** | Payment Processing |
| **Primary Challenge** | Idempotency, handling failures, PCI compliance |
| **Read/Write Ratio** | Write-heavy |
| **Consistency Needs** | Strong (money must be accurate) |
| **Real-time** | Yes |
| **Key Technologies** | SQL with transactions, Idempotency keys, Event sourcing |

**Key Decisions:**
- **Idempotency**: Client-provided idempotency key to prevent duplicates
- **State machine**: Payment states (created, processing, success, failed)
- **Double-entry ledger**: Every transaction has debit and credit entries
- **External calls**: Idempotent retries with backoff
- **Reconciliation**: Daily batch to match with banks

**Payment Flow:**
```
Client → API Gateway → Payment Service → Ledger Service
                              ↓
                        Fraud Detection
                              ↓
                        Bank/Processor
                              ↓
                        Webhook to Client
```

---

### Question 9.2: Design Wallet System

| Category | Details |
|----------|---------|
| **Pattern** | Payment / Financial |
| **Sub-pattern** | Digital Wallet |
| **Primary Challenge** | Balance consistency, concurrent transactions |
| **Read/Write Ratio** | Write-heavy |
| **Consistency Needs** | Strong |
| **Real-time** | Yes |
| **Key Technologies** | SQL with row-level locks, Optimistic concurrency |

**Key Decisions:**
- **Balance storage**: Row in SQL DB with version number
- **Concurrency**: Optimistic locking (compare-and-swap)
- **Transaction log**: Immutable log of all transactions
- **Reconciliation**: Periodic balance verification

---

### Question 9.3: Design UPI-like System (Unified Payments Interface)

| Category | Details |
|----------|---------|
| **Pattern** | Payment / Financial |
| **Sub-pattern** | Real-Time Payment |
| **Primary Challenge** | High throughput, low latency, interoperability between banks |
| **Read/Write Ratio** | Write-heavy |
| **Consistency Needs** | Strong (two-phase commit across banks) |
| **Real-time** | Yes (seconds) |
| **Key Technologies** | Distributed transactions, Message queues, Idempotency |

**Key Decisions:**
- **Two-phase commit**: Prepare → Commit across banks
- **Fallback**: Compensation transactions on failure
- **Queue-based**: Async processing with idempotency
- **Settlement**: End-of-day net settlement between banks

---

## 14. PATTERN 10 — Large-Scale Processing

### Pattern Overview

| Attribute | Description |
|-----------|-------------|
| **Core Challenge** | Processing massive datasets efficiently |
| **Typical Ratio** | Batch or streaming writes |
| **Consistency** | Varies by use case |
| **Key Components** | MapReduce, Stream processors, Data warehouses |
| **Sub-patterns** | Batch Processing, Stream Processing, ETL |

---

### Question 10.1: Design Analytics System (Google Analytics)

| Category | Details |
|----------|---------|
| **Pattern** | Large-Scale Processing |
| **Sub-pattern** | Web Analytics |
| **Primary Challenge** | Tracking billions of events, real-time dashboards, drill-down queries |
| **Read/Write Ratio** | Write-heavy (events) |
| **Consistency Needs** | Eventual |
| **Real-time** | Real-time dashboard + batch reports |
| **Key Technologies** | Kafka, Spark Streaming, Druid, HDFS |

**Architecture:**
```
Website/App → Event Collector → Kafka
                                 ↓
                  ┌──────────────┴──────────────┐
                  ↓                              ↓
           Stream Processor                Batch Processor
           (Real-time counts)               (Deep analytics)
                  ↓                              ↓
              Real-time DB                   Data Lake
              (Druid/Redis)                   (HDFS/S3)
                  ↓                              ↓
            Real-time Dashboard              Reporting DB
                                              (BigQuery)
```

**Key Decisions:**
- **Ingestion**: HTTP endpoint with compression
- **Buffer**: Kafka for durability
- **Real-time**: Stream processing for dashboard counts
- **Batch**: Hadoop/Spark for daily aggregations
- **Storage**: Columnar format (Parquet) for analytics

---

### Question 10.2: Design Clickstream Processing

| Category | Details |
|----------|---------|
| **Pattern** | Large-Scale Processing |
| **Sub-pattern** | User Behavior Pipeline |
| **Primary Challenge** | Sessionization, funnel analysis, user journey |
| **Read/Write Ratio** | Write-heavy |
| **Consistency Needs** | Eventual |
| **Real-time** | Near-real-time |
| **Key Technologies** | Kafka, Flink, Session windowing |

**Key Decisions:**
- **Session definition**: Timeout (30 min inactivity)
- **Processing**: Flink with session windows
- **Storage**: Clickhouse for fast queries
- **Output**: User behavior tables, funnels

---

### Question 10.3: Design Big Data Pipeline (ETL)

| Category | Details |
|----------|---------|
| **Pattern** | Large-Scale Processing |
| **Sub-pattern** | ETL Pipeline |
| **Primary Challenge** | Transforming raw data into structured formats, scheduling dependencies |
| **Read/Write Ratio** | Write-heavy |
| **Consistency Needs** | Strong (data quality) |
| **Real-time** | Batch (daily/hourly) |
| **Key Technologies** | Airflow, Spark, Data warehouse, Data lake |

**Key Decisions:**
- **Orchestration**: Airflow for DAG management
- **Processing**: Spark for transformations
- **Storage**: Data lake (raw) → Data warehouse (structured)
- **Data quality**: Validation checks at each stage
- **Lineage**: Track data origins

---

### Question 10.4: Design Kafka-like System

| Category | Details |
|----------|---------|
| **Pattern** | Large-Scale Processing |
| **Sub-pattern** | Distributed Messaging System |
| **Primary Challenge** | Durable, ordered message storage, high throughput |
| **Read/Write Ratio** | Write and read at scale |
| **Consistency Needs** | Configurable (acks=all for strong) |
| **Real-time** | Yes |
| **Key Technologies** | Distributed commit log, Partitioning, Replication |

**Key Decisions:**
- **Storage**: Append-only log on disk
- **Partitioning**: Topic partitioned for parallelism
- **Replication**: Leader-follower per partition
- **Consumers**: Track offset per consumer group
- **Durability**: Sync to disk (configurable)

---

## 15. PATTERN 11 — Geospatial / Location-Based Systems

### Pattern Overview

| Attribute | Description |
|-----------|-------------|
| **Core Challenge** | Efficient spatial queries, real-time location updates |
| **Typical Ratio** | Write-heavy (location updates) |
| **Consistency** | Eventual (location can be slightly stale) |
| **Key Components** | Spatial indexing (Geohash, Quadtree), Real-time matching |
| **Sub-patterns** | Ride-sharing, Nearby search, Fleet tracking |

---

### Question 11.1: Design Uber / Lyft

| Category | Details |
|----------|---------|
| **Pattern** | Geospatial |
| **Sub-pattern** | Ride-Sharing |
| **Primary Challenge** | Matching riders with nearby drivers, real-time location updates, state management |
| **Read/Write Ratio** | Write-heavy (location pings), read-spiky (match requests) |
| **Consistency Needs** | Eventual for location, strong for trip state |
| **Real-time** | Yes |
| **Key Technologies** | Geohash, Redis, Kafka, State machines |

**Key Decisions:**
- **Location indexing**: Geohash or Quadtree to find nearby drivers
- **Matching**: Pub/sub to geohash cells
- **State management**: Trip states (requesting, matched, en-route, completed)
- **Surge pricing**: Real-time demand calculation
- **ETA calculation**: Routing engine with traffic data

**Geohash:**
- Encodes lat/long into string
- Longer prefix = higher precision
- Nearby points share prefix
- Query by prefix + neighbor cells

---

### Question 11.2: Design Google Maps / Nearby Places

| Category | Details |
|----------|---------|
| **Pattern** | Geospatial |
| **Sub-pattern** | Proximity Search |
| **Primary Challenge** | Finding places within radius, efficient spatial queries |
| **Read/Write Ratio** | Read-heavy |
| **Consistency Needs** | Eventual |
| **Real-time** | Yes |
| **Key Technologies** | Geohash, Spatial databases (PostGIS), Quadtrees |

**Key Decisions:**
- **Indexing**: Geohash + inverted index for categories
- **Query**: Find geohash cells covering radius
- **Ranking**: Distance + relevance + popularity
- **Storage**: DynamoDB with geohash as partition key

---

### Question 11.3: Design Food Delivery App (DoorDash/Zomato)

| Category | Details |
|----------|---------|
| **Pattern** | Geospatial |
| **Sub-pattern** | Logistics + Discovery |
| **Primary Challenge** | Restaurant discovery, delivery tracking, assignment optimization |
| **Read/Write Ratio** | Read-heavy (browsing), write-spiky (orders) |
| **Consistency Needs** | Strong for orders, eventual for location |
| **Real-time** | Yes |
| **Key Technologies** | Geohash, Assignment service, Routing engine |

**Key Decisions:**
- **Discovery**: Geohash-based restaurant search
- **Assignment**: Nearest driver algorithm with capacity
- **Tracking**: Real-time location updates
- **Routing**: Optimized delivery routes

---

### Question 11.4: Design Nearby Friends Feature

| Category | Details |
|----------|---------|
| **Pattern** | Geospatial |
| **Sub-pattern** | Real-Time Location Sharing |
| **Primary Challenge** | Privacy, real-time updates, efficient proximity detection |
| **Read/Write Ratio** | Write-heavy (location pings) |
| **Consistency Needs** | Eventual |
| **Real-time** | Yes |
| **Key Technologies** | Geohash, WebSockets, Redis |

**Key Decisions:**
- **Privacy**: User controls who sees location
- **Frequency**: Adaptive update frequency based on movement
- **Detection**: Check if friends in same geohash cell
- **Storage**: Redis with TTL for current location

---

## 16. PATTERN 12 — Collaborative Systems

### Pattern Overview

| Attribute | Description |
|-----------|-------------|
| **Core Challenge** | Concurrent editing, conflict resolution, real-time sync |
| **Typical Ratio** | Mixed reads and writes |
| **Consistency** | Strong (eventual with conflict resolution) |
| **Key Components** | CRDTs, Operational Transformation, WebSockets |
| **Sub-patterns** | Collaborative Editing, Whiteboard |

---

### Question 12.1: Design Google Docs

| Category | Details |
|----------|---------|
| **Pattern** | Collaborative Systems |
| **Sub-pattern** | Real-Time Collaborative Editor |
| **Primary Challenge** | Concurrent edits, conflict resolution, cursor presence |
| **Read/Write Ratio** | Write-heavy (keystrokes) |
| **Consistency Needs** | Strong convergence (all users see same document) |
| **Real-time** | Yes |
| **Key Technologies** | Operational Transformation (OT) or CRDTs, WebSockets, Version vectors |

**Key Decisions:**
- **Approach**: OT (Operational Transformation) or CRDT (Conflict-Free Replicated Data Type)
- **Operations**: Insert, delete, format
- **Transformation**: Transform concurrent ops to apply in any order
- **Presence**: Cursor positions broadcast via WebSockets
- **Storage**: Append-only op log + snapshot

**OT vs CRDT:**

| Approach | How It Works | Pros | Cons |
|----------|--------------|------|------|
| **OT** | Transform operations based on concurrent ops | Mature, used in Google Docs | Complex transformation logic |
| **CRDT** | Data structure that converges automatically | Mathematically sound | Higher overhead |

---

### Question 12.2: Design Figma / Collaborative Whiteboard

| Category | Details |
|----------|---------|
| **Pattern** | Collaborative Systems |
| **Sub-pattern** | Visual Collaboration |
| **Primary Challenge** | Vector graphics sync, low latency, performance |
| **Read/Write Ratio** | Write-heavy |
| **Consistency Needs** | Strong convergence |
| **Real-time** | Yes |
| **Key Technologies** | CRDTs, WebSockets, Canvas rendering |

**Key Decisions:**
- **Data model**: Scene graph of objects
- **Operations**: Move, resize, change properties
- **Rendering**: Client-side canvas rendering
- **Optimization**: Throttle updates, batch operations

---

## 17. PATTERN 13 — Infrastructure / System Utilities

### Pattern Overview

| Attribute | Description |
|-----------|-------------|
| **Core Challenge** | Building foundational systems that other services depend on |
| **Typical Ratio** | Varies |
| **Consistency** | Often strong |
| **Key Components** | Distributed coordination, Leader election |
| **Sub-patterns** | Job Scheduler, Distributed Lock, ID Generator |

---

### Question 13.1: Design Distributed Job Scheduler

| Category | Details |
|----------|---------|
| **Pattern** | Infrastructure |
| **Sub-pattern** | Cron at Scale |
| **Primary Challenge** | Exactly-once execution, fault tolerance, time accuracy |
| **Read/Write Ratio** | Write on schedule, read on execution |
| **Consistency Needs** | Strong (job must run exactly once) |
| **Real-time** | Scheduled |
| **Key Technologies** | Leader election (ZooKeeper), Distributed locks, Quartz |

**Key Decisions:**
- **Leader election**: One scheduler active, others standby
- **Job storage**: SQL with row locks
- **Missed jobs**: Catch-up logic
- **Distributed execution**: Workers pick jobs from queue
- **Idempotency**: Jobs designed to be idempotent

---

### Question 13.2: Design Distributed Locking Service

| Category | Details |
|----------|---------|
| **Pattern** | Infrastructure |
| **Sub-pattern** | Distributed Coordination |
| **Primary Challenge** | Deadlock prevention, fault tolerance |
| **Read/Write Ratio** | Write on lock/unlock |
| **Consistency Needs** | Strong |
| **Real-time** | Yes |
| **Key Technologies** | Redis Redlock, ZooKeeper, Chubby |

**Key Decisions:**
- **Algorithm**: Redis Redlock or ZooKeeper sequential znodes
- **Lease**: Locks with TTL to handle holder failure
- **Fencing tokens**: Prevent stale lock holders
- **Reentrancy**: Same client can lock multiple times

---

### Question 13.3: Design Unique ID Generator (Snowflake)

| Category | Details |
|----------|---------|
| **Pattern** | Infrastructure |
| **Sub-pattern** | Distributed ID Generation |
| **Primary Challenge** | Unique, time-sortable, high throughput |
| **Read/Write Ratio** | Write (generate ID per request) |
| **Consistency Needs** | Strong (no duplicates) |
| **Real-time** | Yes |
| **Key Technologies** | Snowflake algorithm, ZooKeeper for worker IDs |

**Snowflake ID Structure (64-bit):**
```
| Timestamp (41 bits) | Worker ID (10 bits) | Sequence (12 bits) |
```

**Key Decisions:**
- **Timestamp**: Milliseconds since custom epoch
- **Worker ID**: Unique per generator (via ZooKeeper)
- **Sequence**: Increments within same millisecond
- **Synchronization**: NTP for time sync

---

## 18. PATTERN 14 — Web Crawler / Search Indexing

### Pattern Overview

| Attribute | Description |
|-----------|-------------|
| **Core Challenge** | Polite crawling, deduplication, scale |
| **Typical Ratio** | Write-heavy (download pages) |
| **Consistency** | Eventual |
| **Key Components** | URL frontier, Deduplication, Rate limiting |
| **Sub-patterns** | Web Crawler, Focused Crawler |

---

### Question 14.1: Design Web Crawler

| Category | Details |
|----------|---------|
| **Pattern** | Web Crawler |
| **Sub-pattern** | Distributed Crawling |
| **Primary Challenge** | Politeness (don't hammer sites), deduplication, freshness |
| **Read/Write Ratio** | Write-heavy (downloading) |
| **Consistency Needs** | Eventual |
| **Real-time** | No (batch) |
| **Key Technologies** | URL frontier (priority queue), Bloom filters, Robots.txt parser |

**Architecture:**
```
Seed URLs → URL Frontier (Priority Queue)
                ↓
         Fetch Service
                ↓
        Parse & Extract
                ↓
         Content Store
                ↓
       URL Deduplication (Bloom Filter)
                ↓
         New URLs to Frontier
```

**Key Decisions:**
- **Politeness**: Delay between requests to same domain
- **Deduplication**: Bloom filter for URL seen test
- **Prioritization**: Crawl important pages first (PageRank)
- **Freshness**: Re-crawl based on change frequency
- **Robots.txt**: Respect crawling rules

---

## 19. PHASE 4 — 8-Week Execution Plan

### 19.1 Week-by-Week Breakdown

| Week | Focus | Key Activities |
|------|-------|----------------|
| **Week 1-2** | Core Fundamentals | Study networking, databases, CAP theorem, caching, messaging |
| **Week 3** | CRUD + Storage Systems | Practice URL Shortener, Pastebin, Dropbox, Ticket Booking |
| **Week 4** | Feed Systems | Practice Twitter, Instagram, Facebook Feed, LinkedIn |
| **Week 5** | Real-Time + Search | Practice WhatsApp, Discord, Google Search, Autocomplete |
| **Week 6** | Rate Limiter + Caching + Logging | Practice Rate Limiter, Distributed Cache, Metrics System |
| **Week 7** | Payments + Recommendation | Practice Payment Gateway, Netflix Recs, Uber |
| **Week 8** | Mock Interviews | Full mock interviews with feedback |

### 19.2 Daily Practice Routine

| Time | Activity |
|------|----------|
| **30 min** | Review one concept (caching strategies, consistency models) |
| **1 hour** | Solve one design problem using the 6-step framework |
| **15 min** | Document in tracker (what went well, what to improve) |
| **Weekly** | One full mock interview with peer |

---

## 20. How to Practice (Like Your DP Excel Tracker)

Since you love structured tracking, create this Excel/Notion tracker:

### 20.1 HLD Practice Tracker Template

| Question | Pattern | Sub-pattern | Primary Challenge | Read/Write | Consistency | Attempt 1 Date | Bottlenecks Missed | Key Improvements | Status |
|----------|---------|-------------|-------------------|------------|-------------|----------------|-------------------|-------------------|--------|
| Design Twitter | Feed | Social Media | Celebrity fan-out | Heavy Read | Eventual | 2024-01-15 | Hotkey partitioning | Add hybrid fan-out | 🟡 |
| Design WhatsApp | Real-Time | Messaging | Message ordering | Write-heavy | Strong ordering | 2024-01-16 | Last seen scaling | Redis with TTL | 🟢 |

**Color Coding:**
- 🟢 **Green** = Confident (can explain trade-offs, handled follow-ups well)
- 🟡 **Yellow** = Needs improvement (missed key bottlenecks)
- 🔴 **Red** = Weak (struggled with fundamentals)

---

## 21. How to Think Like Top Engineers

### 21.1 The 5 Critical Questions to Always Ask

1. **"Where does this break?"** — Identify single points of failure
2. **"What if traffic becomes 10x?"** — Think about scaling bottlenecks
3. **"What if one region fails?"** — Consider disaster recovery
4. **"What if DB crashes?"** — Plan for data layer failures
5. **"What is the biggest bottleneck?"** — Prioritize improvements

### 21.2 Trade-off Mindset

At every decision point, ask:
- "Why X over Y?"
- "What do we gain? What do we lose?"
- "Under what conditions would the other choice be better?"

---

## 22. Top 20 Must-Do Questions (Most Frequently Asked)

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

## 23. Advanced (Staff-Level) Topics

Once you're comfortable with standard designs, dive into these advanced topics:

### 23.1 Multi-Region Architecture

| Pattern | Description | Trade-offs |
|---------|-------------|------------|
| **Active-Passive** | One region active, other on standby | Simple, but failover takes time |
| **Active-Active** | All regions serving traffic | Complex, needs global consensus |
| **Geo-sharding** | Data partitioned by region | Reduced latency, compliance |

### 23.2 Data Migration Strategies

| Strategy | When to Use |
|----------|-------------|
| **Blue-Green Deployment** | Zero-downtime migrations |
| **Parallel Runs** | Validate new system before cutover |
| **Gradual Rollout** | Migrate users in batches |
| **Backfill** | Migrate historical data after cutover |

### 23.3 Schema Evolution

| Technique | Description |
|-----------|-------------|
| **Backward Compatible** | New code can read old data |
| **Forward Compatible** | Old code can read new data |
| **Versioning** | Store schema version with data |
| **Dual Writes** | Write to both old and new schemas during migration |

### 23.4 Consistent Hashing Internals

- Virtual nodes for better distribution
- Replication factor for fault tolerance
- Read repair for consistency

### 23.5 Leader Election Algorithms

| Algorithm | How It Works |
|-----------|--------------|
| **Bully Algorithm** | Highest ID node becomes leader |
| **Raft** | Election terms, majority vote |
| **Paxos** | Complex, used in Chubby/ZooKeeper |
| **ZooKeeper** | Sequential znodes for leader election |

### 23.6 Consensus Protocols

- **Paxos**: Classic but complex
- **Raft**: Understandable, used in etcd
- **Zab**: ZooKeeper's protocol

### 23.7 Advanced CAP Scenarios

- **Network partition handling** — What does your system do?
- **Tunable consistency** — Cassandra's consistency levels
- **Quorum-based systems** — Read/write quorums

---

## 24. Complete Resource Guide

### 24.1 Books (Must Read)

| Book | Why |
|------|-----|
| **Designing Data-Intensive Applications** | The bible — read chapters on replication, partitioning, consistency |
| **System Design Interview — An Insider's Guide (Vol 1)** | Best practical introduction |
| **System Design Interview — An Insider's Guide (Vol 2)** | More advanced problems |
| **Building Microservices** | Microservices architecture patterns |
| **Database Internals** | Deep dive into storage engines |

### 24.2 Online Courses

| Course | Platform |
|--------|----------|
| **Grokking the System Design Interview** | DesignGurus |
| **System Design Interview** | Exponent |
| **Hello Interview** | HelloInterview (with AI mock interviews) |

### 24.3 YouTube Channels

| Channel | Best For |
|---------|----------|
| **Gaurav Sen** | System design concepts |
| **CodeKarle** | Detailed walkthroughs |
| **System Design Interview** | Mock interviews |
| **Hussein Nasser** | Networking and databases |

### 24.4 Blogs and Websites

| Site | Content |
|------|---------|
| **High Scalability** | Real-world architecture case studies |
| **The Architect Elevator** | Architecture patterns |
| **Martin Fowler's Blog** | Microservices, architecture |
| **AWS Architecture Blog** | Cloud-native patterns |

### 24.5 Practice Platforms

| Platform | Features |
|----------|----------|
| **Pramp** | Free peer mock interviews |
| **Interviewing.io** | Anonymous mock interviews with engineers |
| **HelloInterview** | AI-powered mock interviews |

---

## 25. Final Advice for You Specifically

Since you're:

- ✅ Strong in pattern-based learning (DSA + DP)
- ✅ Structured in tracking progress
- ✅ Preparing seriously for interviews

### 25.1 Your Success Formula

1. **Treat HLD exactly like DP** — Identify patterns, solve 3-5 per pattern, review trade-offs, repeat until fluent

2. **Use your tracker religiously** — Document every attempt, what you missed, and improvements

3. **Focus on trade-offs** — The interviewer cares more about *why* you chose something than *what* you chose

4. **Practice vocalizing** — Design is collaborative; practice explaining your thinking out loud

5. **Mock interviews are non-negotiable** — At least 5 full mocks before the real interview

### 25.2 Common Pitfalls to Avoid

| Pitfall | How to Avoid |
|---------|--------------|
| Jumping straight to design | Always clarify requirements first |
| Ignoring non-functional requirements | Ask about scale, latency, consistency |
| Memorizing solutions | Learn patterns, not specific designs |
| Not identifying bottlenecks | Always ask "what breaks?" |
| One-size-fits-all database | Justify SQL vs NoSQL choice |

---

## 26. Quick Reference: HLD Interview Checklist

### 26.1 Pre-Interview Checklist

- [ ] Review core concepts (CAP, caching, databases)
- [ ] Refresh 5 most common patterns
- [ ] Practice 1 design problem (timed)
- [ ] Prepare questions for interviewer

### 26.2 During Interview Checklist

- [ ] Step 1: Clarify requirements (5 min)
- [ ] Step 2: Estimate scale (5 min)
- [ ] Step 3: Design APIs (5 min)
- [ ] Step 4: High-level diagram (10 min)
- [ ] Step 5: Deep dive (15 min)
- [ ] Step 6: Trade-offs and bottlenecks (5 min)

### 26.3 Post-Interview Reflection

- [ ] What requirements did I miss?
- [ ] What bottlenecks did I overlook?
- [ ] What trade-offs could I have discussed better?
- [ ] Update tracker with learnings

---

## 27. Your Next Steps

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

## 28. Master Table: The Nine Pillars of High-Level Design

A single reference table covering Scalability, Availability, Durability, Security, Consistency, Fault Tolerance, Reliability, Maintainability, and Cost Efficiency — their definitions, key techniques, metrics, anti-patterns, and typical scenarios.

| Pillar | Definition (The question it answers) | Key Techniques | Typical Metrics | Common Anti‑patterns | Typical Scenario |
|--------|--------------------------------------|----------------|-----------------|----------------------|------------------|
| **Scalability** | Can the system handle more load (traffic, data, complexity) by adding resources? | Horizontal scaling, load balancing, caching, read replicas, sharding, auto‑scaling, asynchronous processing | Requests per second, throughput, data volume per node, scaling time | Premature sharding, ignoring statelessness, single database for all reads/writes | E‑commerce flash sale: 10k → 1M users in 1 hour |
| **Availability** | Is the system up and reachable when users need it? | Redundancy (active‑active/passive), load balancer health checks, failover/leader election, multi‑AZ/region, circuit breakers | Uptime % (99.9%, 99.99%), MTBF, MTTR, error rate | No health checks, shared state across regions, infinite retries | Banking login: must work 24/7, even if one data center loses power |
| **Durability** | Once data is written, will it survive permanently despite crashes or corruption? | Write‑ahead logging (WAL), synchronous/asynchronous replication, distributed consensus (Raft), checksums, erasure coding, backups (PITR) | Probability of data loss (e.g., 1×10⁻¹²), backup recovery time, replication lag | Relying only on RAID, disabling fsync, not verifying backups | Bank account balance: cannot lose a committed transaction even if all servers crash |
| **Security** | Is the system protected from unauthorized access, tampering, or data breaches? | Authentication (MFA, OAuth2), authorization (RBAC/ABAC), encryption at rest & in transit (TLS), WAF, rate limiting, secrets manager, audit logging | Time to detect (MTTD), time to respond (MTTR), compliance (SOC2, HIPAA), number of vulnerabilities | Hardcoding secrets, no MFA, trusting the internal network, rolling your own crypto | Payment gateway: prevent credit card theft and comply with PCI‑DSS |
| **Consistency** | Do all readers see the same data at the same time, or in a predictable order? | Quorum (W+R>N), leader‑based replication, distributed consensus (Raft), idempotency keys, CRDTs, read‑repair | Staleness (seconds), read‑after‑write time, conflict rate, linearizability guarantee | Ignoring replication lag, using LWW for counters, assuming strong consistency for free | Social media like count: temporary inconsistency is fine, but never lose a vote |
| **Fault Tolerance** | Can the system continue operating correctly (with zero perceived interruption) when components fail? | Active‑active redundancy, leader election with fast failover, checkpointing, retries & idempotency, bulkheading, circuit breaker, hedged requests | Recovery time objective (RTO), recovery point objective (RPO), failure detection time, user‑visible error rate | No health checks, shared thread pools, no circuit breakers, assuming failover is instantaneous | Google Search: a web server crashes → user sees no delay, no error |
| **Reliability** | How long can the system run without failure (probability of error‑free operation over time)? | High‑MTBF components, redundant hardware, timeouts & retries, chaos engineering, graceful degradation, monitoring & alerting | MTBF, MTTR, error rate, SLO/SLA attainment, failure rate over time | No retries, retry storms, no monitoring, only testing happy path | Mars rover: must function for years without physical repair |
| **Maintainability** | How easy is it to change, fix, or extend the system safely? | Modular architecture (microservices), clean APIs & versioning, CI/CD, feature toggles, observability (logs/metrics/traces), IaC, coding standards, ADRs | Lead time for change, deployment frequency, time to restore service, code churn rate | Big bang releases, no automated tests, copy‑pasted code, undocumented decisions | Large team of 50 engineers: each can deploy independently without breaking others |
| **Cost Efficiency** | Does the system deliver required performance at the lowest possible financial cost? | Right‑sizing, auto‑scaling (scale‑to‑zero), spot/preemptible instances, reserved capacity, storage tiering, serverless, compression, multi‑region cost‑aware placement | $ per request, $ per GB stored, idle resource percentage, total monthly cloud bill | Over‑provisioning, leaving dev environments on 24/7, not using spot for batch jobs, storing everything in hot tier | Startup MVP: keep costs below $100/month while handling occasional traffic spikes |

## 29. Non-Functional Requirements Glossary

The non-functional requirements ('system qualities') to explicitly define and address in any HLD interview, beyond scalability and availability alone.

| **Aspect** | **What It Means** | **Typical Target / Example** |
|------------|------------------|------------------------------|
| **Low Latency** | Time to serve a request (e.g., redirect). | < 50 ms for reads; < 200 ms for writes. |
| **High Availability** | System uptime despite failures. | 99.99% (four nines) or 99.999% (five nines) availability. |
| **Scalability** | Ability to handle growth in traffic / data. | Horizontal scaling; support millions of new URLs and billions of redirects. |
| **Durability** | Data persistence without loss. | No loss of stored mappings; replication and backups. |
| **Security** | Protection against abuse, spam, malicious URLs. | Rate limiting, malicious URL detection, authentication. |
| **Consistency** | Data correctness across replicas (if using distributed databases). | Often eventual consistency is acceptable for reads, but strong consistency may be needed for unique short code generation. |
| **Fault Tolerance** | System continues operating when components fail. | Redundancy, failover, graceful degradation. |
| **Reliability** | System performs its intended function correctly over time. | Mean time between failures (MTBF) high; minimal errors. |
| **Maintainability** | Ease of making changes, adding features, fixing bugs. | Modular architecture, clear APIs, monitoring, logging. |
| **Cost Efficiency** | Optimizing resource usage (compute, storage, network) to keep costs reasonable. | Use caching to reduce DB load; auto-scaling to match demand. |

## 30. Recommended Study Order

Move from foundational bricks, to architectural blueprints, to case studies — in this order — so you understand *why* choices are made rather than memorizing answers.

### 30.1 Foundations: The "Bricks" 🧱

Before designing a system, you must understand the individual components. This is the **High-Availability (Pillar 4)** phase. You’ll learn how to store and route data reliably.

* **Key Concepts:** Load Balancing, Caching, Scaling (Vertical vs. Horizontal), and CDNs.
* **The Math:** Consistent Hashing and Back-of-the-envelope estimations.

### 30.2 Communication & Protocols: The "Glue" 📡

Next, you learn how those bricks talk to each other. This bridges **Write-Heavy (Pillar 1)** and **Read-Heavy (Pillar 2)** logic.

* **Key Concepts:** REST vs. GraphQL, WebSockets vs. Long Polling, and gRPC.
* **Message Queues:** Understanding how to decouple systems using tools like Kafka or RabbitMQ.

### 30.3 Data Mastery: The "Memory" 💾

This is where you tackle **ACID/Transactions (Pillar 3)**. You need to know which database to pick for which problem.

* **Key Concepts:** SQL vs. NoSQL, CAP Theorem, Database Sharding, and Replication (Leader-Follower).
* **Indexes:** How B-Trees and LSM Trees work under the hood.

### 30.4 Architectural Patterns: The "Blueprints" 🏗️

Once you know the components, you learn the standard ways they are combined.

* **Patterns:** Microservices vs. Monoliths, Event-Driven Architecture, and Fan-out patterns.

### 30.5 System Case Studies: The "Practice" 🏆

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

## 31. The 120-Day Day-by-Day Mastery Schedule

A day-by-day execution schedule (as an alternative to the 8-week plan above) for turning pattern knowledge into interview-ready fluency over four months.

### 31.1 From Pattern-Based Thinker to System Design Expert

You already have the right mindset – treating HLD as a set of recurring patterns, tracking progress, and iterating. This 120-day roadmap is designed to turn you into a top-tier system design candidate for FAANG and other product-based companies. It combines foundational knowledge, pattern-based practice, mock interviews, and continuous refinement.

---

### 31.2 How to Use This Roadmap

- **Follow the phases sequentially** – each builds on the previous.
- **Track your progress** using the Excel/Notion template provided.
- **Apply the 6-step interview framework** to every practice problem.
- **Color-code your confidence** after each attempt: Green (solid), Yellow (needs work), Red (weak). Revisit reds after 2 weeks.
- **Schedule weekly reviews** – what you learned, what to improve.

---

### 31.3 Phase 1: Foundation Building (Days 1–30)

#### Objective: Master the building blocks – networking, scaling, databases, caching, messaging, and core distributed systems concepts.

#### Week 1–2: Core Concepts & Networking
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

### 31.4 Phase 2: Core Patterns (Days 31–60)

#### Objective: Solve 2–3 problems from each core pattern, applying the 6-step framework.

#### Week 5–6: Pattern A – CRUD & Storage Systems
| Day | Question | Pattern | Focus |
|-----|----------|---------|-------|
| 31-33 | URL Shortener (TinyURL) | Key-Value Store | Hashing, key generation, redirection, caching |
| 34-36 | Pastebin | Key-Value + Expiry | Large text storage, expiry, syntax highlighting |
| 37-39 | Online Bookstore | E-commerce CRUD | Product catalog, search, inventory, sharding |
| 40-42 | Ticket Booking (BookMyShow) | High-concurrency booking | Seat locking, optimistic/pessimistic concurrency, transaction isolation |
| 43-45 | Dropbox / Google Drive | File storage & sync | Chunking, deduplication, delta sync, conflict resolution |
| 46   | Review & mock | Pick one and do a full 45-min timed mock | Self-evaluate |

#### Week 7–8: Pattern B – Feed / Timeline Systems
| Day | Question | Sub-pattern | Focus |
|-----|----------|-------------|-------|
| 47-49 | Twitter / X | Social Media Feed | Fan-out (push/pull/hybrid), timeline caching, celebrity problem |
| 50-52 | Instagram Feed | Media-heavy feed | Image storage (CDN), feed ranking, push fan-out |
| 53-55 | Facebook News Feed | Algorithmic feed | Ranking (affinity, weight, time decay), diverse content types, ML integration |
| 56-58 | LinkedIn Feed | Professional network feed | Feed from 2nd/3rd degree connections, relevance scoring, pull-based |
| 59-60 | Review & mock | Timed mock on a feed system | Evaluate bottlenecks & trade-offs |

#### Week 9–10: Pattern C – Real-Time Systems
| Day | Question | Sub-pattern | Focus |
|-----|----------|-------------|-------|
| 61-63 | WhatsApp / Messenger | 1:1 and group chat | WebSockets, message ordering, presence, read receipts, multi-device sync |
| 64-66 | Slack / Discord | Group chat with channels | Channels, voice (WebRTC, SFU), permissions, unread counts |
| 67-69 | Live Comments (YouTube Live) | High-volume live updates | Kafka for ingestion, fan-out via Redis streams, rate limiting |
| 70-72 | Multiplayer Game Backend | Game state sync | UDP vs TCP, server-authoritative, client prediction, room management |
| 73-75 | Review & mock | Timed mock on real-time system | Focus on connection handling and scalability |

#### Week 11–12: Pattern D – Search Systems
| Day | Question | Sub-pattern | Focus |
|-----|----------|-------------|-------|
| 76-78 | Google Search | Web-scale search | Crawling, inverted index, PageRank, sharding, caching |
| 79-81 | YouTube Search | Video search | Indexing metadata & transcripts, ranking by engagement |
| 82-84 | Amazon Product Search | E-commerce search | Faceted search, inventory-aware results, synonyms |
| 85-87 | Search Autocomplete | Typeahead | Trie, frequency-based ranking, personalization, real-time updates |
| 88-90 | Review & mock | Pick a search problem and do full mock | Evaluate ranking and latency trade-offs |

---

### 31.5 Phase 3: Advanced & Specialized Patterns (Days 61–90)

#### Objective: Tackle more complex patterns, including geospatial, financial, infrastructure, and large-scale processing.

#### Week 13–14: Pattern E – Geospatial & Location-Based
| Day | Question | Sub-pattern | Focus |
|-----|----------|-------------|-------|
| 91-93 | Uber / Lyft | Ride-sharing | Geohashing/quadtree, real-time matching, state management, surge pricing |
| 94-96 | Google Maps / Nearby Places | Proximity search | Spatial indexing (Geohash, PostGIS), ranking (distance + popularity) |
| 97-99 | Food Delivery (DoorDash/Zomato) | Logistics + Discovery | Restaurant discovery, delivery tracking, assignment optimization |
| 100-102 | Nearby Friends | Real-time location sharing | Privacy, update frequency, geohash-based proximity detection |
| 103-105 | Review & mock | Timed mock on Uber or similar | Focus on location indexing and real-time matching |

#### Week 15–16: Pattern F – Financial & Critical Consistency
| Day | Question | Sub-pattern | Focus |
|-----|----------|-------------|-------|
| 106-108 | Payment Gateway (Stripe/PayPal) | Payment processing | Idempotency keys, double-entry ledger, state machine, external calls |
| 109-111 | Wallet System | Digital wallet | Balance consistency (optimistic locking), transaction log, reconciliation |
| 112-114 | UPI-like System | Real-time payment | Two-phase commit across banks, distributed transactions, idempotency |
| 115-117 | Distributed Job Scheduler | Infrastructure | Leader election (ZooKeeper), cron at scale, exactly-once execution |
| 118-120 | Review & mock | Mock on payment system | Focus on ACID, concurrency, and idempotency |

#### Week 17–18: Pattern G – Large-Scale Processing & Infrastructure
| Day | Question | Sub-pattern | Focus |
|-----|----------|-------------|-------|
| 121-123 | Google Analytics | Web analytics | Event ingestion (Kafka), stream processing (Flink), batch (Spark), storage (Druid, BigQuery) |
| 124-126 | Clickstream Processing | User behavior pipeline | Sessionization, funnels, real-time dashboards |
| 127-129 | Distributed Logging (Splunk/ELK) | Log aggregation | Log shippers, Kafka buffer, Elasticsearch indexing, hot-warm-cold storage |
| 130-132 | Metrics Monitoring (Prometheus/Datadog) | Time-series metrics | Ingestion, downsampling, query language, alerting |
| 133-135 | Design Kafka-like System | Distributed messaging | Partitioning, replication, consumer offsets, durability |
| 136-138 | Review & mock | Mock on logging or analytics | Focus on high-throughput ingestion and query efficiency |

#### Week 19–20: Pattern H – Collaborative & Infrastructure Utilities
| Day | Question | Sub-pattern | Focus |
|-----|----------|-------------|-------|
| 139-141 | Google Docs | Collaborative editing | Operational Transformation vs CRDT, WebSockets, presence, version vectors |
| 142-144 | Figma / Collaborative Whiteboard | Visual collaboration | Scene graph, CRDTs, rendering optimization |
| 145-147 | Distributed Locking Service | Coordination | Redis Redlock, ZooKeeper, fencing tokens, deadlock prevention |
| 148-150 | Unique ID Generator (Snowflake) | ID generation | Snowflake algorithm, worker coordination, clock synchronization |
| 151-153 | Web Crawler | Crawling | URL frontier, politeness, deduplication (Bloom filter), robots.txt |
| 154-156 | Review & mock | Mock on collaborative or infrastructure tool | Focus on consistency and fault tolerance |

---

### 31.6 Phase 4: Integration, Mock Interviews & Deep Dives (Days 91–120)

#### Objective: Polish your skills, identify weak areas, and simulate real interview conditions.

#### Week 21–22: Advanced Topics & System Integration
| Day | Topic | Activities |
|-----|-------|------------|
| 157-160 | Multi-region architecture | Active-passive vs active-active, geo-sharding, cross-region replication |
| 161-164 | Data migration & schema evolution | Blue-green, parallel runs, backfill, backward/forward compatibility |
| 165-168 | Consensus algorithms | Raft, Paxos, ZooKeeper – understand leader election and replication |
| 169-172 | Real-world case studies | Study architectures of Netflix, Uber, WhatsApp, etc. (High Scalability blog) |
| 173-176 | Revisit weak patterns | Pick your red/yellow patterns and solve 2 more problems each |

#### Week 23–24: Mock Interview Marathon
| Day | Activity |
|-----|----------|
| 177-180 | Full mock interviews (1 per day) – use Pramp, interviewing.io, or peer |
| 181-183 | Analyze recordings of your mocks; note improvements |
| 184-186 | Deep dive on specific bottlenecks you missed |
| 187-189 | Practice articulating trade-offs without a diagram |
| 190-192 | Final review of all patterns and core concepts |

#### Week 25–26: Buffer & Fine-Tuning
| Day | Activity |
|-----|----------|
| 193-196 | Pick one system and design it with extreme scale (e.g., 1B users) |
| 197-200 | Review tracking sheet, ensure all top-20 questions are at least yellow/green |
| 201-204 | Simulate back-to-back interviews (2 in a day) to build stamina |
| 205-210 | Relax and review – light reading, mental preparation |

---

### 31.7 HLD Mastery Tracker (Template)

Create a spreadsheet with these columns. Color-code confidence.

| Priority | Question | Pattern | Sub-pattern | System Type | Core Concepts | Scale Assumption | Attempt 1 | Bottlenecks Missed | Trade-offs Missed | Attempt 2 | Confidence | Status |
|----------|----------|---------|-------------|--------------|----------------|------------------|-----------|---------------------|--------------------|-----------|------------|--------|
| 1 | Design Twitter | Feed | Social Media | Read-heavy | Fan-out, caching, timeline | 200M DAU | 15-Jan | Hotkey partitioning | Push vs pull | 30-Jan | 🟡 | In Progress |

**Confidence Key**: 🟢 Solid, 🟡 Needs work, 🔴 Weak

**Status**: Not Started / In Progress / Done

---

### 31.8 The 6-Step Interview Framework (Practice It Every Time)

1. **Requirements (5 min)** – Functional, non-functional, out of scope.
2. **Estimation (5 min)** – Traffic, storage, bandwidth.
3. **API Design (5 min)** – Endpoints, request/response.
4. **High-Level Architecture (10 min)** – Draw components and data flow.
5. **Deep Dive (15 min)** – Zoom into 1-2 critical components (database, cache, queue).
6. **Bottlenecks & Trade-offs (5 min)** – Identify SPOFs, scaling limits, and justify choices.

---

### 31.9 Final Pro Tips

- **Pattern recognition is key** – After solving 3-4 problems in a pattern, you'll see the common skeleton.
- **Trade-off articulation** – Always compare alternatives: "I chose X over Y because of Z trade-off."
- **Mock with peers** – Explaining to others forces clarity.
- **Stay updated** – Read engineering blogs from FAANG (Netflix TechBlog, Uber Engineering, etc.)
- **Rest & confidence** – The last week is for light review and mental readiness.

You now have a battle-tested 120-day roadmap. Stick to it, track relentlessly, and you will crack any HLD interview. Good luck, Shubham! 🚀

## 32. Ready-to-Use Tracking Template (4-Sheet Spec)

A concrete, ready-to-build 4-sheet tracker — a more detailed alternative to the single-table tracker in the main roadmap above. Build this in Excel, Google Sheets, or Notion.

### 32.1 Sheet 1 — Master Dashboard

| Field | Description |
|---|---|
| Total Questions | `=COUNT(All Questions)` |
| Completed | `=COUNTIF(Status, "Done")` |
| Weak Areas | `=COUNTIF(Confidence, "Red")` |
| Medium | `=COUNTIF(Confidence, "Yellow")` |
| Strong | `=COUNTIF(Confidence, "Green")` |
| Next Focus Pattern | Manual |

Add conditional formatting (Green/Yellow/Red) and a completion‑percentage bar.

### 32.2 Sheet 2 — Question Tracker (Main Sheet)

| Column | Purpose |
|---|---|
| Priority Rank | 1–30, based on FAANG frequency |
| Question Name | e.g. "Design Twitter" |
| Pattern | Feed, CRUD, Payment, etc. |
| Sub‑Pattern | Fan‑out, Locking, etc. |
| System Type | Read‑heavy / Write‑heavy / Strong Consistency / Low Latency |
| Core Concepts | Caching, Sharding, MQ, etc. |
| Scale Assumption | DAU / QPS |
| Attempt 1 Date | First try |
| Attempt 2 Date | Revision |
| Bottlenecks Missed | Self‑analysis |
| Tradeoffs Missed | Self‑analysis |
| Feedback Notes | From mock |
| Confidence | Red / Yellow / Green |
| Status | Not Started / In Progress / Done |

### 32.3 Sheet 3 — Pattern Master Sheet

Group your attempts by pattern to see where you're weakest at a glance:

| Pattern | Sub‑pattern | Core Focus | Questions Completed | Avg Confidence |
|---|---|---|---|---|

Patterns to track: CRUD/Storage, Feed/Timeline, Real‑time Communication, Streaming/Media, Geospatial, Logging/Analytics, Critical Consistency, Infrastructure Utilities, Recommendation Systems.

### 32.4 Sheet 4 — Mock Interview Log

| Date | Question | Time Taken | Estimation Done? | API Defined? | Tradeoffs Explained? | Communication Rating (1–5) | Overall Rating |
|---|---|---|---|---|---|---|---|

**How to use it:** attempt 3 questions per pattern, mark bottlenecks as you find them, revisit anything marked Red after 10 days, and focus each revisit specifically on improving articulation, not just recall.

## 33. Alternative Tiered Priority List (Top 30, by Tier)

A second, independently-ranked priority list — organized into three tiers rather than a single top‑20. Use whichever framing (this tiered view or the Top‑20 list above) fits how you like to plan; the two lists overlap heavily but this one groups by *how essential* a question is rather than pure frequency rank.

### 33.1 Tier 1 — Absolute Must (Top 10)

| Rank | Question | Pattern | System Type | Why High Priority |
|---|---|---|---|---|
| 1 | Design Twitter | Feed | Read‑heavy | Fan‑out tradeoffs |
| 2 | Design WhatsApp | Real‑time | Low latency | WebSockets + ordering |
| 3 | Design URL Shortener | CRUD | Write‑heavy | Hashing + scaling |
| 4 | Design Instagram Feed | Feed | Read‑heavy | Hybrid push/pull |
| 5 | Design Payment Gateway | Critical Consistency | Strong ACID | Idempotency |
| 6 | Design Uber | Geospatial | Write‑heavy | Matching algorithm |
| 7 | Design Ticket Booking System | Locking | Strong consistency | Double‑booking prevention |
| 8 | Design Distributed Rate Limiter | Infrastructure | Write‑heavy | Token bucket |
| 9 | Design Distributed Cache | Infra | Read‑heavy | Consistent hashing |
| 10 | Design YouTube | Streaming | Read‑heavy | CDN + transcoding |

### 33.2 Tier 2 — Very High Value (11–20)

| Rank | Question | Pattern | System Type | Focus |
|---|---|---|---|---|
| 11 | Design Facebook News Feed | Feed | Read‑heavy | Ranking |
| 12 | Design Google Drive | Storage | Storage‑heavy | Chunking |
| 13 | Design Dropbox | Storage | Storage‑heavy | Sync + dedupe |
| 14 | Design Slack | Real‑time | Low latency | Group chat |
| 15 | Design Netflix | Streaming | Read‑heavy | Adaptive streaming |
| 16 | Design Google Maps | Geospatial | Read‑heavy | Proximity search |
| 17 | Design E‑commerce (Amazon) | CRUD | Balanced | Cart + sharding |
| 18 | Design Distributed Logging | Analytics | Write‑heavy | Kafka pipeline |
| 19 | Design Metrics System | Analytics | Write‑heavy | Time‑series |
| 20 | Design Search Autocomplete | Search | Read‑heavy | Prefix index |

### 33.3 Tier 3 — Advanced Depth (21–30)

| Rank | Question | Pattern | System Type | Focus |
|---|---|---|---|---|
| 21 | Design Google Docs | Collaboration | Consistency‑sensitive | OT / CRDT |
| 22 | Design Web Crawler | Infrastructure | Write‑heavy | URL frontier |
| 23 | Design Spotify | Streaming | Read‑heavy | DRM |
| 24 | Design LinkedIn Connections | Graph | Write‑heavy | Graph traversal |
| 25 | Design Live Scoreboard | Real‑time | Low latency | Pub/Sub |
| 26 | Design Hotel Booking | Locking | Strong ACID | Distributed lock |
| 27 | Design Kafka‑like System | Infra | High throughput | Log partitioning |
| 28 | Design Recommendation System | ML pipeline | Read‑heavy | Feature store |
| 29 | Design CDN | Infra | Read‑heavy | Edge caching |
| 30 | Design Unique ID Generator | Infra | Write‑heavy | Snowflake |

### 33.4 Three-Month Strategic Execution Plan

A condensed alternative to the 8‑week plan above, organized by tier instead of by topic-week:

- **Month 1:** Tier 1 fully, plus 3 mock interviews.
- **Month 2:** Tier 2 fully, plus 5 timed mock sessions.
- **Month 3:** Tier 3 + advanced/Staff-level topics, plus multi-region variations of earlier answers.

**Per-question practice loop (40–45 min):** 5 min requirements → 5 min estimation → 10 min architecture → 15 min deep dive → 5 min bottlenecks. Time yourself every single rep — internalizing the clock is as important as the content.

## 34. Cross-Source Notes: CLEAR Method, LB Algorithms & the Trade-off Sentence

A few smaller, high-value nuggets that surfaced in alternate drafts of this roadmap and are worth keeping even though the main content above already covers the same ground in more depth.

**Alternate name for the interview framework — the "CLEAR" method:** Clarify requirements → back-of-the-envelope Estimation → data model / Architecture (high-level) → Reveal deep dives and trade-offs. Same six steps as the framework in section 3 above, useful if you prefer a pronounceable acronym to anchor the sequence under pressure.

**Name the load-balancer algorithm, not just "a load balancer":** interviewers notice when you're specific. Know these three by name and when each applies — Round-Robin (simple, even distribution, no server-load awareness), Least Connections (routes to the server with the fewest active connections — better for long-lived connections like WebSockets), and IP Hash (same client always routes to the same server — useful for sticky sessions without a shared session store).

**The trade-off sentence to practice out loud, verbatim, until it's automatic:**

> *"I'm choosing [X] over [Y] because in this system [reason] matters more than [what we sacrifice], and under [condition] I'd switch to Y instead."*

This single sentence structure is what separates candidates who *list* trade-offs from candidates who *reason* about them — use it every time you make an architectural choice in a mock or real interview.

**Pattern coverage note:** an alternate categorization of the same 70-problem space groups two extra top-level patterns that are worth calling out explicitly if an interviewer frames the question that way — **Streaming & Media** (YouTube, Netflix, Spotify, Zoom: CDNs, adaptive bitrate streaming, transcoding, WebRTC/UDP) and **Recommendation Systems** (Netflix/YouTube/Amazon recommendations: feature stores, offline batch pipelines, online serving layers) — both are covered as full pattern deep-dives in sections 5–18 above (Recommendation Systems is Pattern 8; Streaming & Media threads through Patterns 3 and 4).

---

*End of consolidated notes.*
