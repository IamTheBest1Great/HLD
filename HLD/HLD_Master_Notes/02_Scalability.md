# Scalability — Consolidated HLD Notes

## Table of Contents

- [1. Introduction to Scaling](#1-introduction-to-scaling)
  - [1.1 Why Scaling Matters](#11-why-scaling-matters)
    - [1.1.1 Traffic Growth](#111-traffic-growth)
    - [1.1.2 High Availability Requirements](#112-high-availability-requirements)
    - [1.1.3 Low Latency Requirements](#113-low-latency-requirements)
    - [1.1.4 Cost Efficiency](#114-cost-efficiency)
    - [1.1.5 Global User Base Challenges](#115-global-user-base-challenges)
- [2. Core Scaling Concepts](#2-core-scaling-concepts)
  - [2.1 Vertical Scaling (Scale Up)](#21-vertical-scaling-scale-up)
    - [2.1.1 What It Is](#211-what-it-is)
    - [2.1.2 How It Works](#212-how-it-works)
    - [2.1.3 Pros & Cons](#213-pros--cons)
    - [2.1.4 Hardware Limits](#214-hardware-limits)
    - [2.1.5 When to Use](#215-when-to-use)
  - [2.2 Horizontal Scaling (Scale Out)](#22-horizontal-scaling-scale-out)
    - [2.2.1 What It Is](#221-what-it-is)
    - [2.2.2 How It Works](#222-how-it-works)
    - [2.2.3 Pros & Cons](#223-pros--cons)
    - [2.2.4 Statelessness Requirements](#224-statelessness-requirements)
    - [2.2.5 When to Use](#225-when-to-use)
  - [2.3 Vertical vs Horizontal Scaling](#23-vertical-vs-horizontal-scaling)
    - [2.3.1 Cost Comparison](#231-cost-comparison)
    - [2.3.2 Operational Complexity](#232-operational-complexity)
    - [2.3.3 Fault Tolerance Differences](#233-fault-tolerance-differences)
    - [2.3.4 Performance Tradeoffs](#234-performance-tradeoffs)
  - [2.4 Stateless vs Stateful Services](#24-stateless-vs-stateful-services)
    - [2.4.1 Stateless Services](#241-stateless-services)
    - [2.4.2 Stateful Services](#242-stateful-services)
    - [2.4.3 Session Affinity](#243-session-affinity)
    - [2.4.4 Session Externalization](#244-session-externalization)
    - [2.4.5 Distributed Session Stores](#245-distributed-session-stores)
  - [2.5 Auto-Scaling](#25-auto-scaling)
    - [2.5.1 How Auto-Scaling Works](#251-how-auto-scaling-works)
    - [2.5.2 Reactive Scaling](#252-reactive-scaling)
    - [2.5.3 Scheduled Scaling](#253-scheduled-scaling)
    - [2.5.4 Predictive Scaling](#254-predictive-scaling)
    - [2.5.5 Warm-Up & Cool-Down](#255-warm-up--cool-down)
    - [2.5.6 Stateful Service Scaling Challenges](#256-stateful-service-scaling-challenges)
    - [2.5.7 Kubernetes HPA](#257-kubernetes-hpa)
    - [2.5.8 Cloud Auto-Scaling Groups](#258-cloud-auto-scaling-groups)
  - [2.6 Scalable Reference Architecture](#26-scalable-reference-architecture)
    - [2.6.1 Load Balancer](#261-load-balancer)
    - [2.6.2 Stateless App Layer](#262-stateless-app-layer)
    - [2.6.3 Cache Layer](#263-cache-layer)
    - [2.6.4 Database Layer](#264-database-layer)
    - [2.6.5 Async Processing Layer](#265-async-processing-layer)
- [3. Core Scalability Techniques](#3-core-scalability-techniques)
  - [3.1 Load Balancing](#31-load-balancing)
    - [3.1.1 Why Load Balancing Matters](#311-why-load-balancing-matters)
    - [3.1.2 Round Robin](#312-round-robin)
    - [3.1.3 Least Connections](#313-least-connections)
    - [3.1.4 IP Hash](#314-ip-hash)
    - [3.1.5 Weighted Algorithms](#315-weighted-algorithms)
    - [3.1.6 Layer 4 vs Layer 7](#316-layer-4-vs-layer-7)
    - [3.1.7 Health Checks](#317-health-checks)
    - [3.1.8 Global Load Balancing](#318-global-load-balancing)
  - [3.2 Caching](#32-caching)
    - [3.2.1 Why Caching Matters](#321-why-caching-matters)
    - [3.2.2 CDN Caching](#322-cdn-caching)
    - [3.2.3 In-Memory Cache (Redis / Memcached)](#323-in-memory-cache-redis--memcached)
    - [3.2.4 Database Query Cache](#324-database-query-cache)
    - [3.2.5 Application-Level Cache](#325-application-level-cache)
    - [3.2.6 Cache Invalidation](#326-cache-invalidation)
    - [3.2.7 Cache Eviction Policies](#327-cache-eviction-policies)
    - [3.2.8 Cache Stampede Prevention](#328-cache-stampede-prevention)
  - [3.3 Database Scaling](#33-database-scaling)
    - [3.3.1 Read Replicas](#331-read-replicas)
    - [3.3.2 Leader-Follower Replication](#332-leader-follower-replication)
    - [3.3.3 Sharding](#333-sharding)
    - [3.3.4 Vertical Partitioning](#334-vertical-partitioning)
    - [3.3.5 Table Partitioning](#335-table-partitioning)
    - [3.3.6 Connection Pooling](#336-connection-pooling)
    - [3.3.7 Database Caching](#337-database-caching)
    - [3.3.8 Denormalization](#338-denormalization)
  - [3.4 Sharding Strategies](#34-sharding-strategies)
    - [3.4.1 Hash-Based Sharding](#341-hash-based-sharding)
    - [3.4.2 Range-Based Sharding](#342-range-based-sharding)
    - [3.4.3 Directory-Based Sharding](#343-directory-based-sharding)
    - [3.4.4 Geo-Based Sharding](#344-geo-based-sharding)
    - [3.4.5 Rebalancing Challenges](#345-rebalancing-challenges)
    - [3.4.6 Hotspot Problems](#346-hotspot-problems)
  - [3.5 Asynchronous Processing](#35-asynchronous-processing)
    - [3.5.1 Message Queues](#351-message-queues)
    - [3.5.2 Event-Driven Architecture](#352-event-driven-architecture)
    - [3.5.3 Background Workers](#353-background-workers)
    - [3.5.4 Retry & DLQ](#354-retry--dlq-dead-letter-queue)
    - [3.5.5 Backpressure Handling](#355-backpressure-handling)
  - [3.6 Microservices for Scalability](#36-microservices-for-scalability)
    - [3.6.1 Independent Service Scaling](#361-independent-service-scaling)
    - [3.6.2 Service Isolation](#362-service-isolation)
    - [3.6.3 API Gateway](#363-api-gateway)
    - [3.6.4 Service Discovery](#364-service-discovery)
    - [3.6.5 Distributed Communication Challenges](#365-distributed-communication-challenges)
- [4. CDN (Content Delivery Network)](#4-cdn-content-delivery-network)
  - [4.1 CDN Fundamentals](#41-cdn-fundamentals)
    - [4.1.1 What Is a CDN?](#411-what-is-a-cdn)
    - [4.1.2 CDN Architecture](#412-cdn-architecture)
    - [4.1.3 Edge Servers](#413-edge-servers)
    - [4.1.4 Origin Servers](#414-origin-servers)
  - [4.2 Benefits of CDN](#42-benefits-of-cdn)
    - [4.2.1 Reduced Latency](#421-reduced-latency)
    - [4.2.2 Origin Offloading](#422-origin-offloading)
    - [4.2.3 Higher Throughput](#423-higher-throughput)
    - [4.2.4 DDoS Protection](#424-ddos-protection)
    - [4.2.5 Global Availability](#425-global-availability)
  - [4.3 CDN Caching Strategies](#43-cdn-caching-strategies)
    - [4.3.1 TTL-Based Caching](#431-ttl-based-caching)
    - [4.3.2 Cache Purging](#432-cache-purging)
    - [4.3.3 Versioned URLs](#433-versioned-urls)
    - [4.3.4 Cache Hierarchy](#434-cache-hierarchy)
    - [4.3.5 Origin Shield](#435-origin-shield)
    - [4.3.6 Cache Warming](#436-cache-warming)
    - [4.3.7 Dynamic Content Caching](#437-dynamic-content-caching-edge-side-includes---esi)
  - [4.4 Advanced CDN Concepts](#44-advanced-cdn-concepts)
    - [4.4.1 Edge Computing](#441-edge-computing)
    - [4.4.2 Geo-Targeting](#442-geo-targeting)
    - [4.4.3 Lambda@Edge / Workers](#443-lambdaedge--workers)
    - [4.4.4 CDN Security](#444-cdn-security)
    - [4.4.5 Cache Poisoning Risks](#445-cache-poisoning-risks)
  - [4.5 CDN Use Cases](#45-cdn-use-cases)
    - [4.5.1 Static Asset Delivery](#451-static-asset-delivery)
    - [4.5.2 Video Streaming](#452-video-streaming)
    - [4.5.3 API Acceleration](#453-api-acceleration)
    - [4.5.4 URL Shortener Optimization](#454-url-shortener-optimization)
    - [4.5.5 Global Applications](#455-global-applications)
  - [4.6 CDN Challenges](#46-cdn-challenges)
    - [4.6.1 Stale Content](#461-stale-content)
    - [4.6.2 Cache Misses](#462-cache-misses)
    - [4.6.3 Regional Inconsistencies](#463-regional-inconsistencies)
    - [4.6.4 Cache Stampedes](#464-cache-stampedes)
  - [4.7 Popular CDN Providers](#47-popular-cdn-providers)
- [5. Scaling Patterns by System Type](#5-scaling-patterns-by-system-type)
  - [5.1 Summary: Scaling Techniques by Pattern](#51-summary-scaling-techniques-by-pattern)
  - [5.2 Pattern A – CRUD & Storage Systems (Scaling Focus)](#52-pattern-a---crud--storage-systems-scaling-focus)
  - [5.3 Pattern B – Feed / Timeline Systems (Scaling Focus)](#53-pattern-b---feed--timeline-systems-scaling-focus)
  - [5.4 Pattern C – Real-Time Systems (Scaling Focus)](#54-pattern-c---real-time-systems-scaling-focus)
  - [5.5 Pattern D – Search Systems (Scaling Focus)](#55-pattern-d---search-systems-scaling-focus)
  - [5.6 Pattern E – Geospatial & Location-Based Systems (Scaling Focus)](#56-pattern-e---geospatial--location-based-systems-scaling-focus)
  - [5.7 Pattern F – Financial & Critical Consistency Systems (Scaling Focus)](#57-pattern-f---financial--critical-consistency-systems-scaling-focus)
  - [5.8 Pattern G – Large-Scale Processing & Infrastructure (Scaling Focus)](#58-pattern-g---large-scale-processing--infrastructure-scaling-focus)
  - [5.9 Pattern H – Collaborative & Infrastructure Utilities (Scaling Focus)](#59-pattern-h---collaborative--infrastructure-utilities-scaling-focus)
- [6. Scalability Decision Frameworks](#6-scalability-decision-frameworks)
  - [6.1 Choosing the Right Scaling Strategy](#61-choosing-the-right-scaling-strategy)
    - [6.1.1 Read-Heavy vs Write-Heavy](#611-read-heavy-vs-write-heavy)
    - [6.1.2 Steady vs Spiky Traffic](#612-steady-vs-spiky-traffic)
    - [6.1.3 Latency vs Throughput](#613-latency-vs-throughput)
    - [6.1.4 Consistency vs Scalability](#614-consistency-vs-scalability)
    - [6.1.5 Cost vs Performance](#615-cost-vs-performance)
  - [6.2 Common Anti-Patterns](#62-common-anti-patterns)
    - [6.2.1 Stateful Application Servers](#621-stateful-application-servers)
    - [6.2.2 Database as Bottleneck](#622-database-as-bottleneck)
    - [6.2.3 Missing Cache Layer](#623-missing-cache-layer)
    - [6.2.4 Hot Partitions](#624-hot-partitions)
    - [6.2.5 Synchronous Everything](#625-synchronous-everything)
    - [6.2.6 No Backpressure Handling](#626-no-backpressure-handling)
- [7. Interview-Oriented HLD Notes](#7-interview-oriented-hld-notes)
  - [7.1 Key Interview Topics](#71-key-interview-topics)
    - [7.1.1 Scaling Bottleneck Identification](#711-scaling-bottleneck-identification)
    - [7.1.2 Throughput Estimation](#712-throughput-estimation)
    - [7.1.3 Capacity Planning](#713-capacity-planning)
    - [7.1.4 Tradeoff Discussions](#714-tradeoff-discussions)
    - [7.1.5 Failure Scenarios](#715-failure-scenarios)
  - [7.2 Quick Revision Material](#72-quick-revision-material)
    - [7.2.1 Quick Selection Guide](#721-quick-selection-guide)
    - [7.2.2 Key Interview Sentences](#722-key-interview-sentences)
    - [7.2.3 Self-Test Questions](#723-self-test-questions)
    - [7.2.4 Architecture Cheat Sheet](#724-architecture-cheat-sheet)

---

## 1. Introduction to Scaling

### 1.1 Why Scaling Matters

As your user base grows, your system faces higher traffic, more data, and increased computational load. Without scaling, performance degrades – response times increase, errors occur, and eventually the system becomes unavailable. Scaling ensures your application can handle growth gracefully.

#### 1.1.1 Traffic Growth
A growing user base or a successful feature launch leads to a higher number of requests per second. Systems must be designed to accommodate linear or even exponential growth without architectural redesign.

#### 1.1.2 High Availability Requirements
Users expect the service to be available 24/7. Scaling must introduce redundancy and failover mechanisms so that no single component failure brings down the whole system.

#### 1.1.3 Low Latency Requirements
Modern applications often require sub‑second response times. Scaling strategies must minimize network hops, leverage caching, and bring data closer to users to meet latency goals.

#### 1.1.4 Cost Efficiency
Scaling should optimize resource usage. Pay‑as‑you‑go cloud models, auto‑scaling, and rightsizing prevent over‑provisioning and keep costs under control while still handling peak loads.

#### 1.1.5 Global User Base Challenges
Serving users worldwide introduces challenges like data residency regulations, network latency across continents, and the need for geo‑distributed infrastructure that can localise content and processing.

<img width="572" height="1024" alt="image" src="https://github.com/user-attachments/assets/cd797585-8c21-46c9-8518-092a21677df3" />

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/4cb7d345-f345-4b76-ab9a-26b75419fd7a" />




## 2. Core Scaling Concepts

### 2.1 Vertical Scaling (Scale Up)

#### 2.1.1 What It Is
Vertical scaling means adding more power to an existing server: more CPU, more RAM, faster disks, or a more powerful machine. Think of it as upgrading your current hardware.

**Analogy:** If your coffee shop gets more customers, you replace your single espresso machine with a super‑fast, high‑capacity one.

#### 2.1.2 How It Works
- You increase the resources of a single node (e.g., upgrade from an `m5.large` to an `m5.24xlarge` EC2 instance).
- The application typically requires no changes – it just runs on a bigger machine.

```
BEFORE                          AFTER
─────────────────────           ─────────────────────
┌─────────────────┐             ┌─────────────────┐
│  Server         │             │  Bigger Server  │
│  CPU:  4 cores  │    ──►      │  CPU: 32 cores  │
│  RAM:  16 GB    │             │  RAM: 256 GB    │
│  Disk: 500 GB   │             │  Disk: 10 TB    │
└─────────────────┘             └─────────────────┘
     handles 1K QPS                  handles 10K QPS
```

#### 2.1.3 Pros & Cons

**Pros ✅**
- **Simple:** No architectural changes needed.
- **Low complexity:** You don’t need to deal with distributed systems issues (consistency, coordination).
- **Single node:** Data locality is preserved; no need for partitioning.

**Cons ❌**
- **Hard limit:** You cannot scale beyond the maximum capacity of the most powerful machine available.
- **Single point of failure:** If that one machine fails, the entire system goes down (unless you add redundancy).
- **Expensive:** Top‑end hardware has a price premium, and you pay for it even when not fully utilized.
- **Downtime:** Upgrading often requires rebooting or taking the server offline.

#### 2.1.4 Hardware Limits
There is a hard ceiling – you cannot buy infinite RAM or CPU. The largest cloud instances have physical limits, and beyond them vertical scaling is impossible.

#### 2.1.5 When to Use
- Small to medium applications where growth is predictable and limited.
- Legacy systems that are hard to refactor.
- Databases with strong consistency requirements where sharding is complex (though read replicas can help).

**Example:** Upgrading a database server from 16GB RAM to 64GB RAM to handle a larger working set.

### 2.2 Horizontal Scaling (Scale Out)

#### 2.2.1 What It Is
Horizontal scaling means adding more servers to your pool. Instead of a single powerful machine, you run your application on many smaller ones, distributing the load among them.

**Analogy:** Your coffee shop adds more espresso machines and hires more baristas to serve multiple customers in parallel.

#### 2.2.2 How It Works
- You run multiple instances of your application behind a load balancer.
- The load balancer distributes incoming requests across the instances.
- Data may need to be partitioned (sharded) or replicated across nodes.

```
BEFORE                          AFTER
─────────────────────           ─────────────────────────────────
                                ┌──────────┐
                                │ Server 1 │
┌─────────────────┐             ├──────────┤
│  Single Server  │   ──►   LB─►│ Server 2 │
│  1K QPS         │             ├──────────┤
└─────────────────┘             │ Server 3 │
                                ├──────────┤
                                │ Server N │
                                └──────────┘
                                  handles 100K+ QPS
```

#### 2.2.3 Pros & Cons

**Pros ✅**
- **Virtually unlimited scaling:** You can keep adding commodity hardware.
- **Cost‑effective:** Use many inexpensive machines instead of one expensive supercomputer.
- **High availability:** If one instance fails, others continue serving traffic.
- **Elasticity:** You can add or remove instances on‑demand (auto‑scaling).

**Cons ❌**
- **Increased complexity:** You must handle distributed system challenges (load balancing, data consistency, service discovery, distributed transactions).
- **State management:** Stateful services become harder to scale; you must externalize state.
- **Network overhead:** Communication between nodes adds latency.

#### 2.2.4 Statelessness Requirements
For horizontal scaling to work, each server must be **stateless** – it cannot store user session data locally. All state must be externalised to a shared store (e.g., Redis, database).

```
PROBLEM — Stateful Server:
User logs in → Session stored on Server 1
Next request → Load balancer sends to Server 2
Server 2: "Who are you? No session found!" ❌

SOLUTION — Stateless Server:
User logs in → Session stored in Redis (external)
Next request → Any server checks Redis → finds session ✅
```

#### 2.2.5 When to Use
- Large‑scale, high‑traffic applications (e.g., social networks, e‑commerce).
- Cloud‑native and microservices architectures.
- When you need high availability and elasticity.

**Example:** Running 100 web servers behind an AWS Application Load Balancer to serve millions of users.

### 2.3 Vertical vs Horizontal Scaling

| Aspect                | Vertical Scaling (Scale Up)       | Horizontal Scaling (Scale Out)    |
|-----------------------|------------------------------------|------------------------------------|
| **Approach**          | Add more power to one machine      | Add more machines                  |
| **Maximum capacity**  | Limited by hardware                | Virtually unlimited                |
| **Complexity**        | Low                                | High                               |
| **Cost**              | Premium hardware, potential waste  | Commodity hardware, pay as you grow|
| **High availability** | Hard (single point of failure)     | Built‑in (redundancy)              |
| **State management**  | Simple (local)                     | Must externalize or replicate      |
| **Downtime**          | Often required                     | None if done correctly (rolling updates)|
| **Example**           | Upgrade from 2‑core to 32‑core CPU | Add 10 more EC2 instances          |

#### 2.3.1 Cost Comparison
Vertical scaling uses expensive high‑end hardware with a price premium; horizontal scaling leverages many inexpensive commodity machines, often with pay‑as‑you‑go pricing.

#### 2.3.2 Operational Complexity
Vertical scaling is operationally simple – one machine to manage. Horizontal scaling introduces distributed systems complexity: load balancing, service discovery, data consistency, and monitoring.

#### 2.3.3 Fault Tolerance Differences
A single vertically‑scaled machine is a single point of failure. Horizontal scaling builds in redundancy: if one instance fails, others take over, providing high availability.

#### 2.3.4 Performance Tradeoffs
Vertical scaling can offer lower latency for single‑node operations (no network hops), but hits a performance ceiling. Horizontal scaling can achieve higher overall throughput through parallelism, though individual requests may experience slightly higher latency due to network calls.

### 2.4 Stateless vs Stateful Services

#### 2.4.1 Stateless Services
A stateless service does **not** store any session data or state locally. Each request is independent and can be handled by any instance.

- **Characteristics:** No local user data; all necessary data comes with the request; any instance can serve any request.
- **Scaling:** Extremely easy – just add more instances behind a load balancer.
- **Examples:** REST API reading from a DB, frontend serving static files, stateless microservice.

#### 2.4.2 Stateful Services
A stateful service maintains state (session data, user context) locally. Subsequent requests from the same client must be routed to the same instance.

- **Characteristics:** Stores data locally (disk or memory); requires session affinity; harder to scale.
- **Scaling:** Complex; may need state replication or externalisation; sticky sessions can cause uneven load.
- **Examples:** Web server with in‑memory sessions, multiplayer game server, database server.

#### 2.4.3 Session Affinity
Session affinity (sticky sessions) ensures that all requests from a specific client are routed to the same backend server. The load balancer uses a cookie or client IP hash to pin the client to a server.  
**Drawback:** Uneven load distribution and loss of session if the server fails.

#### 2.4.4 Session Externalization
Instead of storing sessions locally, move session data to an external, shared datastore (e.g., Redis, database). Any server can then fetch the session, making the service effectively stateless and horizontally scalable.

#### 2.4.5 Distributed Session Stores
A distributed session store (e.g., Redis Cluster, Memcached) holds session data in a highly available, scalable cache. It centralizes session management, eliminates sticky sessions, and survives individual server failures.

**Making a Service Stateless:**

| State Type | Where to Move It |
|------------|-----------------|
| User sessions | Redis (key: session_token, value: user_data, TTL) |
| Shopping cart | Redis or database |
| File uploads in progress | Object storage (S3) with pre-signed URLs |
| Background job status | Database or Redis |
| Rate limit counters | Redis |

### 2.5 Auto-Scaling

Auto‑scaling is the ability of a system to automatically adjust the number of active instances based on real‑time demand.

#### 2.5.1 How Auto-Scaling Works
1. **Monitoring:** Continuously monitor metrics (CPU, memory, request count, queue depth).
2. **Policy Evaluation:** When metrics cross a threshold (e.g., CPU > 70% for 5 min), an alarm triggers.
3. **Scaling Action:** The auto‑scaler adds (scale‑out) or removes (scale‑in) instances.
4. **Cool‑down Period:** Prevents thrashing (repeated scaling events).

#### 2.5.2 Reactive Scaling
Responds to real‑time metrics. *Example:* Add one EC2 instance when CPU > 70% for 5 minutes.

#### 2.5.3 Scheduled Scaling
Predictable patterns, e.g., scale out at 8 AM and scale in at 10 PM. *Example:* Black Friday pre‑scaling.

#### 2.5.4 Predictive Scaling
Uses machine learning to forecast demand and pre‑scale before the load arrives.

#### 2.5.5 Warm-Up & Cool-Down
- **Warm‑up:** New instances need time to boot, load caches, and establish connections. To avoid degraded service during spikes, pre‑warm servers or keep a minimum buffer.
- **Cool‑down:** After a scaling action, a stabilization period prevents rapid up‑and‑down cycles.

```
WARM-UP TIME PROBLEM:
Traffic spike at 12:00 PM
Auto-scaler detects high CPU at 12:00:30
New server starts booting at 12:00:35
New server ready at 12:01:20 (JVM warm-up, connection pools, etc.)
Users experienced 80 seconds of degraded service ❌

SOLUTIONS:
1. Pre-warm servers before expected traffic spikes (scheduled scaling)
2. Keep minimum instance count that can handle baseline + burst
3. Use faster-booting instances (containers vs VMs)
4. Implement connection draining before terminating instances
```

#### 2.5.6 Stateful Service Scaling Challenges
Auto‑scaling stateful services is much harder because data must be rebalanced when instances join or leave. Solutions include:
- Using a distributed database that handles rebalancing automatically (e.g., Cassandra, MongoDB).
- Consistent hashing with virtual nodes.
- Avoiding auto‑scaling for the stateful tier; instead scale the stateless frontend aggressively.

#### 2.5.7 Kubernetes HPA
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
```

#### 2.5.8 Cloud Auto-Scaling Groups
```yaml
AutoScalingGroup:
  MinSize: 2
  MaxSize: 10
  DesiredCapacity: 2
  TargetGroup:
    - Instance1
    - Instance2
  Policies:
    - ScaleOutPolicy:
        AdjustmentType: ChangeInCapacity
        ScalingAdjustment: 1
        Cooldown: 300
    - ScaleInPolicy:
        AdjustmentType: ChangeInCapacity
        ScalingAdjustment: -1
        Cooldown: 300
  CloudWatchAlarms:
    - HighCPUAlarm:
        MetricName: CPUUtilization
        Threshold: 70
        Period: 300
        EvaluationPeriods: 2
        AlarmAction: ScaleOutPolicy
    - LowCPUAlarm:
        MetricName: CPUUtilization
        Threshold: 30
        Period: 300
        EvaluationPeriods: 2
        AlarmAction: ScaleInPolicy
```

### 2.6 Scalable Reference Architecture

```
                     TRAFFIC
                        │
              ┌─────────▼──────────┐
              │      CDN           │  ← Static assets, cached responses
              └─────────┬──────────┘
                        │ (dynamic requests only)
              ┌─────────▼──────────┐
              │   Load Balancer    │  ← L7, routes by path, SSL termination
              │   (NGINX / ALB)    │
              └──┬──────┬──────┬───┘
                 │      │      │
            ┌────▼──┐ ┌─▼───┐ ┌▼────┐
            │App1   │ │App2 │ │App3 │  ← Stateless app servers (auto-scaled)
            └────┬──┘ └─┬───┘ └┬────┘
                 │      │      │
              ┌──▼──────▼──────▼──┐
              │     Redis         │  ← Sessions, caching, rate limits
              └───────────────────┘
              ┌───────────────────┐
              │     Database      │  ← Primary (vertical scaled or sharded)
              │  ┌─────────────┐  │
              │  │  Replicas   │  │  ← Read replicas (horizontal read scale)
              │  └─────────────┘  │
              └───────────────────┘
```

#### 2.6.1 Load Balancer
Distributes incoming dynamic requests across stateless application servers. Performs SSL termination and path‑based routing (Layer 7).

#### 2.6.2 Stateless App Layer
Horizontally scalable application servers that hold no local state. They fetch sessions and data from Redis or the database.

#### 2.6.3 Cache Layer
A shared Redis (or Memcached) cluster used for session storage, query result caching, rate‑limiting, and transient data.

#### 2.6.4 Database Layer
Primary database (vertically scaled or sharded) with read replicas to offload read traffic. Writes go to the primary, reads are distributed.

#### 2.6.5 Async Processing Layer
Message queues (Kafka, RabbitMQ) and background workers handle long‑running tasks, smooth write spikes, and decouple services.

## 3. Core Scalability Techniques

### 3.1 Load Balancing

#### 3.1.1 Why Load Balancing Matters
Load balancers distribute incoming requests across multiple servers, preventing overload on any single instance. They enable horizontal scaling, zero‑downtime deployments, and fault tolerance.

#### 3.1.2 Round Robin
```
Request 1 → Server 1
Request 2 → Server 2
Request 3 → Server 3
Request 4 → Server 1 (cycles)
```
Good for uniform request size. Bad for heterogeneous workloads.

#### 3.1.3 Least Connections
New request goes to the server with the fewest active connections. Good for long‑lived connections (WebSockets, file uploads).

#### 3.1.4 IP Hash
`hash(client_IP) % num_servers` → always same server. Used for sticky sessions, but can cause hotspots if many users share an IP.

#### 3.1.5 Weighted Algorithms
Assign higher weights to more powerful servers. Weighted Round Robin: Server1 weight=3 gets 3× more requests than Server2 weight=1.

#### 3.1.6 Layer 4 vs Layer 7

```
Layer 4 Load Balancer:
- Sees: IP addresses and ports only
- Cannot see: HTTP headers, URL path, cookies
- Speed: Very fast (no packet inspection)
- Use: TCP/UDP traffic (AWS NLB)

Layer 7 Load Balancer:
- Sees: Full HTTP request (headers, path, body, cookies)
- Can route by: URL path, header values, cookies, user agent
- Speed: Slightly slower (reads HTTP)
- Use: Most web applications (AWS ALB, NGINX, Envoy)
```

**Practical L7 example:**
```
/api/*   → Backend API servers (weighted round robin)
/static/* → CDN edge servers
/ws/*     → WebSocket servers (least connections)
Mobile UA → Mobile-optimized servers
```

#### 3.1.7 Health Checks
Load balancers continuously monitor the health of backend servers.
- **Active Health Checks:** Periodically probe a `/health` endpoint; unresponsive instances are removed.
- **Passive Health Checks:** Observe actual client requests and mark instances as unhealthy after repeated failures.
- **Configuration:** Interval, timeout, healthy/unhealthy thresholds.

#### 3.1.8 Global Load Balancing
Distributes traffic across multiple data centres or regions.
- **DNS‑based:** GeoDNS or latency‑based routing (e.g., AWS Route 53).
- **Anycast:** The same IP advertised from multiple locations; network routes to the closest point.
- **Benefits:** Disaster recovery, compliance, and latency reduction for global users.

### 3.2 Caching

#### 3.2.1 Why Caching Matters
Caching stores frequently accessed data in a faster storage layer (memory, edge servers) to reduce load on the database and drastically improve read latency.

#### 3.2.2 CDN Caching
A CDN caches content at edge locations close to users. This offloads static assets (images, CSS, JS) and even dynamic but cacheable responses from the origin server. (Detailed coverage in Section 4 – CDN.)

#### 3.2.3 In-Memory Cache (Redis / Memcached)
Store hot data in memory for sub‑millisecond access. Used for session tokens, user profiles, leaderboards, and frequently read objects.  
**Example:** GitHub caches repository metadata in Redis; popular repos only hit the DB once per TTL.

#### 3.2.4 Database Query Cache
Store the results of expensive or repetitive database queries. When the same query is executed, the cached result is returned, bypassing the database. Effective for read‑heavy workloads with rarely changing data.  
**Example:** Airbnb caches “list all properties in Paris” query results for 5 minutes.

#### 3.2.5 Application-Level Cache
A cache within the application process (e.g., Guava, Caffeine). Extremely fast (no network call) but not shared across instances. Best for small, static reference data.

#### 3.2.6 Cache Invalidation
Strategies for removing or updating stale cache entries:
- **TTL (Time‑To‑Live):** Automatic expiration.
- **Write‑through:** Write to cache and DB synchronously.
- **Write‑behind:** Write to cache, then async to DB.
- **Write‑around:** Write to DB only, invalidate cache.
- **Event‑driven:** Purge/update on data change events.

#### 3.2.7 Cache Eviction Policies
When the cache is full, which entry to remove:
- **LRU (Least Recently Used):** Evicts the item not accessed for the longest time.
- **LFU (Least Frequently Used):** Tracks access frequency and evicts the least popular.
- **FIFO (First In, First Out):** Evicts in insertion order.
- **TTL only:** Expiration based solely on time.

#### 3.2.8 Cache Stampede Prevention
When a popular cache entry expires, many concurrent requests hit the database. Prevention:
- **Locking:** First request locks, fetches data, populates cache; others wait.
- **Probabilistic early recomputation:** Refresh before expiration.
- **Stale‑while‑revalidate:** Serve stale data while asynchronously fetching fresh.

### 3.3 Database Scaling

#### 3.3.1 Read Replicas
Copy data to follower databases; writes go to the primary, reads are distributed to replicas. Dramatically scales read throughput. Trade‑off: replication lag may cause stale reads.

#### 3.3.2 Leader-Follower Replication
The primary (leader) accepts writes and replicates changes to one or more read‑only followers. Followers can serve reads and also act as failover candidates if the leader fails.

#### 3.3.3 Sharding
Split data across multiple independent database servers based on a shard key (e.g., `user_id % 128`). Scales writes horizontally. Complex cross‑shard queries and rebalancing are the main challenges.

```
Application → Shard A (users 1-1000)
           → Shard B (users 1001-2000)
           → Shard C (users 2001-3000)
```

#### 3.3.4 Vertical Partitioning
Split tables into different databases based on access patterns. For example, move `user_profile` (frequently read) to a high‑performance DB and `user_login_history` (rarely accessed) to cheaper storage.

#### 3.3.5 Table Partitioning
Split one large table into smaller physical pieces (by range, list, or hash) inside the same database server. Speeds up queries and simplifies maintenance (e.g., dropping old partitions).

```
Orders table (partitioned by order_date):
- orders_2024_q1
- orders_2024_q2
- orders_2024_q3
```

#### 3.3.6 Connection Pooling
Reuse a fixed set of open database connections across many client requests instead of opening/closing per request. Reduces overhead and prevents overwhelming the database.

```
Client 1 ─┐
Client 2 ─┼→ Connection Pool (5 open DB connections) → Database
Client 3 ─┘
```

#### 3.3.7 Database Caching
Store frequent query results or objects in a fast in‑memory store (Redis, Memcached) in front of the database. Reduces database load and latency.

```
Application → Cache (Redis) → Database (only on miss)
```

#### 3.3.8 Denormalization
Add redundant data to tables to avoid expensive joins, making reads faster at the cost of slower writes. Used in read‑heavy reporting and analytics.

```
Normalized:        Denormalized:
Customers ─┐       CustomerOrders
Orders ────┘       (customer_name, order_id, product_name, amount)
```

### 3.4 Sharding Strategies

#### 3.4.1 Hash-Based Sharding
`hash(shard_key) % N` → simple, even distribution. Example: `hash(user_id) % 128`. Rebalancing is difficult when adding/removing shards (consistent hashing can mitigate).

#### 3.4.2 Range-Based Sharding
Data is partitioned by ranges of the shard key (e.g., `user_id` 1‑1M, 1M‑2M). Good for range queries, but can cause hotspots if access patterns are skewed.

#### 3.4.3 Directory-Based Sharding
A lookup service maintains a mapping from shard key to the specific database server. Flexible for moving data, but adds an extra hop and the directory must be highly available and scalable.

#### 3.4.4 Geo-Based Sharding
Data is partitioned by geographic location (e.g., users in Europe vs. Asia). Reduces latency by keeping data close to users and helps with data residency regulations.

#### 3.4.5 Rebalancing Challenges
When adding or removing shards, data must be redistributed. This is a heavy operation and can cause performance degradation. Consistent hashing and virtual nodes help minimise data movement.

#### 3.4.6 Hotspot Problems
A poorly chosen shard key can cause uneven load distribution – one shard gets a disproportionate share of traffic. Mitigations include using high‑cardinality keys, directory‑based sharding, or further splitting hot shards.

### 3.5 Asynchronous Processing

#### 3.5.1 Message Queues
Decouple request receipt from processing using a queue (Kafka, RabbitMQ, SQS). Producers push messages; consumers process them asynchronously. Absorbs bursts and enables retries.

#### 3.5.2 Event-Driven Architecture
Services communicate by publishing events to an event bus. Other services subscribe and react asynchronously. Decouples producers and consumers, allowing independent scaling and easy addition of new functionality.

#### 3.5.3 Background Workers
Dedicated processes or threads that pull jobs from a queue and perform time‑consuming tasks (video transcoding, email sending, PDF generation). They can be scaled independently based on queue depth.

#### 3.5.4 Retry & DLQ (Dead Letter Queue)
When message processing fails, the message is retried with exponential backoff. After a maximum number of attempts, it’s moved to a Dead Letter Queue for manual inspection and remediation, preventing blocking.

#### 3.5.5 Backpressure Handling
When producers outpace consumers, the system can overload. Techniques:
- Rate limiting at the producer.
- Queue length limits – reject or block when full.
- Reactive streams where the consumer signals capacity.
- Load shedding – drop non‑critical requests.

### 3.6 Microservices for Scalability

#### 3.6.1 Independent Service Scaling
Each microservice can be scaled horizontally based on its own metrics (CPU, memory, queue depth). A payment service might need 50 instances while a review service needs only 5, avoiding the waste of scaling a monolith.

#### 3.6.2 Service Isolation
Failures in one service should not cascade. Techniques:
- **Circuit breakers:** Stop calling a failing service and provide a fallback.
- **Bulkheads:** Partition resources (separate thread pools) so a slow downstream doesn’t exhaust all threads.
- **Timeouts and retries** with exponential backoff.

#### 3.6.3 API Gateway
A single entry point for all clients that handles:
- Routing to the appropriate microservice.
- Authentication and authorisation.
- Rate limiting and request/response transformation.
- Aggregating data from multiple services.

#### 3.6.4 Service Discovery
Mechanisms for clients to locate service instances as they scale up/down:
- **Client‑side discovery:** Client queries a registry (Eureka, Consul) and load‑balances.
- **Server‑side discovery:** Client sends a request to a load balancer that queries the registry (e.g., AWS ALB with ECS).

#### 3.6.5 Distributed Communication Challenges
- **Partial failures:** Some calls may succeed while others fail – idempotency and compensating transactions (Saga) are required.
- **Eventual consistency:** Data across services may be temporarily inconsistent; business logic must tolerate it.
- **Observability:** Distributed tracing (Jaeger, Zipkin) and centralised logging become essential.

## 4. CDN (Content Delivery Network)

### 4.1 CDN Fundamentals

#### 4.1.1 What Is a CDN?
A geographically distributed network of proxy servers that cache content close to end users. Instead of every request travelling to the central origin, the CDN serves cached copies from nearby **edge locations**.

<img width="572" height="1024" alt="image" src="https://github.com/user-attachments/assets/1f2b36dc-0e5c-4f57-acc4-15f473fdb32c" />

#### 4.1.2 CDN Architecture
```
User → DNS → CDN → (optional: Load Balancer) → Application Servers → Database
```
The CDN sits between the user and your origin servers. It caches responses and serves them from the edge.

#### 4.1.3 Edge Servers
Servers located close to end users (hundreds to thousands of locations worldwide). They cache content and handle requests with minimal latency.

#### 4.1.4 Origin Servers
The original source of the content (your application servers or cloud storage). Edge servers fetch from origin on cache misses and cache the response.

### 4.2 Benefits of CDN

| Benefit | Explanation |
|---------|-------------|
| **Lower latency** | Content travels a shorter distance, reducing round‑trip time. |
| **Higher throughput** | CDNs use high‑bandwidth networks and parallel connections. |
| **Reduced origin load** | Edge caches absorb most requests. |
| **High availability** | If one edge fails, another takes over. |
| **DDoS protection** | CDNs can absorb large‑scale attacks. |
| **Global reach** | Instantly serve content worldwide. |
| **Cost efficiency** | Serving bytes from CDN is often cheaper than from your own infra. |
| **SSL/TLS offload** | HTTPS termination at the edge reduces CPU load on origin. |

#### 4.2.1 Reduced Latency
By serving content from a nearby edge server, latency drops from hundreds of milliseconds to single digits.

#### 4.2.2 Origin Offloading
The edge absorbs the majority of requests, drastically reducing traffic to the origin (app servers, databases).

#### 4.2.3 Higher Throughput
CDNs leverage high‑bandwidth backbone networks, parallel connections, and optimised TCP stacks.

#### 4.2.4 DDoS Protection
Massive distributed infrastructure can absorb volumetric attacks and filter malicious traffic at the edge.

#### 4.2.5 Global Availability
Built‑in redundancy across many points of presence ensures content is always reachable, even during regional outages.

### 4.3 CDN Caching Strategies

#### 4.3.1 TTL-Based Caching
Each cached object has a fixed expiration time. After TTL, the edge re‑validates with the origin. Used for static assets that change rarely.

#### 4.3.2 Cache Purging
Manually or programmatically remove specific URLs or directories from the CDN cache. Used when content must be updated immediately (e.g., a corrected news article).

#### 4.3.3 Versioned URLs
Change the URL when content changes (e.g., `style.v2.css`). No invalidation needed – the new URL is treated as a fresh object. Best practice for static assets.

#### 4.3.4 Cache Hierarchy
```
User → Edge Cache (L1) → Regional Cache (L2) → Origin Shield (L3) → Origin
```
Multi‑tier caching reduces requests to the origin, with the origin shield acting as a final aggregating cache layer.

#### 4.3.5 Origin Shield
A dedicated cache layer that absorbs simultaneous cache misses from many edge nodes, preventing a stampede on the origin.

#### 4.3.6 Cache Warming
Pre‑load the CDN cache with popular content before traffic arrives (e.g., before a product launch or seasonal event).

#### 4.3.7 Dynamic Content Caching (Edge Side Includes – ESI)
Cache parts of a page while leaving dynamic fragments uncached. Example: Cache the product page HTML but mark the user’s cart as a dynamic fragment to be fetched fresh.

### 4.4 Advanced CDN Concepts

#### 4.4.1 Edge Computing
Run lightweight code at the CDN edge without going back to origin. Enables A/B testing, JWT validation, redirect logic, and more, with ultra‑low latency.

#### 4.4.2 Geo-Targeting
Serve different content based on user location. CDN can use `Vary` headers or edge logic to return localised pricing, language, or legal notices.

#### 4.4.3 Lambda@Edge / Workers
AWS Lambda@Edge and Cloudflare Workers allow you to execute serverless functions at the edge, handling requests/responses before they reach the origin or after they leave it.

#### 4.4.4 CDN Security
- **WAF (Web Application Firewall):** Filter malicious requests.
- **Signed URLs/Cookies:** Restrict access to private content.
- **Bot management:** Mitigate scraping and automated attacks.

#### 4.4.5 Cache Poisoning Risks
Attackers may try to inject malicious content into the CDN cache. Mitigation: strict input validation, authentication of purge requests, and signed URLs.

### 4.5 CDN Use Cases

#### 4.5.1 Static Asset Delivery
Images, CSS, JS, fonts – immutable, cached with long TTL. Example: Netflix thumbnails, Shopify product images.

#### 4.5.2 Video Streaming
Large media files chunked and delivered via CDN edges. YouTube and Netflix serve almost all video traffic through CDNs.

#### 4.5.3 API Acceleration
Cache identical API responses (e.g., top headlines) at the edge for a short TTL to reduce origin load.

#### 4.5.4 URL Shortener Optimization
Cache 301/302 redirects at the edge. Popular short URLs return redirects in <10ms without hitting the origin. Lambda@Edge can look up short codes directly.

#### 4.5.5 Global Applications
Any application with a worldwide audience benefits from the CDN’s distributed presence for both static assets and cacheable dynamic content.

### 4.6 CDN Challenges

| Challenge | Mitigation |
|-----------|-------------|
| **Stale content** | Short TTLs, purge API, versioned URLs |
| **Cache stampede** | Origin shield, stale‑while‑revalidate, randomised TTLs |
| **Cost** | Tiered caching, optimise hit ratio, volume discounts |
| **Geo‑restrictions** | CDN geo‑blocking (403 for disallowed regions) |
| **Security** | HTTPS, WAF, signed URLs, bot management |
| **Cache poisoning** | Validate origin responses, authenticate purges |

#### 4.6.1 Stale Content
CDN may serve outdated content if TTL is too long or purge fails. Mitigate with appropriate TTLs and purge API.

#### 4.6.2 Cache Misses
First request to a new URL always misses. Mitigate with cache warming and pre‑fetching.

#### 4.6.3 Regional Inconsistencies
Edges in different regions may serve different versions after a purge due to propagation delays. Use short TTLs and global purge APIs.

#### 4.6.4 Cache Stampedes
After TTL expiry, many edges may simultaneously request the same object, overwhelming the origin. Mitigate with origin shield and stale‑while‑revalidate.

### 4.7 Popular CDN Providers

| Provider | Key Features |
|----------|--------------|
| **Cloudflare** | Free tier, strong security (DDoS, WAF), Workers for edge compute |
| **Akamai** | Enterprise‑grade, massive network, advanced security |
| **AWS CloudFront** | Deep AWS integration (S3, EC2, Lambda@Edge), pay‑as‑you‑go |
| **Fastly** | Real‑time purging, edge computing (VCL), ideal for dynamic content |

## 5. Scaling Patterns by System Type

### 5.1 Summary: Scaling Techniques by Pattern

| Pattern | System Type | Primary Scaling Techniques | Typical Bottleneck & Solution |
|---------|-------------|----------------------------|-------------------------------|
| **A: CRUD / Storage** | URL shortener, Pastebin, Bookstore, Ticket booking, Dropbox | Horizontal app servers, read replicas, caching (Redis), sharding by key, CDN | DB reads → read replicas / cache; DB writes → sharding |
| **B: Feed / Timeline** | Twitter, Instagram, Facebook, LinkedIn | Push/pull fan‑out, timeline caching (Redis), sharding by user_id, Kafka ingestion | Celebrity user → hybrid fan‑out (push for active, pull for inactive) |
| **C: Real‑Time** | WhatsApp, Slack, Live Comments, Game backend | Sharding by conversation/channel, WebSocket connection pooling, Kafka, consistent hashing | Connection overhead → horizontal scaling of WebSocket servers |
| **D: Search** | Google Search, YouTube Search, Amazon Search, Autocomplete | Inverted index sharding (by term), CDN, stream processing for real‑time updates, trie sharding | Index size → shard by term; query latency → caching top queries |
| **E: Geospatial** | Uber, Google Maps, Food Delivery, Nearby Friends | Geohash sharding, Redis Geo, Kafka for location updates, spatial indexing (PostGIS) | Location writes → Kafka + stream processor; reads → geohash index |
| **F: Financial** | Payment gateway, Wallet, UPI, Job scheduler | Sharding by merchant/user, idempotency cache (Redis), optimistic locking, saga with Kafka | Write contention → sharding + optimistic locking; cross‑service → sagas |
| **G: Large‑Scale Processing** | Analytics, Clickstream, Logging, Metrics, Kafka‑like | Kafka partitions, stream processing (Flink), columnar storage (BigQuery), hot‑warm‑cold tiers | Ingestion throughput → Kafka partitioning; query speed → columnar / pre‑aggregation |
| **H: Collaborative & Infrastructure** | Google Docs, Figma, Distributed locks, Snowflake, Web Crawler | Sharding by document_id, CRDTs (no central lock), etcd/Raft cluster, Bloom filter, consistent hashing | Coordination overhead → CRDTs / leaderless; lock contention → small Raft cluster |

---

### 5.2 Pattern A – CRUD & Storage Systems (Scaling Focus)

| Question | Scaling Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **URL Shortener** | Horizontal scaling (stateless app servers), caching (Redis), read replicas, auto‑scaling | Redirects are read‑heavy (100:1 read/write). Cache reduces DB load. Auto‑scale for viral spikes. | App servers behind LB; Redis cluster for short code → long URL; DynamoDB with read replicas; auto‑scaling based on CPU or cache hit rate. |
| **Pastebin** | Object storage (S3), CDN, horizontal scaling for API | Writes are infrequent, reads are high. CDN offloads static content. | Paste content stored in S3; CDN caches popular pastes; API servers scale horizontally for metadata. |
| **Online Bookstore** | Read replicas (product catalog), sharding (by product ID), caching (Redis) | Catalog reads >> writes. Inventory writes need strong consistency but can be sharded. | Catalog: read replicas of PostgreSQL. Inventory: shard by product_id. Redis cache for product details (TTL 5 min). |
| **Ticket Booking** | Horizontal scaling (stateless booking service), sharding (by event_id), optimistic locking | Seat availability updates are high‑contention. Sharding reduces contention per event. | Shard by event_id across multiple DB nodes. Use optimistic locking (version column) within each shard. |
| **Dropbox / Google Drive** | Chunking + content‑addressed storage, CDN for downloads, metadata sharding | Large files broken into chunks for parallel upload/download. Metadata scales with user count. | File chunks stored in S3 (or similar). Metadata (file tree) sharded by user_id in a NoSQL DB (Cassandra). |

---

### 5.3 Pattern B – Feed / Timeline Systems (Scaling Focus)

| Question | Scaling Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Twitter / X** | Fan‑out (push/pull hybrid), timeline caching, sharding by user_id, Kafka for ingestion | High write volume (tweets). Push fan‑out for active users, pull for inactive. | Use Redis for user timeline cache. Shard tweet storage by user_id. Kafka for tweet ingestion; stream processor updates timelines. |
| **Instagram Feed** | CDN for images, read replicas for feed queries, sharding by user_id | Media is large and read‑heavy. Feed generation can be asynchronous. | Images served via CDN. Feed service uses Cassandra sharded by user_id. Pre‑generate feed for top users. |
| **Facebook News Feed** | Edge caching (CDN), pre‑computed feed for active users, sharding by user_id | News feed is personalised and expensive to compute on the fly. | Pre‑compute feed asynchronously (Spark) and store in a distributed cache (Memcached) sharded by user_id. |
| **LinkedIn Feed** | Pull‑based feed aggregation, caching of profiles, sharding by user_id | Lower update frequency; pull model scales with read volume. | On read, query last N posts from followed users (capped). Use Redis for profile cache. Shard post storage by user_id. |

---

### 5.4 Pattern C – Real-Time Systems (Scaling Focus)

| Question | Scaling Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **WhatsApp / Messenger** | Sharding by conversation_id, WebSocket connection manager (horizontal scaling), message queue | Each conversation is independent. Sharding isolates load. | Use consistent hashing to assign conversations to chat servers. Kafka for async message delivery. |
| **Slack / Discord** | Sharding by channel_id, read replicas for channel history, Redis for presence | Channels are natural shards. Presence updates are high frequency but low consistency. | Channel history stored in PostgreSQL sharded by channel_id. Presence in Redis with TTL. |
| **Live Comments (YouTube Live)** | Kafka for ingestion, stream processing (Flink), Redis for recent comments | High throughput (millions of comments per minute). Stream processing aggregates and orders. | Kafka partitions by video_id. Flink windowed aggregation. Redis sorted set for recent comments per video. |
| **Multiplayer Game Backend** | Consistent hashing for game room assignment, UDP for low latency, state sharding | Each game room is a stateful shard. UDP reduces overhead. | Assign rooms using consistent hashing (e.g., Ketama). Room state in memory with periodic checkpoint to DB. |

---

### 5.5 Pattern D – Search Systems (Scaling Focus)

| Question | Scaling Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Google Search** | Inverted index sharding (by term), batch processing for index building, caching of popular queries | Index is huge. Sharding by term allows parallel query execution. | Index sharded across many nodes (e.g., by term hash). Query router fans out. Cache top queries in Redis. |
| **YouTube Search** | Similar to Google Search, plus video metadata caching | Video metadata changes less frequently. | Index sharded. Metadata cache (Redis) for video titles, views, etc. |
| **Amazon Product Search** | Faceted search with sharding by product category, inventory‑aware ranking | Different categories have different sizes. Inventory updates are frequent. | Shard by product category. Inventory cache (Redis) updated synchronously. Search returns combined results. |
| **Search Autocomplete** | Trie sharding (by prefix), CDN for static suggestions, stream processing for trending terms | Trie is large; sharding reduces memory per node. | Shard by first character or consistent hash of prefix. Update trie asynchronously from query logs. |

---

### 5.6 Pattern E – Geospatial & Location-Based Systems (Scaling Focus)

| Question | Scaling Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Uber / Lyft** | Geohash‑based sharding for driver location, Redis for real‑time matching, Kafka for event ingestion | Driver locations are high‑volume. Sharding by geohash keeps related drivers together. | Driver location updates written to Kafka; stream processor updates Redis geohash index. Ride request queries nearby drivers via geohash. |
| **Google Maps / Nearby Places** | Spatial indexing (Geohash, QuadTree), CDN for map tiles, read replicas for place data | Places data is read‑heavy. Map tiles are static. | Use PostgreSQL with PostGIS and geohash indexing. CDN for map tiles. Read replicas for place search. |
| **Food Delivery (DoorDash)** | Similar to Uber, plus restaurant menu caching (Redis), order assignment sharding | Restaurants are less dynamic than drivers. | Driver assignment same as Uber. Menu cache with TTL. Order assignment sharded by restaurant_id. |
| **Nearby Friends** | Geohash indexing, Redis for real‑time location, rate‑limiting updates | Location updates frequent (every few seconds). Redis is fast. | Each user’s location stored in Redis geohash. Queries use geohash range. Rate limit updates per user (e.g., 1 per second). |

---

### 5.7 Pattern F – Financial & Critical Consistency Systems (Scaling Focus)

| Question | Scaling Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Payment Gateway (Stripe)** | Sharding by merchant_id, idempotency cache (Redis), read replicas for reporting | Payment volume can be high. Sharding isolates merchants. | Shard payment ledger by merchant_id. Idempotency keys stored in Redis (TTL). Reporting reads from replicas. |
| **Wallet System** | Sharding by user_id, optimistic locking, in‑memory balance cache | Balance updates are high‑contention but per user. | Shard by user_id. Use Redis for balance cache with CAS. DB as source of truth with version column. |
| **UPI-like System** | Sharding by bank_id, distributed transaction coordinator (rare), Kafka for reconciliation | Cross‑bank transactions are less frequent. Sharding by bank isolates. | Use saga pattern with Kafka for compensating transactions. Shard ledgers by bank. |
| **Distributed Job Scheduler** | Sharding by job_id, leader election (Raft), persistent queue (Kafka) | Jobs can be partitioned. Leader coordinates. | Use etcd for leader election. Job queue in Kafka partitioned by job_type. Workers consume from partitions. |

---

### 5.8 Pattern G – Large-Scale Processing & Infrastructure (Scaling Focus)

| Question | Scaling Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Google Analytics** | Sharding by customer_id, columnar storage (BigQuery), stream processing (Flink) | Each customer’s data is independent. Columnar storage for analytics queries. | Ingest events via Kafka. Flink writes to BigQuery partitioned by date and customer_id. |
| **Clickstream Processing** | Kafka with many partitions, Flink for sessionisation, Redis for session state | High volume. Sessions need state. | Kafka partitions by user_id. Flink keyed by user_id, storing session state in RocksDB. Output to Druid. |
| **Distributed Logging (ELK)** | Kafka buffer, Elasticsearch sharding (by time), hot‑warm‑cold storage | Logs are time‑based. Sharding by date allows easy rollover. | Logs → Kafka → Logstash → Elasticsearch index per day. Use hot nodes for recent logs, warm for older. |
| **Metrics Monitoring (Prometheus)** | Sharding by metric name, sidecar scraping, remote storage (Thanos) | Millions of metrics. Sharding reduces load. | Prometheus servers scrape subsets (sharded by instance). Thanos aggregates queries across shards. |
| **Design Kafka-like System** | Partitioning by key, replication (leader‑follower), auto‑balancing of partitions | Partitions are the unit of parallelism. | Use consistent hashing to assign partitions to brokers. Raft for leader election. Auto‑rebalance on broker addition/removal. |

---

### 5.9 Pattern H – Collaborative & Infrastructure Utilities (Scaling Focus)

| Question | Scaling Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|-------------------|-------------------|----------------------------|
| **Google Docs** | WebSocket servers (horizontal scaling), CRDT merging without central lock, sharding by document_id | Each document is independent. CRDTs avoid coordination. | Document state stored in memory on WebSocket servers. Shard documents by document_id (consistent hashing). CRDT operations broadcast to all collaborators. |
| **Figma / Collaborative Whiteboard** | Similar to Google Docs, plus CDN for static assets, Redis for presence | Whiteboard objects are many. CRDTs for each object. | WebSocket servers sharded by document_id. CRDT operations are small and frequent. Use Redis for presence (last seen). |
| **Distributed Locking Service** | etcd/ZooKeeper cluster (3 or 5 nodes), sharding by lock key (via consensus) | Locks are strongly consistent. Small cluster is enough. | Use Raft‑based system. Lock keys are stored in a log. Fencing tokens prevent split‑brain. |
| **Unique ID Generator (Snowflake)** | Worker ID assignment via etcd, each worker generates IDs locally (no coordination) | High throughput (millions per second). No sharding needed. | Worker IDs from etcd (atomic increment). Each worker uses timestamp + worker_id + sequence. |
| **Web Crawler** | URL frontier sharded by domain, Bloom filter for deduplication, many worker nodes | Crawling is embarrassingly parallel. Sharding by domain respects politeness. | Frontier in Redis sorted set, sharded by domain hash. Workers pull URLs. Bloom filter in Redis shared across workers. |

---

## 6. Scalability Decision Frameworks

### 6.1 Choosing the Right Scaling Strategy

#### 6.1.1 Read-Heavy vs Write-Heavy
- **Read‑heavy:** Add caching (Redis, CDN), read replicas. Reads can be scaled almost linearly with replication.
- **Write‑heavy:** Use sharding, asynchronous write‑ahead logs, and eventually write‑optimised databases (e.g., Cassandra). A message queue can buffer write bursts.

#### 6.1.2 Steady vs Spiky Traffic
- **Steady traffic:** Predictable load; can use reserved instances, fixed capacity, and simpler auto‑scaling with a small buffer. Vertical scaling may still be cost‑effective.
- **Spiky traffic:** Requires elastic horizontal scaling, pre‑warming, and buffering with queues or streaming platforms to absorb sudden bursts.

#### 6.1.3 Latency vs Throughput
- **Low latency:** Requires cache layers (in‑memory, CDN), edge compute, and minimising network hops. Often achieved with vertical scaling of critical paths.
- **High throughput:** Focus on parallelisation, horizontal scaling, and asynchronous processing. Latency per operation may be relaxed, but total operations per second must be maximised.

#### 6.1.4 Consistency vs Scalability
- **Strong consistency** (ACID) limits horizontal scaling. Use it where correctness is paramount (financial ledgers) and scale vertically or with careful partitioning.
- **Eventual consistency** (BASE) allows massive horizontal scaling and is suitable for social feeds, analytics, etc. Choose based on business needs.

#### 6.1.5 Cost vs Performance
- Using top‑tier hardware, abundant caching, and cross‑region active‑active setups improves performance but increases cost.
- Lean architectures use auto‑scaling, spot/preemptible instances, and serverless to match cost to actual demand. Define SLOs and rightsize.

### Decision Table – Scenario → Technique

| Scenario | Technique | Example Product |
|----------|-----------|----------------|
| Sudden traffic spike on a news site | Horizontal + auto‑scaling | CNN during election night |
| Users complain about slow product image loads | CDN + in‑memory cache | Shopify product pages |
| Database CPU at 100% due to reads | Read replicas + caching | Reddit front page |
| Database cannot keep up with writes (e.g., chat messages) | Sharding + message queue | WhatsApp message persistence |
| A single query runs for 5 seconds and is run 1000x/sec | Query result cache | Facebook friend suggestions |
| Need to process 1M video uploads per day, each takes 1 min | Async queue + worker pool | TikTok upload pipeline |
| Startup with 100 users, expects 200 next month | Vertical scaling | Small B2B SaaS |
| Global e‑commerce site with 10M products, search queries vary wildly | Sharding + CDN + replicas | Amazon product catalog |

### 6.2 Common Anti-Patterns

#### 6.2.1 Stateful Application Servers
Storing user sessions, local files, or caches inside the application server makes it impossible to scale horizontally without sticky sessions or data loss. Always externalise state to a shared store (Redis, DB).

#### 6.2.2 Database as Bottleneck
Treating a single database as the sole source of truth for both reads and writes. Under load, this becomes a bottleneck. Use read replicas, caching, and sharding early.

#### 6.2.3 Missing Cache Layer
Serving every request directly from the database, even for rarely changing data, wastes resources and increases latency. Add caching layers for repeated reads.

#### 6.2.4 Hot Partitions
A poorly chosen sharding key can cause uneven data distribution, where one shard receives a disproportionate amount of traffic. Mitigate by using a high‑cardinality key or directory‑based sharding with dynamic rebalancing.

#### 6.2.5 Synchronous Everything
Blocking on external services, databases, or APIs for every request can exhaust thread pools and cause cascading failures. Use asynchronous processing and non‑blocking I/O where possible.

#### 6.2.6 No Backpressure Handling
When a downstream component cannot keep up, the lack of backpressure causes unbounded queue growth, memory exhaustion, and eventual crash. Implement flow control and load shedding.

## 7. Interview-Oriented HLD Notes

### 7.1 Key Interview Topics

#### 7.1.1 Scaling Bottleneck Identification
Quickly identify where the system will break first under increased load. Common bottlenecks: database writes, single points of failure, bandwidth, CPU‑bound services, or shared resources (locks). Use back‑of‑the‑envelope calculations to estimate limits.

#### 7.1.2 Throughput Estimation
Estimate requests per second (RPS), data volume per request, and bandwidth. For example, a URL shortener with 500 million writes per month translates to ~200 writes/sec and ~2000 reads/sec (10:1 ratio). These numbers drive capacity planning.

#### 7.1.3 Capacity Planning
Based on throughput estimates, determine the number of application servers, database shards, cache size, and network bandwidth required. Include headroom for peak traffic (e.g., 2x–5x average).

#### 7.1.4 Tradeoff Discussions
Explicitly discuss tradeoffs: SQL vs NoSQL, synchronous vs asynchronous, strong vs eventual consistency. Justify your choices based on the system’s functional and non‑functional requirements.

#### 7.1.5 Failure Scenarios
Design for common failures: server crashes, network partitions, regional outages, and slow dependencies. Explain how the system degrades gracefully (e.g., stale reads from replicas, queued writes) and how it recovers automatically.

### 7.2 Quick Revision Material

#### 7.2.1 Quick Selection Guide

| If your bottleneck is… | Start with… |
|------------------------|--------------|
| Too many reads | Replication, Caching |
| Too many writes | Sharding (Horizontal Scaling) |
| Single server maxed out (CPU/RAM) | Vertical Scaling (short‑term), Sharding (long‑term) |
| Too many connections | Connection Pooling |
| Slow queries due to joins | Denormalization |
| Huge table scans | Partitioning |

#### 7.2.2 Key Interview Sentences

> *"I'd scale the application tier horizontally — each server is stateless, with sessions stored in Redis. The load balancer distributes with Least Connections since requests vary in duration."*

> *"For the database, vertical scaling gets us to a point, then we introduce read replicas to offload 80% of read traffic. Writes still go to a single primary, but if writes become the bottleneck, we'd shard by user_id."*

> *"Auto-scaling is configured on CPU utilization. But since JVM warm-up takes ~60 seconds, we keep a minimum of 3 instances to handle unexpected spikes while new instances boot."*

> *“For scalability in this system, I'd start with horizontal scaling behind a load balancer, add read replicas and Redis caching for read-heavy queries, use a message queue to handle write spikes asynchronously, and auto-scale based on CPU or queue depth. For the data layer, if writes exceed single-node capacity, I'd shard on a high-cardinality key like user_id or tenant_id.”*

#### 7.2.3 Self-Test Questions

1. You have a stateful login service — users' sessions are in memory. What's the problem when you add a second server? What's the fix?
2. You're getting 10× traffic on Black Friday (predictable spike). What do you configure in your auto-scaler to avoid degradation when the spike hits?
3. You need to route `/api/*` to backend servers, `/uploads/*` to upload servers, and `/` to frontend servers. Do you need Layer 4 or Layer 7 load balancing?
4. Your app servers are at 90% CPU. Before adding more servers, what should you check first?
5. Why can't you just vertically scale a database forever?

#### 7.2.4 Architecture Cheat Sheet

| System Type | Primary Technique | Secondary Technique |
|-------------|-------------------|---------------------|
| CRUD / Storage | Horizontal app + caching | Read replicas, CDN |
| Feed / Timeline | Fan‑out (push/pull) + caching | Sharding by user ID |
| Real‑Time | Sharding by conversation/channel | Async message queues |
| Search | Sharding by term / index partitioning | Caching top queries |
| Geospatial | Geohash sharding | Kafka for location updates |
| Financial / Critical | Sharding + idempotency cache | Optimistic locking, Saga |
| Large‑Scale Processing | Kafka partitioning + stream processing | Columnar storage |
| Collaborative / Infra | Sharding by document + CRDTs | Raft cluster for coordination |

---

*End of consolidated notes.*
