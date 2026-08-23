# 04 — CAP Theorem & Consistency Models
> **Why this matters:** Every time you choose a distributed database, you're implicitly making a CAP choice. Interviewers will ask "what consistency model does this need?" You need to explain your choice, not just state it.

---

## 1. Why CAP Exists: The Distributed Systems Problem

When you have a single database server, life is simple. But in a distributed system (multiple nodes), you face a fundamental problem: **network partitions**.

```
NORMAL OPERATION:
─────────────────
Node A ←──────────────────► Node B
        (network is healthy)
Alice writes to A → A syncs to B → Bob reads from B → gets Alice's write ✅

NETWORK PARTITION:
──────────────────
Node A    ✂✂✂✂✂✂✂✂✂✂    Node B
        (network is broken)
Alice writes to A → A CANNOT sync to B
Bob reads from B → What should B return?

This is the fundamental challenge CAP addresses.
```

---

## 2. CAP Theorem Explained

In a distributed system experiencing a **network partition**, you can guarantee only ONE of:

```
        CONSISTENCY (C)
               ▲
              /│\
             / │ \
            /  │  \
           /   │   \
          /  CHOOSE  \
         /    ONE     \
        /              \
       ◄────────────────►
AVAILABILITY (A)    PARTITION TOLERANCE (P)

C = Every read receives the most recent write (or an error)
A = Every request receives a response (not an error)
P = System continues operating despite network partitions
```

**The catch:** Network partitions WILL happen in any distributed system. So P is not optional. You must always have P. Therefore the real choice is:

```
CP: Consistency + Partition Tolerance (sacrifice Availability)
    → When partition occurs: system becomes UNAVAILABLE until partition heals
    → But when it is available, data is always consistent

AP: Availability + Partition Tolerance (sacrifice Consistency)  
    → When partition occurs: system stays AVAILABLE with potentially STALE data
    → But some nodes may return old data temporarily
```

---

## 3. CP Systems — Consistency Over Availability

```
PARTITION HAPPENS:

Node A ──✂──── Node B

Client writes to Node A: "Alice's balance = $500"
Client tries to read from Node B:

CP System Response:
  "I can see the partition. I cannot guarantee I have the latest data.
   I will refuse to serve this request until partition heals."
   → Returns ERROR or times out ❌

WHY this is sometimes correct:
  Banking: It's better to show an error than to show Alice has $1000
           when she actually transferred $500 out.
```

**Real CP systems:**
- Zookeeper (coordination service)
- HBase
- Etcd
- Traditional RDBMS (PostgreSQL, MySQL in strict mode)

---

## 4. AP Systems — Availability Over Consistency

```
PARTITION HAPPENS:

Node A ──✂──── Node B

Client writes to Node A: "Alice changed her username to 'alice_v2'"
Client tries to read from Node B 1 second later:

AP System Response:
  "I can see the partition. I can't reach Node A.
   But I'll serve you the data I have."
   → Returns "alice" (the OLD username) ✅ (system is up, data is stale)

Eventually (when partition heals):
   Node B gets the update from Node A → now both show "alice_v2"
```

**Real AP systems:**
- Cassandra
- DynamoDB
- CouchDB
- Riak

---

## 5. Why "CA" Doesn't Exist in Distributed Systems

You'll sometimes see people say CA = Consistency + Availability (no partition tolerance). But:

```
In a SINGLE machine: No network, no partition → you can have CA.
  (This is just a regular local database)

In a DISTRIBUTED system: Network WILL partition.
  If you say "I don't tolerate partitions" = you're saying "I won't distribute".
  = You're back to a single machine.

So in practice: every distributed system must choose CP or AP.
```

---

## 6. Consistency Models (Spectrum)

"Consistency" isn't binary — there's a spectrum from strong to eventual.

```
STRONGER ◄────────────────────────────────────────────► WEAKER
(slower, more coordination needed)          (faster, less coordination)

Linearizability  Causal   Read-Your-Writes  Monotonic   Eventual
(Strict Serial)  Consist.  Consistency      Reads       Consistency
      │              │            │              │            │
   Looks like      Causal      You always    Reads don't   Given enough
   one single      order       read your     go backward   time, all
   machine         preserved   own writes    in time       nodes agree
```

### Strong Consistency (Linearizability)

```
Every read returns the most recent write. Period.

Write: Alice sets x=5 at time T
Read: Anyone reading x after time T gets x=5

Implementation: All writes go through a single leader.
                Reads must check leader or wait for ack.
Cost: Latency (must wait for majority acknowledgment)
```

### Eventual Consistency

```
Given no new writes, all replicas will eventually converge.
But in the meantime, different replicas may return different values.

Write: Alice sets username="alice_new" on Node A
Read on Node B (0.5s later): returns "alice_old" ← stale, but accepted
Read on Node B (2s later): returns "alice_new" ← eventually consistent

Cost: Complexity. Need to handle conflicts. Need to inform users about lag.
```

### Read-Your-Writes Consistency

```
You always see your own writes, even if others don't yet.

Alice updates her profile photo.
Alice refreshes the page → sees new photo ✅ (reads her own write)
Bob refreshes the page → may still see old photo (eventual consistency for others)

Implementation: Route user's reads to the same node they write to,
                OR track write timestamps and wait for replicas to catch up.
```

### Causal Consistency

```
Operations that are causally related are seen in the right order by everyone.

Alice posts: "Going for lunch"
Bob replies (AFTER reading Alice's post): "Enjoy!"

Everyone must see Alice's post BEFORE Bob's reply.
Even if they're in different DCs, causal order is preserved.

Implementation: Vector clocks or logical timestamps.
```

---

## 7. PACELC — Extending CAP

CAP only describes behavior during partitions. PACELC extends it:

```
PACELC:
  If Partition → choose between Availability and Consistency (A or C)
  Else (normal operation) → choose between Latency and Consistency (L or C)

              DURING PARTITION    DURING NORMAL OPERATION
              ────────────────    ────────────────────────
Cassandra:         AP            →       EL (low latency, eventual)
DynamoDB (default):AP            →       EL
HBase:             CP            →       EC (consistent, higher latency)
PostgreSQL:        CP            →       EC
MongoDB (default): CP            →       EC

Cassandra in PACELC notation: PA/EL
  "During partition → Available | Else → Low latency over Consistency"
```

---

## 8. Practical Guide: Which Model for Which System?

```
SYSTEM               REQUIRED MODEL    WHY
────────────────────────────────────────────────────────────────
Bank balance          Linearizable     Wrong balance = lawsuits
Payment processing    Linearizable     Double-charge = disaster
Seat reservation      Linearizable     Overselling = angry users
User session auth     Strong           Login state must be current
Username uniqueness   Strong           Duplicate usernames = bad
User profile bio      Eventual         Slight delay is fine
Tweet counter         Eventual         Approximate likes count is OK
Product recommendation Eventual        Stale recs are acceptable
Social feed           Eventual         1-2s delay is fine
Notification delivery Read-Your-Writes User should see their actions
Shopping cart         Read-Your-Writes Items you add should appear
```

---

## 9. Conflict Resolution in AP Systems

When two nodes accept conflicting writes during a partition, what happens when they reconnect?

```
SCENARIO: Shopping cart on Amazon
Node A (US): User adds "Headphones" to cart at 3:00:01 PM
Node B (EU): User removes "Headphones" from cart at 3:00:02 PM
Partition heals: Which state wins?

STRATEGIES:

1. LAST WRITE WINS (LWW)
   Use timestamps: 3:00:02 PM > 3:00:01 PM → Node B wins → headphones removed
   Risk: Clock skew means "last" may not be accurately determined
   Used by: Cassandra (default)

2. MULTI-VALUE (let the client resolve)
   Show user: "You have 2 versions of your cart. Which is correct?"
   Used by: Amazon's Dynamo (for shopping cart specifically)

3. CRDT (Conflict-free Replicated Data Types)
   Data structures that merge automatically without conflicts.
   Example: G-Counter (grow-only counter) — both nodes' increments are added.
   Used by: Riak, collaborative editing tools

4. OPERATIONAL TRANSFORMATION
   Transform conflicting operations to be compatible.
   Used by: Google Docs for concurrent editing
```

---

## 📊 Quick Reference Card

```
CP SYSTEMS (choose when):          AP SYSTEMS (choose when):
────────────────────────────────   ─────────────────────────────────
Money is involved                  Social feeds / timelines
Inventory counts (exact)           User profiles, bios
Leader election / coordination     Product recommendations
Auth tokens must be valid          Analytics / metrics (approximate)
Seat/ticket reservation            Notification delivery
Any "exactly once" semantic        Search indexes
```

---

## 📝 Key Interview Sentences

> *"This is a payment system, so we need strong consistency. I'd use PostgreSQL in a leader-follower setup. During a partition, we'd rather return an error than allow a transaction with stale balance data. That's a CP tradeoff — we accept reduced availability to guarantee correctness."*

> *"For the social feed, eventual consistency is perfectly fine. If a tweet appears in my feed 2 seconds after it's posted, that's unnoticeable to users. I'd use Cassandra — an AP system — which gives us massive write throughput and is partition-tolerant."*

> *"In PACELC terms, Cassandra is PA/EL — during partitions it stays available, and in normal operation it favors low latency over strict consistency. That's exactly what a feed system needs."*

---

## ✅ Self-Test Questions

1. A bank is designing its transaction ledger. During a network partition, should the system show stale data or return an error? What CAP choice does this represent?

2. Twitter has 200M DAU. When Alice posts a tweet, it's acceptable if Bob sees it 3 seconds later. What consistency model fits here?

3. What's the difference between "eventual consistency" and "no consistency"?

4. Two users simultaneously edit the same Google Doc. The system needs to merge their changes. What conflict resolution strategy does Google Docs use?

5. Why is "CA" (Consistency + Availability, no Partition tolerance) not achievable in a distributed system?
