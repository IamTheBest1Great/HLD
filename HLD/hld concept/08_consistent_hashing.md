# 08 — Consistent Hashing
> **Why this matters:** Consistent hashing is asked in almost every senior HLD interview when you discuss distributed caches or partitioned databases. It solves a specific, critical problem: how do you redistribute data when you add or remove nodes, without rehashing everything?

---

## 1. The Problem: Naive Modulo Hashing Breaks When You Scale

Imagine you have a distributed cache with 3 nodes. You assign keys using:

```
node = hash(key) % 3

key="user:123"  → hash=456789 → 456789 % 3 = 0 → Node 0
key="user:456"  → hash=789012 → 789012 % 3 = 1 → Node 1
key="user:789"  → hash=123456 → 123456 % 3 = 2 → Node 2
```

This works perfectly. But then you add a 4th node:

```
node = hash(key) % 4  ← changed!

key="user:123"  → hash=456789 → 456789 % 4 = 1 → Node 1  ← was Node 0!
key="user:456"  → hash=789012 → 789012 % 4 = 0 → Node 0  ← was Node 1!
key="user:789"  → hash=123456 → 123456 % 4 = 0 → Node 0  ← was Node 2!
```

**Nearly ALL keys moved to different nodes.** Every cache miss! Every database request hits the backend. The entire cache is invalidated during a scaling event. At Twitter-scale, this is catastrophic.

---

## 2. Consistent Hashing — The Solution

Consistent hashing guarantees that when a node is added or removed, only `K/N` keys need to move (where K = number of keys, N = number of nodes). Instead of nearly 100% of keys moving, only ~1/N move.

### The Hash Ring Concept

```
Imagine a circle from 0 to 360 degrees (or 0 to 2^32 for a full hash space).

STEP 1: Place NODES on the ring
─────────────────────────────────────────────────────────────────
                    0°
                    │
          270°──────┼──────90°
                    │
                   180°

Place nodes at their hash positions:
  Node A → hash("NodeA") → 45°
  Node B → hash("NodeB") → 150°
  Node C → hash("NodeC") → 270°

                    0°
                 /      \
            315° \      / 45°  ← Node A
                  \    /
             270° ─┼──── 90°
            Node C │    
                  /    \
            225° /      \ 135°
                 \      /
                 180° ← Node B (150°)
```

### Step 2: Assign Keys

```
For each key:
  1. Hash the key → position on the ring
  2. Walk clockwise from that position
  3. First node you encounter = responsible node

EXAMPLE:
  hash("user:123") → 100°
  Walk clockwise from 100°: next node is Node B at 150°
  → user:123 goes to Node B ✅

  hash("user:456") → 200°
  Walk clockwise from 200°: next node is Node C at 270°
  → user:456 goes to Node C ✅

  hash("user:789") → 300°
  Walk clockwise from 300°: next node is Node A at 45° (wraps around 0°)
  → user:789 goes to Node A ✅

VISUAL:
                    0°
                Node A (45°)
               /            \
      user:789(300°)         user:123(100°)
             |                       |
         Node C(270°)           Node B(150°)
               \            /
                user:456(200°)
```

### Step 3: Adding a New Node

```
ADD Node D at position 220°:

BEFORE:
  Keys at 200° - 270° → Node C

AFTER:
  Keys at 200° - 220° → Node D (new!)  ← Only this range moves!
  Keys at 220° - 270° → Node C (unchanged)

ONLY KEYS IN THE RANGE [150°, 220°] move from Node C to Node D.
Everything else is unchanged.

Impact: Adding 1 of 4 nodes → only ~25% of keys move (1/N, not nearly 100%)
```

### Step 4: Removing a Node

```
REMOVE Node B at 150°:

Keys that were going to Node B (range 45° to 150°) now go to Node C (next clockwise node).
Only those keys are affected. Everything else is unchanged.
```

---

## 3. The Virtual Nodes Problem

Plain consistent hashing has an issue: **non-uniform distribution**.

```
WITH ONLY 3 NODES ON THE RING:
─────────────────────────────────────────────────────────────────
Node A owns: 45° to 150° = 105° of the ring (29%)
Node B owns: 150° to 270° = 120° of the ring (33%)
Node C owns: 270° to 45° = 135° of the ring (38%)

Unequal distribution! Node C handles 38% of traffic, Node A only 29%.
Even worse with random hash positions.
```

### Virtual Nodes (vnodes) — The Fix

Each physical node gets **multiple virtual nodes** spread around the ring.

```
Instead of:
  Node A → 1 position at 45°

Do this:
  Node A → positions at 45°, 192°, 315°  (3 virtual nodes)
  Node B → positions at 120°, 245°, 30°
  Node C → positions at 80°, 210°, 330°

Result: 9 positions on the ring for 3 physical nodes.
        Distribution is much more even.
        Each physical node handles ~1/3 of the ring (statistically).

With 100-200 virtual nodes per physical node:
  Distribution is very close to perfectly uniform.
  Adding a node: its vnodes absorb keys from MANY other nodes (balanced)
```

```
VISUAL WITH VNODES:
─────────────────────────────────────────────────────────────────
         A1        C2        B1
         |         |         |
  B3─────┼─────────┼─────────┼─────A2
         |         |         |
         A3        B2        C1
                   C3
         
Keys are spread across A, B, C nodes fairly.
No single node owns a disproportionate arc.
```

---

## 4. Consistent Hashing in Real Systems

### Redis Cluster

```
Redis Cluster uses a variant called HASH SLOTS instead of consistent hashing.
16,384 hash slots total.

hash_slot = CRC16(key) % 16384

Each node is responsible for a range of slots:
  Node 1: slots 0 – 5460
  Node 2: slots 5461 – 10922
  Node 3: slots 10923 – 16383

When you add a node:
  Move some slots from existing nodes to new node.
  Only data in those slots moves.
  Other slots unchanged.
```

### Apache Cassandra

```
Cassandra uses consistent hashing with virtual nodes (vnodes).
Default: 256 vnodes per physical node.

Each partition key is hashed to a token (number from -2^63 to 2^63).
The vnode responsible for that token range handles the data.

Result: Very even distribution, easy to add/remove nodes,
        minimal data movement during topology changes.
```

### Content Delivery Networks (CDNs)

```
CDN uses consistent hashing to route requests to edge servers.
Given URL: hash(url) % ring → nearest cache server
Adding new edge server: only ~1/N of URLs re-routed to new server
Others continue to be served from cache (no invalidation) ✅
```

---

## 5. Full Flow: Consistent Hashing in a Distributed Cache

```
WRITE: "Store user:123's profile"
──────────────────────────────────────────────────────────────────
1. Client: hash("user:123") → position 100 on ring
2. Walk clockwise → Node B is at 150 → Node B is responsible
3. Client sends write to Node B
4. Node B stores the data

READ: "Get user:123's profile"
──────────────────────────────────────────────────────────────────
1. Client: hash("user:123") → position 100 on ring
2. Walk clockwise → Node B at 150 → Node B is responsible
3. Client sends read to Node B
4. Node B returns data ✅

ADD NEW NODE: Node D added at position 120
──────────────────────────────────────────────────────────────────
1. Node D is now between 100 and 150
2. user:123 (at 100) → next node is now Node D at 120 (not B at 150!)
3. System detects: Node D now owns range (Node_A...120)
4. Data in that range is MIGRATED from Node B to Node D
5. Only data in that arc moves. Everything else: no change.
```

---

## 6. Consistent Hashing vs Rendezvous Hashing

There's another algorithm called Rendezvous Hashing (also called Highest Random Weight):

```
RENDEZVOUS HASHING:
For each key, score every node:
  score(key, node) = hash(key + node_id)
Assign key to node with HIGHEST score.

For "user:123":
  score("user:123", NodeA) = 9823
  score("user:123", NodeB) = 7234  ← highest
  score("user:123", NodeC) = 5512
  → Goes to Node A ← wait I made an error, goes to node with highest score, NodeA

When Node B is removed:
  Only keys where B had the HIGHEST score are affected.
  They get reassigned to whichever other node now has the highest score.
  All other keys: unchanged.

Advantage: Simpler to implement, no ring data structure
Disadvantage: O(N) to find responsible node (must score all N nodes)
              Consistent hashing is O(log N) with binary search on ring
```

---

## 📊 Consistent Hashing Summary

```
PROBLEM SOLVED:
  Adding/removing nodes from a distributed system
  without reshuffling (nearly) all data.

KEY PROPERTY:
  Add/remove 1 of N nodes → Only ~1/N keys move.
  Compare: Naive modulo → nearly 100% of keys move.

VIRTUAL NODES:
  Each physical node gets multiple positions on ring.
  Ensures even distribution even with few physical nodes.

REAL USAGE:
  Redis Cluster (hash slots variant)
  Cassandra (with 256 vnodes/node)
  CDN edge routing
  Memcached clusters
  Any horizontally scaled distributed store
```

---

## 📝 Key Interview Sentences

> *"For the distributed cache, I'd use consistent hashing. The key insight is that when we add or remove a cache node — say during an auto-scaling event — only 1/N of the keys need to be remapped instead of nearly all of them. With virtual nodes set to ~150 per physical node, the load distribution stays uniform."*

> *"Cassandra uses consistent hashing with 256 virtual nodes per physical node by default. When we add a new Cassandra node to handle more write volume, only the data in the token ranges that migrate to the new node is moved. The cluster stays online throughout."*

---

## ✅ Self-Test Questions

1. You have 4 cache nodes using `hash(key) % 4`. Your cache is 80% full so you add a 5th node. What happens to the cache hit rate immediately after? Why?

2. Draw a consistent hashing ring with 3 nodes at positions 60°, 180°, 300°. Where does a key with hash position 120° go? What if you add a node at 150°?

3. What problem do virtual nodes solve in consistent hashing? Why do we typically use 150-300 virtual nodes per physical node?

4. Cassandra uses consistent hashing. You have a cluster of 6 nodes. You add 2 more nodes. Approximately what percentage of data needs to move?

5. Why is consistent hashing used in CDNs for routing requests to edge servers?
