# 🟢 PATTERN 1 — CRUD / Storage-Based Systems

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

# 🟢 PATTERN 2 — Feed / Timeline Systems

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

# 🟢 PATTERN 3 — Real-Time Systems

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

# 🟢 PATTERN 4 — Search Systems

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

# 🟢 PATTERN 5 — Rate Limiter / Throttling

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

# 🟢 PATTERN 6 — Logging / Metrics / Monitoring

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

# 🟢 PATTERN 7 — Distributed Caching Systems

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

# 🟢 PATTERN 8 — Recommendation Systems

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

# 🟢 PATTERN 9 — Payment / Financial Systems

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

# 🟢 PATTERN 10 — Large-Scale Processing

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

# 🟢 PATTERN 11 — Geospatial / Location-Based Systems

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

# 🟢 PATTERN 12 — Collaborative Systems

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

# 🟢 PATTERN 13 — Infrastructure / System Utilities

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

# 🟢 PATTERN 14 — Web Crawler / Search Indexing

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

# Supplement — Pattern-Based Categorization and Practice Map

# 🔷 PART 4 — Complete Pattern-Based Categorization

Below is the full consolidated table including every question mentioned.

---

# 📊 MASTER HLD PRACTICE TABLE

| Pattern | Sub-Pattern | System Type | Core Focus | Practice Questions |
| ------- | ----------- | ----------- | ---------- | ------------------ |

---

## 🟢 Pattern A — CRUD / Storage / Key-Value Systems

| Pattern        | Sub-Pattern        | System Type              | Core Focus             | Questions                                                                      |
| -------------- | ------------------ | ------------------------ | ---------------------- | ------------------------------------------------------------------------------ |
| CRUD / Storage | Key-Value Store    | Read-heavy / Write-heavy | Hashing, ID generation | URL Shortener (TinyURL/Bitly), Pastebin, Key-Value Store (Redis/DynamoDB-like) |
| CRUD / Storage | Content Management | Storage-heavy            | Chunking, Sync, Dedup  | Google Drive, Dropbox, Image Hosting (Imgur/Flickr)                            |
| CRUD           | Multi-tenant CRUD  | Balanced                 | Sharding by user_id    | Online Bookstore, E-commerce Store (Amazon)                                    |

---

## 🟢 Pattern B — Social Networks (Feeds & Graphs)

| Pattern       | Sub-Pattern         | System Type                 | Core Focus             | Questions                              |
| ------------- | ------------------- | --------------------------- | ---------------------- | -------------------------------------- |
| Feeds         | Timeline Generation | Read-heavy                  | Fan-out (Push vs Pull) | Twitter, Instagram, Facebook News Feed |
| Feeds         | Ranking             | Read-heavy                  | Ranking algorithms     | LinkedIn Feed                          |
| Graph Systems | Social Connections  | Write-heavy graph traversal | Graph DB               | LinkedIn Connections, Facebook Friends |

---

## 🟢 Pattern C — Real-Time Communication

| Pattern      | Sub-Pattern       | System Type           | Core Focus           | Questions                                    |
| ------------ | ----------------- | --------------------- | -------------------- | -------------------------------------------- |
| Messaging    | 1:1 & Group Chat  | Low-latency           | WebSockets, ordering | WhatsApp, Facebook Messenger, Slack, Discord |
| Live Updates | Collaborative     | Consistency-sensitive | OT, CRDT             | Google Docs                                  |
| Live Updates | Streaming updates | Low-latency           | Pub/Sub              | Live Scoreboard                              |
| Conferencing | Real-time media   | UDP-heavy             | WebRTC, SFU/MCU      | Zoom                                         |

---

## 🟢 Pattern D — Streaming & Media

| Pattern            | Sub-Pattern     | System Type   | Core Focus           | Questions                |
| ------------------ | --------------- | ------------- | -------------------- | ------------------------ |
| Video Streaming    | Media delivery  | Read-heavy    | CDN, transcoding     | YouTube, Netflix         |
| Audio Streaming    | DRM & streaming | Read-heavy    | Adaptive bitrate     | Spotify                  |
| Large File Storage | Blob systems    | Storage-heavy | CDN + object storage | Video Streaming Platform |

---

## 🟢 Pattern E — Geospatial Systems

| Pattern          | Sub-Pattern        | System Type | Core Focus            | Questions         |
| ---------------- | ------------------ | ----------- | --------------------- | ----------------- |
| Ride Matching    | Real-time matching | Write-heavy | Geohashing, Quadtrees | Uber, Lyft        |
| Proximity Search | Geo indexing       | Read-heavy  | Spatial queries       | Yelp, Google Maps |
| Logistics        | Real-time tracking | Write-heavy | Location updates      | DoorDash, Zomato  |

---

## 🟢 Pattern F — High Throughput & Logging

| Pattern     | Sub-Pattern     | System Type    | Core Focus      | Questions                        |
| ----------- | --------------- | -------------- | --------------- | -------------------------------- |
| Web Crawler | URL frontier    | Write-heavy    | BFS, politeness | Google Search crawler            |
| Analytics   | Batch + Stream  | High ingestion | ETL, MapReduce  | Google Analytics                 |
| Logging     | Event ingestion | Write-heavy    | Kafka pipeline  | Distributed Logging (Splunk/ELK) |
| Metrics     | Time-series     | Write-heavy    | Aggregation     | Prometheus, Datadog              |

---

## 🟢 Pattern G — Critical Consistency & Locking

| Pattern       | Sub-Pattern  | System Type        | Core Focus          | Questions                       |
| ------------- | ------------ | ------------------ | ------------------- | ------------------------------- |
| Payment       | Financial    | Strong consistency | Double-entry ledger | Stripe, PayPal, Payment Gateway |
| Ticketing     | Reservation  | High concurrency   | Seat locking        | Ticketmaster, BookMyShow        |
| Job Scheduler | Coordination | Consistency        | Leader election     | Distributed Job Scheduler       |
| Booking       | Reservation  | Strong ACID        | Distributed locking | Hotel / Airline Booking         |

---

## 🟢 Pattern H — Infrastructure Utilities

| Pattern           | Sub-Pattern     | System Type     | Core Focus         | Questions         |
| ----------------- | --------------- | --------------- | ------------------ | ----------------- |
| Rate Limiting     | Throttling      | Write-heavy     | Token bucket       | API Rate Limiter  |
| Distributed Cache | Caching         | Read-heavy      | Consistent hashing | Memcached, Redis  |
| CDN               | Edge caching    | Read-heavy      | Geo routing        | CDN               |
| ID Generator      | Unique IDs      | Write-heavy     | Snowflake          | Unique ID Service |
| Kafka-like System | Streaming infra | High throughput | Log partitioning   | Kafka-like system |

---

## 🟢 Pattern I — Recommendation Systems

| Pattern         | Sub-Pattern      | System Type | Core Focus      | Questions                     |
| --------------- | ---------------- | ----------- | --------------- | ----------------------------- |
| Recommendations | Offline + Online | Read-heavy  | Feature store   | Netflix Recommendation        |
| Recommendations | Content ranking  | Read-heavy  | ML serving      | YouTube Recommendation        |
| Recommendations | Product ranking  | Read-heavy  | Hybrid pipeline | Amazon Product Recommendation |

---

# Complete HLD Problem Master Map

We've categorized our 70 problems into the **4 Pillars of HLD**. This structure is your "cheat sheet" for interviews—when an interviewer asks a question, your first step is to identify which pillar it belongs to so you know which architectural "tools" to pull out of your bag. 🧰

Below is the complete, master table of all 70 problems, deeply categorized by their sub-category, primary challenge, and their corresponding HLD pillar.

### 🏛️ The 70-Problem HLD Master Map

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

### 🚀 Let's Pick Your Starting Point

To master HLD, we shouldn't just read the answers. We need to build them together. Based on your goals for top product companies, I recommend starting with one of these three high-impact "anchor" problems. They each teach a fundamental skill that applies to dozens of others.

1. **TinyURL (Pillar 4):** Learn how to scale a database from 1 to 1 billion entries. (Foundational) 🔗
2. **Twitter/News Feed (Pillar 2):** Learn how to handle "Celebrity users" and massive data distribution. (The "Fan-out" King) 📣
3. **Rate Limiter (Pillar 1/10):** Learn how to protect your system from crashing when too many people use it at once. (Logic & Concurrency) 🛡️

**Which of these three should we tackle first?**
