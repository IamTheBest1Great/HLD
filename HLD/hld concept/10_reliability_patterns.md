# 10 — Reliability Patterns
> **Why this matters:** Every production system fails. Interviewers always ask "what happens when X fails?" or "how do you protect against traffic spikes?" These patterns are your answers. Without them, your design is a house of cards.

---

## 1. Rate Limiting

Rate limiting controls how many requests a client can make in a time window. It protects your system from abuse, overload, and DDoS.

### Why You Need It

```
WITHOUT RATE LIMITING:
─────────────────────────────────────────────────────────────────
Attacker sends 1,000,000 requests/second to your API
→ Your servers get overwhelmed
→ Legitimate users can't get responses
→ System crashes ❌

WITH RATE LIMITING:
─────────────────────────────────────────────────────────────────
Rate limiter: "IP 1.2.3.4 already sent 100 requests this minute"
→ Return 429 Too Many Requests
→ Legitimate users unaffected ✅
→ System stays healthy ✅
```

### Algorithm 1: Token Bucket (Most Important)

```
CONCEPT:
  Each user gets a "bucket" that holds tokens.
  Tokens are added at a fixed rate (e.g., 10 tokens/second).
  Each request consumes 1 token.
  If bucket is empty → request rejected (429).
  Bucket has a max capacity (burst size).

VISUAL:
  ┌─────────────────────────────────────────────┐
  │           Token Bucket                       │
  │                                             │
  │  Capacity: 100 tokens (max burst)           │
  │  Refill rate: 10 tokens/second              │
  │                                             │
  │  [●][●][●][●][●][●][●][●][ ][ ]            │
  │   8 tokens remaining                        │
  └─────────────────────────────────────────────┘
         ↑                           ↑
   +10 tokens/sec              Request comes in
                                → consume 1 token
                                → if 0 tokens → 429

BURST HANDLING:
  User is idle for 10 seconds → bucket fills to 100 tokens
  User suddenly sends 80 requests → all accepted! (burst allowed)
  81st request → rejected
  10 more seconds → bucket has 100 tokens again

PROS:  Allows bursts (natural user behavior)
CONS:  Two parameters to tune (rate + burst size)
USED BY: AWS API Gateway, Stripe, most major APIs
```

### Algorithm 2: Leaky Bucket

```
CONCEPT:
  Requests go into a queue (the "bucket").
  Queue is processed at a FIXED rate (the "leak").
  If queue is full → request dropped.

VISUAL:
  Requests arrive (any rate)
        │  │  │  │  │  │
        ▼  ▼  ▼  ▼  ▼  ▼
  ┌─────────────────────┐
  │  Queue (capacity=5) │  ← if full, new requests dropped
  │  [R1][R2][R3][R4]  │
  └──────────┬──────────┘
             │ 1 request/second (fixed rate)
             ▼
          Process

PROS:  Smooth, predictable output rate. Good for downstream services.
CONS:  Burst requests are queued (latency) or dropped (if queue full).
       Doesn't allow bursts like Token Bucket.
USED BY: Traffic shaping, QoS in networking.
```

### Algorithm 3: Fixed Window Counter

```
CONCEPT:
  Divide time into fixed windows (e.g., each minute).
  Count requests per window.
  If count exceeds limit → reject.

Timeline:  |──── minute 1 ────|──── minute 2 ────|
Requests:  [1][2][3]...[100]  [1][2][3]...[100]
           ← OK, limit=100 →  ← OK, resets at :00 →

PROBLEM — Edge Case (Boundary Spike):
  Limit = 100/minute
  User sends 100 requests at 12:00:59 (last second of minute 1)
  User sends 100 requests at 12:01:01 (first second of minute 2)
  
  In 2 seconds: user sent 200 requests → both windows say "OK" ❌
  Your backend sees 200 req/2sec = effectively 100 req/sec spike

PROS:  Very simple to implement (just a counter + reset timer).
CONS:  Boundary spike problem.
USED BY: Simple rate limiting where precision doesn't matter much.
```

### Algorithm 4: Sliding Window Log

```
CONCEPT:
  Store timestamp of every request in a log (sorted set in Redis).
  On each request:
    1. Remove all timestamps older than window (e.g., 1 minute ago)
    2. Count remaining timestamps
    3. If count < limit → allow + add timestamp
    4. Else → reject

VISUAL:
  Current time: 12:01:30
  Window: last 60 seconds (12:00:30 to 12:01:30)
  Log: [12:00:31, 12:00:45, 12:01:02, 12:01:15, 12:01:28]
  Count = 5 → if limit=100, allow request

PROS:  Very accurate, no boundary spike problem.
CONS:  Memory intensive (store every request timestamp).
       For high-volume APIs, log can be huge.
USED BY: When precision is important (e.g., security-sensitive APIs).
```

### Algorithm 5: Sliding Window Counter (Best of Both)

```
CONCEPT:
  Hybrid: use fixed windows but approximate a sliding window.
  
  Formula: count = current_window_count + (previous_window_count × overlap_ratio)
  
  Example:
    Limit = 100/minute
    Previous minute count: 80 requests
    Current minute count: 30 requests (in first 30 seconds)
    Overlap ratio = 0.5 (we're 30 seconds into current minute, so prev minute
                         overlaps 50% of our window)
    
    Estimated count = 30 + (80 × 0.5) = 30 + 40 = 70
    70 < 100 → Allow ✅

PROS:  Low memory (only 2 counters per window), accurate enough.
CONS:  Approximate (not exact), slight over/under counting.
USED BY: Cloudflare, most production rate limiters at scale.
```

### Distributed Rate Limiting

```
PROBLEM: You have 10 app servers. Each has its own rate limiter.
  User sends 100 requests distributed across all servers.
  Each server sees 10 requests → all pass! (but should be rejected)

SOLUTION: Centralized rate limit counter in Redis.

  Server 1  ──INCR rate:user:123──►  Redis
  Server 2  ──INCR rate:user:123──►  Redis  ← single source of truth
  Server 3  ──INCR rate:user:123──►  Redis

  Redis uses atomic INCR → no race conditions.
  All servers share the same counter.

IMPLEMENTATION (Redis + Lua for atomicity):
  local count = redis.call('INCR', KEYS[1])
  if count == 1 then
    redis.call('EXPIRE', KEYS[1], ARGV[1])  -- set TTL on first request
  end
  if count > tonumber(ARGV[2]) then
    return 0  -- rate limited
  end
  return 1  -- allowed
```

---

## 2. Circuit Breaker

When a downstream service is failing, the circuit breaker prevents cascading failure by "opening the circuit" and fast-failing instead of waiting.

### The Problem Without Circuit Breaker

```
Payment Service calls Fraud Detection Service

Fraud Detection is slow/down:
  Request 1 → waits 30s → timeout ❌
  Request 2 → waits 30s → timeout ❌
  Request 3 → waits 30s → timeout ❌
  ...1000 requests... all waiting 30s

Meanwhile:
  Payment Service thread pool exhausted (all threads waiting on fraud)
  New payment requests: "No threads available" → crash ❌
  Payment Service goes down because Fraud Service is slow ← CASCADING FAILURE
```

### The Three States

```
     ┌─────────────────────────────────────────────────────┐
     │                                                     │
     │           CIRCUIT BREAKER STATE MACHINE            │
     │                                                     │
     │    ┌──────────┐                                    │
     │    │  CLOSED  │ ← Normal operation                 │
     │    │(pass all)│   All requests go through          │
     │    └────┬─────┘                                    │
     │         │ Failure threshold exceeded                │
     │         │ (e.g., 50% errors in last 60s)           │
     │         ▼                                          │
     │    ┌──────────┐                                    │
     │    │   OPEN   │ ← Fast fail mode                  │
     │    │(fail all)│   All requests rejected immediately │
     │    └────┬─────┘   (no waiting for timeout)         │
     │         │ After reset timeout (e.g., 30s)          │
     │         ▼                                          │
     │  ┌─────────────┐                                   │
     │  │  HALF-OPEN  │ ← Testing mode                   │
     │  │(allow some) │   Let a few requests through      │
     │  └─────┬───────┘                                   │
     │        │                                           │
     │   ┌────┴──────────────┐                           │
     │   │ Success?          │ Failure?                   │
     │   ▼                   ▼                           │
     │  CLOSED             OPEN                          │
     │  (recovered)        (still broken)                │
     └─────────────────────────────────────────────────────┘

CLOSED state: Normal. All requests pass through.
  Tracks: error rate, response time.
  Threshold exceeded → trips to OPEN.

OPEN state: Fast fail. Immediately return error to caller.
  No requests reach the failing service.
  Caller gets instant error (not 30s timeout).
  After "reset timeout" → moves to HALF-OPEN.

HALF-OPEN state: Probe. Send small % of requests to service.
  If those succeed → service recovered → move to CLOSED.
  If those fail → service still broken → move back to OPEN.
```

### Circuit Breaker Example

```
Without Circuit Breaker:
  User pays → Payment waits 30s for Fraud → timeout → user sees spinner → frustrated

With Circuit Breaker:
  First 5 fraud check calls fail → Circuit OPENS
  User pays → Payment sees open circuit → immediately returns:
    "Payment accepted, fraud check pending" (fail-open strategy)
    OR "Service temporarily unavailable, try again" (fail-closed)
  User gets instant response ✅
  Fraud service has time to recover without being hammered
  After 30s → Half-Open → test → if OK, circuit closes ✅
```

---

## 3. Retry with Exponential Backoff and Jitter

When a request fails, retrying immediately often makes things worse. Backoff spreads retries out.

### Naive Retry (Dangerous)

```
Request fails → retry immediately
→ retry immediately
→ retry immediately
→ retry immediately
...all at the same time from 10,000 clients...
→ THUNDERING HERD on recovering server ❌
```

### Exponential Backoff

```
Attempt 1: fails → wait 1s → retry
Attempt 2: fails → wait 2s → retry
Attempt 3: fails → wait 4s → retry
Attempt 4: fails → wait 8s → retry
Attempt 5: fails → wait 16s → retry
(max retries reached → give up, return error to user)

Formula: wait = base_delay × 2^attempt_number
  base_delay = 1s
  Attempt 0: 1 × 2^0 = 1s
  Attempt 1: 1 × 2^1 = 2s
  Attempt 2: 1 × 2^2 = 4s
  Attempt 3: 1 × 2^3 = 8s
```

### Adding Jitter (Critical)

```
PROBLEM WITH PURE EXPONENTIAL BACKOFF:
  10,000 clients all fail at the same time (e.g., server restart)
  All wait exactly 1s → all retry at exactly the same time → thundering herd!
  All wait exactly 2s → same thing → another thundering herd!

SOLUTION: Add randomness (jitter)

  wait = random(0, base_delay × 2^attempt_number)

  Attempt 1 failures spread across: 0s to 2s (random)
  Attempt 2 failures spread across: 0s to 4s (random)

  Instead of 10,000 simultaneous retries:
  Retries are spread across a time window → server recovers gracefully ✅

FULL IMPLEMENTATION:
  max_delay = 32s
  jitter_factor = random(0.5, 1.5)
  delay = min(max_delay, base_delay × 2^attempt × jitter_factor)
```

### What to Retry (and What NOT to)

```
RETRY THESE (transient failures):
  ✅ Network timeout (server was busy)
  ✅ 429 Too Many Requests (rate limited, retry after)
  ✅ 503 Service Unavailable (temporary)
  ✅ 500 Internal Server Error (might be transient)

DO NOT RETRY THESE (permanent failures):
  ❌ 400 Bad Request (client bug, retrying won't help)
  ❌ 401 Unauthorized (wrong credentials, retrying won't help)
  ❌ 404 Not Found (resource doesn't exist)
  ❌ Non-idempotent operations without idempotency key
     (POST /payment without idempotency key → double charge!)
```

---

## 4. Idempotency

An operation is idempotent if calling it multiple times produces the same result as calling it once.

```
IDEMPOTENT:
  GET /user/123        → always returns same user (if unchanged)
  DELETE /tweet/456    → first call: deletes; subsequent calls: "not found" (same state)
  PUT /user/123 {name: "Alice"} → calling twice sets name to Alice twice = same result

NOT IDEMPOTENT:
  POST /payment {amount: $100}  → each call charges $100 → dangerous to retry!
  POST /tweet {content: "Hi"}   → each call creates a new tweet
  INCR counter                  → each call increments (different result each time)
```

### Making Non-Idempotent Operations Safe

```
IDEMPOTENCY KEY PATTERN:

Client generates a unique ID for each logical operation:
  idempotency_key = UUID()  e.g., "pay_f47ac10b-58cc-4372-a567"

Client sends with request:
  POST /payment
  Idempotency-Key: pay_f47ac10b-58cc-4372-a567
  { "amount": 100, "currency": "USD" }

Server logic:
  1. Check DB: "Have I seen pay_f47ac10b-58cc-4372-a567 before?"
  2a. NO: Process payment, store result with idempotency_key, return result
  2b. YES: Return stored result (DO NOT process again!)

Retry scenario:
  Client sends payment → server processes → network drops → client doesn't get response
  Client retries with SAME idempotency key
  Server: "I've seen this key, here's the result" → returns same response
  User charged exactly once ✅

KEY STORAGE:
  Store in Redis with TTL=24h (or in DB permanently for audit)
  {idempotency_key → {status, result, timestamp}}
```

---

## 5. Bulkhead Pattern

Isolate failures so one failing component can't take down everything.

```
WITHOUT BULKHEAD:
─────────────────────────────────────────────────────────────────
  App has one shared thread pool (100 threads)
  
  Search service starts slow → all 100 threads waiting on search
  → No threads left for payment, profile, feed
  → Entire app unresponsive ❌

WITH BULKHEAD:
─────────────────────────────────────────────────────────────────
  Thread pool per service:
  ┌──────────────────────────────────────────────────────────┐
  │   Search threads (30)  │  Payment threads (40)           │
  │   [■][■][■][■][■]...  │  [□][□][□][□][□]...             │
  │   (all busy/blocked)   │  (available)                    │
  ├──────────────────────────────────────────────────────────┤
  │   Profile threads (20)  │  Feed threads (10)             │
  │   [□][□][□][□][□]...   │  [□][□][□][□][□]...            │
  │   (available)           │  (available)                   │
  └──────────────────────────────────────────────────────────┘

Search is slow → ONLY search threads affected
Payment, Profile, Feed: completely unaffected ✅

Named after ship bulkheads: compartments so one flooded section
doesn't sink the whole ship.
```

---

## 6. Health Checks

Systems need to know when a service is alive and ready to receive traffic.

### Liveness vs Readiness

```
LIVENESS CHECK: "Is the process alive?"
  Returns 200 if process is running, even if it can't serve traffic.
  If FAILS → restart the container/process.
  
  GET /health/live
  → 200 OK {"status": "alive"}

READINESS CHECK: "Is the service ready to serve traffic?"
  Returns 200 only if service can actually handle requests.
  Checks: DB connection, cache connection, warm-up complete.
  If FAILS → remove from load balancer rotation (don't kill it).
  
  GET /health/ready
  → 200 OK {"status": "ready", "db": "ok", "redis": "ok"}
  → 503 {"status": "not ready", "db": "connection failed"}
```

### Health Check Flow

```
  Load Balancer
       │
       ├──── Every 10s: GET /health/ready → Server 1
       │           → 200: keep in rotation ✅
       │
       ├──── Every 10s: GET /health/ready → Server 2
       │           → 503: REMOVE from rotation ❌
       │           → Stop sending traffic to Server 2
       │
       └──── Every 10s: GET /health/ready → Server 3
                   → 200: keep in rotation ✅

Meanwhile, orchestrator (Kubernetes):
  Every 30s: GET /health/live → Server 2
  → 200: process alive, don't restart (just not ready)
  → Eventually: /health/ready returns 200 → add back to rotation
```

---

## 7. Eliminating Single Points of Failure (SPOF)

A Single Point of Failure is any component whose failure takes down the whole system.

```
IDENTIFY AND ELIMINATE SPOFs:

┌─────────────────────────────────────────────────────────────────┐
│                SINGLE POINTS OF FAILURE                         │
├───────────────────────┬─────────────────────────────────────────┤
│  SPOF                 │  SOLUTION                               │
├───────────────────────┼─────────────────────────────────────────┤
│ Single load balancer  │ Active-passive LB pair (or use cloud LB)│
│ Single DB primary     │ Leader + followers + auto failover      │
│ Single region DC      │ Multi-region deployment                 │
│ Single DNS server     │ Multiple NS records, anycast DNS        │
│ Single cache node     │ Redis Cluster or Sentinel               │
│ Single Kafka broker   │ Kafka cluster (replication factor ≥ 2)  │
│ Single CDN provider   │ Multi-CDN or CDN with failover          │
└───────────────────────┴─────────────────────────────────────────┘

RULE: Everything critical must have N+1 redundancy minimum.
  N = number needed to handle load
  +1 = spare for when one fails
  
FIVE 9s AVAILABILITY (99.999%):
  = 5.26 minutes downtime/year
  Requires: No SPOF, fast failover, extensive monitoring
```

---

## 8. Putting It All Together: A Resilient Service

```
INCOMING REQUEST
      │
      ▼
┌─────────────────┐
│   Rate Limiter  │ ← Reject abuse; 429 if over limit
│   (Token Bucket)│   Uses Redis for distributed counting
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Load Balancer  │ ← Health checks; removes unhealthy nodes
│  (L7, active-  │   Active-passive for LB redundancy
│   passive pair) │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   App Server    │ ← Stateless; bulkhead per downstream service
│   (N instances) │   Circuit breaker to each dependency
└────────┬────────┘
         │
   ┌─────┴──────┐
   │            │
   ▼            ▼
┌──────┐    ┌───────┐
│Cache │    │  DB   │ ← Primary + replicas; auto failover
│Redis │    │(PG)   │   Retry with backoff for transient errors
│Clstr │    │       │   Idempotency keys for write operations
└──────┘    └───────┘
```

---

## 📝 Key Interview Sentences

> *"I'd put a rate limiter using the sliding window counter algorithm in front of the API. Per-user limits enforced via Redis atomic INCR operations. On the 429 response, the client retries with exponential backoff and jitter — starting at 1 second, doubling each time, with randomness to prevent synchronized retry storms."*

> *"Every downstream call from the app server goes through a circuit breaker. If the recommendation service has a 50% error rate for 10 seconds, the circuit opens and we fast-fail with cached recommendations instead of waiting on timeouts. This prevents the recommendation service's slowness from cascading into a full outage."*

> *"Payment operations use idempotency keys — the client generates a UUID per payment attempt and sends it in the header. The server checks a Redis store before processing. Retries with the same key return the stored result without re-charging the card."*

---

## ✅ Self-Test Questions

1. What is the boundary spike problem with Fixed Window rate limiting? How does Sliding Window fix it?

2. A circuit breaker is in the OPEN state. A request comes in. What happens? After 30 seconds, what does it do?

3. You're retrying a failed API call. 10,000 clients all retry at exactly the same time (no jitter). What happens to the recovering server?

4. A payment API is not idempotent. A user's client times out and retries the payment. What goes wrong? How do idempotency keys fix it?

5. Your app server has 100 threads shared across calls to Search, Payment, and Profile services. Search goes down and all threads are blocked waiting. What pattern solves this? How?
