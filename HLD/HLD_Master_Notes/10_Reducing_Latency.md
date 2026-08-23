# Reduce Latency — Consolidated HLD Notes

## Table of Contents

- [1. Latency Fundamentals](#1-latency-fundamentals)
  - [1.1 What Is Latency?](#11-what-is-latency)
    - [1.1.1 Response Time](#111-response-time)
    - [1.1.2 Network Latency](#112-network-latency)
    - [1.1.3 Disk I/O Latency](#113-disk-io-latency)
    - [1.1.4 Processing Latency](#114-processing-latency)
    - [1.1.5 Queueing Latency](#115-queueing-latency)
  - [1.2 Why Low Latency Matters](#12-why-low-latency-matters)
    - [1.2.1 Better User Experience](#121-better-user-experience)
    - [1.2.2 Real-Time Responsiveness](#122-real-time-responsiveness)
    - [1.2.3 Higher Throughput](#123-higher-throughput)
    - [1.2.4 Reduced Timeouts](#124-reduced-timeouts)
    - [1.2.5 Business Impact](#125-business-impact)
  - [1.3 Sources of Latency](#13-sources-of-latency)
    - [1.3.1 Network Hops](#131-network-hops)
    - [1.3.2 Slow Database Queries](#132-slow-database-queries)
    - [1.3.3 Disk Access](#133-disk-access)
    - [1.3.4 Serialization Overhead](#134-serialization-overhead)
    - [1.3.5 Remote Service Calls](#135-remote-service-calls)
    - [1.3.6 Lock Contention](#136-lock-contention)
- [2. Core Latency Reduction Techniques](#2-core-latency-reduction-techniques)
  - [2.1 Caching](#21-caching)
    - [2.1.1 Why Caching Reduces Latency](#211-why-caching-reduces-latency)
    - [2.1.2 Browser Cache](#212-browser-cache)
    - [2.1.3 CDN Cache](#213-cdn-cache)
    - [2.1.4 Application Cache](#214-application-cache)
    - [2.1.5 Database Cache](#215-database-cache)
    - [2.1.6 Tradeoffs & Staleness](#216-tradeoffs--staleness)
  - [2.2 Content Delivery Networks (CDN)](#22-content-delivery-networks-cdn)
    - [2.2.1 Edge Servers](#221-edge-servers)
    - [2.2.2 Geographic Proximity](#222-geographic-proximity)
    - [2.2.3 Static Asset Delivery](#223-static-asset-delivery)
    - [2.2.4 Media Streaming](#224-media-streaming)
    - [2.2.5 Dynamic Content Acceleration](#225-dynamic-content-acceleration)
    - [2.2.6 Tradeoffs](#226-tradeoffs)
  - [2.3 Database Indexing & Query Optimization](#23-database-indexing--query-optimization)
    - [2.3.1 Indexing](#231-indexing)
    - [2.3.2 Query Plans](#232-query-plans)
    - [2.3.3 Avoiding Full Table Scans](#233-avoiding-full-table-scans)
    - [2.3.4 Pagination Optimization](#234-pagination-optimization)
    - [2.3.5 Read Replicas](#235-read-replicas)
    - [2.3.6 Tradeoffs](#236-tradeoffs)
  - [2.4 Asynchronous Processing](#24-asynchronous-processing)
    - [2.4.1 Background Jobs](#241-background-jobs)
    - [2.4.2 Message Queues](#242-message-queues)
    - [2.4.3 Event-Driven Processing](#243-event-driven-processing)
    - [2.4.4 User Perceived Latency Reduction](#244-user-perceived-latency-reduction)
    - [2.4.5 Tradeoffs](#245-tradeoffs)
  - [2.5 Load Balancing & Horizontal Scaling](#25-load-balancing--horizontal-scaling)
    - [2.5.1 Traffic Distribution](#251-traffic-distribution)
    - [2.5.2 Reducing Server Overload](#252-reducing-server-overload)
    - [2.5.3 Auto-Scaling](#253-auto-scaling)
    - [2.5.4 Global Load Balancing](#254-global-load-balancing)
    - [2.5.5 Tradeoffs](#255-tradeoffs)
  - [2.6 Data Partitioning (Sharding)](#26-data-partitioning-sharding)
    - [2.6.1 Horizontal Partitioning](#261-horizontal-partitioning)
    - [2.6.2 Reducing Query Scope](#262-reducing-query-scope)
    - [2.6.3 Parallel Query Execution](#263-parallel-query-execution)
    - [2.6.4 Geo-Sharding](#264-geo-sharding)
    - [2.6.5 Tradeoffs](#265-tradeoffs)
  - [2.7 Minimizing Network Hops](#27-minimizing-network-hops)
    - [2.7.1 Service Co-Location](#271-service-co-location)
    - [2.7.2 Edge Computing](#272-edge-computing)
    - [2.7.3 Reducing Cross-Region Calls](#273-reducing-cross-region-calls)
    - [2.7.4 API Gateway Optimization](#274-api-gateway-optimization)
    - [2.7.5 Tradeoffs](#275-tradeoffs)
  - [2.8 Parallel & Concurrent Processing](#28-parallel--concurrent-processing)
    - [2.8.1 Parallel API Calls](#281-parallel-api-calls)
    - [2.8.2 Concurrent Workers](#282-concurrent-workers)
    - [2.8.3 Fan-Out/Fan-In](#283-fan-outfan-in)
    - [2.8.4 Async I/O](#284-async-io)
    - [2.8.5 Tradeoffs](#285-tradeoffs)
  - [2.9 Prefetching & Predictive Loading](#29-prefetching--predictive-loading)
    - [2.9.1 Browser Prefetching](#291-browser-prefetching)
    - [2.9.2 Predictive Data Loading](#292-predictive-data-loading)
    - [2.9.3 Feed Precomputation](#293-feed-precomputation)
    - [2.9.4 Recommendation Prefetching](#294-recommendation-prefetching)
    - [2.9.5 Tradeoffs](#295-tradeoffs)
  - [2.10 Data Compression](#210-data-compression)
    - [2.10.1 Gzip / Brotli Compression](#2101-gzip--brotli-compression)
    - [2.10.2 Reducing Payload Size](#2102-reducing-payload-size)
    - [2.10.3 Compression vs CPU Tradeoff](#2103-compression-vs-cpu-tradeoff)
    - [2.10.4 Media Compression](#2104-media-compression)
- [3. Advanced Latency Optimization](#3-advanced-latency-optimization)
  - [3.1 Protocol Optimization](#31-protocol-optimization)
    - [3.1.1 HTTP/2 Multiplexing](#311-http2-multiplexing)
    - [3.1.2 HTTP/3 & QUIC](#312-http3--quic)
    - [3.1.3 gRPC](#313-grpc)
    - [3.1.4 Binary Serialization](#314-binary-serialization)
  - [3.2 Connection Optimization](#32-connection-optimization)
    - [3.2.1 Connection Pooling](#321-connection-pooling)
    - [3.2.2 Persistent Connections](#322-persistent-connections)
    - [3.2.3 TCP Keep-Alive](#323-tcp-keep-alive)
    - [3.2.4 TLS Session Reuse](#324-tls-session-reuse)
  - [3.3 Storage Optimization](#33-storage-optimization)
    - [3.3.1 SSDs vs HDDs](#331-ssds-vs-hdds)
    - [3.3.2 In-Memory Databases](#332-in-memory-databases)
    - [3.3.3 WAL Optimization](#333-wal-optimization)
    - [3.3.4 Hot Data Placement](#334-hot-data-placement)
  - [3.4 Compute Optimization](#34-compute-optimization)
    - [3.4.1 Efficient Algorithms](#341-efficient-algorithms)
    - [3.4.2 CPU Optimization](#342-cpu-optimization)
    - [3.4.3 Batch Processing](#343-batch-processing)
    - [3.4.4 SIMD / Parallelization](#344-simd--parallelization)
  - [3.5 Tail Latency Reduction](#35-tail-latency-reduction)
    - [3.5.1 Hedged Requests](#351-hedged-requests)
    - [3.5.2 Request Timeouts](#352-request-timeouts)
    - [3.5.3 Retry with Jitter](#353-retry-with-jitter)
    - [3.5.4 Adaptive Load Shedding](#354-adaptive-load-shedding)
- [4. Latency vs Tradeoffs](#4-latency-vs-tradeoffs)
  - [4.1 Latency vs Consistency](#41-latency-vs-consistency)
    - [4.1.1 Eventual Consistency](#411-eventual-consistency)
    - [4.1.2 Read Replicas](#412-read-replicas)
    - [4.1.3 Cross-Region Tradeoffs](#413-cross-region-tradeoffs)
  - [4.2 Latency vs Durability](#42-latency-vs-durability)
    - [4.2.1 Sync vs Async Writes](#421-sync-vs-async-writes)
    - [4.2.2 WAL Overhead](#422-wal-overhead)
    - [4.2.3 Replication Costs](#423-replication-costs)
  - [4.3 Latency vs Cost](#43-latency-vs-cost)
    - [4.3.1 CDN Costs](#431-cdn-costs)
    - [4.3.2 Memory Costs](#432-memory-costs)
    - [4.3.3 Multi-Region Infrastructure](#433-multi-region-infrastructure)
  - [4.4 Latency vs Complexity](#44-latency-vs-complexity)
    - [4.4.1 Distributed Systems Complexity](#441-distributed-systems-complexity)
    - [4.4.2 Cache Invalidation Complexity](#442-cache-invalidation-complexity)
    - [4.4.3 Operational Overhead](#443-operational-overhead)
- [5. Decision Framework](#5-decision-framework)
  - [5.1 Choosing the Right Optimization Technique](#51-choosing-the-right-optimization-technique)
    - [5.1.1 Read-Heavy Systems](#511-read-heavy-systems)
    - [5.1.2 Write-Heavy Systems](#512-write-heavy-systems)
    - [5.1.3 Real-Time Systems](#513-real-time-systems)
    - [5.1.4 Media Systems](#514-media-systems)
    - [5.1.5 Financial Systems](#515-financial-systems)
  - [5.2 Matching Techniques to Product Types](#52-matching-techniques-to-product-types)
    - [5.2.1 CRUD Applications](#521-crud-applications)
    - [5.2.2 Social Media](#522-social-media)
    - [5.2.3 Streaming Platforms](#523-streaming-platforms)
    - [5.2.4 Search Engines](#524-search-engines)
    - [5.2.5 Ride-Sharing Systems](#525-ride-sharing-systems)
  - [5.3 Quick Selection Guide](#53-quick-selection-guide)
    - [5.3.1 High Read Latency](#531-high-read-latency)
    - [5.3.2 Slow API Responses](#532-slow-api-responses)
    - [5.3.3 Database Bottlenecks](#533-database-bottlenecks)
    - [5.3.4 Global User Base](#534-global-user-base)
    - [5.3.5 Real-Time Requirements](#535-real-time-requirements)
- [6. Latency Reduction Patterns by System Type](#6-latency-reduction-patterns-by-system-type)
  - [6.1 Summary: Latency Reduction Techniques by Pattern](#61-summary-latency-reduction-techniques-by-pattern)
  - [6.2 Pattern A – CRUD & Storage Systems (Latency Reduction)](#62-pattern-a---crud--storage-systems-latency-reduction)
  - [6.3 Pattern B – Feed / Timeline Systems (Latency Reduction)](#63-pattern-b---feed--timeline-systems-latency-reduction)
  - [6.4 Pattern C – Real-Time Systems (Latency Reduction)](#64-pattern-c---real-time-systems-latency-reduction)
  - [6.5 Pattern D – Search Systems (Latency Reduction)](#65-pattern-d---search-systems-latency-reduction)
  - [6.6 Pattern E – Geospatial & Location-Based Systems (Latency Reduction)](#66-pattern-e---geospatial--location-based-systems-latency-reduction)
  - [6.7 Pattern F – Financial & Critical Consistency Systems (Latency Reduction)](#67-pattern-f---financial--critical-consistency-systems-latency-reduction)
  - [6.8 Pattern G – Large-Scale Processing & Infrastructure (Latency Reduction)](#68-pattern-g---large-scale-processing--infrastructure-latency-reduction)
  - [6.9 Pattern H – Collaborative & Infrastructure Utilities (Latency Reduction)](#69-pattern-h---collaborative--infrastructure-utilities-latency-reduction)
- [7. Common Latency Anti-Patterns](#7-common-latency-anti-patterns)
  - [7.1 Chatty Microservices](#71-chatty-microservices)
  - [7.2 Missing Cache Layers](#72-missing-cache-layers)
  - [7.3 Cross-Region Synchronous Calls](#73-cross-region-synchronous-calls)
  - [7.4 N+1 Database Queries](#74-n1-database-queries)
  - [7.5 Blocking I/O Everywhere](#75-blocking-io-everywhere)
  - [7.6 Large Payload Transfers](#76-large-payload-transfers)
  - [7.7 No CDN for Global Users](#77-no-cdn-for-global-users)
  - [7.8 Serial API Calls Instead of Parallelism](#78-serial-api-calls-instead-of-parallelism)
- [8. Interview-Oriented HLD Notes](#8-interview-oriented-hld-notes)
  - [8.1 Key Tradeoffs](#81-key-tradeoffs)
    - [8.1.1 Latency vs Consistency](#811-latency-vs-consistency)
    - [8.1.2 Latency vs Cost](#812-latency-vs-cost)
    - [8.1.3 Latency vs Durability](#813-latency-vs-durability)
    - [8.1.4 Simplicity vs Performance](#814-simplicity-vs-performance)
  - [8.2 Common Interview Questions](#82-common-interview-questions)
    - [8.2.1 How Would You Reduce API Latency?](#821-how-would-you-reduce-api-latency)
    - [8.2.2 Why Use CDN?](#822-why-use-cdn)
    - [8.2.3 How Does Caching Improve Performance?](#823-how-does-caching-improve-performance)
    - [8.2.4 How to Reduce Database Latency?](#824-how-to-reduce-database-latency)
    - [8.2.5 Why Use Async Processing?](#825-why-use-async-processing)
  - [8.3 Quick Revision Material](#83-quick-revision-material)
    - [8.3.1 Latency Reduction Cheat Sheet](#831-latency-reduction-cheat-sheet)
    - [8.3.2 Optimization Decision Matrix](#832-optimization-decision-matrix)
    - [8.3.3 Key Interview Sentences](#833-key-interview-sentences)
    - [8.3.4 Self-Test Questions](#834-self-test-questions)

---

## 1. Latency Fundamentals

### 1.1 What Is Latency?

Latency is the time it takes for a system to respond to a request. It’s the total delay from the moment a user clicks a button until the result is fully displayed. In distributed systems, latency is measured in milliseconds (ms) and can be broken down into several components.

#### 1.1.1 Response Time
The end‑to‑end time perceived by the client, including network round‑trip time (RTT), server processing, and client rendering.

#### 1.1.2 Network Latency
The time for data to travel across the network, influenced by physical distance, number of hops, and congestion. Inside a data center: ~0.1–1 ms; cross‑continent: 50–200 ms.

#### 1.1.3 Disk I/O Latency
The delay to read/write persistent storage. SSDs: ~0.1 ms; HDDs: ~5–10 ms. Disk I/O is often the slowest component in a server.

#### 1.1.4 Processing Latency
Time spent by the CPU performing business logic, serialization/deserialization, and data transformations.

#### 1.1.5 Queueing Latency
When requests are buffered because the system is overloaded or a resource is contended (e.g., database connection pool, thread pool).

### 1.2 Why Low Latency Matters

#### 1.2.1 Better User Experience
Users expect instant feedback. Studies show that a 100 ms delay can reduce conversion rates; a 1‑second delay can lead to user abandonment.

#### 1.2.2 Real-Time Responsiveness
Applications like multiplayer games, live trading, and video conferencing require sub‑100 ms latency to function.

#### 1.2.3 Higher Throughput
Low per‑request latency allows more requests to be served per second with the same resources.

#### 1.2.4 Reduced Timeouts
Shorter response times reduce the chance of hitting client or server timeouts, avoiding retries and cascading failures.

#### 1.2.5 Business Impact
For e‑commerce, lower latency translates directly to higher revenue. For ad‑tech, faster response means more ads served.

### 1.3 Sources of Latency

#### 1.3.1 Network Hops
Each network hop (router, switch, load balancer) adds ~0.5–2 ms. Minimizing hops and using direct connections reduces latency.

#### 1.3.2 Slow Database Queries
Queries that scan entire tables, lack indexes, or involve many joins can take seconds.

#### 1.3.3 Disk Access
Reading from disk instead of memory adds orders of magnitude more delay.

#### 1.3.4 Serialization Overhead
Converting data to/from JSON, XML, or binary formats consumes CPU and adds latency, especially for large payloads.

#### 1.3.5 Remote Service Calls
Microservices that chain calls add cumulative network latency and risk of timeouts.

#### 1.3.6 Lock Contention
Waiting for database row locks or distributed locks blocks processing and increases latency under high concurrency.

---

## 2. Core Latency Reduction Techniques

### 2.1 Caching

#### 2.1.1 Why Caching Reduces Latency
Caching stores frequently accessed data in a fast storage layer (memory). Instead of fetching from disk or a remote database, the data is served in microseconds.

#### 2.1.2 Browser Cache
Stores static assets (CSS, JS, images) on the client. Controlled via `Cache‑Control` headers. Reduces network transfers to zero for repeat visits.

#### 2.1.3 CDN Cache
Content Delivery Networks cache content at edge locations close to users. Static assets can be served in <10 ms globally.

#### 2.1.4 Application Cache
In‑memory caches like Redis or Memcached store query results, user sessions, or computed values. Typical latency: <1 ms.

#### 2.1.5 Database Cache
Database buffer pools (InnoDB, PostgreSQL) cache frequently accessed data pages in memory, avoiding disk reads.

#### 2.1.6 Tradeoffs & Staleness
Caching introduces the risk of serving stale data. Mitigations: TTL (time‑to‑live), write‑through, write‑invalidate, and event‑based purge.

---

### 2.2 Content Delivery Networks (CDN)

#### 2.2.1 Edge Servers
CDNs deploy thousands of servers globally. Content is cached at the edge, physically closer to users.

#### 2.2.2 Geographic Proximity
Reducing the physical distance cuts network latency dramatically: from 200 ms (cross‑ocean) to 5–20 ms (local edge).

#### 2.2.3 Static Asset Delivery
Images, CSS, JavaScript, and fonts are cached at the edge with long TTLs. This offloads the origin and speeds up page loads.

#### 2.2.4 Media Streaming
Video and audio segments are distributed via CDN edges, enabling smooth playback for a global audience.

#### 2.2.5 Dynamic Content Acceleration
CDNs can also cache dynamic but non‑personalized content (e.g., API responses for top headlines) for short TTLs, reducing origin load.

#### 2.2.6 Tradeoffs
- Cache invalidation requires purge APIs or versioned URLs.
- Cost: CDN egress is billed per GB.
- First request may be slow (cache miss) unless pre‑warmed.

---

### 2.3 Database Indexing & Query Optimization

#### 2.3.1 Indexing
Creating B‑tree, hash, or full‑text indexes on columns used in WHERE, JOIN, and ORDER BY avoids full table scans. Lookups become O(log n) instead of O(n).

#### 2.3.2 Query Plans
Use `EXPLAIN` / `EXPLAIN ANALYZE` to understand how the database executes a query. Identify sequential scans and missing indexes.

#### 2.3.3 Avoiding Full Table Scans
Add covering indexes that include all columns needed by a query, so the database never touches the table.

#### 2.3.4 Pagination Optimization
Replace `OFFSET`‑based pagination with keyset pagination (`WHERE id > last_id ORDER BY id LIMIT n`) to maintain constant performance.

#### 2.3.5 Read Replicas
Offload read‑only queries to replica databases, reducing load on the primary and improving query latency.

#### 2.3.6 Tradeoffs
- Indexes slow down writes (INSERT, UPDATE, DELETE) and consume disk space.
- Read replicas introduce replication lag, causing stale reads.

---

### 2.4 Asynchronous Processing

#### 2.4.1 Background Jobs
Move non‑critical work (sending emails, generating reports) to background workers so the main request returns quickly.

#### 2.4.2 Message Queues
Using Kafka, RabbitMQ, or SQS decouples producers and consumers, allowing tasks to be processed asynchronously without blocking the user.

#### 2.4.3 Event-Driven Processing
Services react to events asynchronously. This eliminates synchronous chains of calls, reducing perceived latency.

#### 2.4.4 User Perceived Latency Reduction
The user gets an immediate response (e.g., "Your order has been placed") while processing continues in the background.

#### 2.4.5 Tradeoffs
- Eventual consistency: the user may not see the result immediately.
- Requires idempotent workers and dead‑letter queues for reliability.

---

### 2.5 Load Balancing & Horizontal Scaling

#### 2.5.1 Traffic Distribution
A load balancer spreads incoming requests across multiple servers, preventing any single server from becoming a bottleneck.

#### 2.5.2 Reducing Server Overload
By distributing load, each server handles fewer requests, lowering processing and queueing latency.

#### 2.5.3 Auto-Scaling
Automatically adding servers when traffic spikes maintains low latency under variable load.

#### 2.5.4 Global Load Balancing
DNS‑based routing (Route 53, Cloudflare) directs users to the nearest healthy region, minimizing network latency.

#### 2.5.5 Tradeoffs
- Sticky sessions may be required for stateful apps, causing uneven load.
- Additional cost of extra servers.

---

### 2.6 Data Partitioning (Sharding)

#### 2.6.1 Horizontal Partitioning
Splitting a large table across multiple servers by a shard key (e.g., user_id) reduces the amount of data scanned per query.

#### 2.6.2 Reducing Query Scope
Queries that include the shard key are routed to a single shard, dramatically reducing I/O and CPU.

#### 2.6.3 Parallel Query Execution
Distributed databases (Cassandra, Elasticsearch) execute queries in parallel across shards, reducing response time.

#### 2.6.4 Geo-Sharding
Placing data in the region where users are located minimizes cross‑region latency and complies with data residency laws.

#### 2.6.5 Tradeoffs
- Cross‑shard queries and joins are complex or impossible.
- Re‑sharding data is expensive and operationally heavy.

---

### 2.7 Minimizing Network Hops

#### 2.7.1 Service Co-Location
Deploy dependent services in the same data center or availability zone to minimize RTT.

#### 2.7.2 Edge Computing
Run lightweight logic at CDN edge nodes (Lambda@Edge, Cloudflare Workers) to handle requests without hitting the origin.

#### 2.7.3 Reducing Cross-Region Calls
Replicate data to multiple regions so services can read locally rather than making remote calls.

#### 2.7.4 API Gateway Optimization
An API Gateway can aggregate multiple backend calls into a single client request, reducing chattiness.

#### 2.7.5 Tradeoffs
- Co‑location can limit fault tolerance (if the zone fails).
- Edge computing adds complexity and limited runtime environment.

---

### 2.8 Parallel & Concurrent Processing

#### 2.8.1 Parallel API Calls
Instead of calling services sequentially, call them concurrently and wait for all results. Total latency = max(individual latencies) instead of sum.

#### 2.8.2 Concurrent Workers
Use thread pools or async frameworks (Node.js, Vert.x) to handle multiple I/O operations simultaneously.

#### 2.8.3 Fan-Out/Fan-In
A single request is fanned out to multiple workers for parallel processing; results are then merged (fan‑in).

#### 2.8.4 Async I/O
Non‑blocking I/O allows a single thread to handle many connections, reducing context‑switching and memory overhead.

#### 2.8.5 Tradeoffs
- Coordination overhead.
- Not all tasks are parallelizable; dependencies must be managed.

---

### 2.9 Prefetching & Predictive Loading

#### 2.9.1 Browser Prefetching
HTML `<link rel="prefetch">` or `<link rel="preload">` instruct the browser to fetch resources before they are needed.

#### 2.9.2 Predictive Data Loading
Apps anticipate user actions (e.g., scrolling, swiping) and load data in advance, hiding network latency.

#### 2.9.3 Feed Precomputation
Social networks pre‑compute user feeds offline and store them in cache, so reads are instant.

#### 2.9.4 Recommendation Prefetching
E‑commerce sites pre‑fetch product recommendations while the user browses, reducing perceived latency.

#### 2.9.5 Tradeoffs
- Wasted bandwidth if predictions are wrong.
- Additional server load from pre‑fetching.

---

### 2.10 Data Compression

#### 2.10.1 Gzip / Brotli Compression
HTTP compression reduces the size of text‑based responses (JSON, HTML, CSS) by 60–80%, cutting transfer time.

#### 2.10.2 Reducing Payload Size
Smaller payloads mean fewer packets, faster transmission, especially on slow mobile networks.

#### 2.10.3 Compression vs CPU Tradeoff
Compression adds CPU overhead. Brotli provides better compression than gzip but at higher CPU cost; use gzip for dynamic content and Brotli for static assets.

#### 2.10.4 Media Compression
Video codecs (H.264, AV1) and image formats (WebP, AVIF) reduce file sizes by orders of magnitude, critical for streaming and image‑heavy sites.

---

## 3. Advanced Latency Optimization

### 3.1 Protocol Optimization

#### 3.1.1 HTTP/2 Multiplexing
Multiple requests can be sent over a single TCP connection, eliminating head‑of‑line blocking and reducing connection overhead.

#### 3.1.2 HTTP/3 & QUIC
Built on UDP, QUIC eliminates TCP handshake latency for new connections and improves performance over lossy networks.

#### 3.1.3 gRPC
Uses HTTP/2 and Protocol Buffers for efficient binary serialization, multiplexed streams, and low‑latency inter‑service communication.

#### 3.1.4 Binary Serialization
Protocol Buffers, Avro, and MessagePack encode data more compactly than JSON/XML, reducing payload size and parse time.

### 3.2 Connection Optimization

#### 3.2.1 Connection Pooling
Reusing pre‑established database or HTTP connections avoids the TCP handshake and TLS negotiation overhead per request.

#### 3.2.2 Persistent Connections
Keep‑Alive headers and connection reuse reduce latency for subsequent requests to the same server.

#### 3.2.3 TCP Keep-Alive
Periodic keep‑alive packets prevent intermediate firewalls from closing idle connections, avoiding re‑connection delays.

#### 3.2.4 TLS Session Reuse
Session tickets or session IDs allow clients to resume a previous TLS session, skipping the full handshake (saving 1–2 RTTs).

### 3.3 Storage Optimization

#### 3.3.1 SSDs vs HDDs
NVMe SSDs provide sub‑millisecond access times vs. ~5–10 ms for HDDs. Critical for databases and I/O‑heavy workloads.

#### 3.3.2 In-Memory Databases
Redis, Memcached, and SAP HANA keep entire datasets in RAM, achieving microsecond read/write latencies.

#### 3.3.3 WAL Optimization
Write‑Ahead Logs (WAL) should be placed on fast, dedicated SSDs to minimize commit latency for databases.

#### 3.3.4 Hot Data Placement
Store frequently accessed data on the fastest storage tier (e.g., SSD) and migrate cold data to cheaper, slower storage.

### 3.4 Compute Optimization

#### 3.4.1 Efficient Algorithms
Choosing O(log n) or O(1) algorithms over O(n²) reduces CPU time and latency.

#### 3.4.2 CPU Optimization
Using native compilation (C++, Rust) or JIT (Java) for hot paths, and leveraging SIMD instructions for data‑parallel operations.

#### 3.4.3 Batch Processing
Processing multiple items in one operation (batch inserts, bulk API calls) amortizes overhead and reduces per‑item latency.

#### 3.4.4 SIMD / Parallelization
Single Instruction Multiple Data (SIMD) accelerates operations on vectors, used in databases and machine learning inference to reduce latency.

### 3.5 Tail Latency Reduction

#### 3.5.1 Hedged Requests
Send the same request to multiple replicas and use the first response. Cancels the others, masking slow nodes.

#### 3.5.2 Request Timeouts
Set aggressive timeouts to avoid waiting indefinitely for a slow or stuck service. Fail fast and fallback.

#### 3.5.3 Retry with Jitter
Retry transient failures with exponential backoff and random jitter to avoid thundering herd and reduce tail latency.

#### 3.5.4 Adaptive Load Shedding
When the system is overloaded, proactively shed a fraction of requests to keep latency low for the remaining ones.

---

## 4. Latency vs Tradeoffs

### 4.1 Latency vs Consistency

#### 4.1.1 Eventual Consistency
Caching and asynchronous replication deliver low latency but allow temporary inconsistencies. Acceptable for social feeds and product recommendations.

#### 4.1.2 Read Replicas
Reading from replicas reduces primary load and latency, but replication lag means data may be stale.

#### 4.1.3 Cross-Region Tradeoffs
Synchronous cross‑region replication adds latency but provides strong consistency. Asynchronous replication offers low latency but risk of data loss.

### 4.2 Latency vs Durability

#### 4.2.1 Sync vs Async Writes
Synchronous disk writes (fsync) and replication guarantee durability but increase write latency. Async writes are faster but can lose data on crash.

#### 4.2.2 WAL Overhead
Write‑Ahead Logging ensures durability but adds I/O latency. Group commit (batching WAL flushes) balances latency and durability.

#### 4.2.3 Replication Costs
Synchronous replication to multiple nodes adds latency. Reducing replication factor or using erasure coding reduces latency but may compromise durability.

### 4.3 Latency vs Cost

#### 4.3.1 CDN Costs
Serving content from CDNs reduces latency but adds egress and request costs. Optimize by caching only hot content and using appropriate TTLs.

#### 4.3.2 Memory Costs
In‑memory caches and databases provide microsecond latency but are more expensive per GB than disk storage.

#### 4.3.3 Multi-Region Infrastructure
Deploying globally reduces latency for users but multiplies infrastructure and replication costs.

### 4.4 Latency vs Complexity

#### 4.4.1 Distributed Systems Complexity
Sharding, replication, and global load balancing add operational complexity and debugging difficulty.

#### 4.4.2 Cache Invalidation Complexity
Keeping caches fresh requires sophisticated invalidation strategies (event‑driven, versioning), increasing system complexity.

#### 4.4.3 Operational Overhead
Techniques like hedged requests, adaptive load shedding, and chaos engineering require advanced monitoring and automation.

---

## 5. Decision Framework

### 5.1 Choosing the Right Optimization Technique

#### 5.1.1 Read-Heavy Systems
Primary: caching (CDN, Redis, Memcached), read replicas, CDN. Secondary: database indexing, pre‑computation.

#### 5.1.2 Write-Heavy Systems
Primary: asynchronous processing, message queues, sharding. Secondary: write‑behind caching, batch writes.

#### 5.1.3 Real-Time Systems
Primary: WebSocket/UDP, in‑memory state, edge servers, protocol buffers. Secondary: connection pooling, timeout tuning.

#### 5.1.4 Media Systems
Primary: CDN, chunked streaming, prefetching, compression. Secondary: adaptive bitrate, edge caching.

#### 5.1.5 Financial Systems
Primary: in‑memory caches (with care for consistency), optimistic locking, local read replicas. Secondary: fast consensus (Raft), batch processing.

### 5.2 Matching Techniques to Product Types

#### 5.2.1 CRUD Applications
Example: URL shortener. Techniques: Redis cache, CDN for redirects, read replicas, connection pooling.

#### 5.2.2 Social Media
Example: Twitter. Techniques: pre‑computed feeds, CDN for media, fan‑out on write, edge caching.

#### 5.2.3 Streaming Platforms
Example: Netflix. Techniques: CDN, adaptive streaming, prefetching, parallel chunk downloads.

#### 5.2.4 Search Engines
Example: Google. Techniques: query result caching, in‑memory inverted indexes, parallel index queries, CDN for UI.

#### 5.2.5 Ride-Sharing Systems
Example: Uber. Techniques: Redis Geo, geohash sharding, WebSocket for location, edge routing.

### 5.3 Quick Selection Guide

#### 5.3.1 High Read Latency
→ Add caching (Redis, CDN), use read replicas, create covering indexes.

#### 5.3.2 Slow API Responses
→ Switch to async processing, parallelize downstream calls, use gRPC or HTTP/2, enable gzip compression.

#### 5.3.3 Database Bottlenecks
→ Index hot queries, shard the database, denormalize, use connection pooling.

#### 5.3.4 Global User Base
→ Deploy CDN, use global load balancing, replicate data to multiple regions, employ edge computing.

#### 5.3.5 Real-Time Requirements
→ Use WebSocket/UDP, keep state in memory, co‑locate services, reduce serialization overhead.

---

## 6. Latency Reduction Patterns by System Type

### 6.1 Summary: Latency Reduction Techniques by Pattern

| Pattern | System Type | Primary Latency Reduction Techniques | Target Latency | Key Trade‑off |
|---------|-------------|--------------------------------------|----------------|---------------|
| **A: CRUD / Storage** | URL shortener, Pastebin, Bookstore, Ticket booking, Dropbox | Redis cache (cache‑aside), CDN for static assets, read replicas, connection pooling, geo‑routing | Redirect <10ms; page load <200ms | Cache staleness vs. freshness |
| **B: Feed / Timeline** | Twitter, Instagram, Facebook, LinkedIn | Pre‑computed timelines (Redis sorted sets), CDN for images, fan‑out (push for active users), edge caching | Feed load <200ms | Write amplification vs. read latency |
| **C: Real‑Time** | WhatsApp, Slack, Live Comments, Game backend | WebSocket (persistent connection), in‑memory state (Redis), UDP for games, geo‑distributed servers, protocol buffers | Message delivery <100ms; game state <50ms | Consistency vs. latency |
| **D: Search** | Google Search, YouTube Search, Amazon Search, Autocomplete | Query result caching, inverted index in memory, CDN for suggestions, parallel query execution, prefix trie | Search <200ms; autocomplete <50ms | Index freshness vs. query speed |
| **E: Geospatial** | Uber, Google Maps, Food Delivery, Nearby Friends | Redis Geo (in‑memory), geohash indexing, CDN for map tiles, edge location for driver matching, UDP for location updates | Driver match <500ms; map tile <100ms | Accuracy vs. speed |
| **F: Financial** | Payment gateway, Wallet, UPI, Job scheduler | Idempotency cache (Redis), in‑memory balance cache, optimistic locking (no blocking), local read replicas | Payment <1s; balance read <10ms | Consistency vs. latency (strong wins) |
| **G: Large‑Scale Processing** | Analytics, Clickstream, Logging, Metrics, Kafka‑like | Stream processing (in‑memory), columnar storage (fast aggregations), time‑based partitioning, pre‑aggregation | Real‑time dashboard <500ms | Accuracy vs. speed (approximations OK) |
| **H: Collaborative & Infrastructure** | Google Docs, Figma, Distributed locks, Snowflake, Web Crawler | CRDTs (no coordination), local generation (Snowflake), WebSocket for real‑time, Bloom filter (fast membership) | Document update <50ms; lock acquire <10ms | Coordination overhead vs. latency |

---

### 6.2 Pattern A – CRUD & Storage Systems (Latency Reduction)

| Question | Latency Reduction Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------------|-------------------|----------------------------|
| **URL Shortener** | **Redis cache** (cache‑aside, TTL 24h); **CDN** for redirects; **Geo‑routing** (Route53 latency‑based); **Connection pooling** (HTTP keep‑alive) | Redirects must be <10ms. Cache eliminates DB round‑trip. | On redirect: check Redis (1ms); on miss, read DynamoDB (5ms), write to Redis. Use AWS Global Accelerator or CloudFront for edge caching. Keep‑alive reduces TCP handshake overhead. |
| **Pastebin** | **CDN** for paste content (long TTL); **Redis** for metadata; **Edge caching** for popular pastes | Paste reads are static; CDN serves from edge (5‑20ms vs origin 100ms). | Paste content stored in S3 with CloudFront. Metadata in Redis (TTL 7 days). For very popular pastes, cache at CDN edge. |
| **Online Bookstore** | **Redis catalog cache** (product details); **Read replicas** for search; **CDN** for images; **Query result caching** for search | Catalog reads are frequent; DB queries can be slow (50ms). | Product cache: TTL 5 min, invalidate on price change. Search results cache: keyed by query hash, TTL 1 min. Images via CDN. Use read replica for browse queries. |
| **Ticket Booking** | **Redis seat lock cache** (short TTL); **In‑memory event metadata**; **Optimistic locking** (no blocking reads); **Connection pooling** | Seat availability must be fast (<50ms). Locks in Redis avoid DB round‑trip. | Seat lock: `SETEX seat:event:row:num 300 user_id` (1ms). Event metadata in Redis hash. Use PostgreSQL with connection pool (HikariCP). Optimistic locking avoids `SELECT FOR UPDATE` overhead. |
| **Dropbox / Google Drive** | **CDN for file downloads** (chunked); **Redis directory cache**; **Delta sync** (only changes); **Compression** | Large files need edge caching. Directory listings are frequent. | CDN caches file chunks. Redis: `dir:{user_id}:{path}` TTL=5min. Delta sync (rsync‑like) reduces upload/download size. Compression (gzip) reduces transfer time. |

---

### 6.3 Pattern B – Feed / Timeline Systems (Latency Reduction)

| Question | Latency Reduction Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------------|-------------------|----------------------------|
| **Twitter / X** | **Pre‑computed timeline in Redis** (sorted set); **Push fan‑out for active users**; **Edge caching** for popular tweets; **Connection pooling** | Feed must load <200ms. Pre‑computation avoids per‑read aggregation. | For each user, store tweet IDs in Redis sorted set (score = timestamp). On tweet, fan‑out to followers' caches. Use push for active users (last 7 days), pull for inactive. CDN caches tweet media. |
| **Instagram Feed** | **Redis sorted set for feed**; **CDN for images**; **Lazy loading** (infinite scroll); **Thumbnail generation** (pre‑resized) | Images are large; lazy loading reduces initial load. | Feed IDs from Redis. Images served via CDN with multiple resolutions. Pre‑generate thumbnails on upload. Client loads images lazily as user scrolls. |
| **Facebook News Feed** | **Pre‑computed ranked feed in Memcached**; **Edge caching** (Akamai); **Incremental updates** (only new stories); **Client‑side caching** (browser storage) | Ranking is expensive; pre‑compute offline. | Memcached stores feed for top N stories (key = user_id). Incremental updates: push only new stories to cache. Client caches feed in localStorage with version token. |
| **LinkedIn Feed** | **Pull‑based aggregation** (on read, query last N posts from followed users); **Profile cache** (Redis); **Read replicas** for posts | Pull model avoids fan‑out latency; profile cache is fast. | On feed request, query up to 100 followed users' last 10 posts from Redis cache (profile cache). Use parallel queries (async). Cache posts individually (LRU). |

---

### 6.4 Pattern C – Real-Time Systems (Latency Reduction)

| Question | Latency Reduction Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------------|-------------------|----------------------------|
| **WhatsApp / Messenger** | **WebSocket persistent connection** (no handshake per message); **In‑memory recent message cache** (Redis list); **Geo‑distributed servers**; **Protocol buffers** (binary serialization) | Message delivery <100ms. WebSocket avoids HTTP overhead. | WebSocket server in each region. Redis: recent 50 messages per conversation (list). Use Protobuf instead of JSON (smaller, faster). Client‑side ack for reliability. |
| **Slack / Discord** | **WebSocket for real‑time**; **Redis for presence** (TTL short); **In‑memory unread counts**; **Edge CDN for files** | Presence updates need low latency (<50ms). | WebSocket server sharded by channel. Presence in Redis with TTL=30s. Unread counts in Redis hash (incremented on new message). Files via CDN. |
| **Live Comments (YouTube Live)** | **Redis sorted set for recent comments**; **WebSocket for broadcast**; **Client‑side comment buffer**; **UDP for high‑volume** (optional) | Live comments need <200ms from send to display. | Redis: comments per video sorted by timestamp, limit to 500. WebSocket servers subscribe to Redis Pub/Sub. Client buffers comments and renders in batches. UDP for very high scale (lossy but fast). |
| **Multiplayer Game Backend** | **UDP (no TCP overhead)**; **In‑memory game state (Redis)**; **Client‑side prediction**; **Server reconciliation** | Game state needs <50ms. UDP avoids retransmission delays. | UDP for player actions. Redis hash for game state. Client predicts movement; server sends corrections. Use delta compression (send only changes). |

---

### 6.5 Pattern D – Search Systems (Latency Reduction)

| Question | Latency Reduction Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------------|-------------------|----------------------------|
| **Google Search** | **Inverted index in memory** (SSD); **Query result cache** (Redis); **Parallel query execution** (fan‑out to shards); **Tiered caching** (L1: Redis, L2: CDN) | Search must return <200ms. In‑memory index is fast. | Index shards loaded in memory on each node. Query router fans out to all shards in parallel, merges results. Cache top 1M queries in Redis (TTL 5 min). CDN caches static UI. |
| **YouTube Search** | **Elasticsearch with SSD**; **Metadata cache (Redis)**; **Prefix caching** for autocomplete; **Edge CDN for thumbnails** | Video metadata is smaller than web index. | Elasticsearch with SSD and `index.refresh_interval=1s`. Redis cache for video metadata (title, views). Autocomplete trie in Redis. Thumbnails via CDN. |
| **Amazon Product Search** | **Inventory cache (Redis)** for real‑time availability; **Product metadata in Elasticsearch**; **Query result caching**; **Parallel shard queries** | Inventory check must be fast (<10ms). | Product search: Elasticsearch with replica shards. Inventory: Redis with TTL=2s, updated on purchase. Search query: fetch results from Elasticsearch, then enrich with inventory from Redis (parallel). |
| **Search Autocomplete** | **Trie in Redis** (or in‑memory); **CDN for static suggestions**; **Client‑side caching**; **Debouncing** (client) | Autocomplete must respond <50ms per keystroke. | Build trie hourly, push to CDN as JSON. Client fetches and caches in localStorage. Client debounces input (wait 100ms before sending request). Redis for real‑time trending prefixes. |

---

### 6.6 Pattern E – Geospatial & Location-Based Systems (Latency Reduction)

| Question | Latency Reduction Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------------|-------------------|----------------------------|
| **Uber / Lyft** | **Redis Geo** (in‑memory, geohash); **Geo‑distributed Redis clusters**; **WebSocket for driver location**; **UDP for high‑frequency updates** | Driver matching must be <500ms. Redis Geo is microsecond. | Driver locations in Redis Geo (per city). `GEORADIUS` query for nearby drivers (1‑2ms). WebSocket for driver location updates. UDP for location streaming (optional). |
| **Google Maps / Nearby Places** | **CDN for map tiles** (pre‑rendered); **Redis for place search cache**; **Geohash indexing**; **Edge routing** (Route53) | Map tiles load <100ms from edge. | Map tiles pre‑rendered and cached on CDN (long TTL). Place search: Redis with geohash key. Route53 latency‑based routing to nearest region. |
| **Food Delivery (DoorDash)** | **Redis Geo for drivers**; **Restaurant menu cache (Redis)**; **Edge CDN for images**; **Parallel search** (drivers + restaurants) | Driver matching <500ms; menu load <100ms. | Same as Uber for drivers. Restaurant menus in Redis hash (TTL=15 min). Search runs driver query and restaurant query in parallel. |
| **Nearby Friends** | **Redis Geo for user locations**; **WebSocket for real‑time updates**; **Client‑side cache** (last known locations); **Throttled updates** (1 per 5s) | Location refresh <100ms; WebSocket pushes changes. | Redis Geo stores current location. WebSocket server pushes location changes to friends (only when location changes significantly). Client caches friend locations and updates incrementally. |

---

### 6.7 Pattern F – Financial & Critical Consistency Systems (Latency Reduction)

| Question | Latency Reduction Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------------|-------------------|----------------------------|
| **Payment Gateway (Stripe)** | **Idempotency cache (Redis)**; **In‑memory merchant config**; **Connection pooling**; **Async processing for non‑critical steps** | Idempotency check must be <5ms. Payment processing can be async. | Idempotency key in Redis (TTL=24h). Merchant config cached in local memory (refresh every minute). Use connection pool for DB and external APIs. Return `202 Accepted` for non‑critical steps (email, analytics). |
| **Wallet System** | **Balance cache (Redis)** with CAS; **Optimistic locking** (no blocking); **Local read replicas**; **Batch writes** | Balance read must be <10ms; writes can be slightly slower. | Balance in Redis (read‑through). Updates use `WATCH` + `MULTI` (CAS). Optimistic locking avoids row locks. Local read replica for balance history. Batch multiple balance updates in one transaction. |
| **UPI-like System** | **Idempotency cache (Redis)**; **Async saga orchestration** (Kafka); **Local bank API caching**; **Circuit breaker with fast fail** | Cross‑bank payments target <1s. Idempotency is critical. | Redis for idempotency keys (TTL=24h). Saga steps via Kafka (async). Cache bank routing info locally (TTL=1 hour). Circuit breaker fails fast (5ms) if bank is slow. |
| **Distributed Job Scheduler** | **Redis for job queue** (list); **Leader local cache**; **Batch job fetch**; **Pre‑warmed workers** | Job pickup must be <50ms. | Redis list for pending jobs (`LPOP`). Leader caches job definitions locally. Workers fetch 10 jobs at once (batch). Pre‑warm workers (keep alive) to avoid cold start. |

---

### 6.8 Pattern G – Large-Scale Processing & Infrastructure (Latency Reduction)

| Question | Latency Reduction Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------------|-------------------|----------------------------|
| **Google Analytics** | **Real‑time counters in Redis**; **Columnar storage for queries** (BigQuery); **Pre‑aggregated tables** (materialized views); **Query result caching** | Dashboard must load <500ms. Pre‑aggregation reduces scan time. | Real‑time counts: Redis `INCR`. Hourly batch jobs pre‑aggregate to BigQuery tables (e.g., `dashboards`). Query results cached in Memcached (TTL=1 min). |
| **Clickstream Processing** | **Stream processing in memory** (Flink with RocksDB); **Windowed aggregations**; **Redis for session cache**; **Micro‑batching** | Real‑time funnels need <1s latency. | Flink with event‑time processing, state in RocksDB (memory‑mapped). Session cache in Redis (TTL=30 min). Micro‑batching (100ms windows) balances latency and throughput. |
| **Distributed Logging (ELK)** | **Kafka for buffering** (avoid Logstash blocking); **Elasticsearch with SSD**; **Hot‑warm architecture**; **Query result caching** | Log search <500ms for recent logs. | Kafka decouples producers from consumers. Elasticsearch hot nodes (NVMe SSD) for last 7 days. Query caching enabled (`index.requests.cache.enable`). Use `timeout=30s` for slow queries. |
| **Metrics Monitoring (Prometheus)** | **Prometheus local TSDB** (in‑memory for recent); **Scrape interval tuned** (15s); **Recording rules** (pre‑computed); **Thanos query caching** | Dashboard <1s; alerting <30s. | Prometheus keeps 2 hours in memory. Recording rules pre‑compute expensive queries (e.g., `rate`). Thanos query cache (Redis). Scrape interval = 15s for critical metrics, 60s for others. |
| **Design Kafka-like System** | **Page cache (OS)** for log segments; **Zero‑copy** (sendfile); **In‑memory index** (offset → position); **Batched writes** (acks=1) | End‑to‑end latency <5ms (if `acks=1`). | OS page cache caches hot log segments. `sendfile()` transfers data kernel‑space. In‑memory index for offset lookup. Batched writes (`linger.ms=5`) reduce overhead. |

---

### 6.9 Pattern H – Collaborative & Infrastructure Utilities (Latency Reduction)

| Question | Latency Reduction Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-----------------------------|-------------------|----------------------------|
| **Google Docs** | **CRDTs (no coordination)**; **WebSocket persistent connection**; **Incremental updates** (diffs); **Client‑side operation queue** | Typing latency <50ms. CRDTs avoid lock coordination. | CRDT operations sent via WebSocket (binary). Server broadcasts to other clients. Client queues operations during reconnect. Use differential sync (send only changes). |
| **Figma / Collaborative Whiteboard** | **WebSocket for real‑time**; **CRDT for objects**; **In‑memory state (Redis)**; **Delta compression**; **Client‑side rendering cache** | Object updates <100ms. | CRDT per object stored in Redis hash. WebSocket broadcasts deltas (only changed fields). Client caches rendered layers (Canvas) and re‑renders only changed objects. |
| **Distributed Locking Service** | **etcd with local cache** (watch); **Client‑side lock re‑use**; **Lease TTL** (no explicit unlock) | Lock acquire <10ms. | etcd provides linearizable locks (fast). Client caches lock token for the duration of request. Leases auto‑release, avoiding network round‑trip for unlock. Use `txn` for atomic operations. |
| **Unique ID Generator (Snowflake)** | **Local ID generation** (no network); **Timestamp + worker ID + sequence**; **Pre‑allocated worker IDs** | ID generation <1ms (local). | Each worker generates IDs locally using timestamp (milliseconds). No network call per ID. Worker IDs pre‑assigned via etcd (cached). Sequence resets every millisecond. |
| **Web Crawler** | **Bloom filter (in‑memory)** for dedup; **Redis frontier (sorted set)** with priority; **DNS caching**; **HTTP/2 multiplexing** | URL check <1ms (Bloom filter). | Bloom filter in local memory (or RedisBloom). Frontier: Redis sorted set with politeness priority. DNS responses cached (OS cache). HTTP/2 allows concurrent requests to same domain. |

---

## 7. Common Latency Anti-Patterns

### 7.1 Chatty Microservices
**Problem:** A single client request triggers dozens of inter‑service calls. **Solution:** Aggregate calls, use batching, or merge services.

### 7.2 Missing Cache Layers
**Problem:** Every read hits the database directly. **Solution:** Add Redis/Memcached for hot data; set appropriate TTLs.

### 7.3 Cross-Region Synchronous Calls
**Problem:** Services in different regions call each other synchronously, adding hundreds of milliseconds. **Solution:** Replicate data, use async messaging, or co‑locate.

### 7.4 N+1 Database Queries
**Problem:** Looping through results and issuing a query per row. **Solution:** Use JOINs or `IN` clauses to fetch related data in one query.

### 7.5 Blocking I/O Everywhere
**Problem:** Threads block waiting for I/O, exhausting thread pools. **Solution:** Use non‑blocking I/O, async frameworks, or reactive programming.

### 7.6 Large Payload Transfers
**Problem:** Sending uncompressed, verbose JSON over the network. **Solution:** Enable gzip/Brotli, use binary serialization, paginate large responses.

### 7.7 No CDN for Global Users
**Problem:** All users fetch static assets from a single origin, causing high latency for distant regions. **Solution:** Distribute assets via CDN.

### 7.8 Serial API Calls Instead of Parallelism
**Problem:** Calling downstream services sequentially when they are independent. **Solution:** Use concurrent API calls, async composition (CompletableFuture, Promise.all).

---

## 8. Interview-Oriented HLD Notes

### 8.1 Key Tradeoffs

#### 8.1.1 Latency vs Consistency
Caching and async replication lower latency at the cost of eventual consistency. Use where business logic tolerates it.

#### 8.1.2 Latency vs Cost
CDNs, in‑memory caches, and multi‑region deployments reduce latency but increase infrastructure spend. Optimize by tiering.

#### 8.1.3 Latency vs Durability
Synchronous writes and replication add latency but guarantee no data loss. Async writes are faster but risk loss.

#### 8.1.4 Simplicity vs Performance
Techniques like sharding, hedged requests, and custom serialization add complexity. Apply only when latency goals demand it.

### 8.2 Common Interview Questions

#### 8.2.1 How Would You Reduce API Latency?
Answer: Add caching (Redis, CDN), use HTTP/2 or gRPC, enable compression, optimize database queries with indexes, use async processing for non‑critical tasks, and parallelize independent calls.

#### 8.2.2 Why Use CDN?
Answer: CDNs serve static assets from edge nodes close to users, reducing network RTT and origin load. Critical for global media delivery.

#### 8.2.3 How Does Caching Improve Performance?
Answer: Caching stores frequently accessed data in memory, avoiding expensive disk/network lookups. It turns O(n) or O(log n) queries into O(1) cache hits.

#### 8.2.4 How to Reduce Database Latency?
Answer: Create appropriate indexes, use read replicas, optimize queries (avoid N+1, use keyset pagination), shard large tables, and add a cache layer (Redis) for hot data.

#### 8.2.5 Why Use Async Processing?
Answer: Moving heavy or non‑critical work to background queues reduces the request‑response cycle time, giving the user an immediate acknowledgment.

### 8.3 Quick Revision Material

#### 8.3.1 Latency Reduction Cheat Sheet
- **Caching:** Redis, Memcached, CDN. Best for read‑heavy, stable data.
- **CDN:** Edge servers for static/dynamic content. Geo‑proximity is key.
- **Indexing:** B‑tree for range, hash for equality. Covering indexes avoid table access.
- **Async:** Message queues (Kafka, SQS) for background work. Decouples services.
- **Load Balancing:** Distribute traffic; global LB routes to nearest region.
- **Sharding:** Split data by key; reduces per‑node data size and query scope.
- **Network:** Minimize hops, co‑locate services, use persistent connections.
- **Parallelism:** Concurrent API calls, fan‑out/fan‑in, non‑blocking I/O.
- **Prefetching:** Anticipate user actions; pre‑load data.
- **Compression:** gzip, Brotli, binary protocols (Protobuf).

#### 8.3.2 Optimization Decision Matrix
| Symptom | Likely Cause | Solution |
|---------|-------------|----------|
| High P95 latency | Long‑tail slow requests | Hedged requests, timeouts, load shedding |
| Slow page loads globally | Far‑away origin server | CDN, global load balancing, multi‑region |
| DB query takes 200ms | Missing index or full scan | Add index, rewrite query, use read replica |
| API response bloated | Large JSON payloads | Enable compression, use pagination |
| Microservice chain slow | Too many synchronous hops | Merge services, use async messaging |

#### 8.3.3 Key Interview Sentences
> *“To reduce latency in the URL shortener, I’d implement a Redis cache‑aside for redirects, set a 24‑hour TTL, and invalidate on update. Combined with a CDN for static assets and keep‑alive connections, the average redirect latency drops from ~50ms to under 5ms.”*

> *“For the social feed, I’d pre‑compute timelines using a fan‑out on write and store them in Redis sorted sets. Reads then hit the cache directly, reducing latency from hundreds of milliseconds to single digits.”*

> *“I’d use gRPC with Protobuf for inter‑service communication to cut serialization overhead and leverage HTTP/2 multiplexing, reducing tail latency in the microservice architecture.”*

#### 8.3.4 Self-Test Questions
1. What is the difference between response time and network latency?
2. You have a read‑heavy API that takes 500ms. The database query alone takes 400ms. How would you reduce latency?
3. Why is a CDN more effective than a single data center for a global user base?
4. What are the risks of using asynchronous processing for a payment system?
5. How does sharding help reduce query latency, and what is the biggest challenge?

*End of consolidated notes.*
