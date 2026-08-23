# 06 — Caching
> **Why this matters:** Caching is the single most impactful optimization in system design. Every large system uses it. Interviewers will always ask "where would you add caching?" You need to know all five strategies, all three eviction policies, and all three cache failure modes.

---

## 1. Why Cache?

```
WITHOUT CACHE:
User requests tweet feed
→ App server queries database
→ DB scans indexes, joins tables
→ Returns in ~50ms
→ This happens for EVERY user, EVERY feed refresh

WITH CACHE:
User requests tweet feed
→ App server checks Redis cache
→ Cache HIT: returns in ~1ms (50x faster!)
→ Only cache MISS goes to DB (~1% of requests)

Impact:
  1M users, 100 feed refreshes/day = 100M DB queries/day
  With 99% cache hit rate = 1M DB queries/day (100x reduction) ✅
```

### What to Cache?

```
CACHE THIS:                           DON'T CACHE THIS:
────────────────────────────────      ────────────────────────────────
✅ User profile data                  ❌ Unique, rarely-repeated queries
✅ Social feed / timeline             ❌ Data that changes every second
✅ Product catalog                    ❌ Sensitive data (unless encrypted)
✅ Trending topics                    ❌ Data requiring ACID guarantees
✅ Popular tweet content              ❌ One-time computed results
✅ Session tokens                     ❌ Write-heavy data (defeats purpose)
✅ Rate limit counters                ❌ Very large objects (wastes cache)
✅ API responses to external APIs
```

---

## 2. The Five Caching Strategies

### Strategy 1: Cache-Aside (Lazy Loading)

The most common strategy. Application code manages the cache.

```
READ PATH:
────────────────────────────────────────────────────────
App → Check cache for key "user:123"
         │
    ┌────┴────┐
    │         │
  HIT        MISS
    │         │
    │         ├─► App queries DB
    │         ├─► App writes result to cache
    │         └─► App returns data to user
    │
    └─► Return cached data (fast!)

WRITE PATH:
────────────────────────────────────────────────────────
App writes to DB
App INVALIDATES the cache key (DELETE "user:123")
  → Next read will be a miss → fresh data loaded
```

**Pros:**
- Cache only what's actually requested (efficient)
- DB failure doesn't take down the whole system (just cache misses)
- Works with any backend

**Cons:**
- Cache miss penalty: First request after a miss is slow (3 round trips: cache + DB + cache write)
- Data can be stale (if you forget to invalidate)
- Cache stampede problem (see below)

**Use when:** General-purpose caching, read-heavy workloads, unpredictable access patterns.

---

### Strategy 2: Read-Through

Cache sits between app and DB. App only talks to cache; cache handles DB.

```
READ PATH:
────────────────────────────────────────────────────────
App → Cache
         │
    ┌────┴────┐
    │         │
  HIT        MISS
    │         │
    │         └─► Cache fetches from DB automatically
    │             Cache stores result
    │             Cache returns to App
    │
    └─► Return cached data

App never touches DB directly.
```

**Pros:**
- App code simpler (no cache-check logic in application)
- Cache always populated before app needs it

**Cons:**
- First request always slow (cache cold start)
- Cache library must know how to talk to your DB

**Use when:** Using a managed cache that supports this (e.g., Ehcache, some Redis frameworks).

---

### Strategy 3: Write-Through

Every write goes to BOTH cache and database simultaneously.

```
WRITE PATH:
────────────────────────────────────────────────────────
App writes data
    │
    ├─► Write to CACHE (sync)
    │
    └─► Write to DB (sync)
Both must succeed before "write complete" is returned.

READ PATH:
────────────────────────────────────────────────────────
Cache always has the latest data (written alongside DB).
Reads are always cache hits (if the key exists).
```

**Pros:**
- Cache always consistent with DB
- Reads are always fast (no miss after write)
- No stale data

**Cons:**
- Every write is slower (write to both cache + DB)
- Cache fills up with data that may never be read (cache pollution)
- Write amplification

**Use when:** You need strong consistency between cache and DB. E.g., user profile data that's both frequently written and frequently read.

---

### Strategy 4: Write-Back (Write-Behind)

Write to cache ONLY. DB is updated asynchronously later.

```
WRITE PATH:
────────────────────────────────────────────────────────
App writes data
    │
    └─► Write to CACHE only (fast! sync)
        Mark as "dirty" (needs DB write)

Background process (async):
    └─► Periodically flush dirty keys to DB

READ PATH:
────────────────────────────────────────────────────────
All reads come from cache (fast).
```

**Pros:**
- Extremely fast writes (memory only)
- DB gets batched writes (more efficient)
- Reduces DB write load significantly

**Cons:**
- Data loss risk: If cache crashes before flush, data is LOST
- Complexity: Background flusher, failure handling
- Consistency: DB is always behind cache

**Use when:** Write performance is critical, some data loss is acceptable, data can be reconstructed. E.g., analytics counters, like counts, gaming scores.

---

### Strategy 5: Write-Around

Writes go DIRECTLY to DB, bypassing the cache.

```
WRITE PATH:
────────────────────────────────────────────────────────
App writes data → DB directly (cache NOT updated)

READ PATH:
────────────────────────────────────────────────────────
App → Check cache → MISS (just wrote, not in cache)
      App → DB → populate cache → return data
```

**Pros:**
- Cache not polluted with data that's written but rarely read again
- Good for write-heavy data that won't be re-read soon

**Cons:**
- Read after write always a cache miss (high latency)

**Use when:** Data is written once and rarely re-read (e.g., logs, audit trails, one-time reports).

---

## 3. Strategy Comparison

```
STRATEGY          WRITE SPEED  READ SPEED  CONSISTENCY  DATA LOSS RISK
─────────────────────────────────────────────────────────────────────
Cache-Aside        Normal       Fast*       Eventual     None (DB has it)
Read-Through       Normal       Fast*       Eventual     None
Write-Through      Slow         Very Fast   Strong       None
Write-Back         Very Fast    Very Fast   Eventual     YES (if crash)
Write-Around       Normal       Slow(first) Strong       None

* Fast after first miss
```

---

## 4. Eviction Policies

When the cache is full, something must be removed to make space.

### LRU — Least Recently Used

```
Cache (4 slots): [A, B, C, D]  ← A is oldest (not recently used)
New item E arrives → EVICT A → Cache: [B, C, D, E]

Implementation: Doubly linked list + HashMap
  HashMap: O(1) access to any node
  Linked list: O(1) move recently used to front, remove from back

GOOD FOR: General use. Most data follows the 80/20 rule:
          80% of reads come from 20% of data → that 20% stays in cache.

EXAMPLE: User profile data. Active users accessed recently → cached.
         Inactive users → evicted. Makes sense.
```

### LFU — Least Frequently Used

```
Cache: A (accessed 100x), B (accessed 3x), C (accessed 50x), D (accessed 1x)
New item E arrives → EVICT D (accessed only once)

GOOD FOR: When frequency matters more than recency.
          Viral content: Popular tweets accessed millions of times → stay in cache.
          Old but frequently accessed items don't get evicted.

BAD FOR: Long-term, the most popular items never get evicted even if
         their popularity wanes (cache pollution with "historical hits").

EXAMPLE: Trending hashtags, viral posts, popular product pages.
```

### TTL — Time-To-Live

```
Cache: key "user:123" → expires after 300 seconds
After 300 seconds → automatically deleted, regardless of usage

GOOD FOR: Data that becomes stale after a known time window.
          Sessions (expire after inactivity period).
          Rate limit counters (expire every minute).
          API responses that go stale (news, stock prices).

Often combined with LRU:
  Primary eviction: LRU (when capacity full)
  Secondary expiry: TTL (regardless of capacity)
```

---

## 5. Cache Problems and Solutions

### Problem 1: Cache Stampede (Thundering Herd)

```
SCENARIO:
Popular item "trending_feed" cached with TTL=300s.
At T=300s, TTL expires → key deleted from cache.
At T=300.001s, 10,000 users request trending_feed simultaneously.
ALL get a cache MISS → ALL query DB simultaneously.
→ DB gets 10,000 queries at once → overwhelmed → crashes 🔥

SOLUTIONS:

1. MUTEX LOCK (One request rebuilds, others wait)
   On miss: acquire lock for "trending_feed"
   Only one request computes and writes to cache.
   Others wait for the lock, then read from cache.
   
2. PROBABILISTIC EARLY EXPIRY
   Before TTL expires, randomly decide to refresh early.
   formula: should_refresh = random() < (time_since_set / TTL)
   As TTL approaches, more likely to refresh → avoids stampede at expiry.

3. BACKGROUND REFRESH
   Before TTL expires, background job refreshes the cache.
   TTL never actually expires while the item is popular.
```

### Problem 2: Cache Penetration

```
SCENARIO:
Malicious user repeatedly requests data that DOESN'T EXIST.
  GET /user/99999999  ← user doesn't exist
  Cache miss every time (no data to cache!)
  Every request hits DB → DB overload 🔥

SOLUTIONS:

1. CACHE NULL RESULTS
   When DB returns "not found" → cache a null value for 60 seconds.
   cache.set("user:99999999", null, TTL=60)
   Next 60 seconds: cache returns null immediately (no DB hit).

2. BLOOM FILTER
   Before checking cache, check if key EXISTS in Bloom filter.
   Bloom filter: memory-efficient structure that answers "definitely not" or "probably yes"
   If Bloom says "DEFINITELY NOT EXISTS" → return 404 immediately, no DB hit.
   
   ┌────────────────────────────────────────────────┐
   │  REQUEST                                        │
   │     │                                           │
   │     ▼                                           │
   │  Bloom Filter                                   │
   │  "Does user 99999999 exist?"                    │
   │     │                                           │
   │  "DEFINITELY NO" ──────────────────► 404        │
   │     │                                           │
   │  "PROBABLY YES" ──► Cache ──► DB               │
   └────────────────────────────────────────────────┘
```

### Problem 3: Cache Breakdown (Hot Key Expiry)

```
SCENARIO:
A single extremely popular key ("super_bowl_feed") is cached.
It expires at the exact moment 5 million users try to access it.
→ 5M DB queries simultaneously 🔥

Different from stampede: single very hot key, not many keys expiring.

SOLUTIONS:
1. Never expire hot keys (manually refresh, never let them expire)
2. Lock-based loading (same as stampede solution)
3. Distribute across multiple keys: 
   "super_bowl_feed:0" through "super_bowl_feed:9"
   Randomly pick which key to hit (load distributed)
```

---

## 6. Multi-Layer Caching

Real systems don't have just one cache — they have many layers.

```
USER REQUEST
     │
     ▼
┌─────────────────────────────┐
│        CDN Cache            │  ← Layer 1: Geographic edge cache
│  Hit rate: ~80%             │    Cached: static files, public pages
│  Miss: pass to next layer   │    TTL: hours to days
└──────────────┬──────────────┘
               │ (cache miss)
     ▼
┌─────────────────────────────┐
│  Reverse Proxy Cache        │  ← Layer 2: NGINX/Varnish at datacenter
│  (NGINX, Varnish)           │    Cached: HTTP responses
│  Hit rate: ~10% of misses   │    TTL: seconds to minutes
└──────────────┬──────────────┘
               │ (cache miss)
     ▼
┌─────────────────────────────┐
│   Application Cache         │  ← Layer 3: Redis/Memcached
│   (Redis / Memcached)       │    Cached: DB query results, computed data
│   Hit rate: ~90% of misses  │    TTL: seconds to minutes
└──────────────┬──────────────┘
               │ (cache miss)
     ▼
┌─────────────────────────────┐
│      Database               │  ← Layer 4: DB has its own buffer pool cache
│  (PostgreSQL, MySQL)        │    (automatic, for recently accessed pages)
└─────────────────────────────┘
```

---

## 7. Redis — The Go-To Cache

Redis is the most common application cache in system design interviews.

```
KEY REDIS FEATURES:
────────────────────────────────────────────────────────────────
Data structures: Strings, Lists, Sets, Sorted Sets, Hashes, Bitmaps
Persistence: Optional (RDB snapshots, AOF log)
Replication: Master-slave, Sentinel for HA
Clustering: Redis Cluster for horizontal scaling
TTL: Per-key expiry built in
Atomic ops: INCR, DECR, LPUSH etc. (safe for counters)
Pub/Sub: Basic messaging (not Kafka-level durable)

COMMON REDIS USE CASES:
────────────────────────────────────────────────────────────────
Session store:       SET session:xyz "{user_id: 123}" EX 3600
Rate limiting:       INCR rate:ip:1.2.3.4 + EXPIRE
Distributed lock:    SET lock:resource "1" NX EX 30
Sorted leaderboard:  ZADD leaderboard 1500 "user:alice"
Pub/Sub:             PUBLISH channel "message"
Cache:               SET user:123 "{...}" EX 300
Counter:             INCR tweet:likes:456
```

---

## 📝 Key Interview Sentences

> *"I'd add a Redis cache in front of the database using cache-aside strategy. Hot user profiles are read millions of times but change rarely — perfect for caching with a 5-minute TTL. Cache invalidation on profile update keeps data fresh."*

> *"For the trending feed, I'd cache it with write-through — every time the feed is recomputed, we update both the cache and a persistent store. To avoid cache stampede at TTL expiry, I'd use background refresh so the TTL never actually hits zero for active keys."*

> *"The multi-layer approach: CDN handles static assets and public content (80% of traffic). Redis handles user-specific data. The DB only sees ~1-2% of total request volume."*

---

## ✅ Self-Test Questions

1. You're caching Twitter user profiles with cache-aside. A user updates their bio. What happens? When will the cache show the new bio? What's the risk and how do you mitigate it?

2. 10,000 users all try to load "the World Cup final feed" at the same time when the cache key expires. What is this problem called? Give two solutions.

3. An attacker sends 1 million requests for user IDs that don't exist. This bypasses the cache and hammers your DB. What are two ways to prevent this?

4. You're designing a like counter for tweets. Likes can be approximate (off by ±100 is fine). Which caching write strategy would you use for maximum performance?

5. When should you use LFU instead of LRU eviction? Give a concrete example where LRU would evict the wrong data.
