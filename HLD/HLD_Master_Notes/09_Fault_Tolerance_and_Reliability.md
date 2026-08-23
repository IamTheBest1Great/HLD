# Fault Tolerance & Reliability — Consolidated HLD Notes

## Table of Contents

- [1. Reliability & Fault Tolerance Foundations](#1-reliability--fault-tolerance-foundations)
  - [1.1 Core Principles](#11-core-principles)
    - [1.1.1 Redundancy](#111-redundancy)
    - [1.1.2 Isolation](#112-isolation)
    - [1.1.3 Failure Detection](#113-failure-detection)
    - [1.1.4 Failure Masking](#114-failure-masking)
    - [1.1.5 Automatic Recovery](#115-automatic-recovery)
    - [1.1.6 Graceful Degradation](#116-graceful-degradation)
    - [1.1.7 Fast Recovery](#117-fast-recovery)
    - [1.1.8 Statelessness](#118-statelessness)
    - [1.1.9 Idempotency](#119-idempotency)
- [2. Core Reliability Patterns](#2-core-reliability-patterns)
  - [2.1 Circuit Breaker](#21-circuit-breaker)
    - [2.1.1 Why Use It](#211-why-use-it)
    - [2.1.2 States (Closed, Open, Half-Open)](#212-states-closed-open-half-open)
    - [2.1.3 Preventing Cascading Failures](#213-preventing-cascading-failures)
    - [2.1.4 Retry Storm Prevention](#214-retry-storm-prevention)
    - [2.1.5 Implementation Considerations](#215-implementation-considerations)
    - [2.1.6 Real-World Examples](#216-real-world-examples)
  - [2.2 Retry with Backoff & Jitter](#22-retry-with-backoff--jitter)
    - [2.2.1 Why Retries Matter](#221-why-retries-matter)
    - [2.2.2 Naive Retry Problems](#222-naive-retry-problems)
    - [2.2.3 Exponential Backoff](#223-exponential-backoff)
    - [2.2.4 Adding Jitter](#224-adding-jitter)
    - [2.2.5 What to Retry vs What NOT to Retry](#225-what-to-retry-vs-what-not-to-retry)
    - [2.2.6 Bounded Backoff](#226-bounded-backoff)
    - [2.2.7 Implementation Considerations](#227-implementation-considerations)
    - [2.2.8 Real-World Examples](#228-real-world-examples)
  - [2.3 Bulkhead Pattern](#23-bulkhead-pattern)
    - [2.3.1 Concept](#231-concept)
    - [2.3.2 Resource Isolation Approaches](#232-resource-isolation-approaches)
    - [2.3.3 Thread Pool / Connection Pool Isolation](#233-thread-pool--connection-pool-isolation)
    - [2.3.4 Implementation Considerations](#234-implementation-considerations)
    - [2.3.5 Real-World Examples](#235-real-world-examples)
  - [2.4 Health Checks & Failure Detection](#24-health-checks--failure-detection)
    - [2.4.1 Why Health Checks Matter](#241-why-health-checks-matter)
    - [2.4.2 Liveness vs Readiness](#242-liveness-vs-readiness)
    - [2.4.3 Heartbeats & Watchdogs](#243-heartbeats--watchdogs)
    - [2.4.4 Health Check Flow](#244-health-check-flow)
    - [2.4.5 Implementation (Express.js, Kubernetes)](#245-implementation-expressjs-kubernetes)
    - [2.4.6 Real-World Examples](#246-real-world-examples)
  - [2.5 Rate Limiting](#25-rate-limiting)
    - [2.5.1 Why Rate Limiting Matters](#251-why-rate-limiting-matters)
    - [2.5.2 Token Bucket](#252-token-bucket)
    - [2.5.3 Leaky Bucket](#253-leaky-bucket)
    - [2.5.4 Fixed Window Counter](#254-fixed-window-counter)
    - [2.5.5 Sliding Window Log](#255-sliding-window-log)
    - [2.5.6 Sliding Window Counter](#256-sliding-window-counter)
    - [2.5.7 Distributed Rate Limiting](#257-distributed-rate-limiting)
    - [2.5.8 Implementation Considerations](#258-implementation-considerations)
    - [2.5.9 Real-World Examples](#259-real-world-examples)
  - [2.6 Idempotency](#26-idempotency)
    - [2.6.1 Why Idempotency Matters](#261-why-idempotency-matters)
    - [2.6.2 Making Non-Idempotent Operations Safe](#262-making-non-idempotent-operations-safe)
    - [2.6.3 Idempotency Keys](#263-idempotency-keys)
    - [2.6.4 Real-World Examples](#264-real-world-examples)
  - [2.7 Timeouts](#27-timeouts)
    - [2.7.1 Connection Timeout](#271-connection-timeout)
    - [2.7.2 Read Timeout](#272-read-timeout)
    - [2.7.3 Deadline Propagation](#273-deadline-propagation)
    - [2.7.4 Timeout Best Practices](#274-timeout-best-practices)
  - [2.8 Fallback & Graceful Degradation](#28-fallback--graceful-degradation)
    - [2.8.1 Cached Response Fallback](#281-cached-response-fallback)
    - [2.8.2 Default Response Fallback](#282-default-response-fallback)
    - [2.8.3 Feature Disabling](#283-feature-disabling)
    - [2.8.4 Partial Availability Strategies](#284-partial-availability-strategies)
  - [2.9 Load Shedding](#29-load-shedding)
    - [2.9.1 Why Load Shedding Matters](#291-why-load-shedding-matters)
    - [2.9.2 Priority-Based Shedding](#292-priority-based-shedding)
    - [2.9.3 Queue-Based Shedding](#293-queue-based-shedding)
    - [2.9.4 Adaptive Load Shedding](#294-adaptive-load-shedding)
  - [2.10 Dead Letter Queue (DLQ)](#210-dead-letter-queue-dlq)
    - [2.10.1 Why DLQ Matters](#2101-why-dlq-matters)
    - [2.10.2 Retry Exhaustion Handling](#2102-retry-exhaustion-handling)
    - [2.10.3 Poison Message Isolation](#2103-poison-message-isolation)
    - [2.10.4 Monitoring DLQs](#2104-monitoring-dlqs)
  - [2.11 Redundancy & Replication](#211-redundancy--replication)
    - [2.11.1 Active-Active Redundancy](#2111-active-active-redundancy)
    - [2.11.2 Active-Passive Redundancy](#2112-active-passive-redundancy)
    - [2.11.3 Leader Election](#2113-leader-election)
    - [2.11.4 Replication with Quorum](#2114-replication-with-quorum)
    - [2.11.5 State Machine Replication](#2115-state-machine-replication)
    - [2.11.6 Checkpointing](#2116-checkpointing)
    - [2.11.7 Redundant Hardware](#2117-redundant-hardware)
    - [2.11.8 Redundant Data Paths](#2118-redundant-data-paths)
  - [2.12 Hedged & Redundant Requests](#212-hedged--redundant-requests)
    - [2.12.1 Hedged Requests](#2121-hedged-requests)
    - [2.12.2 Parallel Request Execution](#2122-parallel-request-execution)
    - [2.12.3 Tail Latency Reduction](#2123-tail-latency-reduction)
  - [2.13 Failover Strategies](#213-failover-strategies)
    - [2.13.1 Automatic Failover](#2131-automatic-failover)
    - [2.13.2 DNS Failover](#2132-dns-failover)
    - [2.13.3 Regional Failover](#2133-regional-failover)
    - [2.13.4 Fast Recovery Techniques](#2134-fast-recovery-techniques)
  - [2.14 Eliminating Single Points of Failure (SPOF)](#214-eliminating-single-points-of-failure-spof)
    - [2.14.1 Infrastructure SPOFs](#2141-infrastructure-spofs)
    - [2.14.2 Database SPOFs](#2142-database-spofs)
    - [2.14.3 Network SPOFs](#2143-network-spofs)
    - [2.14.4 Dependency SPOFs](#2144-dependency-spofs)
- [3. Reliability Engineering Techniques](#3-reliability-engineering-techniques)
  - [3.1 Monitoring & Alerting](#31-monitoring--alerting)
    - [3.1.1 Metrics](#311-metrics)
    - [3.1.2 Logging](#312-logging)
    - [3.1.3 Distributed Tracing](#313-distributed-tracing)
    - [3.1.4 Proactive Alerting](#314-proactive-alerting)
  - [3.2 Chaos Engineering](#32-chaos-engineering)
    - [3.2.1 Purpose of Chaos Testing](#321-purpose-of-chaos-testing)
    - [3.2.2 Failure Injection](#322-failure-injection)
    - [3.2.3 Network Partition Testing](#323-network-partition-testing)
    - [3.2.4 Dependency Failure Testing](#324-dependency-failure-testing)
  - [3.3 Reliability Metrics](#33-reliability-metrics)
    - [3.3.1 Availability](#331-availability)
    - [3.3.2 SLA / SLO / SLI](#332-sla--slo--sli)
    - [3.3.3 MTBF](#333-mtbf)
    - [3.3.4 MTTR](#334-mttr)
    - [3.3.5 Error Budget](#335-error-budget)
- [4. Reliability & Fault Tolerance in HLD](#4-reliability--fault-tolerance-in-hld)
  - [4.1 Decision Tables](#41-decision-tables)
    - [4.1.1 Scenario → Fault Tolerance Technique](#411-scenario--fault-tolerance-technique)
    - [4.1.2 Scenario → Reliability Technique](#412-scenario--reliability-technique)
  - [4.2 Application Archetype Mapping](#42-application-archetype-mapping)
    - [4.2.1 CRUD & Storage Systems](#421-crud--storage-systems)
    - [4.2.2 Feed / Timeline Systems](#422-feed--timeline-systems)
    - [4.2.3 Real-Time Systems](#423-real-time-systems)
    - [4.2.4 Search Systems](#424-search-systems)
    - [4.2.5 Geospatial & Location-Based Systems](#425-geospatial--location-based-systems)
    - [4.2.6 Financial & Strong Consistency Systems](#426-financial--strong-consistency-systems)
    - [4.2.7 Large-Scale Processing Systems](#427-large-scale-processing-systems)
    - [4.2.8 Collaborative & Utility Systems](#428-collaborative--utility-systems)
  - [4.3 Common Anti-Patterns](#43-common-anti-patterns)
    - [4.3.1 Infinite Retries](#431-infinite-retries)
    - [4.3.2 Missing Timeouts](#432-missing-timeouts)
    - [4.3.3 Shared Resource Contention](#433-shared-resource-contention)
    - [4.3.4 Retry Storms](#434-retry-storms)
    - [4.3.5 No Backpressure](#435-no-backpressure)
    - [4.3.6 Single Region Dependency](#436-single-region-dependency)
    - [4.3.7 Non-Idempotent Retries](#437-non-idempotent-retries)
    - [4.3.8 No Graceful Degradation](#438-no-graceful-degradation)
  - [4.4 Interview-Oriented Topics](#44-interview-oriented-topics)
    - [4.4.1 How Patterns Work Together](#441-how-patterns-work-together)
    - [4.4.2 Designing a Resilient Service](#442-designing-a-resilient-service)
    - [4.4.3 Quick Selection Guide — Which Technique to Use?](#443-quick-selection-guide---which-technique-to-use)
    - [4.4.4 Key Interview Sentences](#444-key-interview-sentences)
    - [4.4.5 Self-Test Questions](#445-self-test-questions)
    - [4.4.6 HLD Summary Cheat Sheet](#446-hld-summary-cheat-sheet)

- [5. Real-World System Pattern Playbook](#5-real-world-system-pattern-playbook)
  - [5.1 Summary: Fault Tolerance & Reliability Techniques by Pattern](#51-summary-fault-tolerance--reliability-techniques-by-pattern)
  - [5.2 Pattern A – CRUD & Storage Systems (Fault Tolerance & Reliability)](#52-pattern-a---crud--storage-systems-fault-tolerance--reliability)
  - [5.3 Pattern B – Feed / Timeline Systems (Fault Tolerance & Reliability)](#53-pattern-b---feed--timeline-systems-fault-tolerance--reliability)
  - [5.4 Pattern C – Real-Time Systems (Fault Tolerance & Reliability)](#54-pattern-c---real-time-systems-fault-tolerance--reliability)
  - [5.5 Pattern D – Search Systems (Fault Tolerance & Reliability)](#55-pattern-d---search-systems-fault-tolerance--reliability)
  - [5.6 Pattern E – Geospatial & Location-Based Systems (Fault Tolerance & Reliability)](#56-pattern-e---geospatial--location-based-systems-fault-tolerance--reliability)
  - [5.7 Pattern F – Financial & Critical Consistency Systems (Fault Tolerance & Reliability)](#57-pattern-f---financial--critical-consistency-systems-fault-tolerance--reliability)
  - [5.8 Pattern G – Large-Scale Processing & Infrastructure (Fault Tolerance & Reliability)](#58-pattern-g---large-scale-processing--infrastructure-fault-tolerance--reliability)
  - [5.9 Pattern H – Collaborative & Infrastructure Utilities (Fault Tolerance & Reliability)](#59-pattern-h---collaborative--infrastructure-utilities-fault-tolerance--reliability)
---

## 1. Reliability & Fault Tolerance Foundations

**Fault tolerance** is the ability of a system to continue operating correctly (possibly at reduced performance) when one or more of its components fail. **Reliability** is the probability that a system will function without failure for a specified period – measured by MTBF (Mean Time Between Failures) and MTTR (Mean Time To Repair).

### 1.1 Core Principles

#### 1.1.1 Redundancy
Adding extra components beyond the minimum required. If one fails, the others continue. Forms: N+1, 2N, active‑active, active‑passive.

#### 1.1.2 Isolation
Partitioning resources (thread pools, connections, memory) so that a failure in one component does not cascade and exhaust resources for others. Bulkhead pattern.

#### 1.1.3 Failure Detection
Continuously monitoring components via health checks, heartbeats, and watchdogs to quickly identify failures.

#### 1.1.4 Failure Masking
Hiding failures from end users by switching to redundant components, serving cached data, or falling back to default responses.

#### 1.1.5 Automatic Recovery
The system autonomously recovers from failures without human intervention – e.g., restarting a failed process, promoting a standby replica.

#### 1.1.6 Graceful Degradation
When a component fails, only non‑critical features are disabled while core functionality continues. The system remains available, though with reduced capabilities.

#### 1.1.7 Fast Recovery
Minimizing MTTR through automated failover, checkpointing, and statelessness. Fast recovery directly improves reliability and availability.

#### 1.1.8 Statelessness
Designing services to hold no local state. Any instance can handle any request; failed instances can be replaced instantly without state recovery.

#### 1.1.9 Idempotency
Designing operations so that executing them multiple times has the same effect as once. This allows safe retries without side effects, critical for reliability.

---

## 2. Core Reliability Patterns

### 2.1 Circuit Breaker

#### 2.1.1 Why Use It
Prevents repeated calls to a failing service, avoiding cascading failures and resource exhaustion. Instead of waiting for timeouts, the circuit breaker fails fast.

#### 2.1.2 States (Closed, Open, Half-Open)
- **Closed:** Normal operation. Requests pass through. Failures counted.
- **Open:** Failures exceed threshold → circuit opens; requests fail immediately.
- **Half‑Open:** After a timeout, a few test requests probe the service. Success → Closed; failure → Open.

#### 2.1.3 Preventing Cascading Failures
When a downstream service is slow or down, without a circuit breaker all calling threads can block, exhausting resources and bringing down upstream services. The breaker opens and returns errors instantly, protecting the caller.

#### 2.1.4 Retry Storm Prevention
In combination with retries, the circuit breaker prevents retry storms by failing fast instead of adding to the load on a struggling service.

#### 2.1.5 Implementation Considerations
- Failure threshold (e.g., 50% error rate over a sliding window).
- Reset timeout (how long before attempting half‑open).
- Max requests in half‑open state.
- Which exceptions count as failures (timeouts, 5xx).

#### 2.1.6 Real-World Examples
Netflix Hystrix / Resilience4j, AWS SDK circuit breakers. Payment gateway uses circuit breaker for external bank calls – if >50% failures, circuit opens, and the system shows “try again later.”

---

### 2.2 Retry with Backoff & Jitter

#### 2.2.1 Why Retries Matter
Transient failures (network blips, temporary overload) often succeed on a subsequent attempt. Retries mask these from users, improving perceived reliability.

#### 2.2.2 Naive Retry Problems
Retrying immediately and at the same time from many clients creates thundering herds, overloading the recovering service.

#### 2.2.3 Exponential Backoff
Increasing delay between retries: `delay = base × 2^attempt`. Spreads out retries over time.

#### 2.2.4 Adding Jitter
Add randomness to the backoff delay: `delay = random(0, base × 2^attempt)`. Prevents synchronized retries from many clients.

#### 2.2.5 What to Retry vs What NOT to Retry
- **Retry:** Network timeouts, 429, 503, 500 (transient).
- **Do NOT retry:** 400, 401, 404 (client errors), non‑idempotent operations without idempotency keys.

#### 2.2.6 Bounded Backoff
Cap the maximum delay (e.g., 32 seconds) and the maximum number of retries to avoid infinite waiting.

#### 2.2.7 Implementation Considerations
- Use idempotency keys for non‑idempotent writes.
- Separate retry budgets per downstream service.

#### 2.2.8 Real-World Examples
AWS SDK: up to 3 retries with exponential backoff and jitter. Stripe API clients use idempotency keys and retry on network errors.

---

### 2.3 Bulkhead Pattern

#### 2.3.1 Concept
Isolates resources so that a failure (or overload) in one component cannot exhaust resources for others – like ship compartments.

#### 2.3.2 Resource Isolation Approaches
- Separate thread pools per dependency.
- Separate connection pools per database/service.
- Bounded queues for each downstream.

#### 2.3.3 Thread Pool / Connection Pool Isolation
If the search service becomes slow, only threads assigned to search are blocked; payment and profile services continue unaffected.

#### 2.3.4 Implementation Considerations
- Size pools based on expected concurrency.
- Rejected tasks can use fallback or fail fast.

#### 2.3.5 Real-World Examples
Netflix Hystrix thread‑pool isolation. Kubernetes resource limits as bulkheads at the infrastructure level.

---

### 2.4 Health Checks & Failure Detection

#### 2.4.1 Why Health Checks Matter
Load balancers and orchestrators need to know whether an instance can serve traffic. Unhealthy instances are removed from rotation.

#### 2.4.2 Liveness vs Readiness
- **Liveness:** Is the process alive? Fail → restart container.
- **Readiness:** Is the service ready to handle requests? Fail → remove from load balancer.

#### 2.4.3 Heartbeats & Watchdogs
Nodes send periodic “I’m alive” signals. Missed heartbeats trigger failure detection and recovery.

#### 2.4.4 Health Check Flow
```
Load Balancer → every 10s GET /health/ready
  → 200: keep in rotation
  → 503: remove from rotation
Kubernetes livenessProbe → restart if failed
```

#### 2.4.5 Implementation (Express.js, Kubernetes)
```javascript
app.get('/health', (req, res) => {
  if (db.connected && cache.connected) res.status(200).send('OK');
  else res.status(503).send('Unavailable');
});
```

#### 2.4.6 Real-World Examples
AWS ELB health checks, Kubernetes probes, Consul health checks.

---

### 2.5 Rate Limiting

#### 2.5.1 Why Rate Limiting Matters
Protects services from overload, abuse, and DDoS attacks. Ensures fair usage among clients.

#### 2.5.2 Token Bucket
Tokens are added at a fixed rate. Each request consumes a token. If bucket empty → reject (429). Allows bursts up to bucket capacity.

#### 2.5.3 Leaky Bucket
Requests enter a queue and are processed at a fixed rate. If queue full → drop. Smooths output.

#### 2.5.4 Fixed Window Counter
Count requests per fixed time window (e.g., 1 minute). Exceeding limit → reject. Simple but has boundary spike problem.

#### 2.5.5 Sliding Window Log
Stores timestamps of all requests. On each request, count within sliding window. Accurate but memory‑intensive.

#### 2.5.6 Sliding Window Counter
Hybrid: uses fixed windows but approximates sliding window via weighted counts. Low memory, good accuracy.

#### 2.5.7 Distributed Rate Limiting
Multiple app servers need a shared counter. Use Redis with atomic operations (INCR + TTL) to enforce global limits.

#### 2.5.8 Implementation Considerations
- Key: user ID, IP, API key.
- Response: HTTP 429, `Retry-After` header.
- Burst allowance via token bucket capacity.

#### 2.5.9 Real-World Examples
AWS API Gateway, Cloudflare, Stripe. Rate limiting in API gateways with Redis backend.

---

### 2.6 Idempotency

#### 2.6.1 Why Idempotency Matters
At‑least‑once delivery semantics can cause duplicate processing. Idempotency ensures retries don’t produce duplicate side effects (double charges, duplicate orders).

#### 2.6.2 Making Non-Idempotent Operations Safe
Use idempotency keys. Client generates a unique key per operation. Server stores the key and its result; re‑using the key returns the stored result without re‑executing.

#### 2.6.3 Idempotency Keys
```
Client: POST /payment {idempotency_key: "pay_abc123"}
Server: Check key in DB/Redis; if found → return stored result;
        else process, store result + key, return.
```

#### 2.6.4 Real-World Examples
Stripe, PayPal – all payment APIs use idempotency keys.

---

### 2.7 Timeouts

#### 2.7.1 Connection Timeout
Maximum time to establish a connection. If exceeded, fail fast.

#### 2.7.2 Read Timeout
Maximum wait for a response after connection. Prevents hanging on slow/stuck services.

#### 2.7.3 Deadline Propagation
Passing a deadline across services so that downstream calls know the overall time budget. Ensures resources are freed if the original request has timed out.

#### 2.7.4 Timeout Best Practices
- Set a timeout for every network call.
- Use reasonable values: 1‑2s for internal, 5‑10s for external.
- Combine with retries and circuit breakers.
- Propagate deadlines with trace context.

---

### 2.8 Fallback & Graceful Degradation

#### 2.8.1 Cached Response Fallback
When a service fails, return cached (possibly stale) data instead of an error.

#### 2.8.2 Default Response Fallback
Return a safe default value (empty list, default avatar) when the actual data is unavailable.

#### 2.8.3 Feature Disabling
Use feature toggles to disable non‑critical features when their dependencies are unhealthy, preserving core functionality.

#### 2.8.4 Partial Availability Strategies
The system continues to operate with reduced quality – e.g., lower resolution video, simplified recommendations.

---

### 2.9 Load Shedding

#### 2.9.1 Why Load Shedding Matters
When the system is overloaded, dropping non‑critical requests prevents total collapse and protects critical paths.

#### 2.9.2 Priority-Based Shedding
Assign priorities to requests; drop low‑priority ones (e.g., analytics, logs) first.

#### 2.9.3 Queue-Based Shedding
When the request queue is full, reject new entries instead of accepting them and causing unbounded growth.

#### 2.9.4 Adaptive Load Shedding
Dynamically adjust shedding based on current load, error rates, and latency, using algorithms like Concurrency Limits (Netflix).

---

### 2.10 Dead Letter Queue (DLQ)

#### 2.10.1 Why DLQ Matters
After repeated processing failures, messages should not be lost or infinitely retried. DLQ isolates them for inspection and manual remediation.

#### 2.10.2 Retry Exhaustion Handling
After max retries (e.g., 5), move the message to DLQ. The main queue keeps moving.

#### 2.10.3 Poison Message Isolation
A malformed message that always fails (poison pill) is moved to DLQ to prevent blocking the entire queue.

#### 2.10.4 Monitoring DLQs
Set alerts on DLQ depth to detect and fix issues early.

---

### 2.11 Redundancy & Replication

#### 2.11.1 Active-Active Redundancy
All nodes serve traffic simultaneously. Failure of one node → others continue without interruption. Used for stateless services and some databases.

#### 2.11.2 Active-Passive Redundancy
One active node handles traffic; one or more standbys are ready. On failure, a standby is promoted. There may be a brief failover time.

#### 2.11.3 Leader Election
In distributed systems, a leader is elected using consensus (Raft, Paxos). If the leader fails, a new one is elected automatically.

#### 2.11.4 Replication with Quorum
Data is replicated to N nodes. Writes require acknowledgment from W nodes, reads from R nodes. With W+R > N, strong consistency is achieved; can tolerate (N-1)/2 failures.

#### 2.11.5 State Machine Replication
All replicas execute the same sequence of deterministic operations. If the leader fails, a follower has identical state and can take over.

#### 2.11.6 Checkpointing
Periodic snapshots of state are saved to durable storage. On failure, the system restores from the latest checkpoint and replays operations, enabling exactly‑once processing.

#### 2.11.7 Redundant Hardware
Duplicate power supplies, NICs, disks (RAID), ECC memory. Hardware failures are masked at the physical level.

#### 2.11.8 Redundant Data Paths
Multiple network links, BGP anycast, SD‑N. A single fiber cut does not disconnect the system.

---

### 2.12 Hedged & Redundant Requests

#### 2.12.1 Hedged Requests
Send the same request to multiple replicas. Whichever responds first wins; cancel the others. Masks slow or tail‑latency nodes.

#### 2.12.2 Parallel Request Execution
Requests are dispatched concurrently; the first successful response is used. Increases reliability against transient failures.

#### 2.12.3 Tail Latency Reduction
In large systems, a small percentage of requests are slow (P99 latency). Hedged requests dramatically reduce the long tail.

---

### 2.13 Failover Strategies

#### 2.13.1 Automatic Failover
The system detects a failure and promotes a standby without human intervention – e.g., Raft leader election, PostgreSQL with Patroni.

#### 2.13.2 DNS Failover
DNS health checks detect region failure and update records to point to another region (e.g., Route 53 failover routing).

#### 2.13.3 Regional Failover
Active‑active multi‑region setup: if one region fails, traffic is routed to the healthy region. Services must be stateless or have cross‑region replication.

#### 2.13.4 Fast Recovery Techniques
- Pre‑warmed standbys.
- Connection draining and re‑routing.
- Stateless services (instant replacement).

---

### 2.14 Eliminating Single Points of Failure (SPOF)

#### 2.14.1 Infrastructure SPOFs
Single load balancer, single power feed, single network switch → add redundancy (N+1, active‑passive, dual uplinks).

#### 2.14.2 Database SPOFs
Single primary DB without replicas → use leader‑follower replication with automatic failover.

#### 2.14.3 Network SPOFs
Single ISP or network path → multi‑homing, BGP, redundant links.

#### 2.14.4 Dependency SPOFs
Relying on a single external API without a fallback → circuit breaker + cache + alternative provider.

---

## 3. Reliability Engineering Techniques

### 3.1 Monitoring & Alerting

#### 3.1.1 Metrics
Collect system metrics (CPU, memory, request rate, error rate, latency). Use dashboards (Grafana).

#### 3.1.2 Logging
Centralized structured logs for debugging and auditing. Tools: ELK, Loki.

#### 3.1.3 Distributed Tracing
Trace requests across microservices to identify bottlenecks and failures. Tools: Jaeger, Zipkin.

#### 3.1.4 Proactive Alerting
Set alerts on SLO breaches, high error rates, or resource exhaustion. Alert fatigue is avoided by tuning thresholds.

---

### 3.2 Chaos Engineering

#### 3.2.1 Purpose of Chaos Testing
Intentionally inject failures to verify that the system remains reliable. Finds weaknesses before real outages do.

#### 3.2.2 Failure Injection
Kill processes, terminate instances, corrupt network packets, exhaust memory – simulate real‑world failures.

#### 3.2.3 Network Partition Testing
Isolate services from each other to test how the system behaves under partition. Validates CAP tradeoffs and failover logic.

#### 3.2.4 Dependency Failure Testing
Make downstream services slow or unresponsive and observe if circuit breakers, timeouts, and fallbacks work correctly.

---

### 3.3 Reliability Metrics

#### 3.3.1 Availability
Percentage of time the system is operational: `A = MTBF / (MTBF + MTTR)`.

#### 3.3.2 SLA / SLO / SLI
- **SLI:** Service Level Indicator (e.g., request latency P99).
- **SLO:** Target for SLI (e.g., 99.9% of requests < 200ms).
- **SLA:** Contractual promise, often with penalties.

#### 3.3.3 MTBF
Mean Time Between Failures – average time a component runs before failing.

#### 3.3.4 MTTR
Mean Time To Repair – average time to detect and recover from a failure.

#### 3.3.5 Error Budget
The allowable unreliability (1 – SLO). If the error budget is exhausted, feature releases may be frozen to focus on reliability.

---

## 4. Reliability & Fault Tolerance in HLD

### 4.1 Decision Tables

#### 4.1.1 Scenario → Fault Tolerance Technique
| Scenario | Primary Technique | Secondary |
|----------|-------------------|-----------|
| Stateless web server crash | Active‑active + LB health checks | Auto‑scaling |
| Database primary fails | Leader election + sync replication | Read replicas |
| Stream processor crash | Checkpointing + state machine replication | Exactly‑once semantics |
| Slow external API | Circuit breaker + retry + fallback | Bulkheading |
| Multi‑tenant noisy neighbor | Bulkheading per tenant | Rate limiting |
| Tail latency spikes | Hedged requests | Timeout + retry to different replica |
| Long‑running business transaction | Saga with compensating actions | Idempotency keys |
| Single hardware failure | Redundant hardware (RAID, dual PSU) | Watchdog process restart |
| Malicious/corrupt nodes (blockchain) | Byzantine Fault Tolerance | Quorum N ≥ 3f+1 |
| Feature‑rich website partial outage | Graceful degradation + feature toggles | Circuit breaker per feature |

#### 4.1.2 Scenario → Reliability Technique
| Scenario | Primary Technique | Why |
|----------|-------------------|-----|
| Web server crashes every 30 days | Redundant instances + health checks | Masks individual failures |
| Disk failure after 3 years | RAID 10 + regular backups | Tolerates disk loss |
| Fiber cut | Redundant network paths + BGP failover | Traffic reroutes |
| External API timeouts | Timeouts + retry with exponential backoff | Masks transient failures |
| Service restart – thundering herd | Jitter + circuit breaker | Prevents retry storm |
| New unstable feature | Feature flags + graceful degradation | Disable without full rollback |
| Global 99.999% uptime requirement | Multi‑region active‑active + chaos testing | Survive region failure; validated |
| Financial transaction zero loss | Idempotency keys + synchronous replication | Safe retries; durable |

---

### 4.2 Application Archetype Mapping

#### 4.2.1 CRUD & Storage Systems
Active‑passive DB failover, read replicas, retries with idempotency, health checks. Database primary crash → automatic failover to standby; overload → rate limiting + circuit breaker.

#### 4.2.2 Feed / Timeline Systems
Active‑active web servers, cache replication (Redis Cluster), Kafka ISR, fan‑out retries, graceful degradation. Cache node failure → replica promotes; Kafka broker failure → leader election; show stale cache if needed.

#### 4.2.3 Real-Time Systems
WebSocket connection draining, state checkpointing, Kafka replication, hedged requests, circuit breakers. Chat server crash → clients reconnect and state reloaded from Kafka; live comment spikes → Kafka buffers.

#### 4.2.4 Search Systems
Index replication, query router retries, CDN fallback, circuit breakers for inventory, timeout + fallback. Index node down → router retries to replica; inventory slow → return stale inventory.

#### 4.2.5 Geospatial & Location-Based Systems
Redis Sentinel failover, Kafka replay, driver location re‑broadcast, geohash index replication. Redis master fail → Sentinel promotes replica; driver location loss → replay from Kafka.

#### 4.2.6 Financial & Strong Consistency Systems
Synchronous replication (Raft), idempotency + retries, circuit breakers to banks, checkpointing, saga compensation. DB node failure → sync replica takes over (zero data loss); external bank slow → circuit breaker opens.

#### 4.2.7 Large-Scale Processing Systems
Kafka replication (ISR), Flink checkpointing, Elasticsearch replica shards, Prometheus HA pairs, exactly‑once semantics. Flink task failure → recover from checkpoint; Kafka leader fail → ISR elects new leader.

#### 4.2.8 Collaborative & Utility Systems
WebSocket server redundancy (consistent hashing), etcd Raft cluster, lock fencing tokens, worker retries, Bloom filter fallback. etcd leader failure → new leader elected; WebSocket server down → clients reconnect.

---

### 4.3 Common Anti-Patterns

#### 4.3.1 Infinite Retries
Retrying without bound can amplify load and cause cascading failures. Use max retries + backoff + circuit breaker.

#### 4.3.2 Missing Timeouts
Every network call without a timeout risks hanging the thread indefinitely and exhausting resources.

#### 4.3.3 Shared Resource Contention
A single thread pool for all downstream dependencies allows one slow service to starve all others. Use bulkheads.

#### 4.3.4 Retry Storms
Synchronized retries from many clients after a failure can overwhelm the recovering service. Add jitter.

#### 4.3.5 No Backpressure
When consumers can’t keep up, unbounded queues lead to memory exhaustion. Use bounded queues, rate limiting, load shedding.

#### 4.3.6 Single Region Dependency
A regional outage takes down the entire system. Deploy across multiple regions with automatic failover.

#### 4.3.7 Non-Idempotent Retries
Retrying payment or order creation without idempotency keys leads to duplicates.

#### 4.3.8 No Graceful Degradation
A minor feature failure brings down the whole application. Design fallbacks for non‑critical paths.

---

### 4.4 Interview-Oriented Topics

#### 4.4.1 How Patterns Work Together
- Health checks detect failures.
- Circuit breaker stops calls to a failing service.
- Retry with backoff handles transient errors before tripping the breaker.
- Bulkheads isolate failures so other services remain healthy.
- Rate limiting protects the system from overload.
- Idempotency ensures retries are safe.
- Timeouts prevent resource exhaustion.
- Fallbacks and load shedding preserve core functionality under stress.
- Redundancy and checkpointing provide recovery paths.
- Monitoring and chaos engineering validate the design.

#### 4.4.2 Designing a Resilient Service
A resilient service:
- Is stateless or has externalized state (Redis).
- Uses load balancers with health checks.
- Implements circuit breakers and bulkheads for all downstream calls.
- Retries with exponential backoff and jitter, with idempotency keys for writes.
- Sets aggressive timeouts and propagates deadlines.
- Employs rate limiting to prevent abuse.
- Has fallback logic (cache, default) for every dependency.
- Deploys across multiple availability zones/regions with automatic failover.
- Is continuously tested with chaos engineering.

#### 4.4.3 Quick Selection Guide — Which Technique to Use?
| Problem | Technique |
|---------|-----------|
| Temporary network glitch | Retry (with backoff) |
| Downstream service failing | Circuit Breaker |
| One component hogging resources | Bulkhead |
| Dependency unavailable but need something | Fallback |
| Slow or stuck remote call | Timeouts |
| Poison message / unprocessable event | Dead Letter Queue |
| Too many requests from client | Rate Limiting |
| System overloaded | Load Shedding |
| Partial outage – keep main features | Graceful Degradation |
| Single point of failure | Redundancy |
| Know which instances are alive | Health Checks |
| Primary server crashed | Failover |

#### 4.4.4 Key Interview Sentences
> *"I’d put a rate limiter using the token bucket algorithm in front of the API. Per‑user limits enforced via Redis atomic operations. On 429, clients retry with exponential backoff and jitter."*

> *"Every downstream call from the app server goes through a circuit breaker. If the recommendation service has 50% error rate for 10 seconds, the circuit opens and we fast‑fail with cached recommendations instead of timing out."*

> *"Payment operations use idempotency keys – the client generates a UUID per attempt. The server checks Redis before processing; retries return the stored result without re‑charging."*

> *"To ensure reliability, I focus on prevention (redundancy, statelessness, high‑MTBF components), detection (health checks, heartbeats, monitoring), and recovery (retries with backoff, circuit breakers, idempotency, checkpointing). Chaos engineering continuously validates these mechanisms."*

#### 4.4.5 Self-Test Questions
1. What is the boundary spike problem with fixed window rate limiting? How does sliding window fix it?
2. A circuit breaker is in the OPEN state. A request comes in – what happens? After 30 seconds, what does it do?
3. You’re retrying a failed API call. 10,000 clients all retry at exactly the same time (no jitter). What happens to the recovering server?
4. A payment API is not idempotent. A user’s client times out and retries the payment. What goes wrong? How do idempotency keys fix it?
5. Your app server has 100 threads shared across calls to Search, Payment, and Profile services. Search goes down and all threads are blocked waiting. What pattern solves this? How?

#### 4.4.6 HLD Summary Cheat Sheet
| Pattern | Purpose | Key Points |
|---------|---------|------------|
| Circuit Breaker | Prevent cascading failures | States: Closed, Open, Half‑Open; fail fast |
| Retry + Backoff + Jitter | Handle transient failures | Exponential delay, randomness, idempotency |
| Bulkhead | Isolate failures | Separate thread/connection pools |
| Health Checks | Failure detection | Liveness vs readiness; heartbeat |
| Rate Limiting | Protect against overload | Token bucket, sliding window, Redis |
| Idempotency | Safe retries | Idempotency keys, dedup store |
| Timeouts | Free resources | Connection/read timeout, deadline propagation |
| Fallback | Maintain availability | Cached/default responses, feature disable |
| Load Shedding | Overload protection | Drop non‑critical requests |
| DLQ | Isolate poison messages | Move after max retries, alerting |
| Redundancy | Eliminate SPOF | Active‑active/passive, leader election, replication |
| Hedged Requests | Reduce tail latency | Send to multiple, first wins |
| Failover | Automatic recovery | DNS, regional, leader promotion |
| Chaos Engineering | Validate reliability | Inject failures, observe behavior |

## 5. Real-World System Pattern Playbook

### 5.1 Summary: Fault Tolerance & Reliability Techniques by Pattern

| Pattern | System Type | Primary FT/Reliability Techniques | Key Failure Scenarios & Mitigation |
|---------|-------------|-----------------------------------|-------------------------------------|
| **A: CRUD / Storage** | URL shortener, Pastebin, Bookstore, Ticket booking, Dropbox | Active‑passive DB failover, read replicas, retries with idempotency, health checks, backup/restore | DB primary crash → automatic failover to standby; overload → rate limiting + circuit breaker |
| **B: Feed / Timeline** | Twitter, Instagram, Facebook, LinkedIn | Active‑active web servers, cache replication (Redis Cluster), Kafka ISR, fan‑out retries, graceful degradation | Cache node failure → replica promotes; Kafka broker failure → leader election; slow feed → show stale cache |
| **C: Real‑Time** | WhatsApp, Slack, Live Comments, Game backend | WebSocket connection draining, state checkpointing, Kafka replication, hedged requests, circuit breakers | Chat server crash → clients reconnect (state reloaded from Kafka); live comment spike → Kafka buffers |
| **D: Search** | Google Search, YouTube Search, Amazon Search, Autocomplete | Index replication, query router retries, CDN fallback, circuit breakers for inventory, timeout + fallback | Index node down → router retries to replica; inventory service slow → return stale inventory |
| **E: Geospatial** | Uber, Google Maps, Food Delivery, Nearby Friends | Redis Sentinel failover, Kafka replay, driver location re‑broadcast, geohash index replication, client‑side retries | Redis master fail → Sentinel promotes replica; driver location loss → replay from Kafka |
| **F: Financial** | Payment gateway, Wallet, UPI, Job scheduler | Synchronous replication (Raft), idempotency + retries, circuit breakers to banks, checkpointing, saga compensation | DB node failure → synchronous replica takes over (zero data loss); external bank slow → circuit breaker opens |
| **G: Large‑Scale Processing** | Analytics, Clickstream, Logging, Metrics, Kafka‑like | Kafka replication (ISR), Flink checkpointing, Elasticsearch replica shards, Prometheus HA pairs, exactly‑once semantics | Flink task failure → recover from checkpoint; Kafka leader fail → ISR elects new leader |
| **H: Collaborative & Infrastructure** | Google Docs, Figma, Distributed locks, Snowflake, Web Crawler | WebSocket server redundancy (consistent hashing), etcd Raft cluster, lock fencing tokens, worker retries, Bloom filter fallback | etcd leader failure → new leader elected; WebSocket server down → clients reconnect to another server |

---

### 5.2 Pattern A – CRUD & Storage Systems (Fault Tolerance & Reliability)

| Question | FT / Reliability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|----------------------------|-------------------|----------------------------|
| **URL Shortener** | **Active‑passive DB failover** (RDS Multi‑AZ); **Read replicas** for redirects; **Retries with idempotency** on create; **Health checks** on LB; **Circuit breaker** for cache | Redirects must never fail; DB primary failure should not cause downtime; retries on create must not duplicate short codes. | RDS Multi‑AZ with automatic failover (<60s). Read replicas handle redirects during failover. Idempotency key on `POST /shorten` to avoid duplicates. Circuit breaker on Redis – fallback to DB. |
| **Pastebin** | **S3 11‑nines durability**; **CDN origin failover**; **API server auto‑scaling**; **Background expiry worker retries** | S3 is highly durable; CDN caches even if origin down; expiry must eventually delete. | S3 Cross‑Region Replication. CloudFront with multiple origins. Expiry worker uses SQS with visibility timeout and retries on failure. |
| **Online Bookstore** | **Read replicas** for catalog (stale OK); **Synchronous replication** for inventory DB; **Circuit breaker** for payment gateway; **Timeouts** on external calls | Catalog availability is critical; inventory must not lose writes; payment gateway can be slow. | Inventory: PostgreSQL with synchronous streaming to 2 replicas. Circuit breaker on payment calls (fail fast, retry later). Timeout = 2s. |
| **Ticket Booking** | **Leader election** (Raft) for booking DB; **Optimistic locking** retries; **Seat lock TTL** + expiry queue; **Health checks** on booking service | Seat reservations must be consistent even during failover; locks auto‑expire. | CockroachDB (Raft) for automatic failover. Optimistic locking: retry on version conflict. Redis `SETEX` for seat locks; delayed SQS for expiry. |
| **Dropbox / Google Drive** | **Metadata DB with multi‑region active‑active** (Cassandra); **File chunks in S3** (durable); **Client‑side retries** with exponential backoff; **Conflict resolution** (CRDTs) | Metadata must survive region failure; file chunks are durable by design; sync conflicts must be resolved. | Cassandra with RF=3 across regions. S3 CRR. Client upload retries with backoff (max 5). CRDTs for file tree conflicts (last‑write‑wins with version vectors). |

---

### 5.3 Pattern B – Feed / Timeline Systems (Fault Tolerance & Reliability)

| Question | FT / Reliability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|----------------------------|-------------------|----------------------------|
| **Twitter / X** | **Active‑active web servers**; **Redis Cluster with replicas**; **Kafka ISR** (`min.insync.replicas=2`); **Fan‑out retries**; **Graceful degradation** (show stale feed) | Tweets must be delivered even if some services fail. Users accept slightly stale feeds. | Redis Cluster: each master has replica; on failover, replica promotes. Kafka: `acks=all`. Fan‑out workers retry failed deliveries (up to 3 times). If cache fails, fallback to DB (slower but works). |
| **Instagram Feed** | **CDN for images** (highly available); **Feed service with read replicas**; **Cache replication** (Memcached); **Client‑side retries** | Images are served from CDN. Feed can be stale but not unavailable. | Memcached client with retry and failover to another node. Feed DB (Cassandra) with RF=3. CDN origin shield for high availability. Client retries image load on error. |
| **Facebook News Feed** | **Edge caching (CDN)**; **Pre‑computed feed in replicated cache**; **Compute pipelines with retries**; **Chaos engineering** (test failures) | News feed is pre‑computed; if cache fails, fallback to a slightly older version. | Memcached cluster with consistent hashing and node replication. Spark pipeline writes to multiple cache nodes. Chaos Monkey randomly kills cache nodes in staging. |
| **LinkedIn Feed** | **Pull‑based feed** (no fan‑out failure); **Profile cache replication**; **Read replicas for posts**; **Timeouts** on aggregation | Pull model is more available because it doesn't depend on pre‑computation. | Redis Cluster for profile cache. Post DB (sharded PostgreSQL) with read replicas. Feed aggregation times out after 500ms and returns partial results. |

---

### 5.4 Pattern C – Real-Time Systems (Fault Tolerance & Reliability)

| Question | FT / Reliability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|----------------------------|-------------------|----------------------------|
| **WhatsApp / Messenger** | **WebSocket connection manager with consistent hashing**; **Kafka replication** (ISR); **Chat server state recovery** from Kafka; **Client‑side reconnect** | Chat servers crash – users must reconnect and not lose messages. | Use consistent hashing with virtual nodes. On server failure, clients reconnect to next server; state is reloaded from Kafka (persisted messages). Kafka `min.insync.replicas=2`. |
| **Slack / Discord** | **Channel history replicated (Cassandra RF=3)**; **Redis Sentinel for presence**; **WebSocket connection draining**; **Circuit breaker for file uploads** | Channel history is critical; presence can be stale. | Cassandra with RF=3 across AZs. Redis Sentinel for presence failover. On deployment, drain connections gracefully (wait for in‑flight messages). Circuit breaker on S3 uploads. |
| **Live Comments (YouTube Live)** | **Kafka with ISR**; **Flink with checkpointing**; **Redis Sentinel for comment cache**; **Client‑side retry with backoff** | Comments must flow even if some Kafka brokers or Flink tasks fail. | Kafka: `min.insync.replicas=2`, `acks=all`. Flink checkpointing to S3 (every 10s). Redis Sentinel for comment cache failover. Client sends comment with idempotency key; retries on timeout. |
| **Multiplayer Game Backend** | **Game room state checkpointing** (every 5s to DynamoDB); **Consistent hashing for room assignment**; **UDP (no connection state)**; **Hedged requests** for matchmaking | Game room server crash – players reconnect to a new server, state restored from checkpoint. | Assign rooms via consistent hashing. Periodic checkpoint to DynamoDB. UDP allows quick reconnect. Hedged requests: send matchmaking request to two servers, take first response. |

---

### 5.5 Pattern D – Search Systems (Fault Tolerance & Reliability)

| Question | FT / Reliability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|----------------------------|-------------------|----------------------------|
| **Google Search** | **Index replication (primary/backup shards)**; **Query router with retries**; **CDN for static UI**; **Timeouts** (200ms) | Search must return results even if some index nodes are down. | Each index shard has 2 replicas. Query router tries primary, then replica on timeout/failure. Timeout = 200ms; fallback to cached results. CDN serves search UI. |
| **YouTube Search** | **Elasticsearch replica shards**; **Metadata cache replication (Redis Cluster)**; **Fallback to stale index** | Metadata cache failure should not break search. | Elasticsearch: each shard has replica. Redis Cluster with replicas. On cache miss, fallback to database (slower but available). If index node down, query router uses replica. |
| **Amazon Product Search** | **Inventory cache replication (Redis Sentinel)**; **Search index replication**; **Circuit breaker for inventory service**; **Fallback to stale inventory** | Inventory cache failure – fallback to database (maybe slower but works). | Redis Sentinel for automatic failover. Search index in Elasticsearch with replica shards. Circuit breaker: if inventory service fails, show "check availability later". |
| **Search Autocomplete** | **Trie replication (read‑only copies)**; **CDN for static prefix suggestions**; **Fallback to default suggestions**; **Client‑side cache** | Trie node failure – serve from replica or CDN. | Build trie and push to CDN as JSON. Client requests CDN first; fallback to API server. API server has replicated trie in Redis. Default suggestions if all fail. |

---

### 5.6 Pattern E – Geospatial & Location-Based Systems (Fault Tolerance & Reliability)

| Question | FT / Reliability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|----------------------------|-------------------|----------------------------|
| **Uber / Lyft** | **Redis Sentinel for driver location cache**; **Kafka replication** (RF=3); **Ride matching service active‑active**; **Driver location replay** from Kafka | Driver location updates must be available; stale location is acceptable for a few seconds. | Redis Sentinel (3 nodes). Kafka with RF=3. Ride matching service runs in multiple AZs with idempotent assignment (conditional write to DB). On Redis failover, drivers re‑send locations. |
| **Google Maps / Nearby Places** | **Place DB read replicas (multi‑AZ)**; **Map tiles on CDN** (TTL long); **Geocoding service with retries**; **Circuit breaker for traffic API** | Map tiles are static; place search can use stale data. | Place DB (PostgreSQL) with read replicas in each region. Map tiles on CloudFront. Geocoding calls retry 3 times with backoff. Circuit breaker on traffic API – fallback to historical average. |
| **Food Delivery (DoorDash)** | **Redis Cluster for driver location**; **Restaurant menu cache replication**; **Order assignment with retry queue**; **Health checks on driver app** | Menu cache failure – fallback to DB (read replica). | Redis Cluster with replicas. Restaurant menu DB has read replicas. Order assignment: SQS with visibility timeout; on failure, message returns to queue. Driver app sends heartbeats every 10s. |
| **Nearby Friends** | **Redis Geo with Sentinel**; **Client‑side caching with fallback**; **Location updates idempotent**; **WebSocket reconnect** | Redis master fail – Sentinel promotes replica; client retries. | Redis Sentinel. Clients cache last known locations. On Redis failure, use cached data (stale but available). WebSocket client auto‑reconnects with exponential backoff. |

---

### 5.7 Pattern F – Financial & Critical Consistency Systems (Fault Tolerance & Reliability)

| Question | FT / Reliability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|----------------------------|-------------------|----------------------------|
| **Payment Gateway (Stripe)** | **Synchronous replication (DB with 3 nodes)**; **Idempotency cache replication (Redis Cluster)**; **Circuit breakers to banks**; **Dead letter queue for failed payments** | Financial systems need both consistency and availability. Use synchronous replication to avoid data loss. | DB: PostgreSQL with synchronous replication to 2 standbys. Redis Cluster for idempotency keys. Circuit breaker to external banks (fail fast, retry later). DLQ for unrecoverable failures. |
| **Wallet System** | **DB with leader election (Raft)**; **Balance cache replication (Redis Sentinel)**; **Optimistic locking retries**; **Transaction log in Kafka (replicated)** | Wallet balance must be available and consistent. | Use etcd or CockroachDB for Raft. Redis Sentinel for balance cache. On write conflict, client retries (max 5). Kafka with RF=3 for audit log. |
| **UPI-like System** | **Saga orchestrator with retries and compensating tx**; **Kafka replication**; **Bank idempotency APIs**; **Circuit breaker per bank** | Cross‑bank transactions can retry; use idempotency to avoid double payment. | Kafka with `acks=all`. Saga orchestrator (active‑active with leader election). Circuit breaker per bank (open on >50% failures). Idempotency keys stored in Redis with TTL. |
| **Distributed Job Scheduler** | **etcd cluster (Raft) for leader election**; **Job queue in Kafka with replication**; **Workers with idempotent processing**; **Checkpointing** | Scheduler leader failure – new leader elected via etcd; jobs continue. | etcd (3 or 5 nodes). Kafka with RF=3. Workers store last processed offset in etcd for recovery. Checkpoint every 1000 jobs. |

---

### 5.8 Pattern G – Large-Scale Processing & Infrastructure (Fault Tolerance & Reliability)

| Question | FT / Reliability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|----------------------------|-------------------|----------------------------|
| **Google Analytics** | **Kafka with replication**; **Flink with checkpointing (to S3)**; **Query layer with read replicas**; **Exactly‑once semantics** | Analytics pipeline can recover from failures using checkpoints. | Kafka RF=3. Flink checkpointing every minute to S3. Query layer (Druid) with replica nodes. Exactly‑once via Kafka transactional API and Flink checkpoint. |
| **Clickstream Processing** | **Kafka partitions replicated**; **Flink job recovery from checkpoint**; **Session state in RocksDB with backup**; **Idempotent writes to sink** | Session state is critical for accurate funnels; Flink recovers from checkpoint. | Flink checkpointing to HDFS/S3. RocksDB backup on failure. Kafka `min.insync.replicas=2`. Idempotent writes to Druid using upsert. |
| **Distributed Logging (ELK)** | **Kafka buffer with replication**; **Elasticsearch replica shards**; **Logstash persistent queues**; **Circuit breaker for Elasticsearch** | Log loss is acceptable (eventual). But indexing should survive node failures. | Elasticsearch: each shard has replica. Logstash persistent queue (disk) to survive restart. Kafka RF=2. Circuit breaker: if Elasticsearch slow, Logstash pauses and retries. |
| **Metrics Monitoring (Prometheus)** | **Prometheus HA pairs** (two servers scraping same targets); **Thanos for query federation**; **Long‑term storage in S3**; **Alertmanager HA** | If one Prometheus fails, the other has the same data (short retention). | Run two Prometheus servers with identical config. Thanos deduplicates on query. Alertmanager runs in cluster mode with gossip. S3 storage for historical metrics. |
| **Design Kafka-like System** | **Partition replication (leader‑follower, ISR)**; **Raft for controller**; **Producer `acks=all`**; **Consumer group rebalancing** | Kafka‑like system must tolerate broker failures without data loss. | Each partition has N replicas (e.g., 3). Producer waits for `min.insync.replicas` acks. Controller (Raft) manages broker failover. Consumer group rebalances on member failure. |

---

### 5.9 Pattern H – Collaborative & Infrastructure Utilities (Fault Tolerance & Reliability)

| Question | FT / Reliability Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|----------------------------|-------------------|----------------------------|
| **Google Docs** | **WebSocket servers with consistent hashing and failover**; **CRDT state stored in replicated Redis**; **Document versioning**; **Client‑side operation queue** | Document editing must survive server crash; CRDTs allow recovery. | Use consistent hashing for document assignment. Redis Cluster for document state. On server fail, new server loads state from Redis. Client queues operations during reconnect. |
| **Figma / Collaborative Whiteboard** | **WebSocket server redundancy**; **CRDT merge without central lock**; **Redis Sentinel for presence**; **Asset CDN with fallback** | Whiteboard objects are many; CRDT operations are small. | Same pattern as Google Docs. CDN for fonts, templates. Redis Sentinel for presence (last seen). Client caches operations locally for offline editing. |
| **Distributed Locking Service** | **etcd cluster (Raft) with 3 or 5 nodes**; **Client retries with exponential backoff**; **Fencing tokens**; **Lease TTL** | Locks must be available and consistent. etcd provides both. | etcd cluster across AZs. Client acquires lock via etcd `txn`. Fencing tokens prevent split‑brain. Lease TTL auto‑releases lock on client crash. |
| **Unique ID Generator (Snowflake)** | **Worker ID assignment via etcd (highly available)**; **Local ID generation (no coordination)**; **Cached worker ID on disk** | Worker ID assignment uses etcd; if etcd is down, workers can still generate IDs (use cached worker ID). | etcd cluster (3 nodes). Each worker caches its assigned ID on disk. On etcd failure, continue with cached ID (risk of collision low). ID generation uses monotonic timestamp. |
| **Web Crawler** | **URL frontier in Redis Sentinel**; **Bloom filter in Redis with replicas**; **Workers idempotent**; **Dead letter queue for failed crawls** | Crawler can tolerate worker failures; URLs are re‑assigned. | Redis Sentinel for frontier. Bloom filter replicated. Workers store last crawled URL in DB; on failure, another worker picks up. DLQ for URLs that repeatedly fail. |

---

*End of consolidated notes.*
