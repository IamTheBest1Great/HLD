# HLD (High-Level Design) — Scalability Q&A Bank

A complete question bank for System Design / HLD interview prep, organized by **difficulty** and **type**: conceptual, comparative, scenario-based, real-life system design, and rapid-fire self-test. Based on core scalability concepts (vertical/horizontal scaling, caching, load balancing, sharding, CDN, async processing, and scaling patterns by system type).

---

## Table of Contents
1. [Beginner — Core Concepts (Easy)](#1-beginner--core-concepts-easy)
2. [Intermediate — Techniques & Tradeoffs (Medium)](#2-intermediate--techniques--tradeoffs-medium)
3. [Advanced — Deep Architecture (Hard)](#3-advanced--deep-architecture-hard)
4. [Scenario-Based Questions](#4-scenario-based-questions)
5. [Real-Life System Design Q&A (by Product)](#5-real-life-system-design-qa-by-product)
6. [Comparison Questions](#6-comparison-questions)
7. [Troubleshooting / Debugging Scenarios](#7-troubleshooting--debugging-scenarios)
8. [Tricky / Trap Interview Questions](#8-tricky--trap-interview-questions)
9. [Rapid-Fire One-Liners](#9-rapid-fire-one-liners)
10. [Self-Test Quiz (Answers at the End)](#10-self-test-quiz-answers-at-the-end)

---

## 1. Beginner — Core Concepts (Easy)

**Q1. What is scalability?**
A: The ability of a system to handle increasing load (users, requests, data) by adding resources, without a fundamental redesign, while maintaining acceptable performance.

**Q2. What is vertical scaling?**
A: Adding more resources (CPU, RAM, disk) to an existing single machine — e.g., upgrading an `m5.large` to an `m5.24xlarge`. No architecture change needed, but it has a hard hardware ceiling.

**Q3. What is horizontal scaling?**
A: Adding more machines to a pool and distributing load across them via a load balancer, instead of making one machine bigger. Virtually unlimited but adds distributed-systems complexity.

**Q4. Why is vertical scaling limited?**
A: There's a physical ceiling on how much CPU/RAM/disk a single machine can have; beyond the largest available instance, you cannot scale further, and it remains a single point of failure.

**Q5. What does "stateless" mean for an application server?**
A: It stores no session/user data locally — every request carries or fetches all the context it needs (often from a shared store like Redis), so any instance can handle any request.

**Q6. Why must servers be stateless for horizontal scaling to work well?**
A: If session data lives only on Server 1, and the load balancer routes the next request to Server 2, that server won't recognize the user. Statelessness lets any instance serve any request.

**Q7. What is a load balancer?**
A: A component that distributes incoming requests across multiple backend servers to prevent overload on any one instance, enabling horizontal scaling and fault tolerance.

**Q8. What is caching, and why does it help scalability?**
A: Storing frequently accessed data in a faster layer (memory, edge) so repeat reads don't hit the database/origin, reducing load and latency.

**Q9. What is a CDN?**
A: A Content Delivery Network — a geographically distributed set of edge servers that cache content close to users, reducing latency and offloading the origin server.

**Q10. What is database sharding?**
A: Splitting data across multiple independent database servers based on a shard key (e.g., `user_id % N`), so no single node holds or serves all the data — scales writes horizontally.

**Q11. What is a read replica?**
A: A copy of the primary database that serves read-only queries; writes go to the primary and are replicated to followers, scaling read throughput.

**Q12. What is auto-scaling?**
A: The automatic adjustment of the number of running instances based on real-time demand (CPU, memory, queue depth, or schedule).

**Q13. What is a message queue, and why use one?**
A: A buffer between producers and consumers (e.g., Kafka, RabbitMQ, SQS) that decouples request receipt from processing, absorbs traffic bursts, and enables retries.

**Q14. What is session affinity (sticky sessions)?**
A: A load balancer technique that routes all requests from one client to the same backend server, usually via cookie or IP hash — needed only when a service is stateful.

**Q15. What's the difference between Layer 4 and Layer 7 load balancing?**
A: Layer 4 only sees IPs/ports (fast, protocol-agnostic); Layer 7 reads the full HTTP request (path, headers, cookies) and can route intelligently (e.g., `/api/*` vs `/static/*`) but is slightly slower.

**Q16. What is a health check in load balancing?**
A: A mechanism (active probes to `/health`, or passive observation of real traffic) that lets the load balancer detect and stop routing to unhealthy instances.

**Q17. What's a cache hit vs. a cache miss?**
A: A hit means the requested data is found in cache and returned immediately; a miss means the cache doesn't have it, so the system fetches from the origin/database and (usually) populates the cache.

**Q18. What is TTL in caching?**
A: Time-To-Live — the duration a cached entry is considered valid before it expires and must be refreshed from the source.

**Q19. What is connection pooling?**
A: Reusing a fixed set of already-open database connections across many requests instead of opening/closing a new connection per request, reducing overhead.

**Q20. What is denormalization, and why would you use it?**
A: Deliberately adding redundant data to avoid expensive joins, trading slower/more complex writes for much faster reads — common in read-heavy reporting systems.

---

## 2. Intermediate — Techniques & Tradeoffs (Medium)

**Q21. Compare vertical and horizontal scaling across cost, complexity, and fault tolerance.**
A: Vertical scaling uses premium hardware (higher cost per unit of extra capacity), is operationally simple, but is a single point of failure. Horizontal scaling uses commodity hardware (cheaper, pay-as-you-grow), is operationally complex (load balancing, consistency, discovery), but has built-in redundancy — if one node fails, others keep serving.

**Q22. How do you convert a stateful service into a stateless one?**
A: Externalize all local state — move user sessions to Redis, shopping carts to Redis/DB, in-progress uploads to S3 with pre-signed URLs, job status to a DB/Redis, and rate-limit counters to Redis. Any instance can then serve any request.

**Q23. What are the main cache eviction policies, and when would you pick each?**
A: LRU (evict least-recently-used — good general-purpose default), LFU (evict least-frequently-used — good when popularity, not recency, matters), FIFO (evict oldest inserted — simple, less optimal), and TTL-only (pure time-based expiry — good for data with a known freshness window).

**Q24. What is cache stampede, and how do you prevent it?**
A: When a hot cache key expires, many concurrent requests simultaneously miss and hammer the database. Prevent it with: locking (first request repopulates, others wait), probabilistic early recomputation (refresh before expiry), or stale-while-revalidate (serve stale data while refreshing in the background).

**Q25. What are the main cache invalidation strategies?**
A: TTL (auto-expire), write-through (write to cache + DB synchronously), write-behind (write to cache, async to DB), write-around (write to DB only, invalidate cache), and event-driven invalidation (purge on a data-change event).

**Q26. Explain the four common sharding strategies and a weakness of each.**
A: Hash-based (`hash(key) % N` — even distribution, but rebalancing when adding/removing shards is hard); range-based (good for range queries, but prone to hotspots on skewed access); directory-based (flexible remapping via a lookup service, but adds a hop and the directory itself must be HA); geo-based (keeps data near users / meets residency laws, but uneven if population is skewed by region).

**Q27. What causes a "hot partition," and how do you fix it?**
A: A poorly chosen shard key sends disproportionate traffic to one shard (e.g., sharding by a low-cardinality or skewed key like `country`). Fix with a higher-cardinality key, directory-based sharding with dynamic rebalancing, or splitting the hot shard further.

**Q28. What's the difference between vertical partitioning and table partitioning?**
A: Vertical partitioning splits different *tables* across databases by access pattern (e.g., hot `user_profile` on fast storage, cold `login_history` on cheap storage). Table partitioning splits one large table into smaller physical pieces (by range/list/hash) within the same database, usually to speed up queries and simplify maintenance (e.g., dropping old partitions).

**Q29. What are the main types of auto-scaling, and when is each appropriate?**
A: Reactive (scale on live metrics like CPU > 70%, good for unpredictable load), scheduled (scale at known times, e.g., 8 AM–10 PM, good for predictable daily patterns or planned events), and predictive (ML-based forecasting that pre-scales ahead of demand, good for complex seasonal patterns).

**Q30. Why does warm-up time matter for auto-scaling, and how do you mitigate it?**
A: New instances take time to boot, warm caches, and open connections — during a sudden spike, users can see ~60-90 seconds of degraded service before new capacity is ready. Mitigate with scheduled pre-warming, keeping a minimum instance buffer above baseline, faster-booting containers instead of VMs, and connection draining on scale-in.

**Q31. Why is auto-scaling harder for stateful services than stateless ones?**
A: Adding/removing a stateful instance requires rebalancing the data it held, which is expensive and risky. Solutions: use a distributed database that auto-rebalances (Cassandra, MongoDB), consistent hashing with virtual nodes to minimize data movement, or simply don't auto-scale the stateful tier — scale the stateless frontend instead.

**Q32. What is the role of an API Gateway in a microservices architecture?**
A: A single entry point that handles routing to the right microservice, authentication/authorization, rate limiting, request/response transformation, and aggregating results from multiple services.

**Q33. What is service discovery, and name the two main approaches.**
A: A mechanism for clients to find live service instances as they scale up/down. Client-side discovery (client queries a registry like Eureka/Consul and load-balances itself) vs. server-side discovery (client hits a load balancer, which queries the registry, e.g., AWS ALB + ECS).

**Q34. What are circuit breakers and bulkheads, and why do microservices need them?**
A: Circuit breakers stop calling a service that's repeatedly failing and provide a fallback, preventing cascading failure. Bulkheads partition resources (e.g., separate thread pools per downstream call) so a slow dependency can't exhaust all available threads/connections.

**Q35. What is the Saga pattern, and when is it needed?**
A: A way to manage distributed transactions across microservices using a sequence of local transactions with compensating actions on failure — needed because distributed 2PC doesn't scale well; common in payments/financial systems where a multi-step operation must be rolled back safely if one step fails.

**Q36. What's the tradeoff between CDN TTL-based caching and versioned URLs?**
A: TTL-based caching auto-expires content after a fixed window, which risks serving stale data if content changes before expiry or purge fails. Versioned URLs (e.g. `style.v2.css`) avoid invalidation entirely — a new URL is just a new object — making them the best practice for immutable static assets.

**Q37. What is an origin shield, and what problem does it solve?**
A: An additional cache tier between edge nodes and the origin that absorbs simultaneous cache misses from many edges after a TTL expiry, preventing a "thundering herd" / cache stampede on the origin.

**Q38. What is backpressure, and how do you implement it?**
A: A mechanism to prevent producers from overwhelming consumers when they outpace them. Implement via rate limiting at the producer, bounded queue sizes (reject/block when full), reactive streams (consumer signals its capacity), or load shedding (drop non-critical requests under overload).

**Q39. Why would you choose eventual consistency over strong consistency?**
A: Strong consistency (ACID) limits how easily you can horizontally scale/partition data, so it's reserved for cases where correctness is critical (financial ledgers). Eventual consistency (BASE) allows massive horizontal scaling and fits systems where slightly stale reads are acceptable, like social feeds or analytics dashboards.

**Q40. Give three concrete read-heavy fixes and three concrete write-heavy fixes.**
A: Read-heavy: caching (Redis/CDN), read replicas, query result caching. Write-heavy: sharding, message queue buffering for write bursts, write-optimized storage engines (e.g., Cassandra/LSM-tree based stores).

---

## 3. Advanced — Deep Architecture (Hard)

**Q41. Walk through what happens end-to-end when a hot cache key expires under heavy read load, with mitigations at each stage.**
A: (1) TTL expires on a popular key. (2) Dozens/hundreds of concurrent requests all miss simultaneously. (3) Without protection, all of them hit the database at once (stampede), potentially causing a cascading overload. Mitigations: a distributed lock so only one request repopulates the cache while others wait or get a slightly stale value; probabilistic early expiration so the key refreshes *before* it fully expires, spreading the refresh over time; or serve-stale-while-revalidate, where stale data is returned instantly while a background job refreshes it. At the CDN tier, an origin shield absorbs the same problem for edge-level misses.

**Q42. Design the sharding + rebalancing strategy for a service growing from 4 shards to 16 shards with minimal downtime.**
A: Avoid naive `hash(key) % N` because changing N remaps almost every key. Use consistent hashing with virtual nodes: each physical shard owns many points on a hash ring; adding shards only moves the keys that fall into the new nodes' ranges, not the whole dataset. During migration, run dual-write (old + new shard) or use a directory service that tracks key→shard mapping during a controlled, incremental data-copy process, then cut over reads once the copy is verified, then stop dual-writes.

**Q43. How would you design session management for a horizontally-scaled app with 200 stateless instances expecting both high availability and low latency?**
A: Store sessions in a distributed in-memory store (Redis Cluster) with replication across at least 2 nodes, so a single node failure doesn't drop sessions. Set a sensible TTL and sliding expiration on activity. Use a lightweight session token (not the full payload) in the client cookie; the server resolves it from Redis per request. For latency, keep Redis in the same region/AZ as app servers, and consider a small local (in-process) cache of recently seen sessions guarded by a short TTL to cut network round-trips for very hot users, invalidated on logout/update.

**Q44. Explain the hybrid push/pull fan-out model used by Twitter-like feed systems and why neither pure push nor pure pull works at scale.**
A: Pure push (fan-out-on-write): when a user tweets, the tweet is immediately pushed into every follower's cached timeline. This is fast for reads but breaks down for celebrity accounts with millions of followers — one tweet triggers millions of writes. Pure pull (fan-out-on-read): timelines are computed on read by querying all followed users' recent tweets — cheap writes, but expensive, slow reads for users following many accounts. The hybrid model pushes for "normal" users (few followers) and pulls for celebrity accounts, merging both at read time for any given user's timeline.

**Q45. How do you keep a multi-tier CDN cache hierarchy (edge → regional → origin shield → origin) consistent after a content update?**
A: Use a purge/invalidation API that cascades through all tiers, or avoid invalidation entirely for static assets via versioned URLs (new content = new URL, so old cached objects simply age out unused). For dynamic/semi-dynamic content, keep TTLs short enough that staleness windows are acceptable, and use event-driven purges triggered by the CMS/data pipeline rather than relying purely on TTL.

**Q46. Design the geohash-based driver-matching system for a ride-hailing app (Uber-like), including the write and read paths.**
A: Write path: driver app sends GPS pings every few seconds → ingested into Kafka (partitioned by geohash prefix or driver_id) → a stream processor consumes and updates each driver's position in a Redis Geo index (geohash-indexed sorted structure), overwriting the previous value (only latest position matters). Read path: rider requests nearby drivers → query Redis Geo for drivers within a radius of the rider's geohash cell (and adjacent cells to handle edge cases) → rank by distance/ETA → return candidates. Scaling consideration: geohash sharding can create hotspots in dense cities (e.g., downtown) — mitigate by using smaller geohash cells in high-density regions or hierarchical geohash precision.

**Q47. How would you design a Google-Docs-style collaborative editor to scale to millions of concurrent documents without a central lock?**
A: Shard documents by `document_id` using consistent hashing so each doc's WebSocket connections and in-memory state live on one (or a small replica set of) server(s). Within a document, use CRDTs (Conflict-free Replicated Data Types) so concurrent edits from multiple clients merge deterministically without needing a central lock or coordinator — every edit is broadcast to all connected collaborators and applied locally. Periodically checkpoint the merged document state to durable storage. This avoids global coordination entirely; the only "locking" is implicit in CRDT merge semantics.

**Q48. Explain why a distributed locking service (like etcd/ZooKeeper) deliberately uses a *small* cluster (3 or 5 nodes) rather than scaling it horizontally like a stateless app tier.**
A: Consensus protocols like Raft/Paxos require a quorum (majority) of nodes to agree before committing a change, and the overhead of reaching consensus grows with cluster size. Small odd-numbered clusters (3 or 5) provide fault tolerance (can survive 1 or 2 node failures) while keeping consensus latency low. These systems scale by being fast and highly available for *coordination* metadata, not by handling bulk application traffic — you wouldn't shard the lock service itself, because that would defeat the purpose of a single source of truth for a given lock key.

**Q49. How do you design a URL shortener's redirect path to survive a sudden 50x traffic spike (e.g., a link goes viral) with minimal added infrastructure?**
A: Since reads (redirects) vastly outnumber writes (~100:1), the redirect path should barely touch the database. Cache the short-code → long-URL mapping in Redis (or even at CDN edge via Lambda@Edge/Workers doing the lookup) with a long TTL since mappings are immutable once created. On a cache miss, fall back to the DB and repopulate. Auto-scale the stateless redirect service on request count or cache hit ratio, not just CPU, since redirects are cheap to serve once cached. For the viral link specifically, cache warming (pre-populating cache before you know it'll be hot) isn't possible, but the first few misses self-heal quickly because popularity concentrates on one key, which then stays hot in cache.

**Q50. Design idempotency handling for a payment gateway processing retried requests from unreliable mobile clients.**
A: Require clients to generate a unique idempotency key per logical operation (e.g., UUID generated once per "pay now" tap, reused on retry). On each request, check Redis for that key: if present and already completed, return the cached result without reprocessing; if present and in-progress, reject/wait (prevents double-charging from a race); if absent, acquire a short lock, process the payment, persist the result keyed by the idempotency key (with a TTL of e.g. 24h), and release the lock. The DB ledger remains the source of truth with a unique constraint on the idempotency key as a second line of defense against duplicate writes caused by race conditions.

**Q51. How would you scale a Kafka-like message queue system itself (not just the consumers)?**
A: Partition topics so that parallelism is bounded by partition count — each partition is the unit of ordering and load distribution, consumed by exactly one consumer within a consumer group at a time. Use consistent hashing to assign partitions to brokers, enabling the cluster to grow by adding brokers and rebalancing partitions with minimal movement. Use leader-follower replication per partition for durability/failover (a leader handles reads/writes, followers replicate and can be promoted if the leader dies). Auto-rebalancing partitions when brokers join/leave keeps load even over time.

**Q52. What's the architectural difference between scaling a search system (like Google Search) versus a relational OLTP database, and why does term-based sharding make sense for search?**
A: An OLTP database is typically sharded by a primary entity key (e.g., `user_id`) because most queries filter by that entity. A search index's primary access pattern is "which documents contain term X," so sharding the inverted index by term (or term hash) lets a query for that term touch only the relevant shard(s), and multiple term shards can be queried in parallel and merged (scatter-gather) for multi-term queries. This is fundamentally different from entity-based sharding and optimizes for the index's actual access pattern.

---

## 4. Scenario-Based Questions

**Q53. Scenario: Your e-commerce product page starts timing out during a flash sale. CPU on the database is at 100%, and 95% of the queries are `SELECT` statements on the product catalog. What do you do, in order?**
A: (1) Add a cache layer (Redis) in front of the catalog reads with a short TTL (e.g., product details change rarely during a sale) — this is the fastest win. (2) Add read replicas to offload remaining read traffic that isn't cacheable (e.g., personalized views). (3) Longer-term, consider a CDN layer for product images/static assets if they're also hitting origin. (4) Only shard the catalog DB if write contention (inventory updates) becomes the new bottleneck — reads alone rarely justify sharding.

**Q54. Scenario: A chat application's message-send latency spikes whenever a single large group chat (50,000 members) is active, while 1-on-1 chats remain fast. Diagnose and fix.**
A: This is a hot-partition / hotspot problem — if conversations are sharded by `conversation_id`, the one huge group chat concentrates massive fan-out (every message must be delivered to 50,000 connections) onto whichever shard(s) hold its state. Fix: treat very large groups as a special case — use a dedicated fan-out/broadcast path (e.g., a pub/sub topic per large group that connection-handling servers subscribe to) instead of routing through the same per-conversation shard logic used for small chats, so delivery is parallelized across many WebSocket servers rather than funneled through one shard.

**Q55. Scenario: You're asked to design capacity for a service expecting 50 million writes/day. What's your back-of-envelope math, and what does it tell you about architecture?**
A: 50,000,000 / 86,400 seconds ≈ 580 writes/sec average. If reads are, say, 10:1, that's ~5,800 reads/sec average. Peak traffic is often 2–5× average, so design for roughly 1,500–3,000 writes/sec and 15,000–30,000 reads/sec at peak. This single calculation already tells you: reads need caching/replicas (tens of thousands of reads/sec is squarely cache territory), and writes at a few thousand/sec may still fit on a well-tuned single primary with good indexing — sharding might not be necessary yet unless write patterns are highly contended on a single key.

**Q56. Scenario: After adding auto-scaling, your team notices the system keeps rapidly adding then removing instances every few minutes ("flapping"). What's wrong and how do you fix it?**
A: This is a thrashing problem, usually caused by a scaling threshold too close to normal operating load, or no cool-down period between scaling actions. Fix: widen the gap between scale-out and scale-in thresholds (e.g., scale out at 70% CPU, scale in at 30%, not 50%/49%), add a cool-down period (e.g., 5 minutes) after each scaling action before another is allowed, and consider using a sustained-duration requirement (e.g., "CPU > 70% for 5 minutes," not an instantaneous spike) before triggering.

**Q57. Scenario: Your startup has 100 users today but expects 10,000 within 6 months. How much "scalability" should you build in now?**
A: Resist the urge to over-engineer. At 100–1,000 users, a single reasonably-sized server (vertical scaling) with a managed database and basic monitoring is usually sufficient and far cheaper to build/maintain. Invest instead in making the app *stateless* from day one (externalize sessions) and keeping the data layer reasonably normalized/indexed — this makes the *future* move to horizontal scaling, caching, and read replicas a configuration change rather than a rewrite. Avoid premature sharding, microservices, or multi-region setups at this stage.

**Q58. Scenario: A news site gets a 50× traffic spike during a breaking story, mostly to one article page. What single change gives the most relief fastest?**
A: Put that article behind a CDN/edge cache (even a short TTL like 30–60 seconds helps enormously at this volume) since it's read-only content being requested identically by huge numbers of users — a textbook cache-friendly hot key. This offloads the overwhelming majority of traffic from origin without any code or database change, and can typically be done via CDN config alone (fastest possible mitigation).

**Q59. Scenario: Your analytics dashboard runs a query that aggregates 500 million rows and takes 45 seconds, run by multiple internal users throughout the day. How do you fix it without redesigning the whole data pipeline?**
A: First, cache the query result (not just rows) for a reasonable TTL — if the underlying data only updates hourly, a 10–15 minute cache turns a 45-second query run dozens of times into one real computation. Second, if root-cause fixing is in scope, pre-aggregate the data (materialized views, scheduled rollups) so the dashboard reads a small pre-computed table instead of scanning raw rows, and/or move analytical workloads to a columnar store (e.g., BigQuery/Redshift) better suited for scan-heavy aggregation.

**Q60. Scenario: A mobile banking app's wallet balance updates sometimes show incorrect balances when a user performs two actions quickly (e.g., refund + purchase near-simultaneously). What's the likely cause and fix?**
A: Likely a race condition from concurrent writes to the same balance without proper concurrency control (lost update problem). Fix with optimistic locking (a version column — write fails and retries if the version has changed since read) or pessimistic locking for very high-contention accounts, combined with sharding by `user_id` so each user's balance operations are serialized through one consistent path rather than spread unpredictably.

**Q61. Scenario: Your video platform's CDN cache hit ratio for new uploads is very low in the first hour after publishing, causing origin overload for trending new videos. Fix it.**
A: Use cache warming — when a creator with a large following publishes (or when early view velocity crosses a threshold), proactively push the video's initial segments to edge locations ahead of expected demand, rather than waiting for organic cache misses to populate the edge reactively.

**Q62. Scenario: You need `/api/*` routed to backend API servers, `/static/*` to CDN, and WebSocket traffic on `/ws/*` routed using least-connections. What layer of load balancing do you need, and why?**
A: Layer 7, because the routing decision depends on the URL path (and for WebSockets, connection-duration-aware balancing), which Layer 4 cannot see — Layer 4 only sees IP/port, not HTTP paths or headers.

---

## 5. Real-Life System Design Q&A (by Product)

**Q63. How would you design Twitter/X for scale?**
A: Hybrid fan-out (push for most users, pull for celebrities) for timelines; shard tweet storage by `user_id`; Kafka for tweet ingestion feeding a stream processor that updates cached timelines in Redis; CDN for media attachments; read replicas for profile/follower-graph lookups.

**Q64. How would you design Instagram's feed?**
A: CDN for images/videos (the dominant bandwidth cost); feed-generation service sharded by `user_id` (e.g., on Cassandra); pre-generate/cache feeds for highly active users asynchronously rather than computing on every page load; read replicas for supporting metadata queries.

**Q65. How would you design WhatsApp/Messenger at scale?**
A: Shard by `conversation_id` using consistent hashing to assign conversations to specific chat servers; maintain persistent WebSocket/TCP connections per user, with a connection manager layer that can scale horizontally; use a message queue for reliable async delivery (especially to offline users, who get messages delivered on reconnect); store message history partitioned/sharded similarly.

**Q66. How would you design Uber's driver-matching system?**
A: Geohash-based sharding for driver location; location pings flow through Kafka to a stream processor that updates a Redis Geo index; ride requests query nearby drivers via geohash range queries; handle city-center hotspots with finer-grained geohash precision or additional shard splitting in dense zones.

**Q67. How would you design a payment gateway like Stripe?**
A: Shard the payment ledger by `merchant_id` to isolate load and blast radius; use Redis-backed idempotency keys to make retried requests safe; strong consistency (ACID) on the ledger itself since correctness is non-negotiable; reporting/analytics reads served from replicas, never the primary; use the Saga pattern for any multi-step cross-service transaction (e.g., payment + inventory reservation).

**Q68. How would you design Dropbox/Google Drive's file storage and sync?**
A: Break large files into content-addressed chunks stored in object storage (S3-like); only changed chunks need re-upload/re-download (deduplication + bandwidth savings); metadata (file tree, permissions, versions) stored in a NoSQL store sharded by `user_id`; CDN accelerates downloads of frequently accessed files.

**Q69. How would you design Google Docs' real-time collaboration?**
A: Shard documents by `document_id`; each document's live state and WebSocket connections are handled by one server (or small replica group); use CRDTs so concurrent edits merge without a central lock; broadcast operations to all connected collaborators; periodically persist merged state durably.

**Q70. How would you design a web crawler at scale?**
A: URL frontier (the queue of URLs to crawl) sharded by domain to respect politeness policies (not hammering one site); a Bloom filter (shared across workers, e.g., in Redis) for fast, memory-efficient deduplication of already-crawled URLs; many stateless worker nodes pulling from the frontier in parallel.

**Q71. How would you design a unique ID generator (Snowflake-style) for a distributed system doing millions of IDs/sec?**
A: Each worker gets a unique worker ID (assigned via etcd, e.g., atomic increment) at startup; each worker then generates IDs completely independently using `timestamp + worker_id + sequence_number`, requiring zero coordination between workers at generation time — this is what allows millions of IDs/sec with no bottleneck.

**Q72. How would you design search autocomplete (like Google's)?**
A: A trie (prefix tree) sharded by first character or a consistent hash of the prefix, since the trie can be too large for one node; CDN caching for very common static suggestions; asynchronous updates to the trie from query logs to surface trending terms without blocking live queries.

**Q73. How would you design a distributed logging/metrics pipeline (ELK/Prometheus-style)?**
A: Logs flow through a Kafka buffer to absorb bursts, then into Elasticsearch with indices sharded/rolled over by time (e.g., one index per day), using hot nodes for recent/frequently-queried data and warm/cold nodes for older data. For metrics, shard scraping by metric name/instance across multiple Prometheus servers, with a system like Thanos aggregating queries across all of them.

---

## 6. Comparison Questions

**Q74. Read replicas vs. caching — when would you use one, the other, or both?**
A: Caching is best for data that's read far more often than it changes and can tolerate the cache's TTL staleness window — it removes load from the DB entirely for cached keys. Read replicas are best when you need near-real-time data (within replication lag) across a wide variety of queries that aren't easily cacheable (e.g., ad-hoc filters). Most high-traffic systems use both: cache for hot/predictable queries, replicas for everything else.

**Q75. Sharding vs. read replicas — which solves which problem?**
A: Read replicas scale *read* throughput only (all writes still go to one primary). Sharding scales *both* reads and writes by splitting data itself across independent nodes, at the cost of cross-shard query complexity. If your bottleneck is reads, replicas are simpler; if it's writes, you need sharding.

**Q76. Push-based vs. pull-based fan-out for feeds — what's the core tradeoff?**
A: Push (fan-out-on-write) optimizes read latency (timeline is pre-built) at the cost of expensive, potentially huge writes for high-follower accounts. Pull (fan-out-on-read) optimizes write cost (just store the post once) at the cost of expensive reads (must aggregate from many sources at read time). Hybrid approaches split by follower count to get the best of both.

**Q77. Optimistic locking vs. pessimistic locking — when to use each?**
A: Optimistic locking (version check at write time, retry on conflict) suits low-to-moderate contention where conflicts are rare, avoiding the cost of holding locks. Pessimistic locking (acquire a lock before reading/writing) suits high-contention scenarios where retries would be frequent and wasteful, at the cost of reduced concurrency and potential deadlocks.

**Q78. CDN cache purging vs. versioned URLs — which is the better default and why?**
A: Versioned URLs are generally the better default for static assets because they eliminate invalidation entirely (a changed asset is just a new URL) and avoid propagation-delay inconsistencies across edge regions. Purging is necessary for dynamic/rarely-changing-but-sometimes-urgent content (e.g., correcting a published article) where you can't pre-know a new URL ahead of time.

**Q79. Synchronous request/response vs. asynchronous processing — how do you decide?**
A: If the caller needs the result immediately to proceed (e.g., "is this login valid?"), synchronous is appropriate. If the work is long-running, can tolerate delay, or shouldn't block the caller (e.g., video transcoding, email sending, report generation), make it asynchronous via a queue and notify/poll for completion.

**Q80. Monolith vs. microservices — purely from a scalability lens, what's the real benefit of microservices?**
A: The core scalability benefit is *independent scaling*: a payment service with 50 instances and a review service with 5 instances can each be sized to their own load, instead of a monolith where you must scale the entire application (wasting resources on low-traffic code paths) to handle load on one hot path.

---

## 7. Troubleshooting / Debugging Scenarios

**Q81. Symptom: App server CPU is low, but response times are high and climbing. Where do you look first?**
A: Suspect a downstream dependency (database, third-party API) that's slow or a synchronous blocking call exhausting the thread pool while waiting — not the app server's own compute. Check DB query latency, connection pool exhaustion, and whether calls to external services have timeouts.

**Q82. Symptom: Adding more app servers behind the load balancer doesn't improve throughput at all.**
A: The bottleneck isn't the app tier — it's almost certainly the database or a shared downstream resource (e.g., a single Redis instance, a rate-limited third-party API) that all the new app servers are still funneling through. Check DB CPU/connections and shared cache/queue saturation before adding more app capacity.

**Q83. Symptom: One users' requests are always slow, but it's intermittent and unrelated to overall system load.**
A: Check for session affinity routing that user to a specific degraded/overloaded instance, or check if that user's data lands on a particular hot shard/partition (hotspot) while other users' data is spread more evenly.

**Q84. Symptom: Right after a deploy, latency spikes for a few minutes then recovers on its own.**
A: Classic cold-cache / cold-start effect — new instances (or a restarted cache) have no warm data, so the first wave of requests all miss and hit the database until caches repopulate naturally. Mitigate with cache warming before cutting traffic to new instances, or rolling deploys that keep some warm instances serving while new ones ramp up.

**Q85. Symptom: Database replication lag spikes during peak traffic, and users occasionally see "disappearing" data they just wrote.**
A: This is a classic read-after-write consistency problem with read replicas under load — the user wrote to the primary, but their next read hit a lagging replica. Mitigate by routing a user's own reads-after-write to the primary for a short window, or using "read-your-writes" session consistency (sticky to primary briefly after a write), or reducing replica lag via better replica sizing/network.

**Q86. Symptom: A queue's consumer lag keeps growing even though you've added more consumer instances.**
A: If the queue (e.g., Kafka topic) has fewer partitions than the number of consumers you're adding, extra consumers sit idle — parallelism is capped by partition count, not consumer count. Increase partitions (if ordering constraints allow) or check if individual messages are slow to process (the real bottleneck might be a downstream call each consumer makes, not consumer *count*).

---

## 8. Tricky / Trap Interview Questions

**Q87. "If horizontal scaling is virtually unlimited, why would anyone ever use vertical scaling?"**
A: Trap — horizontal scaling adds real complexity (distributed consistency, coordination, network overhead) that isn't worth it for small/predictable workloads, strongly-consistent single-node databases, or legacy systems that are hard to refactor into stateless architecture. "Unlimited" capacity doesn't mean "free" or "simple" — the right answer weighs cost/complexity against actual need.

**Q88. "More caching is always better for performance. True or false?"**
A: False — over-caching can introduce staleness bugs, cache-invalidation complexity, and memory pressure/eviction thrashing if the working set doesn't fit. Caching should target genuinely hot, read-heavy, tolerant-of-staleness data; caching everything indiscriminately adds operational risk without proportional benefit.

**Q89. "Sharding always improves performance. True or false?"**
A: False — sharding adds complexity (cross-shard joins/transactions, rebalancing, hotspot risk from a bad key) and should only be introduced once a single, well-tuned, replicated node genuinely can't keep up with writes. Premature sharding is a common anti-pattern.

**Q90. "A load balancer with Round Robin guarantees even load across all servers. True or false?"**
A: False — Round Robin distributes *requests* evenly, not *load*, if request costs vary significantly (e.g., one request does a cheap read, another triggers a heavy report). For heterogeneous workloads, Least Connections or weighted algorithms account for actual server load better.

**Q91. "Stateless services can't have any persistent data at all. True or false?"**
A: False — "stateless" refers to the *application server* not holding session/request state locally between requests. The service can absolutely read/write persistent data in a database or cache; it just can't rely on that data being physically present in its own process/memory for the next unrelated request to work correctly.

**Q92. "Eventual consistency means data is eventually always wrong until it catches up. True or false?"**
A: False/misleading — eventual consistency means all replicas *will* converge to the same value given no further writes, and in practice the lag window is often milliseconds to a few seconds. It's not "wrong," it's "not yet synchronized" — and many systems (social feeds, analytics) tolerate this without correctness issues.

**Q93. "You should always add a CDN for any public-facing system. True or false?"**
A: Mostly true for performance/cost, but the trap is assuming it solves everything — CDNs help with cacheable, mostly-static-or-rarely-changing content. Highly personalized, rapidly-changing, or write-heavy endpoints (e.g., posting a comment) get little benefit from a CDN and need application-layer or database-layer scaling instead.

---

## 9. Rapid-Fire One-Liners

| # | Question | Answer |
|---|----------|--------|
| 94 | Too many reads, what first? | Caching / read replicas |
| 95 | Too many writes, what first? | Sharding (horizontal) |
| 96 | Single server maxed on CPU/RAM short-term fix? | Vertical scaling |
| 97 | Too many open connections? | Connection pooling |
| 98 | Slow queries from joins? | Denormalization |
| 99 | Huge table scans? | Table partitioning |
| 100 | Celebrity-user fan-out problem? | Hybrid push/pull |
| 101 | Hot shard key? | Higher-cardinality key / directory-based sharding |
| 102 | Cache stampede fix? | Locking / stale-while-revalidate / origin shield |
| 103 | Session on a stateful server, scaling problem? | Externalize to Redis |
| 104 | Predictable traffic spike (Black Friday)? | Scheduled auto-scaling + pre-warming |
| 105 | Need routing by URL path? | Layer 7 load balancer |
| 106 | Need raw TCP speed, no path awareness needed? | Layer 4 load balancer |
| 107 | Payment retried twice by flaky client? | Idempotency key (Redis) |
| 108 | Multi-step transaction across services? | Saga pattern |
| 109 | Large group chat fan-out bottleneck? | Pub/sub broadcast, not per-shard routing |
| 110 | Unique IDs at massive scale, no coordination? | Snowflake-style generator |
| 111 | Need strongly consistent distributed lock? | Small Raft/ZooKeeper/etcd cluster |
| 112 | Document collaborative editing without locks? | CRDTs |
| 113 | Avoid cache invalidation entirely for static assets? | Versioned URLs |
| 114 | Producer faster than consumer? | Backpressure / load shedding |

---

## 10. Self-Test Quiz (Answers at the End)

1. You have a stateful login service — users' sessions are in memory. What's the problem when you add a second server? What's the fix?
2. You're getting 10× traffic on Black Friday (a predictable spike). What do you configure in your auto-scaler to avoid degradation when the spike hits?
3. You need to route `/api/*` to backend servers, `/uploads/*` to upload servers, and `/` to frontend servers. Do you need Layer 4 or Layer 7 load balancing?
4. Your app servers are at 90% CPU. Before adding more servers, what should you check first?
5. Why can't you just vertically scale a database forever?
6. A popular product's cache key just expired, and 10,000 requests hit at the same millisecond. What happens without protection, and what's the fix?
7. Your sharding key is `country`, and 80% of your users are in one country. What's this problem called, and how do you fix it?
8. You need millions of unique IDs per second across many servers with zero coordination overhead at generation time. What pattern do you use?
9. Why do distributed lock services (etcd/ZooKeeper) use small clusters instead of scaling horizontally like a web tier?
10. What's the key architectural difference between scaling reads vs. scaling writes?

### Answers

1. **Problem:** A second server has no knowledge of sessions created on the first — users get logged out or see inconsistent state when the load balancer routes them elsewhere. **Fix:** Externalize sessions to a shared store like Redis so any server can look up any user's session.
2. **Config:** Scheduled scaling to pre-warm/add instances *before* the predictable spike hits (not purely reactive scaling, which would be too slow due to boot/warm-up time), plus a higher minimum instance floor during the event window.
3. **Answer:** Layer 7 — routing decisions depend on the URL path, which only Layer 7 (HTTP-aware) load balancers can see.
4. **Check first:** Whether the CPU load is from genuinely necessary compute or from inefficiency — e.g., missing caching (serving every request from scratch), missing indexes causing expensive queries, or a resource leak — before assuming you need more servers.
5. **Why not:** Vertical scaling hits a hard hardware ceiling (max CPU/RAM/disk on the largest available machine) and remains a single point of failure; there's no infinite machine to buy.
6. **Without protection:** A cache stampede — all 10,000 requests miss simultaneously and hit the database at once, potentially overloading it. **Fix:** Locking (one request repopulates, others wait), stale-while-revalidate (serve the old value while refreshing), or probabilistic early expiration.
7. **Called:** A hot partition / hotspot. **Fix:** Use a higher-cardinality shard key (e.g., `user_id` instead of `country`), or use directory-based sharding so the one overloaded region can be split across multiple physical shards.
8. **Pattern:** A Snowflake-style distributed ID generator — each worker gets a unique worker ID once (via etcd) and then generates IDs locally using `timestamp + worker_id + sequence`, with no per-ID coordination needed.
9. **Why small:** Consensus protocols (Raft/Paxos) require a majority quorum to agree on each change, and that coordination overhead grows with cluster size — small odd-sized clusters (3 or 5) balance fault tolerance against low-latency consensus; these systems aren't meant to absorb bulk application traffic, just coordinate metadata.
10. **Key difference:** Scaling reads is "solved" by replication/caching, since copies of the same data can be spread across many nodes/caches without conflict. Scaling writes requires partitioning the data itself (sharding), because a single logical record ultimately has one source of truth and concurrent writes to it must be coordinated or isolated per-partition.

---

*End of HLD Scalability Q&A Bank.*
