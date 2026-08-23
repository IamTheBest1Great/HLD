# 03 — Databases: SQL vs NoSQL
> **Why this matters:** The single most common follow-up in any HLD interview is "why did you choose that database?" You must know the full decision matrix — not just "SQL for structured data" but the real tradeoffs that drive the choice.

---

## 1. The Big Picture

```
                    ALL DATABASES
                          │
           ┌──────────────┴──────────────┐
           │                             │
         SQL                           NoSQL
    (Relational)               (Non-Relational)
    Tables, Rows               Many sub-types
    ACID                       Usually BASE
    Schema enforced            Flexible schema
           │                             │
           │                 ┌───────────┼───────────┬───────────┐
           │              Key-Value    Document   Wide-Column   Graph
           │               Redis       MongoDB    Cassandra    Neo4j
           │               DynamoDB    Firestore  HBase        Neptune
```

---

## 2. SQL Databases (Relational)

SQL databases store data in **tables** with **rows** and **columns**, related by **foreign keys**.

### Structure

```
USERS table:
┌─────────────────────────────────────────────────────┐
│ user_id │ username  │ email             │ created_at │
├─────────┼───────────┼───────────────────┼────────────┤
│ 1       │ alice     │ alice@example.com │ 2024-01-01 │
│ 2       │ bob       │ bob@example.com   │ 2024-01-02 │
└─────────────────────────────────────────────────────┘

TWEETS table:
┌──────────────────────────────────────────────────────────┐
│ tweet_id │ user_id (FK) │ content        │ created_at    │
├──────────┼──────────────┼────────────────┼───────────────┤
│ 101      │ 1            │ "Hello world!" │ 2024-03-01    │
│ 102      │ 2            │ "Hi there!"    │ 2024-03-01    │
└──────────────────────────────────────────────────────────┘
```

### Key Features of SQL

**1. JOINs** — combine data from multiple tables

```sql
-- Get all tweets with username
SELECT u.username, t.content, t.created_at
FROM tweets t
JOIN users u ON t.user_id = u.user_id
WHERE u.username = 'alice'
ORDER BY t.created_at DESC
LIMIT 10;
```

**2. Schema enforcement** — every row must match the table schema. Bad data doesn't get in.

**3. ACID transactions** — see below

**4. Indexes** — speed up queries dramatically (B-Tree by default)

### Popular SQL Databases

| Database | Best For | Notes |
|----------|---------|-------|
| PostgreSQL | General purpose, complex queries | Best open-source SQL DB |
| MySQL | Web applications | Simple, fast reads |
| SQLite | Embedded, single-user | Not for production scale |
| Oracle | Enterprise, banking | Expensive, powerful |
| SQL Server | Microsoft stack | Good Windows integration |

---

## 3. ACID Properties (Critical for Interviews)

ACID is what makes SQL databases safe for financial and critical data.

```
A — ATOMICITY
─────────────
A transaction is all-or-nothing.

Example: Transfer $100 from Alice to Bob
  Step 1: Deduct $100 from Alice
  Step 2: Add $100 to Bob

  If Step 2 fails → Step 1 is ROLLED BACK automatically
  You never get a state where Alice lost $100 but Bob didn't receive it.

C — CONSISTENCY
───────────────
The database goes from one valid state to another.
Rules (constraints) are never violated.

Example: user_id is a foreign key in tweets.
  You cannot insert a tweet with user_id=999 if user 999 doesn't exist.
  The constraint is enforced.

I — ISOLATION
─────────────
Concurrent transactions don't see each other's in-progress state.

Example: Alice reads her balance ($500) while Bob is transferring $100 to her.
  Alice sees $500 (the committed value), not $600 (Bob's in-progress change).

  Isolation levels (tradeoff: consistency vs performance):
  ┌─────────────────────┬──────────────────────────────────┐
  │ READ UNCOMMITTED    │ Can see dirty (uncommitted) data  │
  │ READ COMMITTED      │ Only see committed data (default) │
  │ REPEATABLE READ     │ Same read returns same result     │
  │ SERIALIZABLE        │ Transactions execute as if serial │
  └─────────────────────┴──────────────────────────────────┘
  More isolation = more locking = lower throughput

D — DURABILITY
──────────────
Once committed, data survives crashes.
Database writes to disk (WAL — Write-Ahead Log) before confirming.

Example: After "Payment successful", even if server crashes immediately,
  the payment record exists on disk and survives a reboot.
```

---

## 4. NoSQL Databases

NoSQL databases sacrifice some SQL features for **scalability** and **flexibility**.

### Type 1: Key-Value Store

```
Like a giant dictionary / HashMap.

KEY              VALUE
──────────────────────────────────────────────
"user:123"     → { "name": "Alice", "age": 28 }
"session:abc"  → { "user_id": 123, "expires": "..." }
"rate:ip:1.2"  → 47 (request count)

Operations:
  GET key      → returns value
  SET key val  → sets value (with optional TTL)
  DEL key      → deletes
  INCR key     → atomically increments integer

Speed: O(1) — extremely fast
Weakness: No queries, no relationships, no range scans
```

**Examples:** Redis, Memcached, DynamoDB (also key-value), Etcd

**Use for:** Caching, sessions, rate limiting counters, leaderboards, distributed locks

### Type 2: Document Store

```
Stores documents (JSON-like) that can have nested structure.

{
  "_id": "tweet_123",
  "user_id": 456,
  "content": "Hello world!",
  "hashtags": ["tech", "coding"],
  "media": {
    "type": "image",
    "url": "https://cdn.../img.jpg",
    "dimensions": { "w": 1920, "h": 1080 }
  },
  "likes": 42,
  "created_at": "2024-03-01T10:00:00Z"
}

Queries: Can query on any field, including nested
  db.tweets.find({ "hashtags": "tech", "likes": { "$gt": 10 } })
```

**Examples:** MongoDB, Firestore, CouchDB

**Use for:** Product catalogs, content management, user profiles with varying attributes, any schema that changes frequently

### Type 3: Wide-Column Store

```
Imagine a table where each row can have DIFFERENT columns.

                user_id │ name   │ email          │ phone      │ address
               ─────────┼────────┼────────────────┼────────────┼────────
               user:1   │ Alice  │ alice@...      │ 555-0001   │ (none)
               user:2   │ Bob    │ (none)         │ (none)     │ NY, USA
               user:3   │ Carol  │ carol@...      │ 555-0003   │ CA, USA

Also: optimized for massive write throughput.
Rows are partitioned by "partition key" and sorted by "clustering key".

Example — Cassandra for message history:
  Partition key: conversation_id  (all messages for a chat together)
  Clustering key: message_timestamp (sorted within the partition)
  → Very fast: "get last 50 messages from conversation X"
```

**Examples:** Cassandra, HBase, Google Bigtable

**Use for:** Time-series data, IoT data, messaging history, analytics, any write-heavy workload at massive scale

### Type 4: Graph Database

```
Stores NODES and EDGES (relationships between nodes).

     Alice ──────FOLLOWS────── Bob
       │                        │
    LIKES                    FRIEND_OF
       │                        │
     Tweet1                  Carol
                                │
                             FOLLOWS
                                │
                              Alice

Queries: "Find all people Alice follows who also follow Carol"
         In SQL: multiple JOINs, expensive
         In graph DB: follow edges, O(1) per hop
```

**Examples:** Neo4j, Amazon Neptune, TigerGraph

**Use for:** Social networks (friend recommendations), fraud detection, knowledge graphs, recommendation engines, anything with complex many-to-many relationships

---

## 5. BASE Properties (NoSQL's Alternative to ACID)

```
B — BASICALLY AVAILABLE
────────────────────────
The system is always available (reads and writes accepted)
even during failures, though some data may be stale.

A — SOFT STATE
───────────────
The state of the system can change over time, even without input.
(due to eventual consistency propagating)

E — EVENTUALLY CONSISTENT
──────────────────────────
Given enough time with no new writes, all replicas will converge
to the same value.

Example: You post a tweet. Your follower in Tokyo might see it
  2 seconds later than your follower in New York.
  But eventually, all nodes have the tweet.
```

---

## 6. SQL vs NoSQL Decision Matrix

```
Choose SQL when:                    Choose NoSQL when:
────────────────────────────────    ────────────────────────────────
✅ Data has complex relationships   ✅ Simple lookups by key or ID
✅ ACID transactions needed         ✅ Massive scale (billions of rows)
✅ Schema is stable and known       ✅ Schema changes frequently
✅ Complex queries with JOINs       ✅ Horizontal scaling is priority
✅ Financial/inventory data         ✅ High write throughput needed
✅ Reporting and analytics          ✅ Eventual consistency acceptable
✅ Team knows SQL well              ✅ Data is denormalized (no JOINs)
```

### Concrete Examples

| System | Database Choice | Why |
|--------|----------------|-----|
| Twitter timeline | Cassandra | Massive writes, partition by user, time-sorted |
| Twitter user profiles | PostgreSQL | Structured, ACID for auth |
| Facebook social graph | TAO (graph) | Friend relationships = graph queries |
| Uber ride matching | Redis | Sub-millisecond driver location lookup |
| Payment transactions | PostgreSQL | ACID required, no money lost |
| YouTube video metadata | Bigtable | Scale, flexible schema |
| Instagram media | Cassandra + S3 | Write-heavy, distributed |
| E-commerce cart | Redis | Fast, TTL auto-expiry |
| E-commerce orders | PostgreSQL | ACID, inventory updates |

---

## 7. NoSQL Sub-Type Decision

```
Is the data a simple lookup (get/set by key)?
  YES → Key-Value (Redis, DynamoDB)

Is the data a document with flexible structure (JSON-like)?
  YES → Document Store (MongoDB, Firestore)

Is the data time-series or do you need extreme write throughput?
  YES → Wide-Column (Cassandra, HBase)

Is the data about RELATIONSHIPS between entities?
  YES → Graph Database (Neo4j, Neptune)
```

---

## 8. Polyglot Persistence

Real systems use **multiple databases**, each for what it's best at.

```
Instagram Architecture (simplified):

┌──────────────────────────────────────────────────────────────┐
│                    User Action: Post a photo                  │
└──────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────────┐
        ▼                     ▼                           ▼
┌──────────────┐    ┌──────────────────┐    ┌──────────────────┐
│  PostgreSQL  │    │    Cassandra     │    │      S3          │
│              │    │                  │    │                  │
│ User accounts│    │ Photo metadata   │    │ Actual image     │
│ Auth data    │    │ Feed data        │    │ files            │
│ (ACID needed)│    │ (massive scale)  │    │ (blob storage)   │
└──────────────┘    └──────────────────┘    └──────────────────┘
        │
┌──────────────┐    ┌──────────────────┐
│    Redis     │    │  Elasticsearch   │
│              │    │                  │
│ Sessions     │    │ Search / tags    │
│ Rate limits  │    │ (text search)    │
│ Feed cache   │    │                  │
└──────────────┘    └──────────────────┘
```

---

## 📝 Key Interview Sentences

> *"For user accounts and payment data, I'd use PostgreSQL — we need ACID transactions and the schema is well-defined. For the activity feed, I'd use Cassandra — it's write-heavy and we can partition by user_id, clustering by timestamp for efficient range queries."*

> *"I'd keep session data in Redis — it's key-value lookups, we need sub-millisecond reads, and TTL handles expiry automatically. No need for a relational DB here."*

> *"The friend recommendation feature needs graph traversal — who do your friends follow that you don't? That's multiple hops in a social graph. A graph database like Neo4j handles this natively; in SQL it would require expensive recursive JOINs."*

---

## ✅ Self-Test Questions

1. Design a simple e-commerce system. Which databases would you use for: user accounts, product catalog, orders, shopping cart, search? Justify each.

2. You're building a ride-sharing app. Driver locations update every 3 seconds. Would you use SQL or NoSQL for the driver location store? Which specific type? Why?

3. What are the four ACID properties? Give a concrete example of what breaks if each one is missing.

4. A startup says "let's use MongoDB for everything because it's flexible." What's the risk of this approach?

5. Cassandra's slogan is "write fast, read carefully." What does this mean for how you model data?
