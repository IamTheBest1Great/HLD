# 05 — Database Internals: Sharding, Replication & Indexing
> **Why this matters:** When your database becomes the bottleneck (and it always does), you have exactly three moves: add read replicas, shard (partition), or add a cache. You need to know all three deeply and their tradeoffs.

---

## 1. The Problem: One Database Doesn't Scale Forever

```
STAGE 1: One database, works fine
──────────────────────────────────
App → DB (handles 1K QPS, 100GB data)

STAGE 2: Reads become the bottleneck
──────────────────────────────────────
App → DB ← Everyone reads, DB can't keep up
Solution: Read Replicas (copies for reads)

STAGE 3: Writes become the bottleneck
──────────────────────────────────────
App → DB (writes too fast for one DB)
Solution: Sharding (split writes across multiple DBs)

STAGE 4: Dataset is too large for one machine
───────────────────────────────────────────────
Solution: Already sharding + archive old data
```

---

## 2. Replication

Replication = maintaining copies of data on multiple nodes.

### Leader-Follower (Master-Slave) Replication

```
           WRITES go here ONLY
                   │
            ┌──────▼──────┐
            │   Leader    │ ← (Primary / Master)
            └──────┬──────┘
                   │ replicates to
      ┌────────────┼────────────┐
      ▼            ▼            ▼
┌──────────┐ ┌──────────┐ ┌──────────┐
│ Follower │ │ Follower │ │ Follower │  ← (Replicas / Slaves)
└──────────┘ └──────────┘ └──────────┘
      ▲            ▲            ▲
           READS go here
```

**How it works:**
1. All writes go to the Leader
2. Leader writes to its log (WAL — Write-Ahead Log)
3. Followers pull from Leader's log and apply changes
4. Reads can go to any Follower (or Leader)

**Practical benefits:**
- 10M reads/day with 1 leader + 4 followers = 200K reads per node instead of 10M on one
- Leader can fail → a follower gets promoted to leader
- Followers in different regions → lower read latency globally

### Synchronous vs Asynchronous Replication

```
SYNCHRONOUS REPLICATION:
─────────────────────────
Client writes to Leader
Leader: Sends to Follower 1, Follower 2
Leader: WAITS for both to acknowledge
Leader: Returns "write successful" to client

Pros: Data never lost if leader crashes (it's already on followers)
Cons: Write latency includes replication time. If a follower is slow → all writes slow

ASYNCHRONOUS REPLICATION (more common):
──────────────────────────────────────────
Client writes to Leader
Leader: Returns "write successful" immediately
Leader: Sends to followers asynchronously (best effort)

Pros: Low write latency, leader doesn't wait
Cons: If leader crashes before replication → data loss (small window)
      Replication lag: followers may be seconds behind leader
```

### Replication Lag Problem

```
Alice updates her profile photo at T=0
Leader processes write at T=0
Follower 1 gets the update at T=2s

Alice refreshes her profile at T=1s:
  Request goes to Follower 1
  Follower 1 returns OLD photo ← Stale read!

SOLUTIONS:
1. "Read-your-writes" routing:
   After a write, route that user's reads to the Leader for 5 seconds.

2. Monotonic reads:
   A user always reads from the same follower.
   They might see stale data, but they won't see time go backward.

3. Leader reads for critical paths:
   Payment confirmation page → always read from leader.
   Browse catalog → can read from follower.
```

### Multi-Leader Replication

```
Used when you have multiple data centers.

DC1 (US)            DC2 (Europe)
┌──────────┐         ┌──────────┐
│  Leader  │◄───────►│  Leader  │
└──────────┘         └──────────┘
│ followers │         │ followers │

Writes go to nearest DC's leader.
Leaders replicate to each other asynchronously.

Pros: Lower write latency (write to nearest DC)
Cons: CONFLICT RESOLUTION NEEDED
      What if User A changes their name in DC1 AND DC2 simultaneously?
```

---

## 3. Database Sharding (Horizontal Partitioning)

Sharding splits your data across multiple database instances so no single node holds everything.

```
WITHOUT SHARDING: 1 DB holds 1 billion users
┌──────────────────────────┐
│  DB (1 billion rows)     │ ← Too big, too slow
│  Users 1 – 1,000,000,000│
└──────────────────────────┘

WITH SHARDING: 4 DBs, each holds 250M users
┌────────────────┐ ┌────────────────┐
│   Shard 1      │ │   Shard 2      │
│ Users 1–250M   │ │ Users 250M–500M│
└────────────────┘ └────────────────┘
┌────────────────┐ ┌────────────────┐
│   Shard 3      │ │   Shard 4      │
│ Users 500M–750M│ │ Users 750M–1B  │
└────────────────┘ └────────────────┘
```

### Shard Key — The Most Important Decision

The shard key determines which shard a row goes to. Choosing poorly causes **hotspots**.

#### Strategy 1: Range-Based Sharding

```
user_id 1–1M       → Shard 1
user_id 1M–2M      → Shard 2
user_id 2M–3M      → Shard 3

PROBLEM: Non-uniform distribution
  If users are mostly new (high IDs) → Shard 3 gets all traffic (HOTSPOT)
  If users are mostly old (low IDs) → Shard 1 gets all traffic

GOOD FOR: Range queries ("get all users from ID 5000 to 10000")
```

#### Strategy 2: Hash-Based Sharding

```
shard_number = hash(user_id) % num_shards

hash("user:123") % 4 = 2 → goes to Shard 2
hash("user:456") % 4 = 0 → goes to Shard 0
hash("user:789") % 4 = 3 → goes to Shard 3

PROS: Even distribution (hash distributes uniformly)
CONS: Range queries are impossible (scattered across shards)
      Adding/removing shards requires rehashing everything → use Consistent Hashing

GOOD FOR: User data, any lookup-by-ID pattern
```

#### Strategy 3: Directory-Based Sharding

```
Maintain a lookup table:
  user_id → shard_id

user 123 → Shard 2
user 456 → Shard 0
user 789 → Shard 3

PROS: Most flexible, can migrate data between shards easily
CONS: Lookup table becomes a bottleneck and single point of failure
      Must cache aggressively
```

### The Hotspot Problem

```
Twitter has celebrity users with 100M followers each.
If we shard by user_id:
  Tweet reads for @elonmusk → ALL go to Shard 3 (his shard)
  Shard 3: 10M QPS ← HOTSPOT 🔥
  Other shards: 100 QPS each

SOLUTIONS:
1. Dedicated shard for high-traffic users
2. Cache celebrity data aggressively
3. Shard differently (by tweet_id instead of user_id)
4. Use a read-heavy architecture (pre-compute, denormalize)
```

### Cross-Shard Queries (The Big Problem)

```
Query: "Get the top 10 most liked tweets from users that Alice follows"

Alice follows users on Shard 1, 2, 3, and 4.
Tweets of those users are also spread across Shard 1, 2, 3, 4.

Must query ALL 4 shards, merge results, sort, return top 10.
= SCATTER-GATHER QUERY (slow and expensive)

MITIGATION: Design data model to avoid cross-shard queries.
  Denormalize: copy relevant data to the shard it's most often needed from.
  Pre-compute: maintain aggregated results in Redis.
```

---

## 4. Database Indexes

Indexes dramatically speed up reads at the cost of write overhead.

### B-Tree Index (Default for Most Databases)

```
Imagine a phone book: sorted alphabetically.
To find "Smith, John" you don't scan all 1M entries — you use the alphabetical structure.
B-Tree is that sorted structure, optimized for disk.

HOW IT WORKS:
                     [50]
                    /    \
               [25]       [75]
              /    \     /    \
           [10]  [40] [60]  [90]
           / \   / \   / \   / \
          ...             ...

Root node → branch nodes → leaf nodes (actual data pointers)
Finding a value: traverse from root → O(log N) 
Range query (IDs 40-75): find 40, scan right → fast

GOOD FOR: Equality queries (WHERE id = 123)
          Range queries (WHERE id BETWEEN 40 AND 75)
          ORDER BY (data already sorted)

BAD FOR: Write-heavy workloads (every write must update the index tree)
```

### LSM Tree (Log-Structured Merge-Tree)

Used by: Cassandra, HBase, RocksDB, LevelDB

```
PHILOSOPHY: Writes are always sequential (fast). Reads are handled by merge.

WRITE PATH:
New write → WAL (crash recovery) + MemTable (in-memory sorted structure)
When MemTable is full → flush to SSTable on disk (sorted, immutable file)
Over time → Multiple SSTables on disk

READ PATH:
Read key → Check MemTable (hot data) → Check SSTables (merge results)
Background compaction → merge SSTables, remove deleted keys

WRITE SPEED: Very fast (sequential writes, no random I/O)
READ SPEED:  Slower (may need to check multiple SSTables)

B-TREE vs LSM-TREE:
──────────────────────────────────────────────────────────────
B-Tree:   Read fast, Write OK     → PostgreSQL, MySQL
LSM Tree: Write very fast, Read OK → Cassandra, HBase, RocksDB
```

### Covering Index and Composite Index

```
COVERING INDEX:
  Index contains ALL the columns a query needs.
  Query can be answered from the index alone — no row lookup needed.

  Query: SELECT email FROM users WHERE username = 'alice'
  Index: (username, email) ← covering index

  Without covering index: find row ID from index, then fetch row (2 lookups)
  With covering index: get email directly from index (1 lookup)

COMPOSITE INDEX:
  Index on multiple columns together.
  (last_name, first_name) → very fast for "WHERE last_name='Smith'"
  Also fast for "WHERE last_name='Smith' AND first_name='John'"
  NOT helpful for "WHERE first_name='John'" alone (left-prefix rule)
```

---

## 5. Read Replicas vs Sharding — When to Use Which

```
PROBLEM: Reads are slow
SOLUTION: Read Replicas (copies of the DB for reads)

Why:
  90% of web traffic is reads.
  1 Primary + 4 Replicas = 5x read capacity.
  Simple to implement, works with any DB.
  Queries don't need to change.

WHEN REPLICAS ARE NOT ENOUGH:
  The dataset is too large for one machine (even replicas have full copy).
  Writes are the bottleneck (replicas don't help writes).

PROBLEM: Writes are slow OR data is too big for one machine
SOLUTION: Sharding

Trade-off: Complex queries become hard.
            Need to choose shard key carefully.
            Cross-shard transactions are difficult.
```

---

## 📊 Decision Framework

```
Database is slow. What's the bottleneck?

              ┌──────────────────────────┐
              │ Is it reads or writes?    │
              └──────────┬───────────────┘
                         │
         ┌───────────────┴────────────────┐
         │ READS                          │ WRITES
         ▼                                ▼
  Add Read Replicas                Is dataset too big?
  + Add Caching (Redis)                   │
                                  ┌───────┴────────┐
                                  │ NO             │ YES
                                  ▼                ▼
                           Optimize queries    SHARD the DB
                           Add indexes        (partition data)
                           Connection pool
```

---

## 📝 Key Interview Sentences

> *"I'd start with a single primary database with 2-3 read replicas. 80% of traffic is reads, so this buys significant headroom. As write volume grows, I'd introduce sharding by user_id using consistent hashing — that avoids the mass rehashing problem when we add or remove shards."*

> *"Cassandra's LSM tree is write-optimized — that's why it's ideal for append-heavy workloads like message history or time-series data. We'd lose the ability to do efficient range queries across partition keys, so we need to design the data model with our access patterns in mind."*

> *"The replication lag problem is real — after a user writes data, routing their next read to the primary for 5 seconds avoids seeing stale data. After that window, reads can go to replicas safely."*

---

## ✅ Self-Test Questions

1. You have 1 billion users in a SQL database. Reads are slow. What do you do first? What do you do if that's not enough?

2. What is a "hotspot" in sharding and how would you detect and fix one for a Twitter-like system?

3. A company is building a message history system. Messages are written at 50,000/second and read in time order. Would you use B-Tree indexes or LSM tree? Why?

4. What is replication lag and when does it matter? Give a scenario where it causes a real bug.

5. You need to find all orders placed by a user in the last 30 days. Your orders table is sharded by order_id. What's the problem and how do you fix it?
