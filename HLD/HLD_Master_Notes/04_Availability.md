# Availability — Consolidated HLD Notes

---

## Table of Contents

- [1. Availability in HLD](#1-availability-in-hld)
  - [1.1 Core Principle: Redundancy + Failure Detection + Automatic Failover](#11-core-principle-redundancy--failure-detection--automatic-failover)
- [2. Availability Techniques & When to Use Them](#2-availability-techniques--when-to-use-them)
  - [2.1 Redundancy (Active-Passive)](#21-redundancy-active-passive)
  - [2.2 Redundancy (Active-Active)](#22-redundancy-active-active)
  - [2.3 Load Balancing with Health Checks](#23-load-balancing-with-health-checks)
  - [2.4 Failover & Leader Election](#24-failover--leader-election)
  - [2.5 Retries with Exponential Backoff & Jitter](#25-retries-with-exponential-backoff--jitter)
  - [2.6 Circuit Breaker](#26-circuit-breaker)
  - [2.7 Timeouts](#27-timeouts)
  - [2.8 Data Replication (Synchronous vs. Asynchronous)](#28-data-replication-synchronous-vs-asynchronous)
  - [2.9 Multi-AZ / Multi-Region Deployment](#29-multi-az--multi-region-deployment)
  - [2.10 Graceful Degradation & Feature Toggles](#210-graceful-degradation--feature-toggles)
  - [2.11 Database Quorum (for Distributed DBs)](#211-database-quorum-for-distributed-dbs)
  - [2.12 Decision Table – Scenario → Availability Technique](#212-decision-table---scenario--availability-technique)
  - [2.13 Common Anti-patterns for Availability](#213-common-anti-patterns-for-availability)
  - [2.14 Summary for HLD Interviews – Availability](#214-summary-for-hld-interviews---availability)
- [3. Availability Patterns by System Type](#3-availability-patterns-by-system-type)
  - [3.1 Summary: Availability Techniques by Pattern](#31-summary-availability-techniques-by-pattern)
  - [3.2 Pattern A – CRUD & Storage Systems (Availability Focus)](#32-pattern-a---crud--storage-systems-availability-focus)
  - [3.3 Pattern B – Feed / Timeline Systems (Availability Focus)](#33-pattern-b---feed--timeline-systems-availability-focus)
  - [3.4 Pattern C – Real-Time Systems (Availability Focus)](#34-pattern-c---real-time-systems-availability-focus)
  - [3.5 Pattern D – Search Systems (Availability Focus)](#35-pattern-d---search-systems-availability-focus)
  - [3.6 Pattern E – Geospatial & Location-Based Systems (Availability Focus)](#36-pattern-e---geospatial--location-based-systems-availability-focus)
  - [3.7 Pattern F – Financial & Critical Consistency Systems (Availability Focus)](#37-pattern-f---financial--critical-consistency-systems-availability-focus)
  - [3.8 Pattern G – Large-Scale Processing & Infrastructure (Availability Focus)](#38-pattern-g---large-scale-processing--infrastructure-availability-focus)
  - [3.9 Pattern H – Collaborative & Infrastructure Utilities (Availability Focus)](#39-pattern-h---collaborative--infrastructure-utilities-availability-focus)
---

## 1. Availability in HLD

**Availability** is the percentage of time a system is operational and accessible. It's measured in "nines" (99.9%, 99.99%, etc.). In HLD, you design to eliminate single points of failure (SPOF) and recover automatically from failures.

### 1.1 Core Principle: Redundancy + Failure Detection + Automatic Failover

The foundation of high availability is:
- **Redundancy** – having multiple copies of critical components (servers, databases, network links).
- **Failure Detection** – continuously monitoring the health of components (via health checks, heartbeats).
- **Automatic Failover** – when a failure is detected, automatically switching to a redundant component without manual intervention.

---

## 2. Availability Techniques & When to Use Them

### 2.1 Redundancy (Active-Passive)

**How it works:**  
A standby replica that takes over only when the primary fails. Resources are idle most of the time.

**Scenario:**  
- Stateful services where hot‑standby is acceptable  
- Databases with strong consistency requirements (e.g., financial transactions)  
- When you can tolerate a few seconds of failover time

**Example:**  
**PostgreSQL** with streaming replication. Primary handles all writes. Standby replicates data synchronously. If primary dies, a watchdog (e.g., Patroni) promotes standby in 5–10 seconds. Clients reconnect to the new primary.

**When to avoid:**  
- When you need zero downtime (failover still has a brief interruption)  
- When you want to utilize all hardware (active-passive wastes capacity)

---

### 2.2 Redundancy (Active-Active)

**How it works:**  
Multiple nodes handle traffic simultaneously. If one fails, others continue without interruption.

**Scenario:**  
- Stateless services (web servers, APIs)  
- Geo‑distributed systems needing local writes  
- High throughput requirements

**Example:**  
**Google Search** frontend – thousands of servers behind load balancers. Any server can handle any request. If 5% of servers crash, users see no impact – the load balancer stops sending traffic to dead nodes.

**Database example:**  
**Cassandra** or **DynamoDB** – writes go to multiple replicas. If one replica is down, the others still acknowledge the write (quorum). No single point of failure.

---

### 2.3 Load Balancing with Health Checks

**How it works:**  
Load balancer periodically pings backend servers (HTTP, TCP, or custom). Unhealthy servers are removed from the pool.

**Scenario:**  
- Any multi‑server architecture  
- Zero‑downtime deployments (drain connections before removing a node)  
- Partial failures (e.g., server is up but returning 500 errors)

**Example:**  
**AWS ALB** – checks `/health` endpoint every 5 seconds. If three consecutive failures, the server is marked unhealthy and traffic is redirected to healthy instances. When the server recovers, it's added back.

**Advanced scenario:**  
**Netflix** uses “health check” that also verifies the server can connect to dependent services (database, cache). If a server can’t reach the DB, it’s considered unhealthy even if its own process is running.

---

### 2.4 Failover & Leader Election

**How it works:**  
A distributed consensus algorithm (Raft, Paxos) elects a leader. If leader fails, followers elect a new leader automatically.

**Scenario:**  
- Distributed databases (etcd, ZooKeeper, Consul)  
- Coordination services  
- Any system requiring a single active controller

**Example:**  
**Kubernetes control plane** – etcd cluster with 3 or 5 nodes. If the leader etcd node goes down, remaining nodes elect a new leader in ~1 second. The API server continues to serve requests.

**Another example:**  
**Kafka** – each partition has one leader broker. If leader fails, a follower is elected as the new leader (controlled by ZooKeeper or KRaft). Producers and consumers automatically redirect.

---

### 2.5 Retries with Exponential Backoff & Jitter

**How it works:**  
When a request fails (network timeout, 5xx error), the client retries after a delay. Delay increases exponentially to avoid thundering herd.

**Scenario:**  
- Transient failures (network blip, service restart)  
- Calls to external APIs that occasionally throttle  
- Distributed systems where failures are expected

**Example:**  
**Stripe API** client:  
- First retry after 100ms  
- Second after 200ms  
- Third after 400ms  
- Max 5 retries, then fail.  
Adds jitter (± random ms) to prevent all clients retrying simultaneously.

**When not to use:**  
- Non‑idempotent operations without careful design (retrying a money transfer could double-spend – use idempotency keys instead).

---

### 2.6 Circuit Breaker

**How it works:**  
Wraps calls to a downstream service. Monitors failure rate. When failures exceed a threshold, the circuit “opens” – all calls fail fast without hitting the downstream service. After a timeout, the circuit “closes” partially to test if service recovered.

**Scenario:**  
- Preventing cascading failures in microservices  
- When a slow or failing downstream service can exhaust resources (thread pools, connections)  
- Degrading gracefully instead of hanging

**Example:**  
**Netflix Hystrix** (or Resilience4j):  
- Payment service calls fraud detection service. Fraud detection becomes slow (2 second response). Without circuit breaker, payment service threads get stuck → eventually whole payment system fails.  
- Circuit breaker trips after 50% failures in 10 seconds → opens for 30 seconds → all fraud checks are skipped (or return a safe default). After 30 seconds, one request is allowed to test recovery. If it succeeds, circuit closes.

**Real world:**  
**Amazon checkout** – if the recommendation service fails, the circuit breaker opens and the page simply doesn’t show “frequently bought together” – checkout still works.

---

### 2.7 Timeouts

**How it works:**  
Set a maximum wait time for any operation. If exceeded, fail fast.

**Scenario:**  
- Every network call (database, API, cache)  
- Preventing resource exhaustion (threads, connections)  
- Avoiding cascading failures

**Example:**  
**Twitter** API gateway sets:  
- DB query timeout: 100ms  
- Cache read timeout: 10ms  
- External social API call: 500ms  

If the cache takes >10ms, treat it as a miss (fail fast) and go to the database. This prevents a slow cache from killing performance.

---

### 2.8 Data Replication (Synchronous vs. Asynchronous)

| Type | How it works | Availability impact | Scenario |
|------|--------------|---------------------|----------|
| **Synchronous** | Write waits for confirmation from replica(s) before returning success | Lower availability (if replica down, write fails) | Banking, inventory (zero data loss) |
| **Asynchronous** | Write returns immediately; replica syncs later | Higher availability (replica down doesn’t block writes) | Social media feeds, logs (some data loss tolerable) |

**Example:**  
**Google Docs** – writes are synchronous to primary region, then asynchronously replicated to secondary region. If primary region fails, you might lose the last few seconds of edits – acceptable for a collaborative editor.  
**Bank transfer** – synchronous replication to two data centers. If one doesn’t acknowledge, the transaction aborts. No data loss, but lower availability during network partitions.

---

### 2.9 Multi-AZ / Multi-Region Deployment

**Scenario:**  
- Protecting against entire data center outage (power, network, natural disaster)  
- Regulatory requirements for disaster recovery  
- Global low latency for users

**Example:**  
**Dropbox** stores user data across three AWS availability zones (AZs) within a region. If one AZ goes offline, the other two still serve traffic. For disaster recovery, they also replicate to a different region (e.g., us-east-1 to eu-west-1).

**Active‑Active multi‑region:**  
**Netflix** runs in multiple AWS regions simultaneously. If us-east-1 fails, Route 53 health checks redirect traffic to us-west-2 within minutes. Users may see degraded quality (lower resolution) but the site stays up.

---

### 2.10 Graceful Degradation & Feature Toggles

**How it works:**  
When a non‑critical dependency fails, the system disables that feature but keeps core functionality running.

**Scenario:**  
- Systems with many optional features (recommendations, analytics, comments)  
- When you can tolerate reduced user experience over total outage

**Example:**  
**Airbnb** search page:  
- Core: search results from DB (must work)  
- Optional: “price forecast” from ML service. If ML service is slow or down, the page simply doesn’t show the forecast – search still works.  
- They use feature toggles to disable the ML call remotely without redeploying.

---

### 2.11 Database Quorum (for Distributed DBs)

**How it works:**  
With N replicas, a write needs acknowledgement from W replicas, a read from R replicas. Common choices:  
- **W = N, R = 1** (max consistency, lower availability)  
- **W = 1, R = 1** (max availability, possible stale reads)  
- **W = (N/2)+1, R = (N/2)+1** (balanced – can tolerate up to (N-1)/2 failures)

**Scenario:**  
- Distributed databases (Cassandra, DynamoDB, MongoDB)  
- You need to tune trade‑off between consistency and availability (CAP theorem)

**Example:**  
**Cassandra** with N=3, W=2, R=2.  
- If one replica is down, writes still succeed because two acknowledgements are possible.  
- Reads also succeed.  
- You lose one replica without downtime. If two replicas fail, writes fail (but reads may still work).

---

### 2.12 Decision Table – Scenario → Availability Technique

| Scenario | Technique | Example |
|----------|-----------|---------|
| One web server keeps crashing | Load balancer + active‑active redundancy | Any web app with ≥2 instances |
| Database server fails – need automatic failover | Active‑passive + leader election | PostgreSQL with Patroni |
| Downstream API is slow and makes your service hang | Circuit breaker + timeout | Netflix Hystrix |
| Entire data center loses power | Multi‑AZ deployment | AWS RDS Multi‑AZ |
| A single bad deployment could take down everything | Rolling updates + health checks | Kubernetes rolling update |
| You can tolerate 5 minutes of stale data but zero downtime | Asynchronous replication + read replicas | Social media feed |
| Financial transaction cannot lose a single write | Synchronous replication + quorum (W=N) | Bank ledger |
| User profile picture service is down – but login must work | Graceful degradation | Facebook login without profile pic |
| Network blip causes random request failures | Retries with exponential backoff | Stripe API client |

---

### 2.13 Common Anti-patterns for Availability

1. **No health checks** – Load balancer sends traffic to dead servers → partial outage.
2. **Shared state across regions** – Multi‑region active‑active becomes impossible.
3. **Too short timeouts** – Normal network variance causes false failures.
4. **Infinite retries** – Can overload a struggling system (use bounded retries).
5. **Single cache instance** – Cache failure kills database. Use cache cluster (Redis Sentinel or Cluster).

---

### 2.14 Summary for HLD Interviews – Availability

> *“To ensure high availability, I design with **redundancy at every tier**: stateless services behind a load balancer with health checks; databases with **leader election** and **synchronous replication** for critical data, asynchronous for less critical; **circuit breakers** to prevent cascading failures; **retries with backoff** for transient errors; **multi‑AZ deployment** to survive data center failures; and **graceful degradation** of non‑essential features. I also set **timeouts** on every network call and use **feature toggles** to disable problematic features instantly.”*

---

## 3. Availability Patterns by System Type

### 3.1 Summary: Availability Techniques by Pattern

| Pattern | System Type | Primary Availability Techniques | Typical Failure & Mitigation |
|---------|-------------|--------------------------------|------------------------------|
| **A: CRUD / Storage** | URL shortener, Pastebin, Bookstore, Ticket booking, Dropbox | Active‑passive DB failover, read replicas, load balancer health checks, multi‑AZ deployment | DB primary failure → automatic failover to standby; web server crash → LB redirects to healthy nodes |
| **B: Feed / Timeline** | Twitter, Instagram, Facebook, LinkedIn | Active‑active web servers, cache replication (Redis Cluster), Kafka replication, fan‑out retries | Cache node failure → replica takes over; Kafka broker failure → leader election |
| **C: Real‑Time** | WhatsApp, Slack, Live Comments, Game backend | WebSocket connection draining, chat server replication (consistent hashing), Kafka replication, state checkpointing | Chat server crash → clients reconnect to another server (state restored from Kafka) |
| **D: Search** | Google Search, YouTube Search, Amazon Search, Autocomplete | Index replication (primary/backup), query router with retries, CDN for static suggestions | Index node down → router retries to replica; CDN edge failure → fallback to origin |
| **E: Geospatial** | Uber, Google Maps, Food Delivery, Nearby Friends | Redis Sentinel (failover), Kafka replication, geohash index replication, driver location re‑broadcast | Redis master fail → Sentinel promotes replica; driver location stream replays from Kafka |
| **F: Financial** | Payment gateway, Wallet, UPI, Job scheduler | Synchronous replication (DB), leader election (Raft), idempotency cache replication, circuit breakers to external banks | DB node failure → synchronous replica takes over (zero data loss); external bank slow → circuit breaker opens |
| **G: Large‑Scale Processing** | Analytics, Clickstream, Logging, Metrics, Kafka‑like | Kafka partition replication, Flink job recovery (checkpoints), Elasticsearch replica shards, Prometheus high‑availability pairs | Flink task failure → recover from checkpoint; Kafka leader fail → ISR elects new leader |
| **H: Collaborative & Infrastructure** | Google Docs, Figma, Distributed locks, Snowflake, Web Crawler | WebSocket server redundancy (consistent hashing), etcd cluster (Raft), lock fencing tokens, crawler worker retries | etcd leader failure → new leader elected; WebSocket server down → clients reconnect |

---

### 3.2 Pattern A – CRUD & Storage Systems (Availability Focus)

| Question | Availability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------|-------------------|----------------------------|
| **URL Shortener** | Active‑passive DB (DynamoDB global tables or RDS Multi‑AZ), load balancer health checks, read replicas for redirects, auto‑scaling | Redirects must never fail (millions of embedded links). DB primary failure should not cause downtime. | Use RDS Multi‑AZ for automatic failover (<60s). Read replicas handle redirects during failover. LB health checks (HTTP /health) every 5s. |
| **Pastebin** | Object storage (S3) with 99.999999999% durability, CDN with origin failover, API servers behind LB | S3 is highly available by design. CDN edge caches even if origin is down. | S3 automatically replicates across AZs. CloudFront with multiple origins (primary + backup). API servers in auto‑scaling group behind ALB. |
| **Online Bookstore** | Read replicas for catalog (can serve stale during failover), inventory DB with synchronous replication, LB health checks | Catalog availability is critical for browsing; inventory can tolerate brief failover. | Catalog: read replicas in different AZs; if primary fails, promote replica. Inventory: PostgreSQL with synchronous streaming replication (2 replicas). |
| **Ticket Booking** | Active‑active booking service (stateless), DB with leader election (Raft), row‑level locks survive failover | Booking service must stay up during flash sales. DB failover must not lose seat reservations. | Booking servers behind LB. Use CockroachDB (Raft) or Yugabyte for automatic failover. Seat locks are durable across failover. |
| **Dropbox / Google Drive** | Metadata DB with multi‑region replication (active‑active), file chunks stored in S3 (11 nines), CDN for downloads | Metadata is critical; file chunks are durable by design. | Metadata: Cassandra with replication factor 3 across AZs. File chunks: S3 Cross‑Region Replication (CRR). CDN caches popular files. |

---

### 3.3 Pattern B – Feed / Timeline Systems (Availability Focus)

| Question | Availability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------|-------------------|----------------------------|
| **Twitter / X** | Active‑active web servers, Redis Cluster with replicas, Kafka replication (ISR), fan‑out retries | Tweets must be delivered even if some services fail. Users accept slightly stale feeds. | Redis Cluster: each master has replica; on failover, replica promotes. Kafka: `min.insync.replicas=2`. Fan‑out workers retry failed deliveries. |
| **Instagram Feed** | CDN for images (highly available), feed service with read replicas, cache replication (Memcached) | Images are served from CDN. Feed can be stale but not unavailable. | Memcached client with retry and failover to another node. Feed DB (Cassandra) with RF=3. CDN origin shield for high availability. |
| **Facebook News Feed** | Edge caching (CDN), pre‑computed feed stored in replicated cache (Memcached), compute pipelines with retries | News feed is pre‑computed; if cache fails, fallback to a slightly older version. | Memcached cluster with consistent hashing and node replication. Compute pipeline (Spark) writes to multiple cache nodes. |
| **LinkedIn Feed** | Pull‑based feed (no fan‑out failure), profile cache replication, read replicas for posts | Pull model is more available because it doesn't depend on pre‑computation. | Redis Cluster for profile cache. Post DB (sharded PostgreSQL) with read replicas. Feed aggregation times out after 500ms and returns partial results. |

---

### 3.4 Pattern C – Real-Time Systems (Availability Focus)

| Question | Availability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------|-------------------|----------------------------|
| **WhatsApp / Messenger** | WebSocket connection manager with consistent hashing and failover, Kafka replication, chat server state recovery | Chat servers crash – users must reconnect and not lose messages. | Use consistent hashing with virtual nodes. On server failure, clients reconnect to next server; state is reloaded from Kafka (persisted messages). |
| **Slack / Discord** | Channel history replicated (Cassandra), presence via Redis Sentinel, WebSocket connection draining | Channel history is critical; presence can be stale. | Cassandra with RF=3 across AZs. Redis Sentinel for presence failover. On deployment, drain connections gracefully (wait for in‑flight messages). |
| **Live Comments (YouTube Live)** | Kafka with replication (ISR), stream processor (Flink) with checkpointing, Redis Sentinel for comment cache | Comments must flow even if some Kafka brokers or Flink tasks fail. | Kafka: `min.insync.replicas=2`, `acks=all`. Flink checkpointing to S3 (every 10s). Redis Sentinel for comment cache failover. |
| **Multiplayer Game Backend** | Game room state replication (in‑memory with checkpoint to DB), consistent hashing for room assignment, UDP (no connection state) | Game room server crash – players reconnect to a new server, state restored from DB. | Assign rooms via consistent hashing. Periodic checkpoint to DynamoDB (every 5s). On crash, new server loads checkpoint. UDP allows quick reconnect. |

---

### 3.5 Pattern D – Search Systems (Availability Focus)

| Question | Availability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------|-------------------|----------------------------|
| **Google Search** | Index replication (primary/backup shards), query router with retries, CDN for static UI | Search must return results even if some index nodes are down. | Each index shard has 2 replicas. Query router tries primary, then replica on timeout/failure. Use a load balancer for index nodes. |
| **YouTube Search** | Similar to Google Search, plus video metadata cache replication (Redis Cluster) | Metadata cache failure should not break search. | Redis Cluster with replicas. On cache miss, fallback to database (slower but available). |
| **Amazon Product Search** | Inventory cache replication (Redis Sentinel), search index replication, fallback to stale inventory | Inventory cache failure – fallback to database (maybe slower but works). | Redis Sentinel for automatic failover. Search index in Elasticsearch with replica shards. |
| **Search Autocomplete** | Trie replication (read‑only copies), CDN for static prefix suggestions, fallback to default suggestions | Trie node failure – serve from replica or CDN. | Build trie and push to CDN as JSON. Client requests CDN first; fallback to API server. |

---

### 3.6 Pattern E – Geospatial & Location-Based Systems (Availability Focus)

| Question | Availability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------|-------------------|----------------------------|
| **Uber / Lyft** | Redis Sentinel for driver location cache, Kafka replication, ride matching service active‑active | Driver location updates must be available; stale location is acceptable for a few seconds. | Redis Sentinel (3 nodes). Kafka with RF=3. Ride matching service runs in multiple AZs with idempotent assignment (conditional write to DB). |
| **Google Maps / Nearby Places** | Place DB read replicas (multi‑AZ), map tiles on CDN, geocoding service with retries | Map tiles are static; place search can use stale data. | Place DB (PostgreSQL) with read replicas in each region. Map tiles on CloudFront. Geocoding calls retry 3 times. |
| **Food Delivery (DoorDash)** | Similar to Uber, plus restaurant menu cache replication (Redis Cluster) | Menu cache failure – fallback to DB (read replica). | Redis Cluster with replicas. Restaurant menu DB has read replicas. Order assignment uses same pattern as Uber. |
| **Nearby Friends** | Redis Geo with Sentinel, client‑side caching with fallback, location updates idempotent | Redis master fail – Sentinel promotes replica; client retries. | Redis Sentinel. Clients cache last known locations. On Redis failure, use cached data (stale but available). |

---

### 3.7 Pattern F – Financial & Critical Consistency Systems (Availability Focus)

| Question | Availability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------|-------------------|----------------------------|
| **Payment Gateway (Stripe)** | Synchronous replication (DB with 3 nodes), idempotency cache replication (Redis Cluster), circuit breakers to banks | Financial systems need both consistency and availability. Use synchronous replication to avoid data loss. | DB: PostgreSQL with synchronous replication to 2 standbys. Redis Cluster for idempotency keys. Circuit breaker to external banks (fail fast, retry later). |
| **Wallet System** | DB with leader election (Raft), balance cache replication (Redis Sentinel), optimistic locking retries | Wallet balance must be available and consistent. | Use etcd or CockroachDB for Raft. Redis Sentinel for balance cache. On write conflict, client retries. |
| **UPI-like System** | Saga orchestrator with retries and compensating transactions, Kafka replication, bank idempotency APIs | Cross‑bank transactions can retry; use idempotency to avoid double payment. | Kafka with `acks=all`. Saga orchestrator (active‑active with leader election). Bank APIs must be idempotent (idempotency key). |
| **Distributed Job Scheduler** | etcd cluster (Raft) for leader election, job queue in Kafka with replication, workers with idempotent processing | Scheduler leader failure – new leader elected via etcd; jobs continue. | etcd (3 or 5 nodes). Kafka with RF=3. Workers store last processed offset in etcd for recovery. |

---

### 3.8 Pattern G – Large-Scale Processing & Infrastructure (Availability Focus)

| Question | Availability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------|-------------------|----------------------------|
| **Google Analytics** | Kafka with replication, Flink with checkpointing (to S3), query layer with read replicas | Analytics pipeline can recover from failures using checkpoints. | Kafka RF=3. Flink checkpointing every minute to S3. Query layer (Druid) with replica nodes. |
| **Clickstream Processing** | Kafka partitions replicated, Flink job recovery, session state in RocksDB with backup | Session state is critical for accurate funnels; Flink recovers from checkpoint. | Flink checkpointing to HDFS/S3. RocksDB backup on failure. Kafka `min.insync.replicas=2`. |
| **Distributed Logging (ELK)** | Kafka buffer with replication, Elasticsearch replica shards, Logstash with persistent queues | Log loss is acceptable (eventual). But indexing should survive node failures. | Elasticsearch: each shard has replica. Logstash persistent queue (disk) to survive restart. Kafka RF=2. |
| **Metrics Monitoring (Prometheus)** | Prometheus high‑availability pairs (two servers scraping same targets), Thanos for query federation | If one Prometheus fails, the other has the same data (short retention). | Run two Prometheus servers with identical config. Thanos deduplicates on query. Long‑term storage in S3. |
| **Design Kafka-like System** | Partition replication (leader‑follower), ISR (in‑sync replicas), leader election via Raft, controller failover | Kafka‑like system must tolerate broker failures without data loss. | Each partition has N replicas (e.g., 3). Producer waits for `min.insync.replicas` acks. Controller (Raft) manages broker failover. |

---

### 3.9 Pattern H – Collaborative & Infrastructure Utilities (Availability Focus)

| Question | Availability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|------------------------|-------------------|----------------------------|
| **Google Docs** | WebSocket servers with consistent hashing and failover, CRDT state stored in replicated cache (Redis), document versioning | Document editing must survive server crash; CRDTs allow recovery. | Use consistent hashing for document assignment. Redis Cluster for document state. On server fail, new server loads state from Redis. |
| **Figma / Collaborative Whiteboard** | Similar to Google Docs, plus CDN for static assets, presence via Redis Sentinel | Whiteboard objects are many; CRDT operations are small. | Same pattern as Google Docs. CDN for fonts, templates. Redis Sentinel for presence (last seen). |
| **Distributed Locking Service** | etcd cluster (Raft) with 3 or 5 nodes, client retries with exponential backoff, fencing tokens | Locks must be available and consistent. etcd provides both. | etcd cluster across AZs. Client acquires lock via etcd `txn`. Fencing tokens prevent split‑brain. |
| **Unique ID Generator (Snowflake)** | Worker ID assignment via etcd (highly available), local ID generation (no coordination) | Worker ID assignment uses etcd; if etcd is down, workers can still generate IDs (use cached worker ID). | etcd cluster (3 nodes). Each worker caches its assigned ID. On etcd failure, continue with cached ID (risk of collision low). |
| **Web Crawler** | URL frontier in Redis Sentinel, Bloom filter in Redis with replicas, workers idempotent | Crawler can tolerate worker failures; URLs are re‑assigned. | Redis Sentinel for frontier. Bloom filter replicated. Workers store last crawled URL in DB; on failure, another worker picks up. |

---


---

*End of consolidated notes.*
