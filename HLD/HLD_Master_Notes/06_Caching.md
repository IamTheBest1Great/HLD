# Caching — Consolidated HLD Notes

## Table of Contents

- [1. Caching Fundamentals](#1-caching-fundamentals)
  - [1.1 What Is Caching?](#11-what-is-caching)
    - [1.1.1 Why Caching Matters](#111-why-caching-matters)
    - [1.1.2 Latency Reduction](#112-latency-reduction)
    - [1.1.3 Throughput Improvement](#113-throughput-improvement)
    - [1.1.4 Database Offloading](#114-database-offloading)
    - [1.1.5 Cost Optimization](#115-cost-optimization)
  - [1.2 Where Caching Is Applied](#12-where-caching-is-applied)
    - [1.2.1 Browser Cache](#121-browser-cache)
    - [1.2.2 CDN Cache](#122-cdn-cache)
    - [1.2.3 API Gateway Cache](#123-api-gateway-cache)
    - [1.2.4 Application Cache](#124-application-cache)
    - [1.2.5 Database Query Cache](#125-database-query-cache)
    - [1.2.6 Distributed Cache](#126-distributed-cache)
  - [1.3 What to Cache](#13-what-to-cache)
    - [1.3.1 Frequently Accessed Data](#131-frequently-accessed-data)
    - [1.3.2 Expensive Computations](#132-expensive-computations)
    - [1.3.3 Read-Heavy Data](#133-read-heavy-data)
    - [1.3.4 Static Content](#134-static-content)
    - [1.3.5 Session Data](#135-session-data)
  - [1.4 When NOT to Cache](#14-when-not-to-cache)
    - [1.4.1 Highly Dynamic Data](#141-highly-dynamic-data)
    - [1.4.2 Sensitive Data](#142-sensitive-data)
    - [1.4.3 Write-Heavy Data](#143-write-heavy-data)
    - [1.4.4 Low Reuse Data](#144-low-reuse-data)
- [2. Caching Strategies](#2-caching-strategies)
  - [2.1 Cache-Aside (Lazy Loading)](#21-cache-aside-lazy-loading)
    - [2.1.1 How It Works](#211-how-it-works)
    - [2.1.2 Read Flow](#212-read-flow)
    - [2.1.3 Cache Miss Handling](#213-cache-miss-handling)
    - [2.1.4 Advantages](#214-advantages)
    - [2.1.5 Disadvantages](#215-disadvantages)
    - [2.1.6 Real-World Usage](#216-real-world-usage)
  - [2.2 Read-Through Cache](#22-read-through-cache)
    - [2.2.1 How It Works](#221-how-it-works)
    - [2.2.2 Advantages](#222-advantages)
    - [2.2.3 Disadvantages](#223-disadvantages)
    - [2.2.4 Real-World Usage](#224-real-world-usage)
  - [2.3 Write-Through Cache](#23-write-through-cache)
    - [2.3.1 How It Works](#231-how-it-works)
    - [2.3.2 Consistency Benefits](#232-consistency-benefits)
    - [2.3.3 Latency Tradeoffs](#233-latency-tradeoffs)
    - [2.3.4 Real-World Usage](#234-real-world-usage)
  - [2.4 Write-Back (Write-Behind)](#24-write-back-write-behind)
    - [2.4.1 How It Works](#241-how-it-works)
    - [2.4.2 Async Persistence](#242-async-persistence)
    - [2.4.3 Performance Benefits](#243-performance-benefits)
    - [2.4.4 Durability Risks](#244-durability-risks)
    - [2.4.5 Real-World Usage](#245-real-world-usage)
  - [2.5 Write-Around Cache](#25-write-around-cache)
    - [2.5.1 How It Works](#251-how-it-works)
    - [2.5.2 Cache Pollution Avoidance](#252-cache-pollution-avoidance)
    - [2.5.3 Tradeoffs](#253-tradeoffs)
    - [2.5.4 Real-World Usage](#254-real-world-usage)
  - [2.6 Strategy Comparison](#26-strategy-comparison)
    - [2.6.1 Read Performance](#261-read-performance)
    - [2.6.2 Write Performance](#262-write-performance)
    - [2.6.3 Consistency](#263-consistency)
    - [2.6.4 Complexity](#264-complexity)
    - [2.6.5 Decision Matrix](#265-decision-matrix)
- [3. Cache Eviction Policies](#3-cache-eviction-policies)
  - [3.1 LRU (Least Recently Used)](#31-lru-least-recently-used)
    - [3.1.1 How It Works](#311-how-it-works)
    - [3.1.2 Advantages](#312-advantages)
    - [3.1.3 Limitations](#313-limitations)
  - [3.2 LFU (Least Frequently Used)](#32-lfu-least-frequently-used)
    - [3.2.1 How It Works](#321-how-it-works)
    - [3.2.2 Advantages](#322-advantages)
    - [3.2.3 Limitations](#323-limitations)
  - [3.3 TTL (Time-To-Live)](#33-ttl-time-to-live)
    - [3.3.1 Expiration-Based Eviction](#331-expiration-based-eviction)
    - [3.3.2 Staleness Control](#332-staleness-control)
    - [3.3.3 TTL Selection Strategies](#333-ttl-selection-strategies)
  - [3.4 FIFO & Random Eviction](#34-fifo--random-eviction)
    - [3.4.1 FIFO](#341-fifo)
    - [3.4.2 Random Replacement](#342-random-replacement)
    - [3.4.3 Use Cases](#343-use-cases)
  - [3.5 Choosing an Eviction Policy](#35-choosing-an-eviction-policy)
    - [3.5.1 Access Pattern Analysis](#351-access-pattern-analysis)
    - [3.5.2 Memory Constraints](#352-memory-constraints)
    - [3.5.3 Freshness Requirements](#353-freshness-requirements)
- [4. Distributed Caching](#4-distributed-caching)
  - [4.1 Distributed Cache Fundamentals](#41-distributed-cache-fundamentals)
    - [4.1.1 Why Distributed Caches Are Needed](#411-why-distributed-caches-are-needed)
    - [4.1.2 Horizontal Scaling](#412-horizontal-scaling)
    - [4.1.3 Shared Cache Access](#413-shared-cache-access)
    - [4.1.4 High Availability](#414-high-availability)
  - [4.2 Consistent Hashing](#42-consistent-hashing)
    - [4.2.1 Why Simple Modulo Hashing Fails](#421-why-simple-modulo-hashing-fails)
    - [4.2.2 Hash Ring](#422-hash-ring)
    - [4.2.3 Key Distribution](#423-key-distribution)
    - [4.2.4 Adding Nodes](#424-adding-nodes)
    - [4.2.5 Removing Nodes](#425-removing-nodes)
    - [4.2.6 Virtual Nodes (vnodes)](#426-virtual-nodes-vnodes)
    - [4.2.7 Real-World Usage](#427-real-world-usage)
  - [4.3 Cache Replication](#43-cache-replication)
    - [4.3.1 Leader-Follower Replication](#431-leader-follower-replication)
    - [4.3.2 Multi-Leader Replication](#432-multi-leader-replication)
    - [4.3.3 Peer-to-Peer Replication](#433-peer-to-peer-replication)
    - [4.3.4 Quorum-Based Systems](#434-quorum-based-systems)
    - [4.3.5 Consistency vs Availability](#435-consistency-vs-availability)
  - [4.4 Redis — The Go-To Cache](#44-redis---the-go-to-cache)
    - [4.4.1 Redis Data Structures](#441-redis-data-structures)
    - [4.4.2 Redis Persistence](#442-redis-persistence)
    - [4.4.3 Redis Clustering](#443-redis-clustering)
    - [4.4.4 Redis Sentinel](#444-redis-sentinel)
    - [4.4.5 Common Redis Use Cases](#445-common-redis-use-cases)
  - [4.5 Multi-Layer Caching](#45-multi-layer-caching)
    - [4.5.1 Browser + CDN + App Cache](#451-browser--cdn--app-cache)
    - [4.5.2 Local Cache + Distributed Cache](#452-local-cache--distributed-cache)
    - [4.5.3 Hierarchical Caching](#453-hierarchical-caching)
- [5. Cache Invalidation & Consistency](#5-cache-invalidation--consistency)
  - [5.1 Cache Invalidation](#51-cache-invalidation)
    - [5.1.1 Why Invalidation Is Hard](#511-why-invalidation-is-hard)
    - [5.1.2 TTL-Based Invalidation](#512-ttl-based-invalidation)
    - [5.1.3 Event-Based Invalidation](#513-event-based-invalidation)
    - [5.1.4 Versioned Keys](#514-versioned-keys)
    - [5.1.5 Write-Triggered Invalidation](#515-write-triggered-invalidation)
  - [5.2 Cache Consistency](#52-cache-consistency)
    - [5.2.1 Stale Reads](#521-stale-reads)
    - [5.2.2 Read-After-Write Problems](#522-read-after-write-problems)
    - [5.2.3 Eventual Consistency](#523-eventual-consistency)
    - [5.2.4 Strong Consistency Tradeoffs](#524-strong-consistency-tradeoffs)
  - [5.3 Cache Coherency](#53-cache-coherency)
    - [5.3.1 Distributed Cache Synchronization](#531-distributed-cache-synchronization)
    - [5.3.2 Pub/Sub Invalidation](#532-pubsub-invalidation)
    - [5.3.3 Cache Update Propagation](#533-cache-update-propagation)
- [6. Common Caching Problems & Solutions](#6-common-caching-problems--solutions)
  - [6.1 Cache Stampede (Thundering Herd)](#61-cache-stampede-thundering-herd)
    - [6.1.1 What It Is](#611-what-it-is)
    - [6.1.2 Early Recomputation](#612-early-recomputation)
    - [6.1.3 Probabilistic Expiration](#613-probabilistic-expiration)
    - [6.1.4 Mutex / Distributed Locking](#614-mutex--distributed-locking)
    - [6.1.5 Background Refresh](#615-background-refresh)
    - [6.1.6 Short TTL + Retry](#616-short-ttl--retry)
  - [6.2 Cache Penetration](#62-cache-penetration)
    - [6.2.1 Invalid Key Requests](#621-invalid-key-requests)
    - [6.2.2 Bloom Filters](#622-bloom-filters)
    - [6.2.3 Null Caching](#623-null-caching)
  - [6.3 Cache Breakdown (Hot Key Expiry)](#63-cache-breakdown-hot-key-expiry)
    - [6.3.1 Hotspot Problems](#631-hotspot-problems)
    - [6.3.2 Hot Key Replication](#632-hot-key-replication)
    - [6.3.3 Non-Expiring Hot Keys](#633-non-expiring-hot-keys)
  - [6.4 Cache Avalanche](#64-cache-avalanche)
    - [6.4.1 Simultaneous Expiration](#641-simultaneous-expiration)
    - [6.4.2 Randomized TTLs](#642-randomized-ttls)
    - [6.4.3 Traffic Smoothing](#643-traffic-smoothing)
  - [6.5 Cache Pollution](#65-cache-pollution)
    - [6.5.1 Rarely Used Data](#651-rarely-used-data)
    - [6.5.2 Admission Policies](#652-admission-policies)
- [7. Caching Patterns by System Type](#7-caching-patterns-by-system-type)
  - [7.1 Summary: Caching Techniques by Pattern](#71-summary-caching-techniques-by-pattern)
  - [7.2 Pattern A – CRUD & Storage Systems (Caching Focus)](#72-pattern-a---crud--storage-systems-caching-focus)
  - [7.3 Pattern B – Feed / Timeline Systems (Caching Focus)](#73-pattern-b---feed--timeline-systems-caching-focus)
  - [7.4 Pattern C – Real-Time Systems (Caching Focus)](#74-pattern-c---real-time-systems-caching-focus)
  - [7.5 Pattern D – Search Systems (Caching Focus)](#75-pattern-d---search-systems-caching-focus)
  - [7.6 Pattern E – Geospatial & Location-Based Systems (Caching Focus)](#76-pattern-e---geospatial--location-based-systems-caching-focus)
  - [7.7 Pattern F – Financial & Critical Consistency Systems (Caching Focus)](#77-pattern-f---financial--critical-consistency-systems-caching-focus)
  - [7.8 Pattern G – Large-Scale Processing & Infrastructure (Caching Focus)](#78-pattern-g---large-scale-processing--infrastructure-caching-focus)
  - [7.9 Pattern H – Collaborative & Infrastructure Utilities (Caching Focus)](#79-pattern-h---collaborative--infrastructure-utilities-caching-focus)
- [8. Caching in Real System Design](#8-caching-in-real-system-design)
  - [8.1 Caching in URL Shortener](#81-caching-in-url-shortener)
    - [8.1.1 Hot URL Caching](#811-hot-url-caching)
    - [8.1.2 Redirect Optimization](#812-redirect-optimization)
    - [8.1.3 Edge Caching with CDN](#813-edge-caching-with-cdn)
  - [8.2 Caching in Social Media](#82-caching-in-social-media)
    - [8.2.1 Feed Caching](#821-feed-caching)
    - [8.2.2 User Profile Cache](#822-user-profile-cache)
    - [8.2.3 Recommendation Cache](#823-recommendation-cache)
  - [8.3 Caching in E-Commerce](#83-caching-in-e-commerce)
    - [8.3.1 Product Cache](#831-product-cache)
    - [8.3.2 Inventory Cache](#832-inventory-cache)
    - [8.3.3 Pricing Cache](#833-pricing-cache)
  - [8.4 Caching in Search Engines](#84-caching-in-search-engines)
    - [8.4.1 Query Cache](#841-query-cache)
    - [8.4.2 Snippet Cache](#842-snippet-cache)
    - [8.4.3 Ranking Cache](#843-ranking-cache)
- [9. Interview-Oriented HLD Notes](#9-interview-oriented-hld-notes)
  - [9.1 Key Tradeoffs](#91-key-tradeoffs)
    - [9.1.1 Freshness vs Performance](#911-freshness-vs-performance)
    - [9.1.2 Consistency vs Availability](#912-consistency-vs-availability)
    - [9.1.3 Cost vs Latency](#913-cost-vs-latency)
    - [9.1.4 Complexity vs Scalability](#914-complexity-vs-scalability)
  - [9.2 Common Interview Questions](#92-common-interview-questions)
    - [9.2.1 What Should Be Cached?](#921-what-should-be-cached)
    - [9.2.2 Which Strategy Should Be Used?](#922-which-strategy-should-be-used)
    - [9.2.3 How to Prevent Cache Stampede?](#923-how-to-prevent-cache-stampede)
    - [9.2.4 How to Handle Cache Invalidation?](#924-how-to-handle-cache-invalidation)
  - [9.3 Quick Revision Material](#93-quick-revision-material)
    - [9.3.1 Strategy Comparison Table](#931-strategy-comparison-table)
    - [9.3.2 Eviction Policy Cheat Sheet](#932-eviction-policy-cheat-sheet)
    - [9.3.3 Key Interview Sentences](#933-key-interview-sentences)
    - [9.3.4 Self-Test Questions](#934-self-test-questions)

---

## 1. Caching Fundamentals

### 1.1 What Is Caching?

A **cache** is a temporary, high‑speed storage layer that holds a subset of data, usually the most frequently accessed or recently used. When a request is made, the cache is checked first. On a **cache hit**, data is returned immediately (often in microseconds). On a **miss**, the data is fetched from the slower primary store (database, disk, external API) and a copy is placed in the cache for future requests.

**Analogy:** Your browser’s history – instead of downloading the same web page every time, you load it from local disk.

#### 1.1.1 Why Caching Matters
Caching reduces latency, increases throughput, offloads backend services, and helps absorb traffic spikes. Without caching, every request would hit the database or backend, causing high load and slow responses.

#### 1.1.2 Latency Reduction
In‑memory caches (Redis, Memcached) can serve data in <1ms, while a typical database query takes 10–100ms. This order‑of‑magnitude improvement directly enhances user experience.

#### 1.1.3 Throughput Improvement
A single cache node can handle hundreds of thousands of operations per second, far surpassing a typical database instance. This allows the system to serve more concurrent users.

#### 1.1.4 Database Offloading
By absorbing the majority of read traffic, the cache dramatically reduces the number of queries hitting the database, freeing it to handle more writes and complex transactions.

#### 1.1.5 Cost Optimization
Fewer database replicas and lower I/O requirements translate into reduced infrastructure costs. Caching is often the most cost‑effective performance optimization.

### 1.2 Where Caching Is Applied

#### 1.2.1 Browser Cache
The user’s browser stores static assets (CSS, JS, images) and even full pages. Controlled via HTTP headers (`Cache‑Control`, `ETag`). Latency: 0ms (local). Ideal for static resources and user preferences.

#### 1.2.2 CDN Cache
Content Delivery Networks (CloudFront, Cloudflare, Akamai) cache content at edge locations close to users. Latency: ~10‑50ms. Used for static assets, media, and even dynamic but cacheable responses (e.g., redirects).

#### 1.2.3 API Gateway Cache
An API gateway or reverse proxy (NGINX, Varnish) can cache entire HTTP responses. Latency: ~1‑5ms. Reduces load on upstream services.

#### 1.2.4 Application Cache
In‑memory caches within the application process (Caffeine, Guava). Latency: ~0.1‑1ms. Very fast but not shared across multiple application servers.

#### 1.2.5 Database Query Cache
Some databases can cache query results (MySQL query cache, PostgreSQL buffer pool). Modern systems often prefer external caches like Redis for better control and scalability.

#### 1.2.6 Distributed Cache
A separate caching service shared by all application instances. Examples: Redis Cluster, Memcached. Latency: ~1‑5ms. Provides a consistent view and scales horizontally.

### 1.3 What to Cache

#### 1.3.1 Frequently Accessed Data
Data that is requested repeatedly (e.g., popular user profiles, viral content). High cache hit rates justify the memory cost.

#### 1.3.2 Expensive Computations
Results of CPU‑intensive operations, complex aggregations, or ML inference. Caching avoids recomputation.

#### 1.3.3 Read-Heavy Data
Data where reads vastly outnumber writes (e.g., product catalog, news articles). Caching is most beneficial here.

#### 1.3.4 Static Content
Images, CSS, JavaScript, and other assets that rarely change. Use CDN or browser cache with long TTLs.

#### 1.3.5 Session Data
User session tokens, shopping carts, and temporary state. Often stored in Redis with TTL matching the session timeout.

### 1.4 When NOT to Cache

#### 1.4.1 Highly Dynamic Data
Data that changes every second (e.g., real‑time stock prices, live counters). Caching may add little value or require extremely short TTLs that negate the benefit.

#### 1.4.2 Sensitive Data
Unless encrypted and properly secured, sensitive data (PII, financial credentials) should not be cached in shared or external caches.

#### 1.4.3 Write-Heavy Data
If every read is immediately followed by a write, caching may not reduce database load significantly and could introduce complexity.

#### 1.4.4 Low Reuse Data
Data that is accessed only once or very rarely. Caching wastes memory and can even degrade performance (eviction overhead).

---

## 2. Caching Strategies

### 2.1 Cache-Aside (Lazy Loading)

#### 2.1.1 How It Works
The application is responsible for interacting with both the cache and the database. It checks the cache first; on a miss, it loads the data from the database, stores it in the cache, and returns it.

```
Read:    Application → Cache (miss) → Database → Cache (set) → Client
Write:   Application → Database → (optional cache invalidation)
```

#### 2.1.2 Read Flow
1. Check cache for the key.
2. On hit → return data.
3. On miss → read from database, write to cache, return data.

#### 2.1.3 Cache Miss Handling
The cache miss penalty involves three steps: cache check (fail), database query (success), and cache population. The first request after a miss is slower. To mitigate, use background pre‑warming or request coalescing.

#### 2.1.4 Advantages
- Only data that is actually requested gets cached (efficient memory use).
- Resilient to cache failures – the database still serves requests.
- Works with any backend, simple to implement.

#### 2.1.5 Disadvantages
- Cache miss latency (3 round trips).
- Data can become stale if the database is updated but the cache is not invalidated.
- Requires application logic to manage cache.

#### 2.1.6 Real-World Usage
The most common strategy. Used in URL shorteners, social media feeds, e‑commerce catalogs, and many web applications.

**Example (Python):**
```python
def get_user(user_id):
    user = cache.get(f"user:{user_id}")
    if user:
        return user
    user = db.query("SELECT * FROM users WHERE id = ?", user_id)
    cache.set(f"user:{user_id}", user, ttl=3600)
    return user
```

### 2.2 Read-Through Cache

#### 2.2.1 How It Works
The cache sits between the application and the database. The application always reads from the cache. On a miss, the cache itself fetches the data from the database, stores it, and returns it.

```
Read:    Application → Cache (miss) → Cache loads from DB → Cache stores → Client
```

#### 2.2.2 Advantages
- Application code is simplified – it only talks to the cache.
- The cache can coordinate loading strategies (e.g., read‑ahead).

#### 2.2.3 Disadvantages
- Requires the cache to support custom loader logic.
- First access to cold data is still slow (miss penalty).
- More complex to set up than cache‑aside.

#### 2.2.4 Real-World Usage
Common with ORM‑integrated caches (Hibernate 2nd level cache, Caffeine with loaders).

### 2.3 Write-Through Cache

#### 2.3.1 How It Works
Writes are first performed on the cache, which then synchronously writes to the database. Both operations must succeed before the write is acknowledged.

```
Write:   Application → Cache → Database → (both updated) → Client
```

#### 2.3.2 Consistency Benefits
The cache always holds the latest data – no stale reads. Ideal for scenarios where read‑after‑write consistency is critical.

#### 2.3.3 Latency Tradeoffs
Higher write latency (cache + DB round trips). Not suitable for write‑heavy workloads unless combined with batching.

#### 2.3.4 Real-World Usage
Used for user profile updates, custom alias creation in URL shorteners, and other cases where immediate visibility is required.

**Example:**
```python
def update_user(user_id, data):
    cache.set(f"user:{user_id}", data)
    db.execute("UPDATE users SET ... WHERE id = ?", data)
```

### 2.4 Write-Back (Write-Behind)

#### 2.4.1 How It Works
Writes go only to the cache and are acknowledged immediately. The cache asynchronously persists the data to the database later (batched or after a delay).

```
Write:   Application → Cache (ack) → (later) Cache → Database
```

#### 2.4.2 Async Persistence
Background workers or threads flush dirty entries to the database. This decouples write latency from database I/O.

#### 2.4.3 Performance Benefits
Extremely low write latency and the ability to batch writes reduces database load.

#### 2.4.4 Durability Risks
If the cache crashes before flushing, unpersisted writes are lost. This strategy demands durable write‑ahead logs or replication to mitigate data loss.

#### 2.4.5 Real-World Usage
Ideal for high‑throughput, loss‑tolerant workloads like analytics counters, like counts, and gaming scores.

### 2.5 Write-Around Cache

#### 2.5.1 How It Works
Writes go directly to the database, bypassing the cache entirely. The cache is populated only on a subsequent read (cache‑aside style).

```
Write:   Application → Database (cache untouched)
Read:    Cache → (miss) → Database → populate cache
```

#### 2.5.2 Cache Pollution Avoidance
Prevents the cache from being filled with data that is written but rarely read again, preserving space for hot data.

#### 2.5.3 Tradeoffs
Reads after a write will experience a cache miss and higher latency until the cache is refreshed. Suitable when writes are frequent but reads of the same data are rare.

#### 2.5.4 Real-World Usage
Log ingestion, one‑time reports, and scenarios where write‑once‑read‑rarely is the norm.

### 2.6 Strategy Comparison

#### 2.6.1 Read Performance
- Cache‑Aside / Read‑Through: Fast after the first miss.
- Write‑Through: Always fast (data is already in cache).
- Write‑Back: Fast reads (data in cache).
- Write‑Around: Slow first read after a write.

#### 2.6.2 Write Performance
- Write‑Back: Very fast (cache only).
- Cache‑Aside / Read‑Through: Moderate (DB write).
- Write‑Through: Slower (cache + DB).
- Write‑Around: Moderate (DB only, no cache overhead).

#### 2.6.3 Consistency
- Write‑Through: Strong consistency.
- Write‑Back: Eventual (risk of data loss).
- Cache‑Aside / Read‑Through / Write‑Around: Eventual (stale reads possible).

#### 2.6.4 Complexity
- Cache‑Aside: Low to moderate.
- Read‑Through: Moderate.
- Write‑Through: Moderate.
- Write‑Back: High (async flushing, failure handling).
- Write‑Around: Low.

#### 2.6.5 Decision Matrix

| Strategy | Write Speed | Read Speed | Consistency | Best For |
|----------|-------------|------------|-------------|----------|
| Cache‑Aside | Normal | Fast* | Eventual | General read‑heavy |
| Read‑Through | Normal | Fast* | Eventual | Simplified app code |
| Write‑Through | Slow | Very Fast | Strong | Consistency‑critical |
| Write‑Back | Very Fast | Very Fast | Eventual | Extreme write throughput |
| Write‑Around | Normal | Slow (first) | Eventual | Write‑once, read‑rarely |

*Fast after first miss.

---

## 3. Cache Eviction Policies

### 3.1 LRU (Least Recently Used)

#### 3.1.1 How It Works
Evicts the item that has not been accessed for the longest time. Relies on temporal locality: recently used items are likely to be used again soon.

Implementation: hash map + doubly linked list. O(1) access and O(1) eviction.

#### 3.1.2 Advantages
- Simple and effective for many workloads.
- Good temporal locality.

#### 3.1.3 Limitations
- Can be polluted by one‑time large scans (scan‑resistant variants exist).
- Overhead of maintaining access order.

### 3.2 LFU (Least Frequently Used)

#### 3.2.1 How It Works
Evicts the item with the lowest access frequency. Each item has a counter; the one with the smallest count is removed. Variants use aging to avoid keeping historically popular but now cold items.

#### 3.2.2 Advantages
- Retains frequently accessed items, even if not recently used.
- Excellent for stable popularity distributions (e.g., product catalog).

#### 3.2.3 Limitations
- Higher implementation complexity (frequency counters).
- Can suffer from "cache pollution" by past popularity unless aging is used.

### 3.3 TTL (Time-To-Live)

#### 3.3.1 Expiration-Based Eviction
Each entry has a fixed lifespan. After the TTL expires, the entry is automatically removed, regardless of access frequency.

#### 3.3.2 Staleness Control
TTL ensures data does not become too stale. It's a freshness mechanism rather than a pure eviction policy.

#### 3.3.3 TTL Selection Strategies
- Short TTL (seconds) for rapidly changing data (e.g., inventory, stock prices).
- Long TTL (hours/days) for stable data (e.g., reference tables).
- Randomized TTL to avoid simultaneous expiry of many keys (cache avalanche prevention).

### 3.4 FIFO & Random Eviction

#### 3.4.1 FIFO
Removes the oldest item by insertion time. Simple but often leads to poor hit rates because it ignores access patterns.

#### 3.4.2 Random Replacement
Randomly selects a victim. Surprisingly effective in some workloads, with very low overhead.

#### 3.4.3 Use Cases
Rarely used in production caches for general workloads; occasionally used when simplicity and low CPU overhead are paramount.

### 3.5 Choosing an Eviction Policy

#### 3.5.1 Access Pattern Analysis
- Strong temporal locality → LRU.
- Stable popularity distribution → LFU.
- Data freshness critical → TTL with any policy.

#### 3.5.2 Memory Constraints
LRU and LFU have higher memory overhead (maintaining order/counters). Random and FIFO are more memory‑efficient.

#### 3.5.3 Freshness Requirements
Combine TTL with LRU/LFU to ensure data is both fresh and frequently used.

---

## 4. Distributed Caching

### 4.1 Distributed Cache Fundamentals

#### 4.1.1 Why Distributed Caches Are Needed
A single cache server has limited memory and CPU. To scale read throughput and cache capacity horizontally, a cluster of cache nodes is used.

#### 4.1.2 Horizontal Scaling
Data is partitioned (sharded) across multiple nodes. Each node holds a subset of keys, increasing total cache size and throughput.

#### 4.1.3 Shared Cache Access
All application servers access the same distributed cache, ensuring consistency of cached data across the system.

#### 4.1.4 High Availability
Cache nodes can be replicated (leader‑follower) or use peer‑to‑peer replication to tolerate failures without data loss.

### 4.2 Consistent Hashing

#### 4.2.1 Why Simple Modulo Hashing Fails
`hash(key) % N` causes nearly all keys to be remapped when the number of nodes `N` changes (added or removed). This leads to a massive cache miss storm, potentially overloading the database.

Consistent hashing ensures only `K/N` keys are remapped, where `K` is the total number of keys.

#### 4.2.2 Hash Ring
A conceptual circle from 0 to 2³²–1. Both cache nodes and keys are hashed to positions on the ring. A key is assigned to the first node encountered moving clockwise from the key’s position.

```
Ring: 
Node A at 45°, Node B at 150°, Node C at 270°
Key X at 100° → clockwise → Node B at 150°
```

#### 4.2.3 Key Distribution
Keys are spread across nodes based on their hash positions. If the ring is balanced, distribution is uniform.

#### 4.2.4 Adding Nodes
A new node is placed at its hash position. Only the keys that now fall between the new node and its predecessor move. Other keys are unaffected.

#### 4.2.5 Removing Nodes
When a node is removed, its keys are taken over by the next clockwise node. No other keys move.

#### 4.2.6 Virtual Nodes (vnodes)
To improve load balancing, each physical node is represented by many virtual nodes (e.g., 150‑256) scattered across the ring. This statistically evens out the distribution and allows proportional capacity.

#### 4.2.7 Real-World Usage
- Cassandra and DynamoDB use consistent hashing for data partitioning.
- Redis Cluster uses a hash slot model (16384 slots) similar in spirit.
- CDN edge routing uses consistent hashing to minimize cache disruption when servers are added/removed.

**Simplified Python example:**
```python
import hashlib, bisect

class ConsistentHash:
    def __init__(self, nodes, replicas=100):
        self.ring = {}
        self.sorted_keys = []
        for node in nodes:
            self.add_node(node)
    def _hash(self, key):
        return int(hashlib.md5(key.encode()).hexdigest(), 16)
    def add_node(self, node):
        for i in range(self.replicas):
            h = self._hash(f"{node}:{i}")
            self.ring[h] = node
            self.sorted_keys.append(h)
        self.sorted_keys.sort()
    def get_node(self, key):
        if not self.ring: return None
        h = self._hash(key)
        idx = bisect.bisect(self.sorted_keys, h)
        if idx == len(self.sorted_keys): idx = 0
        return self.ring[self.sorted_keys[idx]]
```

### 4.3 Cache Replication

#### 4.3.1 Leader-Follower Replication
One node is the leader (accepts writes), followers replicate asynchronously. Reads can go to any follower. If the leader fails, a follower is promoted. Used in Redis (Sentinel/Cluster).

**Pros:** Simple, scales reads.  
**Cons:** Write bottleneck, replication lag.

#### 4.3.2 Multi-Leader Replication
Multiple nodes accept writes and propagate to each other. Conflicts must be resolved (last‑write‑wins, vector clocks, CRDTs). Used in Cassandra, Riak.

#### 4.3.3 Peer-to-Peer Replication
All nodes are equal; data is replicated to a configurable number of nodes (replication factor). Quorum reads/writes provide tunable consistency. Used in Cassandra, DynamoDB.

#### 4.3.4 Quorum-Based Systems
Write to `W` replicas, read from `R` replicas. If `R + W > N`, strong consistency is achieved. Example: Cassandra with `QUORUM`.

#### 4.3.5 Consistency vs Availability
- Synchronous replication: strong consistency, lower availability.
- Asynchronous replication: high availability, eventual consistency.

### 4.4 Redis — The Go-To Cache

#### 4.4.1 Redis Data Structures
Strings, Lists, Sets, Sorted Sets, Hashes, Bitmaps, HyperLogLog, Streams. Provides atomic operations (INCR, LPUSH, etc.).

#### 4.4.2 Redis Persistence
Optional: RDB snapshots (point‑in‑time) and AOF (append‑only file). Can be configured for durability at the cost of performance.

#### 4.4.3 Redis Clustering
Redis Cluster shards data across multiple masters (16384 hash slots). Each master can have replicas. Automatic failover. Clients need to support cluster protocol.

#### 4.4.4 Redis Sentinel
Provides high availability for non‑clustered Redis setups. Monitors masters, promotes replicas on failure, and notifies clients.

#### 4.4.5 Common Redis Use Cases
- Session store: `SETEX session:xyz 3600 "{user_id}"`
- Rate limiting: `INCR rate:ip:1.2.3.4` + `EXPIRE`
- Leaderboard: `ZADD leaderboard 1500 "user:alice"`
- Distributed locks: `SET lock:resource token NX EX 30`
- Pub/Sub: `PUBLISH channel "message"`
- Cache: `SET user:123 "{...}" EX 300`

### 4.5 Multi-Layer Caching

#### 4.5.1 Browser + CDN + App Cache
A typical modern web stack uses multiple cache layers:
- Browser cache for static assets (0ms).
- CDN for edge caching of public content (~10‑50ms).
- Application cache (Redis/Memcached) for dynamic data (~1‑5ms).

```
USER REQUEST → CDN (80% hit) → NGINX/Varnish (10% of misses) → Redis (90% of remaining) → DB
```

#### 4.5.2 Local Cache + Distributed Cache
A local in‑process cache (L1) provides sub‑millisecond access for hot keys, while a distributed cache (L2) ensures consistency and capacity. Cache invalidation messages propagate between them.

#### 4.5.3 Hierarchical Caching
- L1: In‑memory, per‑server (Caffeine) – extremely fast.
- L2: Distributed Redis Cluster – shared across servers.
- L3: CDN – edge caching for static and semi‑static content.

---

## 5. Cache Invalidation & Consistency

### 5.1 Cache Invalidation

#### 5.1.1 Why Invalidation Is Hard
Keeping the cache in sync with the source of truth (database) is famously one of the hardest problems in computer science. Too strict invalidation hurts performance; too loose leads to stale data.

#### 5.1.2 TTL-Based Invalidation
Each cache entry has a time‑to‑live. After expiry, it is automatically removed. Simple and reliable, but may serve stale data within the TTL window.

#### 5.1.3 Event-Based Invalidation
On a database update, an event is published (e.g., via message queue or change data capture). Cache nodes listen and delete or update the affected key. Provides near‑real‑time consistency.

#### 5.1.4 Versioned Keys
Cache keys include a version number or hash of the data. When data changes, a new key is used, and old entries expire naturally. Avoids explicit invalidation.

#### 5.1.5 Write-Triggered Invalidation
In cache‑aside, after writing to the database, the application explicitly deletes or updates the corresponding cache entry. Immediate consistency but adds application complexity.

### 5.2 Cache Consistency

#### 5.2.1 Stale Reads
A read from cache may return outdated data if the database was updated without invalidating the cache. Mitigations: short TTL, write‑through, or immediate invalidation.

#### 5.2.2 Read-After-Write Problems
A user writes data and then immediately reads it. If the read hits a stale cache, the user sees old data. Solutions:
- Route post‑write reads to the leader/primary.
- Use write‑through to keep cache updated.
- Use client‑side timestamps and wait for replication.

#### 5.2.3 Eventual Consistency
Many caching systems accept eventual consistency: data will be correct after the TTL expires or after an invalidation event propagates. This enables high performance at the cost of temporary staleness.

#### 5.2.4 Strong Consistency Tradeoffs
Achieving strong consistency (immediate) requires synchronous updates (write‑through) or distributed locking, which increases latency and reduces availability during partitions.

### 5.3 Cache Coherency

#### 5.3.1 Distributed Cache Synchronization
In a multi‑node cache cluster, ensure all nodes agree on the state of cached data. Strategies include pub/sub invalidation, global version numbers, and quorum‑based updates.

#### 5.3.2 Pub/Sub Invalidation
A central channel (Redis Pub/Sub, Kafka) broadcasts invalidation messages. All cache nodes subscribe and evict or refresh the affected keys.

#### 5.3.3 Cache Update Propagation
Updates can be pushed (write‑through/write‑behind) or pulled (TTL, polling). Pushing provides faster consistency; pulling is simpler.

---

## 6. Common Caching Problems & Solutions

### 6.1 Cache Stampede (Thundering Herd)

#### 6.1.1 What It Is
When a popular cache entry expires, multiple concurrent requests all experience a cache miss simultaneously, causing a flood of requests to the backend (database), potentially overloading it.

#### 6.1.2 Early Recomputation
Proactively refresh the cache entry before it expires. A background job recalculates the value and updates the cache, so TTL never reaches zero.

#### 6.1.3 Probabilistic Expiration
When a request finds the remaining TTL below a threshold, it probabilistically triggers an async refresh. This spreads the recomputation load.

```python
if ttl_remaining < threshold and random.random() < (threshold - ttl_remaining)/threshold:
    async_refresh(key)
```

#### 6.1.4 Mutex / Distributed Locking
Only one request is allowed to recompute the value; others wait for the lock to be released and then read the fresh cache.

```python
lock_key = f"lock:{key}"
if cache.setnx(lock_key, "locked", expire=10):
    # recompute and update cache
    cache.delete(lock_key)
else:
    sleep(0.1)
    return get_with_lock(key)
```

#### 6.1.5 Background Refresh
Instead of blocking, serve stale data and queue a refresh job. Once the job completes, the cache is updated. Acceptable when serving slightly stale data is tolerable.

#### 6.1.6 Short TTL + Retry
Use very short TTLs combined with jittered retry logic to spread the miss storm across time.

### 6.2 Cache Penetration

#### 6.2.1 Invalid Key Requests
Requests for non‑existent keys bypass the cache (since there is nothing to cache) and hit the database every time. Malicious actors can exploit this to overload the database.

#### 6.2.2 Bloom Filters
A memory‑efficient probabilistic structure that can quickly answer "definitely not in set" for keys. Before querying the database, check the Bloom filter. If it says "no", return immediately.

#### 6.2.3 Null Caching
When the database returns "not found", cache a null value with a short TTL. Subsequent requests for the same key will get a cache hit (null) and avoid the DB.

### 6.3 Cache Breakdown (Hot Key Expiry)

#### 6.3.1 Hotspot Problems
A single extremely popular key (e.g., a viral event) expires, and all traffic for that key hits the database at once.

#### 6.3.2 Hot Key Replication
Replicate the hot key across multiple cache nodes or within the same node via read replicas. Clients randomly pick a replica to spread the load.

#### 6.3.3 Non-Expiring Hot Keys
For known hot keys, avoid automatic expiration. Use manual refresh or a dedicated background process to update the value without ever deleting it.

### 6.4 Cache Avalanche

#### 6.4.1 Simultaneous Expiration
Many keys expire at the same time (e.g., set with identical TTL), causing a massive wave of cache misses and database overload.

#### 6.4.2 Randomized TTLs
Add a random jitter to the TTL when setting keys. For example, instead of `TTL=3600`, use `TTL=3600 + random(0, 600)`.

#### 6.4.3 Traffic Smoothing
Implement request coalescing and gradual traffic release to avoid a sudden surge of requests to the backend.

### 6.5 Cache Pollution

#### 6.5.1 Rarely Used Data
Data that is written once and never read again occupies cache space and causes the eviction of useful hot data.

#### 6.5.2 Admission Policies
Use a cache admission filter (e.g., TinyLFU, probabilistic filtering) to only admit items that are likely to be useful. Combine with write‑around strategy to avoid polluting the cache.

---

## 7. Caching Patterns by System Type

### 7.1 Summary: Caching Techniques by Pattern

| Pattern | System Type | Primary Cache Strategy | Cache Technology | Eviction Policy | Why |
|---------|-------------|------------------------|------------------|-----------------|-----|
| **A: CRUD / Storage** | URL shortener, Pastebin, Bookstore, Ticket booking, Dropbox | **Cache‑aside** (read‑through) | Redis / Memcached | LRU + TTL | Reads are frequent; writes update cache on miss or via invalidation |
| **B: Feed / Timeline** | Twitter, Instagram, Facebook, LinkedIn | **Write‑through / cache‑aside** for timelines | Redis sorted sets, Memcached | LRU, TTL (short for feeds) | Feeds are read‑heavy; cache pre‑computed timelines |
| **C: Real‑Time** | WhatsApp, Slack, Live Comments, Game backend | **In‑memory state cache** + **write‑behind** | Redis (hash, sorted set) | TTL for presence, LRU for message cache | Low latency needed; presence is ephemeral |
| **D: Search** | Google Search, YouTube Search, Amazon Search, Autocomplete | **Query result cache**, **index cache** | Redis, CDN | LRU, LFU | Expensive queries can be cached; popular searches hit cache |
| **E: Geospatial** | Uber, Google Maps, Food Delivery, Nearby Friends | **Location cache** (Redis Geo), **tile cache** (CDN) | Redis Geo, CloudFront, Memcached | TTL (short for locations), LRU for tiles | Driver locations change frequently; map tiles are static |
| **F: Financial** | Payment gateway, Wallet, UPI, Job scheduler | **Idempotency cache**, **balance cache** | Redis (with persistence) | TTL (idempotency keys), LRU | Idempotency keys need fast lookup; balance cache reduces DB load |
| **G: Large‑Scale Processing** | Analytics, Clickstream, Logging, Metrics, Kafka‑like | **Windowed state cache** (Flink/RocksDB), **query cache** (Druid) | RocksDB, Redis, Memcached | LRU, size‑based | Stream processing needs state; queries on hot data benefit from caching |
| **H: Collaborative & Infrastructure** | Google Docs, Figma, Distributed locks, Snowflake, Web Crawler | **CRDT state cache**, **Bloom filter cache** | Redis, in‑memory (CRDT) | TTL, LRU | Real‑time collaboration needs low latency; dedup uses Bloom filter |

---

### 7.2 Pattern A – CRUD & Storage Systems (Caching Focus)

| Question | Caching Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **URL Shortener** | **Cache‑aside** (Redis) for short code → long URL; **CDN** for redirects (optional) | Redirects are 100x more frequent than creations. Cache reduces DB load and latency. | On redirect: check Redis; on miss, read DB, write to Redis with TTL (e.g., 24h). On creation: write DB, delete cache entry (or update). Use Redis Cluster for scalability. |
| **Pastebin** | **CDN** for paste content; **Redis** for metadata (paste ID → S3 key) | Paste content is static; CDN caches globally. Metadata cache reduces S3 metadata lookups. | Paste content: serve via CDN with long TTL (1 year). Metadata: Redis with TTL (7 days). On paste creation, warm the cache. |
| **Online Bookstore** | **Redis** for product catalog cache; **CDN** for product images; **Query cache** for search results | Product catalog is read‑heavy. Caching reduces DB read replicas cost. | Product cache: key = `product_id`, TTL = 5 min (or invalidate on price change). Search results cache: key = query hash, TTL = 1 min. Use cache‑aside pattern. |
| **Ticket Booking** | **Redis** for seat lock cache (short‑term); **Cache‑aside** for event metadata | Seat locks need microsecond TTL (5 min). Event metadata (venue, time) is read‑heavy. | Seat lock: `SETEX seat:event:row:num 300 "user_id"`. Event metadata: Redis hash, TTL = 10 min. On booking, invalidate seat lock cache. |
| **Dropbox / Google Drive** | **CDN** for file downloads; **Redis** for directory listing cache; **Browser cache** (ETags) | Files are large; CDN offloads origin. Directory listings are frequent. | CDN: cache file chunks with TTL based on file popularity. Redis: `dir:{user_id}:{path}` → list of files, TTL = 5 min. Invalidate on file upload/delete. |

---

### 7.3 Pattern B – Feed / Timeline Systems (Caching Focus)

| Question | Caching Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Twitter / X** | **Redis sorted set** for user timeline cache; **Memcached** for tweet metadata; **Write‑through** on tweet creation | Timelines are pre‑computed; reading from cache is faster than DB. | For each user, store tweet IDs in Redis sorted set (score = timestamp). On new tweet, push to followers’ timeline caches (write‑through). Limit to last 800 tweets per user. |
| **Instagram Feed** | **Redis sorted set** for feed; **CDN** for images; **Client‑side cache** (local storage) | Images are large; feed IDs are small. CDN caches images. | Feed cache: same as Twitter. Images: CDN with long TTL. Client caches image URLs with `Cache-Control: max-age=3600`. |
| **Facebook News Feed** | **Memcached** for pre‑computed feed (ranked); **Edge caching** (CDN) for static assets; **Graph cache** for social graph | News feed is expensive to compute; caching reduces recomputation. | Pre‑compute feed for top N stories per user, store in Memcached (key = user_id, value = list of post IDs). Use TTL = 1 min for real‑time, or invalidate on new posts. |
| **LinkedIn Feed** | **Redis** for profile cache; **Cache‑aside** for posts | Pull model: on read, need fast profile lookups. | Profile cache: `profile:{user_id}` → JSON, TTL = 1 hour. Posts: cache individual posts (popular posts). Use LRU eviction. |

---

### 7.4 Pattern C – Real-Time Systems (Caching Focus)

| Question | Caching Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **WhatsApp / Messenger** | **Redis** for presence cache (TTL); **In‑memory** for recent messages (per connection) | Presence needs fast expiration. Recent messages avoid DB reads. | Presence: `SETEX presence:{user_id} 30 "online"`. Recent messages: per conversation, store last 50 messages in Redis list (`LPUSH`, `LTRIM`). |
| **Slack / Discord** | **Redis** for unread counts; **Memcached** for channel metadata; **Write‑behind** for message history | Unread counts change frequently. Channel metadata is read‑heavy. | Unread counts: Redis hash `unread:{user_id}` → `{channel_id: count}`. Channel metadata: Memcached TTL=5 min. On new message, increment unread count in Redis. |
| **Live Comments (YouTube Live)** | **Redis sorted set** for recent comments (per video); **Client‑side cache** (comment buffer) | Comments are high velocity; only recent comments matter. | Redis: `comments:video_id` sorted set (score = timestamp), limit to last 500 comments. Client polls or WebSocket; client caches comments to avoid re‑rendering. |
| **Multiplayer Game Backend** | **Redis** for game state (in‑memory); **Client‑side prediction** (not a cache but similar) | Game state needs microsecond latency; Redis provides that. | Store game state as Redis hash (players, positions, scores). Use Redis replication for durability. Client caches last known state for prediction. |

---

### 7.5 Pattern D – Search Systems (Caching Focus)

| Question | Caching Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Google Search** | **Redis** for popular query results; **CDN** for static UI; **Browser cache** for search results (short TTL) | Expensive to run search on every keystroke. Popular queries are repetitive. | Cache query results: key = query hash + page, TTL = 5 min. Use cache‑aside. For trending queries, pre‑warm cache. CDN for images, CSS, JS. |
| **YouTube Search** | **Redis** for search result cache; **CDN** for video thumbnails; **Browser cache** for search history | Similar to Google Search, plus thumbnails are static. | Search result cache: TTL = 10 min (because new videos appear). Thumbnails: CDN with long TTL (1 year). Invalidate cache on new uploads via background job. |
| **Amazon Product Search** | **Redis** for product metadata cache; **Query cache** for search results; **Inventory cache** (short TTL) | Product details change rarely; inventory changes frequently. | Product metadata: TTL = 1 hour. Search results: TTL = 1 min (inventory‑aware). Inventory cache: TTL = 10 seconds, invalidated on purchase. |
| **Search Autocomplete** | **CDN** for static trie (pre‑computed); **Redis** for real‑time trending prefixes; **Client‑side cache** (localStorage) | Trie is large but can be cached at edge. Client cache reduces network calls. | Build trie hourly, push to CDN as JSON. Client fetches and caches locally (localStorage). Redis for real‑time suggestions: `ZRANK` on prefix. |

---

### 7.6 Pattern E – Geospatial & Location-Based Systems (Caching Focus)

| Question | Caching Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Uber / Lyft** | **Redis Geo** for driver location cache (in‑memory); **Write‑behind** to DB; **Client‑side cache** for rider’s last known location | Driver locations change every few seconds; DB writes are expensive. | Use Redis Geo (`GEOADD` on each update). Periodically (every 5s) flush to DB in background. Client caches rider’s last location for 1 second to reduce network calls. |
| **Google Maps / Nearby Places** | **CDN** for map tiles; **Redis** for place search results; **Browser cache** for tile images | Map tiles are static and huge; CDN is essential. | Map tiles: CDN with TTL = 1 year. Place search: cache results for popular locations (e.g., “restaurants near Times Square”) with TTL = 1 hour. |
| **Food Delivery (DoorDash)** | **Redis Geo** for driver location; **Redis** for restaurant menu cache; **CDN** for restaurant images | Same as Uber, plus menus change rarely. | Driver location: Redis Geo (same as Uber). Restaurant menu: Redis hash, TTL = 15 min (invalidate on menu update). |
| **Nearby Friends** | **Redis Geo** for user locations; **Client‑side cache** for friend locations; **TTL = 5 seconds** | Location updates are frequent; client cache reduces server load. | Redis Geo stores current location. Client polls every 5 seconds; client caches friend locations for 5 seconds to avoid re‑rendering. |

---

### 7.7 Pattern F – Financial & Critical Consistency Systems (Caching Focus)

| Question | Caching Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Payment Gateway (Stripe)** | **Redis** for idempotency key cache; **Write‑through** to DB; **Cache‑aside** for merchant config | Idempotency keys need fast lookup (high QPS). Merchant config is read‑heavy. | Idempotency cache: `SETEX idempotency:{key} 86400 "response"`. Check cache first; on miss, check DB. Merchant config: Redis hash, TTL = 1 hour. |
| **Wallet System** | **Redis** for balance cache with **optimistic locking**; **Write‑behind** for transaction log | Balance reads are frequent; caching reduces DB load. | Balance cache: `WATCH balance:{user_id}` + `MULTI` + `SET`. Write‑behind: accumulate balance changes in Redis and flush to DB every 10 seconds. |
| **UPI-like System** | **Redis** for idempotency cache; **Cache‑aside** for bank routing info | Idempotency across banks; bank routing changes rarely. | Same as Stripe for idempotency. Bank routing: Redis with TTL = 1 day, refreshed from DB. |
| **Distributed Job Scheduler** | **Redis** for job queue (list or sorted set); **No cache for job state** (use DB for durability) | Job queue needs low latency; state is durable in DB. | Use Redis list for pending jobs (`LPUSH` / `RPOP`). For scheduled jobs, use sorted set (score = timestamp). Workers fetch from Redis, then mark complete in DB. |

---

### 7.8 Pattern G – Large-Scale Processing & Infrastructure (Caching Focus)

| Question | Caching Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Google Analytics** | **Redis** for real‑time counters; **Memcached** for query result cache; **CDN** for dashboards | Real‑time dashboards need low latency; historical queries can be cached. | Real‑time counters: Redis `INCR` per dimension. Query results: cache with TTL = 1 hour (or invalidate on new data). Dashboard assets on CDN. |
| **Clickstream Processing** | **RocksDB** as state backend for Flink (local cache); **Redis** for session cache; **LRU** eviction | Flink state is a cache of recent events per key. Session data is hot. | Flink uses RocksDB as keyed state (on disk with memory cache). Redis: session metadata with TTL = session timeout (e.g., 30 min). |
| **Distributed Logging (ELK)** | **Elasticsearch query cache** (LRU); **Filebeat registry** (local cache); **Browser cache** for Kibana | Search queries on logs are repetitive; Elasticsearch caches filters and aggregations. | Elasticsearch: enable `index.requests.cache.enable`. Filebeat stores registry locally to avoid re‑sending logs. Kibana assets on CDN. |
| **Metrics Monitoring (Prometheus)** | **Prometheus TSDB cache** (in‑memory index); **Query result cache** (via Thanos or VictoriaMetrics); **LRU** | Prometheus keeps recent samples in memory; query caching for dashboards. | Prometheus stores last 2 hours in memory. Thanos has query result cache (Redis or Memcached). Use `--query.auto-downsampling` to cache aggregated data. |
| **Design Kafka-like System** | **Page cache** (OS) for log segments; **In‑memory index** for offset mapping; **Broker cache** for recent messages | Kafka heavily relies on OS page cache for read performance. | Log segments are files; OS caches recently read/written pages. Broker keeps in‑memory index of offset → file position. Consumers fetch from page cache. |

---

### 7.9 Pattern H – Collaborative & Infrastructure Utilities (Caching Focus)

| Question | Caching Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Google Docs** | **Redis** for document CRDT state (in‑memory); **Browser cache** for document assets; **Incremental state updates** | CRDT operations are small; Redis provides low‑latency access. | Redis stores the entire document CRDT as a hash (key = character position). Clients cache local version and send diffs. |
| **Figma / Collaborative Whiteboard** | **Redis** for object state; **CDN** for assets; **WebSocket** for real‑time updates; **Client‑side cache** (rendered objects) | Similar to Google Docs, but objects are more complex. | Redis hash per object ID. Client caches rendered objects (Canvas bitmap) to avoid re‑drawing on every update. |
| **Distributed Locking Service** | **No cache** (locks are strongly consistent); **Client‑side lock cache** (re‑use) | Caching locks would break consistency. Clients can cache lock handles. | etcd/ZooKeeper do not use cache for lock state. Client can cache the lock token to avoid re‑acquiring within the same request. |
| **Unique ID Generator (Snowflake)** | **No cache** (ID generation is local); **Worker ID cache** (in memory) | Worker ID is assigned once and cached locally. | Each worker fetches worker ID from etcd and caches it in memory (never expires). ID generation uses timestamp + worker ID + sequence (no network). |
| **Web Crawler** | **Bloom filter** in Redis (probabilistic cache); **Redis** for URL frontier; **DNS cache** | Bloom filter avoids duplicate crawling. DNS cache reduces network calls. | Redis Bloom filter module: `BF.ADD visited:url`. DNS responses cached locally (OS cache). Frontier: Redis sorted set (priority queue). |

---

## 8. Caching in Real System Design

### 8.1 Caching in URL Shortener

#### 8.1.1 Hot URL Caching
Use Redis cache‑aside: on redirect request, check `short_code → long_url` mapping. On miss, load from DynamoDB, store in Redis with TTL = 24 hours.

#### 8.1.2 Redirect Optimization
Cache redirect HTTP responses (301/302) at CDN edge for extremely popular URLs to serve <10ms latency globally.

#### 8.1.3 Edge Caching with CDN
CloudFront caches the redirect response. On cache miss, forwards to API servers, which check Redis and DB. High hit ratio reduces origin load.

### 8.2 Caching in Social Media

#### 8.2.1 Feed Caching
Redis sorted sets per user’s timeline (score = timestamp). On new tweet, push to followers’ timeline caches (write‑through). Limit size to 800 tweets. Use LRU eviction with TTL.

#### 8.2.2 User Profile Cache
Cache user profile data in Redis (TTL = 5 min). On profile update, invalidate the cache key (cache‑aside).

#### 8.2.3 Recommendation Cache
Pre‑compute "who to follow" suggestions and cache in Memcached (TTL = 1 hour). Refresh periodically via background jobs.

### 8.3 Caching in E-Commerce

#### 8.3.1 Product Cache
Cache product details in Redis (TTL = 1 hour). Invalidate on price or description changes via events.

#### 8.3.2 Inventory Cache
Use Redis with very short TTL (10s) for inventory counts to prevent overselling. On purchase, decrement in Redis with atomic operations and write‑through to DB.

#### 8.3.3 Pricing Cache
Cache computed prices (including discounts, taxes) with TTL = 5 min. Invalidate when any pricing rule changes.

### 8.4 Caching in Search Engines

#### 8.4.1 Query Cache
Cache the top 10,000 most frequent queries in Redis with TTL = 5 min. Use hash of query + page as key.

#### 8.4.2 Snippet Cache
Cache pre‑rendered search result snippets to avoid re‑computing highlights and formatting.

#### 8.4.3 Ranking Cache
Cache the ranking scores or pre‑ranked document lists for popular queries to speed up retrieval.

---

## 9. Interview-Oriented HLD Notes

### 9.1 Key Tradeoffs

#### 9.1.1 Freshness vs Performance
Strong consistency requires write‑through or immediate invalidation, adding latency. Eventual consistency (TTL, cache‑aside) delivers better performance but allows stale reads.

#### 9.1.2 Consistency vs Availability
During network partitions, a distributed cache must choose between returning stale data (available) or erroring (consistent). This mirrors the CAP tradeoff.

#### 9.1.3 Cost vs Latency
More memory and more cache nodes reduce latency but increase cost. Balance hit ratio, TTL, and cluster size based on budget.

#### 9.1.4 Complexity vs Scalability
Adding a distributed cache, replication, and invalidation logic increases system complexity. Assess whether the performance gain justifies the overhead.

### 9.2 Common Interview Questions

#### 9.2.1 What Should Be Cached?
Focus on read‑heavy, frequently accessed, expensive to compute, and tolerable staleness data. Avoid caching highly dynamic or one‑time data.

#### 9.2.2 Which Strategy Should Be Used?
For general workloads, cache‑aside. For strong consistency, write‑through. For extreme write speed, write‑back. For write‑once data, write‑around.

#### 9.2.3 How to Prevent Cache Stampede?
Use mutex locking, probabilistic early recomputation, background refresh, and randomized TTLs.

#### 9.2.4 How to Handle Cache Invalidation?
Prefer TTL for simplicity, event‑driven invalidation for real‑time needs, and versioned keys for immutable caching.

### 9.3 Quick Revision Material

#### 9.3.1 Strategy Comparison Table
See Section 2.6.5.

#### 9.3.2 Eviction Policy Cheat Sheet
| Policy | Best For |
|--------|----------|
| LRU | General workloads with temporal locality |
| LFU | Stable popularity distributions |
| TTL | Data that becomes stale after known time |
| Random | Low overhead, surprisingly decent |

#### 9.3.3 Key Interview Sentences
> *“I’d use Redis cache‑aside with a 5‑minute TTL for user profiles. On profile update, I’d delete the cache key – next read will miss and load fresh data.”*

> *“To prevent cache stampede on the trending feed, I’d implement request coalescing with a distributed lock: only one request computes the feed, others wait.”*

> *“For the product catalog, I’d combine CDN (for images and static HTML) with a Redis layer (for dynamic pricing and inventory), using write‑through for inventory updates to avoid overselling.”*

#### 9.3.4 Self-Test Questions
1. You’re caching Twitter user profiles with cache‑aside. A user updates their bio. What happens? When will the cache show the new bio? What’s the risk and how do you mitigate it?
2. 10,000 users all try to load "the World Cup final feed" at the same time when the cache key expires. What is this problem called? Give two solutions.
3. An attacker sends 1 million requests for user IDs that don’t exist. This bypasses the cache and hammers your DB. What are two ways to prevent this?
4. You’re designing a like counter for tweets. Likes can be approximate (off by ±100 is fine). Which caching write strategy would you use for maximum performance?
5. When should you use LFU instead of LRU eviction? Give a concrete example where LRU would evict the wrong data.

*End of consolidated notes.*
