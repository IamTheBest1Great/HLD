# Complete High‑Level Design for a URL Shortening Service

## Table of Contents
1. [Introduction](#introduction)
2. [Requirements](#requirements)
   - [Functional Requirements](#functional-requirements)
   - [Non‑Functional Requirements](#nonfunctional-requirements)
3. [Traffic Estimation and Capacity Planning](#traffic-estimation-and-capacity-planning)
4. [API Design](#api-design)
5. [Database Design](#database-design)
6. [High‑Level Architecture](#highlevel-architecture)
7. [Detailed Component Design](#detailed-component-design)
   - [7.1 Short Code Generation – Key Generation Service (KGS)](#71-short-code-generation---key-generation-service-kgs)
   - [7.2 Caching Layer](#72-caching-layer)
   - [7.3 Redirection Flow](#73-redirection-flow)
   - [7.4 URL Creation Flow](#74-url-creation-flow)
   - [7.5 Analytics Pipeline](#75-analytics-pipeline)
   - [7.6 Handling Hot URLs](#76-handling-hot-urls)
   - [7.7 Bloom Filter for Invalid Requests](#77-bloom-filter-for-invalid-requests)
8. [Multi‑Region Deployment](#multiregion-deployment)
9. [Trade‑offs and Discussion](#tradeoffs-and-discussion)
10. [Failure Scenarios and Mitigations](#failure-scenarios-and-mitigations)
11. [Security Considerations](#security-considerations)
12. [Conclusion and Interview Summary](#conclusion-and-interview-summary)

---

## Introduction
A URL shortening service (like Bitly) converts long URLs into short, easy‑to‑share aliases. When a user clicks a short link, they are redirected to the original URL. The system must be highly available, low latency, and scalable to billions of redirects. This document presents a complete high‑level design covering requirements, estimations, APIs, database, architecture, component details, trade‑offs, and failure handling.

---

## Requirements

### Functional Requirements
- **Shorten URL**: Given a long URL, generate a unique, short alias (e.g., `https://short.url/abc123`).
- **Redirect**: When a user accesses the short URL, they are redirected to the original long URL (HTTP 301/302).
- **Custom Alias** (optional): Allow users to specify a custom short path (e.g., `https://short.url/my-link`).
- **Expiration** (optional): URLs may expire after a defined time (TTL).
- **Analytics** (optional): Track number of clicks, referrer, geographic location, device, etc.
- **User Accounts** (optional): Users can manage their links and view statistics.

### Non‑Functional Requirements
- **High Availability**: The service must be up 99.99% of the time; redirection should never fail.
- **Low Latency**: Redirection should happen in <100ms, ideally <50ms; URL creation <500ms.
- **Scalability**: Support 100 million new URLs per month and 10 billion redirects per month.
- **Durability**: Mappings should persist indefinitely (≥5 years) unless expired.
- **Collision‑free**: No two different long URLs can produce the same short code.
- **Security**: Prevent abuse (e.g., excessive creation, malicious URLs) via rate limiting and validation.

---

## Traffic Estimation and Capacity Planning

**Assumptions**  
- New URLs per month: 100 million  
- Read/write ratio: 100:1  
- Peak factor (general): 3× average traffic; viral spikes up to 10–20×  
- Record size: ~500 bytes (short code 7B + long URL 200B + metadata)  
- Retention: 5+ years permanent storage  
- Replication factor: 3× for durability  

**Calculations**  
- New URLs per day: 100M / 30 ≈ 3.33 million  
- New URLs per second (avg): 3.33M / 86,400 ≈ 38.5 writes/sec  
- Peak writes (3×): 115 writes/sec  
- Redirects per month: 10 billion  
- Redirects per day: 10B / 30 ≈ 333 million  
- Redirects per second (avg): 333M / 86,400 ≈ 3,850 reads/sec  
- Peak reads (3×): 11,550 reads/sec  
- Daily storage growth: 3.33M × 500 B = 1.67 GB/day  
- 5‑year storage (with replication): 1.67 GB × 365 × 5 × 3 ≈ 9.15 TB  
- Bandwidth (redirects): each redirect ~500 B; daily egress 166.5 GB, negligible.  

---

## API Design

**API Style**: REST, JSON, versioned (`/v1/`).

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/v1/shorten` | `POST` | Create a new short URL |
| `/{shortCode}` | `GET` | Redirect to original long URL |
| `/v1/analytics/{shortCode}` | `GET` | Retrieve click statistics (optional, requires auth) |

**POST /v1/shorten**  
Request:
```json
{
  "long_url": "https://example.com/very/long/path",
  "custom_alias": "my-link",   // optional
  "expiration_days": 30        // optional
}
```
Response (201 Created):
```json
{
  "short_url": "https://short.url/abc123",
  "short_code": "abc123",
  "long_url": "https://example.com/very/long/path",
  "created_at": "2025-03-08T10:37:00Z"
}
```

**GET /{shortCode}**  
- Returns HTTP 301/302 with `Location` header.  
- 404 if not found.

**GET /v1/analytics/{shortCode}**  
Returns aggregated click data.

**Authentication**: Public API – no authentication for core operations. Optional API keys for user accounts.

**Rate Limiting**: Per IP, 100 requests/hour for URL creation. Token bucket algorithm, stored in Redis. Returns 429 with `Retry-After`.

**Idempotency**: Optional `Idempotency-Key` header to prevent duplicate creation on retries.

---

## Database Design

**Data Model**  
Primary table `url_mappings`:
- `short_code` (string, partition key) – unique identifier  
- `long_url` (string)  
- `created_at` (timestamp)  
- `expiration` (timestamp, optional, used for TTL)  
- `user_id` (string, optional, for user accounts)  
- `click_count` (number, denormalized for fast analytics – optional)

**Database Choice**: **NoSQL** (DynamoDB or Cassandra)  
- **Why**: Simple key‑value lookups, high throughput, automatic horizontal scaling, built‑in replication, low latency.  
- **Alternative**: SQL with sharding is possible but adds complexity.

**Indexing and Sharding**  
- Primary index on `short_code`.  
- Global Secondary Index (GSI) on `user_id` to list user’s URLs (optional).  
- Shard key = `short_code` using consistent hashing (Cassandra) or DynamoDB auto‑partitioning.

**Consistency**  
- Redirects: eventual consistency (read from replicas, cheaper, faster).  
- URL creation: standard write durability (quorum).  
- Custom aliases: conditional write with strong consistency (e.g., `IF NOT EXISTS`).

**Backup and Recovery**  
- Point‑in‑time recovery (PITR) enabled (e.g., DynamoDB PITR).  
- Daily snapshots to S3 for long‑term retention.

**Analytics Storage**  
- Raw click events stored in S3 (Parquet).  
- Aggregated data in time‑series database (ClickHouse) for fast querying.

---

## High‑Level Architecture

**Component Diagram**
```
┌─────────────┐     ┌──────────────┐     ┌─────────────────┐
│   Clients   │────▶│   DNS (e.g., │────▶│   CDN (for      │
│ (Web/App)   │     │   Route53)   │     │   static assets)│
└─────────────┘     └──────────────┘     └─────────────────┘
        │                                           │
        │ (HTTP requests)                           │ (optional)
        ▼                                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                        Load Balancer (e.g., AWS ALB)            │
└─────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────┐
│                     Application Servers (Stateless)              │
└─────────────────────────────────────────────────────────────────┘
                                   │
           ┌───────────────────────┼───────────────────────┐
           │                       │                       │
           ▼                       ▼                       ▼
┌────────────────────┐    ┌────────────────────┐    ┌────────────────────┐
│   Cache (Redis)    │    │  Primary Database  │    │  Message Queue     │
│   - Store frequent │    │  (DynamoDB /       │    │  (Kafka / SQS)     │
│     short→long     │    │   Cassandra)       │    │   - Click events   │
└────────────────────┘    └────────────────────┘    └────────┬───────────┘
                                                             │
                                          ┌──────────────────▼──────────────────┐
                                          │        Analytics Workers            │
                                          │   - Aggregate and store in          │
                                          │     ClickHouse / BigQuery           │
                                          └─────────────────────────────────────┘
```

**Component Roles**  
- **DNS (Route53)**: Geo‑routing to nearest region.  
- **CDN**: Optional, for static assets; can also cache redirects for hot URLs (advanced).  
- **Load Balancer**: Distributes traffic, terminates SSL.  
- **Application Servers**: Stateless, handle logic, scaling horizontally.  
- **Cache (Redis)**: In‑memory key‑value store, LRU eviction, cache‑aside pattern.  
- **Primary Database**: Persistent storage for URL mappings.  
- **Message Queue (Kafka)**: Buffers click events for analytics.  
- **Analytics Workers**: Stream processors (Flink, Spark) that aggregate and store analytics data.

---



<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/76474247-cbb1-4832-a888-e4eff649cff5" />




## Detailed Component Design

### 7.1 Short Code Generation – Key Generation Service (KGS)
**Why KGS?**  
- Avoids database uniqueness checks on every write.  
- Pre‑generates keys in batches, eliminating per‑request collisions.  
- Allows high throughput.

**Algorithm**  
- Use Base62 characters (`[a-z][A-Z][0-9]`). 7‑character keys provide 62⁷ ≈ 3.5 trillion combinations.  
- Two common approaches:
  1. **Pre‑generated pool**: Background job generates random strings and stores them in a small `KeyDB` with a `used` flag. KGS hands out batches (e.g., 1000) to app servers.
  2. **Snowflake‑like + Base62**: Generate 64‑bit unique IDs (timestamp + machine ID + sequence) and encode them in Base62. This eliminates the need for a separate key database.

**KGS Workflow**  
1. App server requests a batch of keys from KGS.  
2. KGS marks those keys as used (atomically) and returns them.  
3. App server stores batch in local memory, assigns keys to new URLs.  
4. When batch is exhausted, requests another batch.  
5. If a server crashes, unused keys in its memory are lost – acceptable given the huge key space.

**High Availability**: KGS instances are replicated; use a distributed lock (ZooKeeper) to coordinate batch allocation.

### 7.2 Caching Layer
- **Technology**: Redis Cluster (multi‑node) with replicas for failover.  
- **Data**: Key = short code, value = long URL + metadata.  
- **Eviction**: LRU (Least Recently Used) – keeps hot URLs.  
- **Pattern**: Cache‑aside – read: check cache, if miss read DB and store; write: optional write‑through or let cache be populated on first read.  
- **TTL**: Set reasonable TTL (e.g., 1 hour) to avoid stale data, but can be longer for permanent links.

### 7.3 Redirection Flow
1. User requests `https://short.url/abc123`.  
2. DNS routes to nearest region load balancer.  
3. Load balancer forwards to an app server.  
4. App server:  
   - Checks Redis for key `abc123`.  
   - **Cache hit**: returns HTTP 302 (or 301) with `Location` header.  
   - **Cache miss**: queries database by `short_code`.  
     - If found: stores in Redis (with TTL), returns redirect.  
     - If not found: returns 404.  
5. **Asynchronous**: A click event (short code, timestamp, referrer, user‑agent, IP) is sent to Kafka.

### 7.4 URL Creation Flow
1. Client POSTs long URL (optionally with custom alias, expiration).  
2. App server validates URL format and checks against malware blacklist.  
3. If **custom alias**:  
   - Perform conditional write to DB (`INSERT IF NOT EXISTS`) to ensure uniqueness.  
   - If alias already exists, return 409 Conflict.  
4. If **no custom alias**:  
   - Obtain a short code from local batch (or request from KGS).  
5. Insert mapping into database (with expiration if set).  
6. Optionally pre‑warm cache with the new mapping.  
7. Return short URL to client.

### 7.5 Analytics Pipeline
- **Event**: Each redirect produces a lightweight message.  
- **Queue**: Kafka (or SQS) to decouple analytics from main path.  
- **Stream processing**: Consumers (Flink, Spark Streaming) aggregate counts per short code, per country, per device, per referrer.  
- **Storage**:  
  - Aggregated results stored in ClickHouse (or similar) for fast queries.  
  - Raw events archived in S3 (Parquet) for batch processing.  
- **API**: The `/analytics/{shortCode}` endpoint reads from ClickHouse.

### 7.6 Handling Hot URLs
- **CDN edge caching**: For extremely popular short URLs, configure CDN (CloudFront) to cache the HTTP redirect response. The edge returns redirect without hitting origin.  
- **Replicated cache**: Hot keys are replicated across multiple Redis nodes to balance load.  
- **Local cache**: Application servers maintain a small in‑memory LRU cache for the hottest keys, further reducing latency.

### 7.7 Bloom Filter for Invalid Requests
To avoid unnecessary cache and database lookups for non‑existent short codes, use a **Bloom filter** in front of the cache.  
- **Data structure**: Probabilistic set of all existing short codes.  
- **Operation**: On request, check filter:  
  - If “not present” → return 404 immediately.  
  - If “possibly present” → proceed to cache/db.  
- **False positives** possible but acceptable; **false negatives** impossible. Tune filter size and hash functions for desired accuracy.

---

## Multi‑Region Deployment

**Geo‑Routing**: Use GeoDNS (Route53) to route users to the nearest region (e.g., US‑East, US‑West, EU, Asia).  

**Replication Strategy**: **Active‑Active**  
- Each region accepts both reads and writes.  
- Data replicated asynchronously using DynamoDB Global Tables or Cassandra multi‑datacenter replication.  
- Conflict resolution: last‑write‑wins (LWW) using timestamps or version vectors – suitable for URL mappings (each short code is unique).  
- **Benefits**: Lowest latency worldwide, high availability.  
- **Challenges**: Cross‑region replication lag (eventual consistency acceptable for redirects).

**Alternative** (Active‑Passive): One primary region for writes, others read‑only replicas. Simpler but higher write latency for remote users.

---

## Trade‑offs and Discussion

| Decision | Trade‑off |
|----------|-----------|
| **NoSQL vs SQL** | NoSQL gives horizontal scalability and low latency for key‑value lookups, but sacrifices complex queries. We add a GSI for user‑based queries. |
| **KGS vs hashing** | KGS guarantees no collisions and high throughput, but introduces an extra service. Hashing is simpler but may require collision resolution and DB checks. |
| **Redis vs local cache** | Redis is a shared cache, consistent across servers, but adds network hop. Local cache is faster but can become inconsistent. We use Redis as primary cache, possibly L1 local cache. |
| **301 vs 302 redirect** | 301 (permanent) is cached by browsers, reducing load but making updates difficult. 302 (temporary) is not cached, allowing analytics on every click. We default to 302 for trackability. |
| **Strong vs eventual consistency** | For redirects, eventual consistency is fine; a newly created URL might take seconds to propagate. For custom aliases, we need strong consistency (conditional write). |

**Scaling to 100×**  
- **Bottlenecks**: Database reads, cache size, network bandwidth.  
- **Solutions**:  
  - Push redirects to CDN edge (CloudFront + Lambda@Edge) – absorbs most read traffic.  
  - Scale Redis cluster horizontally (more nodes).  
  - Use database auto‑partitioning (DynamoDB) to handle writes.  
  - Analytics pipeline scales with Kafka partitions and stream processors.

---

## Failure Scenarios and Mitigations

| Failure | Mitigation |
|---------|------------|
| **Single component** | Redundancy at all layers – multi‑AZ load balancers, multiple app instances, Redis replicas, database replication. |
| **Database node down** | Multi‑AZ replication; automatic failover. DynamoDB multi‑AZ, Cassandra quorum. |
| **Cache cluster failure** | Fallback to database reads. Cache rebuilt gradually. Redis replicas for failover. |
| **Application server failure** | Stateless; auto‑scaling group replaces. Health checks remove unhealthy nodes. |
| **KGS failure** | KGS replicated; app servers keep local batches; they can request from another KGS instance. |
| **Data corruption / accidental deletion** | Point‑in‑time recovery (PITR) + daily snapshots. Optionally soft deletes. |
| **DDoS / traffic spike** | Rate limiting, CDN, AWS Shield, auto‑scaling, queueing for analytics. |
| **Malicious URL creation** | URL validation, malware blacklists, rate limiting. |
| **Regional outage** | DNS failover to another region; data already replicated (active‑active). |
| **Network partition** | AP design: serve from local region with eventual consistency. Custom alias writes may fail if they cannot reach consensus. |

---

## Security Considerations
- **Rate limiting**: 100 requests/hour per IP for creation, implemented via Redis token bucket.  
- **URL validation**: Ensure valid format; optionally check against Google Safe Browsing API.  
- **HTTPS**: Enforce TLS for all endpoints.  
- **Authentication**: Optional API keys for user accounts (if added).  
- **Abuse prevention**: Log suspicious activity, use WAF (Web Application Firewall).  
- **CORS**: Restrict as needed.

---

## Conclusion and Interview Summary

### Key Points
- **Short code generation**: Key Generation Service (KGS) with Base62 – pre‑generated batches for high throughput.  
- **Database**: NoSQL (DynamoDB/Cassandra) for simple key‑value lookups, horizontal scaling, high availability.  
- **Caching**: Redis cluster, cache‑aside, LRU eviction to handle high read QPS.  
- **Analytics**: Asynchronous pipeline via Kafka and ClickHouse, decoupled from critical path.  
- **Scalability**: Multi‑region active‑active deployment, CDN edge caching for hot URLs, Bloom filter for invalid requests.  
- **Reliability**: Redundancy at every layer, auto‑scaling, multi‑AZ, automated failover, point‑in‑time recovery.  
- **Trade‑offs**: Prioritised availability, low latency, and scalability over strong consistency, with explicit handling for uniqueness where required.

### Perfect Interview Answer (30‑second version)
> "A URL shortener stores a mapping between a short code and a long URL. When a user creates a short URL, we generate a unique ID using a distributed key generation service (KGS) and encode it in Base62. The mapping is stored in a NoSQL database like DynamoDB for scalability. For redirection, we first check a Redis cache; on a miss, we query the database and populate the cache. To handle billions of redirects, we use CDN edge caching for popular links, a Bloom filter to reject invalid requests, and an asynchronous analytics pipeline with Kafka. The system is deployed across multiple regions with active‑active replication and geo‑routing for low latency and high availability."

This comprehensive design addresses all functional and non‑functional requirements, ensures scalability, reliability, and security, and is ready for a top‑level system design interview.
Ah, skipping straight to the next section! I like the momentum. 

To close the loop on that KGS concurrency question before we move on: in a real interview, the magic words the interviewer is listening for are **Atomic Updates** or **Distributed Locking**. You would want to mention that the KGS uses a database transaction (like an atomic `UPDATE ... RETURNING` or `SELECT FOR UPDATE`) or a distributed lock (like Redis Redlock or ZooKeeper) to ensure that once a batch of keys is claimed by Server A, it is instantly locked and cannot be handed to Server B.

***

Now, let's move to **Step 7: Failure Scenarios & Trade-offs**. 

In senior interviews, the "happy path" (which you just designed perfectly) is only half the interview. The rest is about how you handle things when they catch on fire.

**Your Task:**
Based on the architecture you drew, tell me how your system handles these two specific scenarios:

1.  **Cache Outage:** Your entire Redis Cache cluster suddenly crashes and will take 5 minutes to reboot. What happens to your system, and how do you mitigate the impact so the service doesn't completely die?
2.  **The Viral Spike:** Elon Musk tweets one of your short links, causing a sudden **100x traffic spike** for that *single* URL. How does your architecture handle this massive, targeted load?

That is completely okay! These are the "make or break" questions in senior interviews, and it is exactly why we practice them. It's actually much better to say "I don't know" in an interview than to guess wildly. 

Let's look at the solutions based on the brilliant reference notes you shared earlier. 

### 1. The Cache Outage
**The Problem:** If Redis completely dies, all 3,800 reads per second will suddenly bypass the cache and hit your database directly. 
**The Solution:**
* **Fallback to Database:** The application servers are programmed with a "cache-aside" pattern. If the cache is unreachable, they automatically query the NoSQL database instead. 
* **Mitigation:** To survive this, your NoSQL database (like DynamoDB) must be configured to auto-scale its read capacity so it doesn't crash under the sudden load. You also mitigate this risk architecturally by using a **Redis Cluster with Replicas**, so if the primary cache node dies, a backup takes over immediately without the whole system going down.

### 2. The Viral Spike (Hot Keys)
**The Problem:** If Elon Musk tweets a link, millions of people click it at the exact same second. Even a powerful Redis cluster can crash if a million requests try to read the *exact same key* simultaneously (this is known as a "Hot Key" problem).
**The Solution:**
* **CDN Edge Caching:** You mentioned a CDN in your architecture drawing! For incredibly viral links, we can configure the CDN to cache the HTTP 301 Redirect response directly at the edge. The user's browser hits the CDN, gets the redirect, and the request *never even reaches your servers or database*. 
* **Local L1 Cache:** Alternatively, you can add a tiny, fast in-memory cache (like an LRU cache) directly inside the Application Server's RAM. It only holds the top 100 most active URLs, absorbing the viral spike before it hits the network.

***

We have officially covered Requirements, Capacity, APIs, Database, Architecture, and Bottlenecks. You have built a highly scalable, robust URL shortener!

To wrap up this mock interview, interviewers often ask for a brief final summary to see how well you communicate technical concepts concisely. 

**Your Final Task:**
Can you give me your "Elevator Pitch" summary of this system? Imagine you have 30 to 60 seconds to explain the high-level architecture we just built from end to end. Type out what you would say!
