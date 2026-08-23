# Consistency & CAP Theorem — Consolidated HLD Notes

## Table of Contents

- [1. Distributed Systems Foundations](#1-distributed-systems-foundations)
  - [1.1 Why Distributed Systems Are Hard](#11-why-distributed-systems-are-hard)
    - [1.1.1 Network Partitions](#111-network-partitions)
    - [1.1.2 Replication Delays](#112-replication-delays)
    - [1.1.3 Independent Node Failures](#113-independent-node-failures)
    - [1.1.4 Latency vs Consistency Tradeoffs](#114-latency-vs-consistency-tradeoffs)
    - [1.1.5 Split-Brain Scenarios](#115-splitbrain-scenarios)
- [2. CAP Theorem & PACELC](#2-cap-theorem--pacelc)
  - [2.1 CAP Theorem](#21-cap-theorem)
    - [2.1.1 Consistency (C)](#211-consistency-c)
    - [2.1.2 Availability (A)](#212-availability-a)
    - [2.1.3 Partition Tolerance (P)](#213-partition-tolerance-p)
    - [2.1.4 Why Partitions Are Unavoidable](#214-why-partitions-are-unavoidable)
    - [2.1.5 Why CA Doesn't Exist in Distributed Systems](#215-why-ca-doesnt-exist-in-distributed-systems)
    - [2.1.6 CAP Tradeoffs in Real Systems](#216-cap-tradeoffs-in-real-systems)
    - [2.1.7 CP Systems](#217-cp-systems---consistency-over-availability)
    - [2.1.8 AP Systems](#218-ap-systems---availability-over-consistency)
    - [2.1.9 Real-World Examples](#219-real-world-examples)
  - [2.2 PACELC Theorem](#22-pacelc-theorem)
    - [2.2.1 Why CAP Is Not Enough](#221-why-cap-is-not-enough)
    - [2.2.2 PACELC Explained](#222-pacelc-explained)
    - [2.2.3 Latency vs Consistency Tradeoff](#223-latency-vs-consistency-tradeoff)
    - [2.2.4 Real-World Examples](#224-real-world-examples)
    - [2.2.5 CAP vs PACELC Comparison](#225-cap-vs-pacelc-comparison)
- [3. Consistency Models](#3-consistency-models)
  - [3.1 Strong Consistency (Linearizability)](#31-strong-consistency-linearizability)
    - [3.1.1 What It Means](#311-what-it-means)
    - [3.1.2 How It Is Achieved](#312-how-it-is-achieved)
    - [3.1.3 Synchronous Replication](#313-synchronous-replication)
    - [3.1.4 Consensus Protocols](#314-consensus-protocols)
    - [3.1.5 Pros & Cons](#315-pros--cons)
    - [3.1.6 Real-World Examples](#316-real-world-examples)
  - [3.2 Eventual Consistency](#32-eventual-consistency)
    - [3.2.1 What It Means](#321-what-it-means)
    - [3.2.2 Replication & Convergence](#322-replication--convergence)
    - [3.2.3 Background Synchronization](#323-background-synchronization)
    - [3.2.4 Pros & Cons](#324-pros--cons)
    - [3.2.5 Real-World Examples](#325-real-world-examples)
  - [3.3 Read-Your-Writes Consistency](#33-read-your-writes-consistency)
    - [3.3.1 What It Means](#331-what-it-means)
    - [3.3.2 Why It Matters](#332-why-it-matters)
    - [3.3.3 Sticky Sessions / Session Routing](#333-sticky-sessions--session-routing)
    - [3.3.4 Leader Reads](#334-leader-reads)
    - [3.3.5 Real-World Examples](#335-real-world-examples)
  - [3.4 Causal Consistency](#34-causal-consistency)
    - [3.4.1 Causality Relationships](#341-causality-relationships)
    - [3.4.2 Ordering Guarantees](#342-ordering-guarantees)
    - [3.4.3 Real-World Examples](#343-real-world-examples)
  - [3.5 Other Consistency Models](#35-other-consistency-models)
    - [3.5.1 Monotonic Reads](#351-monotonic-reads)
    - [3.5.2 Monotonic Writes](#352-monotonic-writes)
    - [3.5.3 Session Consistency](#353-session-consistency)
    - [3.5.4 Sequential Consistency](#354-sequential-consistency)
    - [3.5.5 Tunable Consistency](#355-tunable-consistency)
  - [3.6 Comparing Consistency Models](#36-comparing-consistency-models)
    - [3.6.1 Consistency Spectrum](#361-consistency-spectrum)
    - [3.6.2 Latency vs Consistency Tradeoff](#362-latency-vs-consistency-tradeoff)
    - [3.6.3 Availability vs Consistency Tradeoff](#363-availability-vs-consistency-tradeoff)
    - [3.6.4 Decision Matrix](#364-decision-matrix)
- [4. Replication & Consensus](#4-replication--consensus)
  - [4.1 Replication Strategies](#41-replication-strategies)
    - [4.1.1 Leader-Follower Replication](#411-leader-follower-replication)
    - [4.1.2 Multi-Leader Replication](#412-multi-leader-replication)
    - [4.1.3 Leaderless Replication](#413-leaderless-replication)
    - [4.1.4 Synchronous Replication](#414-synchronous-replication)
    - [4.1.5 Asynchronous Replication](#415-asynchronous-replication)
  - [4.2 Consensus & Coordination](#42-consensus--coordination)
    - [4.2.1 Why Consensus Is Needed](#421-why-consensus-is-needed)
    - [4.2.2 Quorum Reads & Writes](#422-quorum-reads--writes)
    - [4.2.3 Raft Overview](#423-raft-overview)
    - [4.2.4 Paxos Overview](#424-paxos-overview)
    - [4.2.5 ZooKeeper / etcd Usage](#425-zookeeper--etcd-usage)
  - [4.3 Conflict Resolution in Distributed Systems](#43-conflict-resolution-in-distributed-systems)
    - [4.3.1 Last Write Wins (LWW)](#431-last-write-wins-lww)
    - [4.3.2 Vector Clocks](#432-vector-clocks)
    - [4.3.3 CRDTs](#433-crdts-conflict-free-replicated-data-types)
    - [4.3.4 Application-Level Conflict Resolution](#434-application-level-conflict-resolution)
    - [4.3.5 Anti-Entropy Mechanisms](#435-anti-entropy-mechanisms)
- [5. Consistency in System Design](#5-consistency-in-system-design)
  - [5.1 Choosing Consistency by Application Type](#51-choosing-consistency-by-application-type)
    - [5.1.1 CRUD & Storage Systems](#511-crud--storage-systems)
    - [5.1.2 Feed / Timeline Systems](#512-feed--timeline-systems)
    - [5.1.3 Real-Time Systems](#513-real-time-systems)
    - [5.1.4 Search Systems](#514-search-systems)
    - [5.1.5 Geospatial & Location-Based Systems](#515-geospatial--location-based-systems)
    - [5.1.6 Financial & Critical Consistency Systems](#516-financial--critical-consistency-systems)
    - [5.1.7 Large-Scale Processing Systems](#517-large-scale-processing-systems)
    - [5.1.8 Collaborative Systems](#518-collaborative-systems)
  - [5.2 Practical Decision Framework](#52-practical-decision-framework)
    - [5.2.1 Strong vs Eventual Consistency](#521-strong-vs-eventual-consistency)
    - [5.2.2 CP vs AP Decision Flow](#522-cp-vs-ap-decision-flow)
    - [5.2.3 When to Sacrifice Availability](#523-when-to-sacrifice-availability)
    - [5.2.4 When to Sacrifice Consistency](#524-when-to-sacrifice-consistency)
    - [5.2.5 Hybrid Approaches](#525-hybrid-approaches)
  - [5.3 Common Design Patterns](#53-common-design-patterns)
    - [5.3.1 Leader-Based Architectures](#531-leader-based-architectures)
    - [5.3.2 CQRS](#532-cqrs-command-query-responsibility-segregation)
    - [5.3.3 Event Sourcing](#533-event-sourcing)
    - [5.3.4 Saga Pattern](#534-saga-pattern)
    - [5.3.5 Cache Invalidation Strategies](#535-cache-invalidation-strategies)
  - [5.4 Common Anti-Patterns](#54-common-anti-patterns)
    - [5.4.1 Assuming Strong Consistency Everywhere](#541-assuming-strong-consistency-everywhere)
    - [5.4.2 Ignoring Replication Lag](#542-ignoring-replication-lag)
    - [5.4.3 Cross-Region Synchronous Writes Everywhere](#543-cross-region-synchronous-writes-everywhere)
    - [5.4.4 Poor Conflict Resolution](#544-poor-conflict-resolution)
    - [5.4.5 Distributed Transactions Overuse](#545-distributed-transactions-overuse)
- [6. Interview-Oriented HLD Notes](#6-interview-oriented-hld-notes)
  - [6.1 Practical Guide: Which Model for Which System?](#61-practical-guide-which-model-for-which-system)
    - [6.1.1 Banking Systems](#611-banking-systems)
    - [6.1.2 Social Media Feeds](#612-social-media-feeds)
    - [6.1.3 Messaging Systems](#613-messaging-systems)
    - [6.1.4 Search Engines](#614-search-engines)
    - [6.1.5 Ride Sharing Systems](#615-ride-sharing-systems)
    - [6.1.6 Collaborative Editors](#616-collaborative-editors)
  - [6.2 Quick Revision Material](#62-quick-revision-material)
    - [6.2.1 Quick Reference Card](#621-quick-reference-card)
    - [6.2.2 Key Interview Sentences](#622-key-interview-sentences)
    - [6.2.3 Self-Test Questions](#623-self-test-questions)
    - [6.2.4 Most Common Interview Tradeoffs](#624-most-common-interview-tradeoffs)
    - [6.2.5 Summary Cheat Sheet](#625-summary-cheat-sheet)

- [7. Real-World System Pattern Playbook](#7-real-world-system-pattern-playbook)
  - [7.1 Pattern A – CRUD & Storage Systems](#71-pattern-a---crud--storage-systems)
  - [7.2 Pattern B – Feed / Timeline Systems](#72-pattern-b---feed--timeline-systems)
  - [7.3 Pattern C – Real-Time Systems](#73-pattern-c---real-time-systems)
  - [7.4 Pattern D – Search Systems](#74-pattern-d---search-systems)
  - [7.5 Pattern E – Geospatial & Location-Based](#75-pattern-e---geospatial--location-based)
  - [7.6 Pattern F – Financial & Critical Consistency](#76-pattern-f---financial--critical-consistency)
  - [7.7 Pattern G – Large-Scale Processing & Infrastructure](#77-pattern-g---large-scale-processing--infrastructure)
  - [7.8 Pattern H – Collaborative & Infrastructure Utilities](#78-pattern-h---collaborative--infrastructure-utilities)
---

## 1. Distributed Systems Foundations

### 1.1 Why Distributed Systems Are Hard
Distributed systems are fundamentally more complex than single‑node systems because they introduce **partial failures** – parts of the system can fail independently while others continue to run. This creates challenges that directly lead to the need for consistency and partition tolerance trade‑offs.

#### 1.1.1 Network Partitions
A network partition occurs when the communication link between two or more nodes breaks. The nodes are still running, but they cannot exchange messages.

**Example:** A cable cut between two data centres prevents synchronisation. The system must decide whether to continue accepting requests (risking inconsistency) or to refuse requests (losing availability).

```
NETWORK PARTITION:
──────────────────
Node A    ✂✂✂✂✂✂✂✂✂✂    Node B
        (network is broken)
Alice writes to A → A CANNOT sync to B
Bob reads from B → What should B return?
```

#### 1.1.2 Replication Delays
Even in a healthy network, data replication takes time because signals travel at a finite speed. In a data centre, microseconds; across continents, tens to hundreds of milliseconds. During that interval, different replicas hold different values.

- **Synchronous replication** – the system waits for all (or a quorum of) replicas to acknowledge the write before confirming success. This eliminates the delay gap at the cost of higher latency.
- **Asynchronous replication** – the primary acknowledges immediately and propagates updates in the background. The gap persists, making stale reads possible.

This unavoidable delay is the root of the consistency/availability trade‑off.

#### 1.1.3 Independent Node Failures
Nodes can crash due to hardware faults, software bugs, or power outages. A crashed node can no longer serve requests, and its data may become temporarily unreachable. The system must tolerate these failures without losing data or availability. Typical solutions include replication, automatic failover, and health checks.

#### 1.1.4 Latency vs Consistency Tradeoffs
During normal operation (no partition), there is still a trade‑off between **low latency** and **strong consistency**. To achieve strong consistency, a write must be acknowledged by multiple replicas, adding network round‑trips. If low latency is preferred, the system can reply immediately and let replicas catch up later, resulting in eventual consistency.

This trade‑off is formalised by the **PACELC** extension to the CAP theorem (see Section 2.2).

#### 1.1.5 Split‑Brain Scenarios
A split‑brain occurs when a cluster partitions into two or more subgroups, and each subgroup believes it is the only active cluster. Both may elect their own leader, leading to conflicting decisions.

```
         ┌──────────┐          ┌──────────┐
         │  Leader  │          │  Leader  │
         │   (old)  │    ✂     │  (new)   │
         └────┬─────┘          └────┬─────┘
              │      partition       │
         ┌────▼─────┐          ┌────▼─────┐
         │ Node A   │          │ Node B   │
         │ x=5      │          │ x=5      │
         └──────────┘          └──────────┘
              ↓                     ↓
        Client writes x=10    Client writes x=20
              ↓                     ↓
         x=10 on A             x=20 on B
              ↓                     ↓
      After partition heals: two conflicting values!
```

Mitigation techniques include **quorum‑based voting**, **fencing tokens**, and **consensus algorithms** (Raft/Paxos) that ensure only one leader can operate.

---

## 2. CAP Theorem & PACELC

### 2.1 CAP Theorem

The **CAP theorem**, formulated by Eric Brewer, states that a distributed data store can provide only two of the following three guarantees simultaneously:

- **Consistency (C):** Every read receives the most recent write or an error. All nodes see the same data at the same time.
- **Availability (A):** Every request receives a (non‑error) response, without the guarantee that it contains the most recent write.
- **Partition Tolerance (P):** The system continues to operate despite an arbitrary number of messages being dropped or delayed by the network between nodes.

**The Trade‑off:** In a distributed system, network partitions are inevitable. Therefore, you must choose between **CP** (consistency + partition tolerance) and **AP** (availability + partition tolerance). A system that sacrifices partition tolerance (CA) is not practical in a distributed environment because partitions will happen.

#### 2.1.1 Consistency (C)
All nodes see the same data at the same time. After a write completes, any subsequent read (from any node) will observe that write – or the system will refuse to answer.

**Example:** A bank account balance – after you deposit money, every ATM must show the updated balance.

#### 2.1.2 Availability (A)
Every request (read or write) receives a response, even if some nodes are down. The response may not contain the latest data, but it is a valid response.

**Example:** A social media feed – even if one server fails, the system still returns a feed (possibly slightly stale).

#### 2.1.3 Partition Tolerance (P)
The system continues to function despite network failures that prevent communication between nodes. This is a **must‑have** in any distributed system.

**Example:** A network cable cut between data centres: the system should still serve requests from the reachable part of the network.

#### 2.1.4 Why Partitions Are Unavoidable
In a distributed system, network partitions **will** happen – cables get cut, routers fail, firewalls misconfigure. The CAP theorem points out that you cannot sacrifice P; you must always tolerate partitions. Therefore, the real choice is between C and A **when a partition occurs**.

#### 2.1.5 Why CA Doesn't Exist in Distributed Systems
A CA system (Consistency + Availability, no Partition tolerance) would require a network that never partitions. In practice, that means running on a **single machine** – a traditional local database. As soon as you distribute the system across a network, partitions are inevitable, so P becomes non‑optional. Therefore, every distributed system must choose either **CP** (Consistency + Partition Tolerance) or **AP** (Availability + Partition Tolerance).

#### 2.1.6 CAP Tradeoffs in Real Systems
The choice between CP and AP depends on the application:

| System Type | Choice | Reasoning |
|-------------|--------|-----------|
| Banking, payment, inventory | CP | Correctness is paramount; better to reject than show wrong data |
| Social feeds, CDN, DNS | AP | User experience tolerates slight staleness; availability is critical |
| Coordination services (etcd, ZooKeeper) | CP | Must guarantee leader election and configuration consistency |
| Collaborative editing (Google Docs) | CP (within session) | Use CRDTs to maintain convergence; temporary conflicts resolved automatically |

#### 2.1.7 CP Systems – Consistency Over Availability
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

**Real CP systems:** ZooKeeper, HBase, etcd, traditional RDBMS (PostgreSQL, MySQL in strict mode).

#### 2.1.8 AP Systems – Availability Over Consistency
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

**Real AP systems:** Cassandra, DynamoDB, CouchDB, Riak.

#### 2.1.9 Real-World Examples

| System Type | Choice | Reasoning | Example Technology |
|-------------|--------|-----------|---------------------|
| **CP System** | Consistency + Partition Tolerance | Availability is sacrificed during a partition – the system may reject writes or become read‑only until consistency can be restored. | Traditional relational databases with synchronous replication, HBase, ZooKeeper. |
| **AP System** | Availability + Partition Tolerance | Consistency is sacrificed – during a partition, all nodes remain available but may return stale or conflicting data. Eventually, they will reconcile. | Amazon DynamoDB (eventual consistency mode), Cassandra, CouchDB. |

**Detailed example – Two‑node database with a partition:**

Starting state: both nodes agree `x = 10`. A network partition occurs.

- **AP behaviour:** Write `x=20` to Node A succeeds immediately. Read from Node B returns `10` (stale). Availability is preserved, consistency broken.
- **CP behaviour:** Write `x=20` to Node A fails because Node B cannot be reached to form a quorum. The system returns an error, preserving consistency at the cost of availability.

---

### 2.2 PACELC Theorem

#### 2.2.1 Why CAP Is Not Enough
CAP only addresses behaviour **during a network partition**. In a healthy network, there is still a choice to make: do you wait for synchronisation (latency cost) or reply immediately (consistency cost)? PACELC extends CAP to cover both situations.

#### 2.2.2 PACELC Explained
- **P** – During a **Partition**, trade‑off between **A**vailability and **C**onsistency (the original CAP choice).
- **E** – **Else** (when the system is running normally, without a partition), trade‑off between **L**atency and **C**onsistency.

| Scenario | Trade‑off | Explanation |
|----------|-----------|-------------|
| **Partition (P)** | Availability vs. Consistency | Exactly the CAP choice. For example, a CP system may block writes during a partition to avoid inconsistency; an AP system will accept writes everywhere and reconcile later. |
| **Else (E)** | Latency vs. Consistency | When the network is healthy, you can choose to replicate data synchronously (strong consistency but higher latency) or asynchronously (low latency but eventual consistency). |

#### 2.2.3 Latency vs Consistency Tradeoff
Even when all nodes are reachable, synchronous replication requires waiting for network round‑trips. Asynchronous replication delivers low latency at the cost of possible stale reads. PACELC formalises this everyday trade‑off.

#### 2.2.4 Real-World Examples

| System | PACELC Notation | Partition Behaviour | Normal Operation Behaviour |
|--------|----------------|---------------------|----------------------------|
| Cassandra | PA/EL | Available | Low latency, eventual consistency |
| DynamoDB (default) | PA/EL | Available | Low latency, eventual consistency |
| HBase | PC/EC | Consistent, possibly unavailable | Consistent, higher latency |
| PostgreSQL (synchronous) | PC/EC | Consistent, may be unavailable | Consistent |
| MongoDB (default) | PC/EC | Consistent, may be unavailable | Consistent |

#### 2.2.5 CAP vs PACELC Comparison

| Aspect | CAP | PACELC |
|--------|-----|--------|
| **Coverage** | Only during a network partition | During partition AND during normal operation |
| **Choice during partition** | A or C | A or C (same as CAP) |
| **Choice during normal operation** | Not addressed | L (low latency) or C (strong consistency) |
| **Usage** | High‑level reasoning about fault tolerance | Finer‑grained trade‑off for performance and consistency under normal conditions |

---

## 3. Consistency Models

### 3.1 Strong Consistency (Linearizability)

#### 3.1.1 What It Means
Strong consistency guarantees that after a write completes, **any subsequent read** (from any node) will see that write. The system behaves as if there were only one copy of the data, updated atomically. This is equivalent to **linearizability**.

#### 3.1.2 How It Is Achieved
- **Synchronous replication:** A write is acknowledged only after all replicas have confirmed it.
- **Quorum‑based protocols:** In systems like Apache Cassandra, you can achieve strong consistency by setting read and write consistency levels such that `R + W > N` (e.g., `QUORUM` for both).
- **Consensus algorithms:** Paxos, Raft, or Zab (used in ZooKeeper) ensure a single order of operations across all nodes.

#### 3.1.3 Synchronous Replication
The primary (leader) sends the write to all followers and waits for acknowledgement from a majority (or all) before confirming to the client. This adds latency but ensures every replica sees the same value.

#### 3.1.4 Consensus Protocols
Protocols like **Raft** elect a single leader that sequences all writes. Followers apply operations in the same order. The protocol guarantees safety even if the leader fails and a new one is elected.

#### 3.1.5 Pros & Cons

| ✅ Pros | ❌ Cons |
|---------|---------|
| Easy to reason about – no stale data, no conflicts | Higher latency (must wait for multiple nodes) |
| Simplifies application logic | Reduced availability during partitions |
| Works well for critical data | Can become a scalability bottleneck |

#### 3.1.6 Real-World Examples
- **Banking ledgers** – balance updates must be atomic and visible everywhere.
- **Seat reservation systems** – two users cannot book the same seat.
- **Payment gateways (Stripe)** – idempotent, exactly‑once charge.
- **Leader election (etcd, ZooKeeper)** – only one node must be elected.
- **Google Spanner** – uses TrueTime and synchronous replication to provide external consistency.

**Code example (Cassandra quorum):**
```cql
CONSISTENCY QUORUM;
INSERT INTO users (id, name) VALUES (1, 'Alice');
-- The write is acknowledged after a majority of replicas have persisted it.
SELECT * FROM users WHERE id = 1;
-- The read will wait for responses from a majority of replicas and return the latest version.
```

---

### 3.2 Eventual Consistency

#### 3.2.1 What It Means
Eventual consistency guarantees that if no new updates are made to a given data item, eventually all accesses will return the last updated value. In the absence of writes, the system converges.

#### 3.2.2 Replication & Convergence
- Writes are propagated **asynchronously** to replicas.
- Different replicas may temporarily return different values.
- The system converges when all pending updates have been applied everywhere.

#### 3.2.3 Background Synchronization
Replicas continuously catch up using anti‑entropy protocols (Merkle trees), read‑repair (fix stale data on read), and hinted handoffs (temporarily store updates for failed nodes).

#### 3.2.4 Pros & Cons

| ✅ Pros | ❌ Cons |
|---------|---------|
| High availability and low latency | Temporary inconsistencies |
| Scales well – no waiting for all replicas | Conflict resolution logic may be complex |
| Tolerates partitions gracefully | Not suitable for critical data (e.g., finance) |

#### 3.2.5 Real-World Examples
- **DNS** – updates propagate slowly; different resolvers may see different IPs.
- **Social media feeds** – a tweet appears for its author immediately but may take seconds to reach followers.
- **Cassandra (default)** – writes are fast, reads are eventually consistent unless higher consistency is requested.
- **CouchDB** – multi‑version concurrency control, eventual reconciliation.
- **DynamoDB (eventual consistency mode):**
```java
GetItemRequest request = new GetItemRequest()
    .withTableName("Users")
    .withKey(Collections.singletonMap("id", new AttributeValue("123")))
    .withConsistentRead(false); // eventual consistency
GetItemResult result = dynamoDBClient.getItem(request);
```

---

### 3.3 Read-Your-Writes Consistency

#### 3.3.1 What It Means
Read‑after‑write (read‑your‑writes) guarantees that after a client writes data, any subsequent read by the **same client** will reflect that write. Other clients may not see it immediately.

#### 3.3.2 Why It Matters
Users expect to see the result of their own actions instantly. Posting a comment and then not seeing it appears as a bug, even if the system is globally eventually consistent.

#### 3.3.3 Sticky Sessions / Session Routing
Route all requests from a given client to the same application server. The server maintains a local cache of recent writes and can serve them without waiting for global replication.

```
User A → Load Balancer → Server 1 (session created, data stored locally)
Subsequent requests from User A are always routed to Server 1 (sticky session cookie).
```

#### 3.3.4 Leader Reads
For the user’s own data, always read from the leader (primary) that accepted the write, or use a version timestamp to ensure the replica is up‑to‑date.

#### 3.3.5 Real-World Examples
- **Facebook** – after posting a status, it appears in your own feed immediately.
- **GitHub** – a pushed commit shows up instantly in the repository view.
- **Amazon** – an order appears in order history right after placement.

**Example with client‑side timestamp (DynamoDB‑like):**
```java
long timestamp = System.currentTimeMillis();
// ... write includes timestamp ...
// On read, if stored timestamp < client's last known, retry with strong consistency.
```

---

### 3.4 Causal Consistency

#### 3.4.1 Causality Relationships
Operations that are causally related must be seen in the same order by everyone. If Alice posts a photo and Bob comments on it, Bob’s comment is causally dependent on Alice’s post. Every observer must see Alice’s post before Bob’s comment.

#### 3.4.2 Ordering Guarantees
Causal consistency is implemented using **vector clocks** or **logical timestamps**. Each node tracks the causal dependencies of operations and ensures that dependent operations are delivered only after their cause.

#### 3.4.3 Real-World Examples
- **Chat applications** – a reply must appear after the original message.
- **Collaborative editing** – text inserted based on previously seen text must be ordered correctly.
- **Social media** – comments on a post should never appear before the post itself.

---

### 3.5 Other Consistency Models

#### 3.5.1 Monotonic Reads
A guarantee that if a process has seen a particular value for a data item, it will never see an older value on subsequent reads. Prevents “time travel” for a single client.

#### 3.5.2 Monotonic Writes
Ensures that writes by a process are serialized in the order they were issued. If a process writes `x=1` then `x=2`, the system will never see `x=2` then later `x=1`.

#### 3.5.3 Session Consistency
A client‑centric model that guarantees monotonic reads, monotonic writes, and read‑your‑writes within a single session. Once the session ends, the guarantees are relaxed.

#### 3.5.4 Sequential Consistency
All operations appear to execute in some total order, and that order is consistent with the order of operations on each individual process. Weaker than linearizability because it does not require real‑time ordering across processes.

#### 3.5.5 Tunable Consistency
Some systems (e.g., Cassandra, Riak) allow the client to choose the desired consistency level on a per‑operation basis. For example, `ONE`, `QUORUM`, or `ALL` for reads and writes, trading off latency, availability, and consistency dynamically.

---

### 3.6 Comparing Consistency Models

#### 3.6.1 Consistency Spectrum
```
STRONGER ◄────────────────────────────────────────────────► WEAKER

Linearizability → Sequential → Causal → Read‑Your‑Writes → Monotonic Reads → Eventual
```

#### 3.6.2 Latency vs Consistency Tradeoff
Stronger consistency requires more coordination, leading to higher latency. Eventual consistency delivers low latency by avoiding synchronous replication.

#### 3.6.3 Availability vs Consistency Tradeoff
During a partition, strong consistency may require the system to become unavailable (CP). Eventual consistency keeps the system available but sacrifices consistency (AP).

#### 3.6.4 Decision Matrix

| Model | Guarantee | Use Cases | Trade‑offs |
|-------|-----------|-----------|------------|
| **Strong (Linearizability)** | All reads see the latest write; total order of operations. | Banking, stock trading, leader election. | High latency, lower availability during partitions. |
| **Eventual** | If no new updates, all replicas will eventually become consistent. | Social media feeds, product catalogs, DNS. | High availability, low latency, but temporary inconsistencies. |
| **Read‑After‑Write** | A client sees its own writes immediately. Others may not. | User profiles, comment systems, order history. | Simple to implement; ensures good UX; still scalable. |
| **Causal** | Causally related operations are seen in order. | Chat, collaborative editing. | More complex implementation; better than eventual for causality. |
| **Monotonic Reads** | Reads never go backward in time for a single client. | News feeds, versioned data. | Prevents user confusion. |
| **Sequential** | Total order consistent with each client’s order. | Distributed shared memory. | Less strict than linearizability but still hard to scale. |

---

## 4. Replication & Consensus

### 4.1 Replication Strategies

#### 4.1.1 Leader-Follower Replication
One node (the leader) accepts all writes and replicates them to followers. Followers serve read‑only traffic. If the leader fails, a follower can be promoted.
- **Advantages:** Simple to implement, strong consistency on the leader.
- **Disadvantages:** Write bottleneck, failover may cause brief unavailability.
- **Used by:** PostgreSQL, MySQL, MongoDB (default).

#### 4.1.2 Multi-Leader Replication
Multiple nodes accept writes and replicate to each other. Often used in multi‑data‑center deployments for better write latency and conflict handling.
- **Advantages:** Higher write throughput, better local latency.
- **Disadvantages:** Conflicts must be resolved, complex conflict resolution logic.
- **Used by:** CouchDB, some cloud databases.

#### 4.1.3 Leaderless Replication
Any replica can accept writes and reads. Quorums are used to achieve consistency. Clients send writes to multiple nodes and wait for acknowledgments from a sufficient number.
- **Advantages:** High availability, no single point of failure, good for multi‑region.
- **Disadvantages:** Complex to achieve strong consistency; needs anti‑entropy and read‑repair.
- **Used by:** Cassandra, DynamoDB, Riak.

#### 4.1.4 Synchronous Replication
The leader waits for acknowledgment from followers (or a quorum) before confirming the write. Ensures that the data is durable on multiple nodes and provides strong consistency, but adds latency and may block during follower failures.

#### 4.1.5 Asynchronous Replication
The leader acknowledges the write immediately and sends updates to followers in the background. Low latency, high throughput, but followers may lag and return stale data. Used in many AP systems.

---

### 4.2 Consensus & Coordination

#### 4.2.1 Why Consensus Is Needed
Distributed systems often need agreement on a single value or order of operations – for example, who is the leader, which transaction committed first, or what is the current configuration. Without consensus, split‑brain and data corruption can occur.

#### 4.2.2 Quorum Reads & Writes
By choosing appropriate read (`R`) and write (`W`) consistency levels relative to the total number of replicas (`N`), you can guarantee strong consistency when `R + W > N`. Quorums allow a trade‑off between consistency and latency.

#### 4.2.3 Raft Overview
Raft is a consensus algorithm designed for understandability. It elects a single leader, which manages a replicated log. All changes flow through the leader and are committed when a majority of nodes have persisted the log entry. Raft provides linearizable consistency.

#### 4.2.4 Paxos Overview
Paxos is the classic consensus algorithm. It ensures that a single value is chosen even in the presence of failures. Multi‑Paxos extends it for a sequence of values. While theoretically robust, it is notoriously difficult to implement.

#### 4.2.5 ZooKeeper / etcd Usage
Both ZooKeeper and etcd are coordination services built on consensus protocols (Zab and Raft, respectively). They provide linearizable reads/writes, distributed locks, leader election, and configuration management. Used in systems like Kafka, Kubernetes, and Hadoop.

---

### 4.3 Conflict Resolution in Distributed Systems

#### 4.3.1 Last Write Wins (LWW)
Uses timestamps to determine the “latest” value. The write with the highest timestamp wins, and older values are discarded. Simple, but risk of data loss due to clock skew; used as default in Cassandra.

#### 4.3.2 Vector Clocks
Each replica maintains a vector of counters (one per node) to track causal relationships. When a conflict is detected, the application can decide how to merge or present multiple versions. Used in DynamoDB.

#### 4.3.3 CRDTs (Conflict-Free Replicated Data Types)
Data structures designed so that concurrent updates can be merged automatically without conflicts. Examples: G‑Counter (grow‑only counter), OR‑Set (observed‑remove set). Used in Riak, collaborative editing tools (automerge, yjs).

#### 4.3.4 Application-Level Conflict Resolution
When automatic resolution is insufficient, the application itself receives multiple versions and uses business logic to decide. For example, merging a shopping cart where the same item was added and removed – present both options to the user.

#### 4.3.5 Anti-Entropy Mechanisms
Background processes that compare replicas and repair differences. Common approaches:
- **Merkle trees:** Efficient comparison of replica data.
- **Read‑repair:** On a read, if a stale value is detected, it is updated.
- **Hinted handoff:** A coordinator temporarily stores updates for an unreachable node and delivers them when the node rejoins.

---

## 5. Consistency in System Design

### 5.1 Choosing Consistency by Application Type

#### 5.1.1 CRUD & Storage Systems
| Question | CAP Choice | Consistency Method | Why | Implementation |
|----------|------------|--------------------|-----|----------------|
| URL Shortener | AP | Eventual for redirects; strong for custom aliases | Custom aliases must be unique. | Conditional write for aliases; Redis cache with TTL for redirects. |
| Pastebin | AP | Eventual consistency | Write‑once, read‑many; staleness acceptable. | Object storage (S3) + CDN; background job for expiry. |
| Online Bookstore | CP (inventory) / AP (catalog) | Strong for inventory; eventual for reviews | Avoid overselling; catalog can be slightly stale. | Inventory: pessimistic/optimistic locking; Catalog: Redis cache + async replication. |
| Ticket Booking | CP | Strong consistency (row‑level locking) | Two users cannot book the same seat. | Distributed lock (Redis Redlock) or idempotent reservation with timeout. |
| Dropbox / Drive | CP for metadata; AP for file chunks | Strong for metadata; eventual for chunks | Directory structure must be consistent; chunks can be reconciled. | Metadata: relational DB with synchronous replication; chunks: content‑addressable store with CRDTs. |

#### 5.1.2 Feed / Timeline Systems
| Question | CAP Choice | Consistency Method | Why | Implementation |
|----------|------------|--------------------|-----|----------------|
| Twitter / X | AP | Read‑your‑writes for own posts; eventual for others | User expects own tweet immediately. | Sticky sessions or write‑through cache; fan‑out via Kafka asynchronously. |
| Instagram | AP | Read‑after‑write for own media; eventual for feed | Media larger; own content must be visible. | CDN for media; dedicated cache updated synchronously on upload; global feed via pull model. |
| Facebook | AP | Eventual + monotonic reads | Computed feed in background; no going backward. | Pre‑compute feed (Spark), store with version numbers; clients send last‑seen version. |
| LinkedIn | AP | Eventual with pull‑based fan‑out | Lower update frequency. | Query last N posts from followed users; cache 30 sec. |

#### 5.1.3 Real-Time Systems
| Question | CAP Choice | Consistency Method | Why | Implementation |
|----------|------------|--------------------|-----|----------------|
| WhatsApp / Messenger | CP for ordering; AP for presence | Strong per conversation; eventual for presence | Message order must be linearizable. | Raft‑based log or single leader Redis per conversation; presence via TTL. |
| Slack / Discord | CP for history; AP for delivery | Strong for persistence; at‑least‑once delivery | Channel history is immutable and ordered. | Sharded PostgreSQL; Kafka with idempotent consumers. |
| Live Comments | AP | Eventual + idempotency | High volume; approximate ordering okay. | Kafka ingest with UUID; Flink assigns timestamp; Redis sorted set. |
| Multiplayer Game | CP for state; AP for chat | Strong (authoritative server); eventual for chat | Game state must be consistent. | Single authoritative server per room (consistent hashing); UDP chat. |

#### 5.1.4 Search Systems
| Question | CAP Choice | Consistency Method | Why | Implementation |
|----------|------------|--------------------|-----|----------------|
| Google Search | AP | Eventual for index updates | Freshness SLA is minutes. | Batch indexing (MapReduce) + streaming index (Elasticsearch). |
| YouTube Search | AP | Eventual + read‑your‑writes for uploads | Uploader expects own video soon. | Global index async; personalised index (Redis) checked first. |
| Amazon Product Search | AP for metadata; CP for inventory | Eventual for metadata; strong for inventory | Avoid overselling. | Elasticsearch async; separate Redis inventory cache updated synchronously. |
| Search Autocomplete | AP | Eventual + consistent prefix | Derived from historical logs. | Trie built hourly; serve from CDN. |

#### 5.1.5 Geospatial & Location-Based Systems
| Question | CAP Choice | Consistency Method | Why | Implementation |
|----------|------------|--------------------|-----|----------------|
| Uber / Lyft | CP for matching; AP for location | Strong for assignment; eventual for location | Driver assigned once. | Distributed lock on driver; Redis Geo async for locations. |
| Google Maps | AP | Eventual for place data; read‑your‑writes for reviews | Place data changes rarely. | Read replicas updated nightly; write‑through cache for reviews. |
| Food Delivery | CP for order; AP for menus | Strong for driver pairing; eventual for menus | Same as Uber. | Same as Uber; menus CDN with 5‑min TTL. |
| Nearby Friends | AP | Eventual + monotonic reads | Frequent location updates. | Redis geohash with version numbers; client sends last‑seen version. |

#### 5.1.6 Financial & Critical Consistency Systems
| Question | CAP Choice | Consistency Method | Why | Implementation |
|----------|------------|--------------------|-----|----------------|
| Payment Gateway | CP | Strong + idempotency | Money cannot be double‑charged. | Idempotency key with unique constraint; saga with compensating actions. |
| Wallet System | CP | Strong with optimistic locking | Balance updates atomic. | Version column in DB; update with `WHERE version = old_version`. |
| UPI-like System | CP | Strong across banks (2PC or saga) | Real‑time payments require atomic debit/credit. | Distributed transaction coordinator or idempotent saga. |
| Distributed Job Scheduler | CP | Strong via leader election | Only one instance owns a job. | etcd for leader election; fencing tokens. |

#### 5.1.7 Large-Scale Processing Systems
| Question | CAP Choice | Consistency Method | Why | Implementation |
|----------|------------|--------------------|-----|----------------|
| Google Analytics | AP | Eventual + exactly‑once | Duplicate counts bad; stale acceptable. | Kafka idempotent producer; Flink exactly‑once; write to BigQuery. |
| Clickstream | AP | Eventual + sessionization | Tolerates out‑of‑order events. | Flink event‑time processing; session state in RocksDB. |
| ELK | AP | Eventual (append‑only logs) | Logs immutable; search lag okay. | Kafka buffer; Elasticsearch near‑real‑time refresh. |
| Prometheus | AP | Eventual for scrape data | Missing a few points acceptable. | Pull every 15 sec; remote storage (Thanos) for HA. |
| Kafka-like System | CP for leaders; AP for consumers | Strong for leadership; at‑least‑once delivery | Per‑partition ordering. | Raft for leader election; idempotent consumers. |

#### 5.1.8 Collaborative Systems
| Question | CAP Choice | Consistency Method | Why | Implementation |
|----------|------------|--------------------|-----|----------------|
| Google Docs | CP (within session) | Strong eventual (CRDTs/OT) | Multiple editors; must converge. | CRDTs (automerge/yjs); WebSocket server broadcasts merges. |
| Figma | CP | CRDTs with low latency | Real‑time drawing needs no conflicts. | Spatial CRDTs; WebRTC or central relay. |
| Distributed Locks | CP | Strong (Raft) | Locks must be linearizable. | etcd/ZooKeeper ephemeral nodes. |
| Snowflake ID | CP (worker coordination) | Strong for worker ID; eventual for ID generation | Worker IDs unique. | etcd for worker ID assignment; local ID generation. |
| Web Crawler | AP | Eventual for URL frontier | Duplicate detection can be probabilistic. | Bloom filter; priority queue with politeness delays. |

---

### 5.2 Practical Decision Framework

#### 5.2.1 Strong vs Eventual Consistency
- **Strong:** When correctness is non‑negotiable (money, inventory, leader election).
- **Eventual:** When availability and low latency matter more than immediate freshness (social feeds, analytics, CDN).

#### 5.2.2 CP vs AP Decision Flow
```
Start: Is the data critical and must never be stale/wrong?
  ├─ YES → Choose CP (sacrifice availability during partitions)
  └─ NO  → Can you tolerate temporary inconsistencies?
              ├─ YES → Choose AP (high availability, eventual consistency)
              └─ NO  → Reconsider if eventual consistency can work with read‑your‑writes or causal consistency; otherwise CP.
```

#### 5.2.3 When to Sacrifice Availability
- Financial transactions, inventory decrements, seat reservations, any exactly‑once semantics.
- During a partition, it's better to return an error than to allow a double charge or oversell.

#### 5.2.4 When to Sacrifice Consistency
- Social media timelines, user profiles, product recommendations, DNS, CDN.
- A short delay (seconds) is invisible to users; availability is critical for engagement.

#### 5.2.5 Hybrid Approaches
Many systems use different consistency models for different parts:
- **Amazon:** Strong consistency for cart updates, eventual for product reviews.
- **Uber:** Strong for ride matching, eventual for driver location.
- **Twitter:** Read‑your‑writes for own tweets, eventual for others’ timelines.
- **CQRS:** Separate read and write models with eventual synchronization.

---

### 5.3 Common Design Patterns

#### 5.3.1 Leader-Based Architectures
A single writer (leader) coordinates all updates, providing a natural point of linearizability. Followers replicate for fault tolerance and read scaling. Used in most relational databases.

#### 5.3.2 CQRS (Command Query Responsibility Segregation)
Separates read and write models. Writes go to a normalized, strongly consistent store; reads are served from a separate, denormalized, eventually consistent store optimized for queries. Common in event‑driven systems.

#### 5.3.3 Event Sourcing
All changes are stored as an append‑only sequence of events. The current state is derived by replaying events. This naturally provides a full audit trail and supports eventual consistency across projections.

#### 5.3.4 Saga Pattern
A sequence of local transactions, each with a compensating action. Used in microservices to maintain eventual consistency across services without distributed transactions. Example: travel booking (flight, hotel, car).

#### 5.3.5 Cache Invalidation Strategies
- **Write‑through:** Update cache and DB together; strong consistency but higher latency.
- **Write‑behind:** Update cache, async update DB; low latency, eventual consistency.
- **Write‑around:** Update DB, invalidate cache; next read populates from DB.
- **TTL + refresh‑ahead:** Entries expire after a fixed time, proactively refreshed before expiry to prevent stampedes.

---

### 5.4 Common Anti-Patterns

#### 5.4.1 Assuming Strong Consistency Everywhere
Not every system needs linearizability. Using strong consistency for non‑critical features (e.g., user bios, like counts) adds unnecessary latency and complexity. Use the right model for the use case.

#### 5.4.2 Ignoring Replication Lag
Designing the application as if all reads are always up‑to‑date leads to user confusion when they see stale data. Handle stale reads gracefully, provide feedback, and use read‑your‑writes where needed.

#### 5.4.3 Cross-Region Synchronous Writes Everywhere
Synchronous replication across regions adds hundreds of milliseconds of latency and kills availability if a region fails. Only use it for critical data; otherwise, use asynchronous replication with conflict resolution.

#### 5.4.4 Poor Conflict Resolution
Relying solely on LWW without understanding clock skew can cause silent data loss. Use CRDTs or application‑level merge strategies for data that requires correctness.

#### 5.4.5 Distributed Transactions Overuse
Using 2PC for every microservice interaction is a scalability and availability killer. Prefer Sagas with compensating actions for long‑lived business transactions.

---

## 6. Interview-Oriented HLD Notes

### 6.1 Practical Guide: Which Model for Which System?

#### 6.1.1 Banking Systems
- **Model:** Strong (linearizable)
- **Why:** Money is at stake; double charges or incorrect balances are unacceptable.
- **Technique:** Leader‑based replication with synchronous commit (e.g., PostgreSQL with `synchronous_commit`), or distributed consensus for multi‑region.

#### 6.1.2 Social Media Feeds
- **Model:** Eventual + Read‑Your‑Writes for own posts
- **Why:** A 2‑second delay in seeing a friend’s post is invisible; users expect their own actions to appear instantly.
- **Technique:** Sticky sessions or write‑through cache for own data; asynchronous fan‑out (Kafka) for others.

#### 6.1.3 Messaging Systems
- **Model:** Strong per‑conversation ordering; eventual for presence
- **Why:** Message order must never be violated; presence can tolerate slight staleness.
- **Technique:** Raft‑based log per conversation; distributed cache with TTL for presence.

#### 6.1.4 Search Engines
- **Model:** Eventual for index updates; read‑your‑writes for own uploads
- **Why:** Index freshness in minutes is acceptable; uploaders expect to find their content quickly.
- **Technique:** Batch + streaming indexing; personalized index for recent uploads.

#### 6.1.5 Ride Sharing Systems
- **Model:** Strong for ride matching; eventual for location
- **Why:** A driver can only be assigned once; location updates every few seconds can be slightly stale.
- **Technique:** Distributed lock or conditional write for matching; Redis Geo async for locations.

#### 6.1.6 Collaborative Editors
- **Model:** Strong eventual (CRDTs/OT)
- **Why:** Multiple users edit simultaneously; system must converge without conflicts.
- **Technique:** CRDTs for each operation; broadcast via WebSocket server.

---

### 6.2 Quick Revision Material

#### 6.2.1 Quick Reference Card
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

#### 6.2.2 Key Interview Sentences
> *"This is a payment system, so we need strong consistency. I'd use PostgreSQL in a leader-follower setup. During a partition, we'd rather return an error than allow a transaction with stale balance data. That's a CP tradeoff — we accept reduced availability to guarantee correctness."*

> *"For the social feed, eventual consistency is perfectly fine. If a tweet appears in my feed 2 seconds after it's posted, that's unnoticeable to users. I'd use Cassandra — an AP system — which gives us massive write throughput and is partition-tolerant."*

> *"In PACELC terms, Cassandra is PA/EL — during partitions it stays available, and in normal operation it favors low latency over strict consistency. That's exactly what a feed system needs."*

> *"For the user's timeline, we need read‑after‑write consistency so that users see their own posts immediately. We achieve this by using sticky sessions: all requests from a user go to the same application server, which maintains a short‑lived cache of recent writes. For other users, eventual consistency is acceptable, so we replicate data asynchronously."*

#### 6.2.3 Self-Test Questions
1. A bank is designing its transaction ledger. During a network partition, should the system show stale data or return an error? What CAP choice does this represent?
2. Twitter has 200M DAU. When Alice posts a tweet, it's acceptable if Bob sees it 3 seconds later. What consistency model fits here?
3. What's the difference between "eventual consistency" and "no consistency"?
4. Two users simultaneously edit the same Google Doc. The system needs to merge their changes. What conflict resolution strategy does Google Docs use?
5. Why is "CA" (Consistency + Availability, no Partition tolerance) not achievable in a distributed system?

#### 6.2.4 Most Common Interview Tradeoffs
| Tradeoff | Question to Ask | Typical Answer |
|----------|-----------------|----------------|
| CP vs AP | What happens during a network partition? | If money involved → CP (return error). If social feed → AP (serve stale data). |
| Strong vs Eventual | How quickly must all users see the update? | Milliseconds → strong consistency. Seconds → eventual with read‑your‑writes for the actor. |
| Latency vs Consistency | Can we afford to wait for replication? | User‑facing low‑latency → async replication (eventual). Critical background operation → sync replication. |
| Simple vs Complex | Can we handle conflict resolution in code? | Simple app → leader‑based. Multi‑region, offline‑first → CRDTs or OT. |
| Single leader vs Multi‑leader | Do we need multi‑region writes? | Single region → leader‑follower. Global active‑active → multi‑leader with conflict resolution. |

#### 6.2.5 Summary Cheat Sheet
| Application Type | CAP Choice | Consistency Model | Key Technique |
|-----------------|------------|------------------|---------------|
| Financial, payments, inventory | CP | Strong (linearizable) | Synchronous replication, leader‑based |
| Social feeds, profiles | AP | Eventual + read‑your‑writes | Asynchronous fan‑out, sticky sessions |
| Chat / messaging | CP (ordering) / AP (presence) | Strong per conversation | Raft log or single leader |
| Collaborative editing | CP (within session) | Strong eventual (CRDTs) | CRDTs or OT |
| Search, analytics | AP | Eventual | Batch + streaming index |
| Ride matching | CP | Strong for assignment | Distributed lock, conditional update |
| DNS / CDN | AP | Eventual | TTL‑based caching |
| Coordination (locks, leader election) | CP | Strong (linearizable) | Consensus (Raft, Paxos) |

## 7. Real-World System Pattern Playbook

### 7.1 Pattern A – CRUD & Storage Systems

| Question | CAP Choice | Consistency Method | Why | Deep Dive / Implementation |
|----------|------------|--------------------|-----|----------------------------|
| **URL Shortener (TinyURL)** | AP (PACELC: AP/EL) | Eventual for redirects; strong for custom aliases | Redirects can be stale; custom aliases must be unique. | For custom aliases: use **conditional write** (e.g., DynamoDB `ConditionExpression` or Redis `SETNX`). For redirects: cache short codes in Redis with TTL; use asynchronous replication across regions. |
| **Pastebin** | AP | Eventual consistency | Write‑once, read‑many; staleness is acceptable. | Store paste in object storage (S3). Generate a unique key. Use a CDN for reads. For expiry, run a background job that deletes expired pastes; no strong consistency needed. |
| **Online Bookstore** | CP for inventory; AP for catalog | Strong for inventory; eventual for reviews | Inventory cannot oversell; catalog can be cached. | Inventory: use **pessimistic locking** (SELECT FOR UPDATE) or **optimistic locking** (version number). Catalog: use Redis cache with TTL and asynchronous updates from a read replica. |
| **Ticket Booking (BookMyShow)** | CP | Strong consistency (row‑level locking) | Two users cannot book the same seat. | Use a **distributed lock** (Redis Redlock) per seat or a database with row‑level locks. Alternatively, use **idempotent reservation** with a timeout (hold seat for 5 minutes). |
| **Dropbox / Google Drive** | CP for metadata; AP for file chunks | Strong for metadata; eventual for chunks | Directory structure must be consistent; file chunks can be reconciled. | Metadata: use a relational DB with synchronous replication (e.g., PostgreSQL with `synchronous_commit`). File chunks: use a content‑addressable store (CAS) with CRDTs for conflict resolution (last‑write‑wins on timestamps). |

---

### 7.2 Pattern B – Feed / Timeline Systems

| Question | CAP Choice | Consistency Method | Why | Deep Dive / Implementation |
|----------|------------|--------------------|-----|----------------------------|
| **Twitter / X** | AP (AP/EL) | Read‑your‑writes for own posts; eventual for others | User expects to see own tweet immediately. | Use **sticky sessions** or a **write‑through cache** (Redis) for the user’s own timeline. For others’ feeds: fan‑out via Kafka to followers’ inboxes (asynchronous). |
| **Instagram Feed** | AP | Read‑after‑write for own media; eventual for feed | Similar to Twitter, but media is larger. | Store media in CDN. For the user’s own feed, read from a dedicated cache that is updated synchronously on upload. For the global feed, use a **pull model** (aggregate from followed users on read) with caching. |
| **Facebook News Feed** | AP | Eventual + monotonic reads | Feed is computed in background; monotonic reads prevent regression. | Use a **scoring service** that pre‑computes feeds asynchronously. Store the computed feed in a distributed cache (e.g., Memcached) with version numbers. Clients send their last‑seen version; server ensures monotonic increase. |
| **LinkedIn Feed** | AP | Eventual with pull‑based fan‑out | Lower update frequency; pull model reduces write amplification. | On read, query the last N posts from each followed user (limit to 100 users). Use a **timeline service** that returns posts sorted by timestamp. Cache results for 30 seconds. |

---

### 7.3 Pattern C – Real-Time Systems

| Question | CAP Choice | Consistency Method | Why | Deep Dive / Implementation |
|----------|------------|--------------------|-----|----------------------------|
| **WhatsApp / Messenger** | CP for message ordering; AP for presence | Strong per conversation; eventual for presence | Message order must be linearizable. | Per conversation, use a **Raft‑based log** (e.g., etcd) or a **single leader** Redis node. Assign a monotonic sequence number. Presence can be stored in a distributed cache with TTL and updated via WebSocket heartbeats. |
| **Slack / Discord** | CP for channel history; AP for delivery | Strong for persistence; at‑least‑once delivery | Channel history is immutable and ordered. | Store messages in a **sharded PostgreSQL** (by channel_id) with a serial primary key. For delivery, use a message queue (Kafka) with idempotent consumers and retries. |
| **Live Comments (YouTube Live)** | AP | Eventual + idempotency | High volume; ordering by server timestamp is fine. | Use Kafka to ingest comments with a client‑generated UUID for idempotency. A stream processor (Flink) assigns a server‑side timestamp and writes to a Redis sorted set (per video). |
| **Multiplayer Game Backend** | CP for game state; AP for chat | Strong for state (authoritative server); eventual for chat | Game state must be consistent to prevent cheating. | Assign each game room to a single **authoritative server** (consistent hashing). The server processes all state updates synchronously. Chat can be broadcast via UDP with no ordering guarantees. |

---

### 7.4 Pattern D – Search Systems

| Question | CAP Choice | Consistency Method | Why | Deep Dive / Implementation |
|----------|------------|--------------------|-----|----------------------------|
| **Google Search** | AP | Eventual for index updates | Freshness SLA is minutes, not seconds. | Use a **batch indexing pipeline** (MapReduce or Spark) that rebuilds the inverted index periodically (e.g., every 5 minutes). For real‑time updates, use a **streaming index** (e.g., Elasticsearch with near‑real‑time refresh). |
| **YouTube Search** | AP | Eventual + read‑after‑write for own uploads | User expects own video to appear quickly. | For general search, use a **global index** updated asynchronously. For the uploader, after upload, write to a **personalised index** (e.g., Redis) that is queried first, then fallback to global. |
| **Amazon Product Search** | AP for metadata; CP for inventory | Eventual for metadata; strong for inventory | Inventory must be real‑time to avoid overselling. | Product metadata: use Elasticsearch with async updates. Inventory: use a **separate in‑memory cache** (Redis) that is updated synchronously on purchase (CAS). Search results combine both. |
| **Search Autocomplete** | AP | Eventual + consistent prefix | Suggestions derived from historical data. | Build a **trie** from query logs every hour. Serve from CDN. For real‑time personalisation, use a **client‑side trie** that merges with server suggestions. |

---

### 7.5 Pattern E – Geospatial & Location-Based

| Question | CAP Choice | Consistency Method | Why | Deep Dive / Implementation |
|----------|------------|--------------------|-----|----------------------------|
| **Uber / Lyft** | CP for ride matching; AP for location updates | Strong for assignment; eventual for location | A driver can only be assigned to one ride. | For ride matching: use **distributed lock** on driver ID (Redis Redlock) or a conditional update in Cassandra (`IF NOT EXISTS`). Driver location: update a **geohash‑based cache** (Redis Geo) asynchronously; staleness of 2 seconds is fine. |
| **Google Maps / Nearby Places** | AP | Eventual for place data; read‑your‑writes for reviews | Place data changes rarely; user reviews should appear immediately to the submitter. | Place data: stored in a **read‑only replica** (PostgreSQL) updated nightly. Reviews: use a **write‑through cache** for the user’s own reviews; for others, eventual via background sync. |
| **Food Delivery (DoorDash)** | CP for order assignment; AP for menus | Strong for order‑driver pairing; eventual for menus | Same as Uber. | Same as Uber for order assignment. Menus: cache in CDN with 5‑minute TTL; background worker updates from restaurant databases. |
| **Nearby Friends** | AP | Eventual + monotonic reads | Location updates are frequent; monotonic reads prevent teleporting. | Store each user’s location in a **geohash‑indexed Redis** with a version number (timestamp). Client sends last‑seen version; server returns only newer locations, ensuring monotonic progression. |

---

### 7.6 Pattern F – Financial & Critical Consistency

| Question | CAP Choice | Consistency Method | Why | Deep Dive / Implementation |
|----------|------------|--------------------|-----|----------------------------|
| **Payment Gateway (Stripe)** | CP | Strong + idempotency | Money cannot be lost or double‑charged. | Use an **idempotency key** stored in a database with a unique constraint. The payment workflow is a **state machine** (created → authorized → captured → settled). Use a **distributed transaction** (saga) with compensating actions. |
| **Wallet System** | CP | Strong with optimistic locking | Balance updates must be atomic. | Store balance in a relational DB with a **version column**. On update: `UPDATE wallet SET balance = balance - 10, version = version + 1 WHERE user_id = 123 AND version = old_version`. Retry on conflict. |
| **UPI-like System** | CP | Strong across banks (2PC or saga) | Real‑time payments require atomic debit/credit. | Use a **distributed transaction coordinator** (e.g., XA) or a **saga** with idempotent steps. Each bank exposes a `prepare`, `commit`, `rollback` API. For high scale, use **eventual consistency with reconciliation** (rare for UPI). |
| **Distributed Job Scheduler** | CP | Strong via leader election | Only one instance should own a job. | Use **etcd** or **ZooKeeper** to elect a leader. The leader stores job leases in the consensus store. On failover, a new leader reads the leases and reassigns. Use **fencing tokens** to prevent split‑brain. |

---

### 7.7 Pattern G – Large-Scale Processing & Infrastructure

| Question | CAP Choice | Consistency Method | Why | Deep Dive / Implementation |
|----------|------------|--------------------|-----|----------------------------|
| **Google Analytics** | AP | Eventual + exactly‑once | Duplicate counts are bad, but stale counts are fine. | Use Kafka with **idempotent producers**. Stream processor (Flink) uses **state checkpoints** and exactly‑once semantics. Write to a **druid** or **BigQuery** for querying. Deduplication by event ID. |
| **Clickstream Processing** | AP | Eventual + sessionization | Session windows tolerate out‑of‑order events. | Use **Flink** with event‑time processing and watermarks. Store sessions in a **windowed state** (e.g., RocksDB). Output to a **columnar store** (Parquet) for analytics. |
| **Distributed Logging (ELK)** | AP | Eventual (logs are append‑only) | Logs are immutable; search indexing can lag. | Log shippers (Filebeat) send to Kafka. Logstash consumes and indexes into Elasticsearch. Elasticsearch uses **near‑real‑time** refresh (1 second). For durability, use replicas. |
| **Metrics Monitoring (Prometheus)** | AP | Eventual for scrape data | Missing a few data points is acceptable. | Prometheus pulls metrics from targets every 15 seconds. It stores data locally in a **time‑series database** (TSDB). For high availability, run two Prometheus servers scraping the same targets; queries can deduplicate. |
| **Design Kafka-like System** | CP for partition leaders; AP for consumers | Strong for leadership (Raft); at‑least‑once delivery | Message ordering per partition requires a leader. | Use **Raft** for partition leader election. Replicate logs to followers (synchronous). Consumers track offsets; if a consumer fails, at‑least‑once delivery is achieved via idempotent processing. |

---

### 7.8 Pattern H – Collaborative & Infrastructure Utilities

| Question | CAP Choice | Consistency Method | Why | Deep Dive / Implementation |
|----------|------------|--------------------|-----|----------------------------|
| **Google Docs** | CP (within a session) | Strong eventual (CRDTs or OT) | Multiple users edit simultaneously; document must converge. | Use **CRDTs** (e.g., automerge or yjs) for each character/operation. Clients send operations to a **WebSocket server** that broadcasts and merges. No central lock; merging is deterministic. |
| **Figma / Collaborative Whiteboard** | CP | CRDTs with low latency | Real‑time drawing requires convergence without conflicts. | Similar to Google Docs, but with **spatial CRDTs** (e.g., RGA for vector graphics). Use **WebRTC** data channels for peer‑to‑peer sync or a central relay. |
| **Distributed Locking Service** | CP | Strong (Raft) | Locks must be linearizable. | Use **etcd** or **ZooKeeper** with ephemeral nodes and session heartbeats. Client acquires a lock by creating a sequential ephemeral node. Lock release happens on session expiry. |
| **Unique ID Generator (Snowflake)** | CP (worker coordination) | Strong for worker ID assignment; eventual for ID generation | Worker IDs must be unique. | Assign worker IDs via **etcd** (atomic compare‑and‑swap). Each worker generates IDs using Snowflake: timestamp + worker ID + sequence. No further coordination needed. |
| **Web Crawler** | AP | Eventual for URL frontier | Duplicate detection can be probabilistic. | Use a **Bloom filter** in front of a set of visited URLs. The frontier is a priority queue (Redis sorted set) with politeness delays. Strong consistency is not required; occasional duplicates are acceptable. |

---

*End of consolidated notes.*
