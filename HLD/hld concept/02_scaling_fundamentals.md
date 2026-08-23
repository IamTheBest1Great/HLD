# 02 — Scaling Fundamentals
> **Why this matters:** Every HLD interview asks "how does this scale?" You need to answer fluently. Scaling isn't just "add more servers" — it's about what type of scaling, what the limits are, and what breaks when you do it.

---

## 1. Vertical Scaling (Scale Up)

Add more power to the **same** machine: more CPU, more RAM, faster disk.

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

### Pros and Cons

| ✅ Pros | ❌ Cons |
|---------|---------|
| Simple — no code changes needed | Has a hard ceiling (can't buy infinite RAM) |
| No distributed systems complexity | Very expensive at high end |
| Consistent data (no sync issues) | Single point of failure — if it dies, everything dies |
| Works great for databases | Downtime to upgrade hardware |

### When to Vertical Scale

- **Databases** — especially when you need ACID (horizontal DB scaling is hard)
- **Early stage** — simplicity matters more than infinite scale
- **Small services** with predictable, bounded load

---

## 2. Horizontal Scaling (Scale Out)

Add **more machines** and distribute the load.

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

### Pros and Cons

| ✅ Pros | ❌ Cons |
|---------|---------|
| Theoretically unlimited scale | Requires stateless services |
| No single point of failure | Distributed systems complexity (consistency, networking) |
| Cheaper commodity hardware | Need load balancer in front |
| Can scale incrementally | Data synchronization challenges |

### The Critical Requirement: Stateless Services

For horizontal scaling to work, each server must be **stateless** — it cannot store user session data locally.

```
PROBLEM — Stateful Server:
──────────────────────────
User logs in → Session stored on Server 1
Next request → Load balancer sends to Server 2
Server 2: "Who are you? No session found!" ❌

SOLUTION — Stateless Server:
──────────────────────────────
User logs in → Session stored in Redis (external)
Next request → Any server checks Redis → finds session ✅

                           ┌─────────────┐
                           │    Redis    │
                           │  (sessions) │
                           └──────┬──────┘
                                  │ all servers read/write here
              ┌───────────────────┼───────────────────┐
         ┌────▼────┐         ┌────▼────┐         ┌────▼────┐
         │Server 1 │         │Server 2 │         │Server 3 │
         └─────────┘         └─────────┘         └─────────┘
```

---

## 3. Load Balancers

A load balancer distributes incoming requests across multiple servers.

```
                    Incoming Requests
                          │
              ┌───────────▼───────────┐
              │      Load Balancer    │
              │   (reverse proxy)     │
              └───┬───────┬───────┬───┘
                  │       │       │
             ┌────▼──┐ ┌──▼───┐ ┌─▼────┐
             │Server1│ │Svr 2 │ │Svr 3 │
             └───────┘ └──────┘ └──────┘
```

### Load Balancing Algorithms

```
1. ROUND ROBIN
   Request 1 → Server 1
   Request 2 → Server 2
   Request 3 → Server 3
   Request 4 → Server 1 (cycles)
   Good for: uniform request size. Bad for: if requests vary in cost.

2. WEIGHTED ROUND ROBIN
   Server 1 weight=3, Server 2 weight=1
   Req1→S1, Req2→S1, Req3→S1, Req4→S2 (then repeat)
   Good for: servers with different capacities.

3. LEAST CONNECTIONS
   New request → goes to server with fewest active connections
   Good for: long-lived connections (WebSockets, file uploads)
   Bad for: overhead of tracking connection counts

4. IP HASH (Sticky Sessions)
   hash(client_IP) % num_servers → always same server
   Good for: stateful servers (not recommended, but sometimes needed)
   Bad for: hotspots if many users share an IP (office, NAT)

5. LEAST RESPONSE TIME
   New request → goes to server responding fastest
   Good for: heterogeneous workloads
```

### Layer 4 vs Layer 7 Load Balancing

This is a frequently asked concept.

```
OSI Model (simplified):
Layer 7 — Application (HTTP, WebSocket, gRPC)
Layer 4 — Transport (TCP, UDP)
Layer 3 — Network (IP)

LAYER 4 LOAD BALANCER
─────────────────────
Sees: IP addresses and port numbers only
Cannot see: HTTP headers, URL path, cookies
Decision: based on IP + TCP port
Speed: Very fast (no packet inspection)
Use: When you don't need to route by URL (TCP traffic, UDP)
Example: AWS NLB (Network Load Balancer)

LAYER 7 LOAD BALANCER
─────────────────────
Sees: Full HTTP request (headers, path, body, cookies)
Can route by: URL path, header values, cookies, user agent
Speed: Slightly slower (reads HTTP)
Use: Most web applications (route /api → backend, /static → CDN)
Example: AWS ALB, NGINX, Envoy
```

### Practical L7 Load Balancing Example

```
Incoming request: GET /api/v1/tweets/123
                  Host: twitter.com
                  User-Agent: Mobile/iOS

L7 Load Balancer Rules:
  /api/*        → Backend API servers (weighted round robin)
  /static/*     → CDN edge servers
  /ws/*         → WebSocket servers (least connections)
  Mobile UA     → Mobile-optimized servers
```

---

## 4. Stateless vs Stateful Services

### Stateful Service (Hard to Scale)

```
User Alice logs in:
Server 1 memory: { "alice": { "cart": [...], "session": "xyz" } }

If Server 1 crashes → Alice loses her cart ❌
If you add Server 2 → Alice's requests might go there, no session ❌
```

### Stateless Service (Easy to Scale)

```
User Alice logs in:
Server: Store Alice's session in Redis with token "abc123"
Return token "abc123" to Alice (stored in browser cookie/header)

Next request from Alice:
Browser sends: Authorization: Bearer abc123
ANY server can look up "abc123" in Redis → gets Alice's data ✅
Server 1 can die → Server 2 handles Alice with no problem ✅
```

### Making a Service Stateless

| State Type | Where to Move It |
|------------|-----------------|
| User sessions | Redis (key: session_token, value: user_data, TTL) |
| Shopping cart | Redis or database |
| File uploads in progress | Object storage (S3) with pre-signed URLs |
| Background job status | Database or Redis |
| Rate limit counters | Redis |

---

## 5. Auto-Scaling

Auto-scaling automatically adds or removes servers based on load.

```
SCALE OUT (add servers when load increases):
────────────────────────────────────────────
Normal:   [Server1] [Server2]           → 500 QPS, 50% CPU
Traffic spike:
          [Server1] [Server2] [Server3] [Server4] → 2000 QPS, 40% CPU

SCALE IN (remove servers when load decreases):
───────────────────────────────────────────────
Off-peak: [Server1] [Server2]           → 100 QPS, 10% CPU
          (Server3 and Server4 terminated to save cost)
```

### Auto-Scaling Metrics

| Metric | Scale Out When | Example |
|--------|---------------|---------|
| CPU utilization | > 70% | General compute |
| Memory usage | > 80% | Memory-intensive apps |
| Request queue depth | > 100 requests waiting | Background workers |
| Network in/out | > 80% of capacity | Download servers |
| Custom metric | Any business metric | Messages per consumer |

### Auto-Scaling Challenges

```
WARM-UP TIME PROBLEM:
─────────────────────
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

---

## 6. The Complete Scaling Picture

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

---

## 📊 Scaling Decision Framework

```
Is this service stateless or can it be made stateless?
                    │
         ┌──────────▼──────────┐
         │ YES → Horizontal    │  → Deploy behind load balancer, auto-scale
         │ NO  → Vertical first│  → Then consider sharding (databases)
         └─────────────────────┘

Is the bottleneck reads or writes?
         │
         ├── Reads → Add read replicas + caching layer
         │
         └── Writes → Sharding, write-optimized DB, async queues

What's the traffic pattern?
         │
         ├── Steady → Reserve instances (cost savings)
         │
         └── Spiky → Auto-scaling + pre-warming for known spikes
```

---

## 📝 Key Interview Sentences

> *"I'd scale the application tier horizontally — each server is stateless, with sessions stored in Redis. The load balancer distributes with Least Connections since requests vary in duration."*

> *"For the database, vertical scaling gets us to a point, then we introduce read replicas to offload 80% of read traffic. Writes still go to a single primary, but if writes become the bottleneck, we'd shard by user_id."*

> *"Auto-scaling is configured on CPU utilization. But since JVM warm-up takes ~60 seconds, we keep a minimum of 3 instances to handle unexpected spikes while new instances boot."*

---

## ✅ Self-Test Questions

1. You have a stateful login service — users' sessions are in memory. What's the problem when you add a second server? What's the fix?

2. You're getting 10× traffic on Black Friday (predictable spike). What do you configure in your auto-scaler to avoid degradation when the spike hits?

3. You need to route `/api/*` to backend servers, `/uploads/*` to upload servers, and `/` to frontend servers. Do you need Layer 4 or Layer 7 load balancing?

4. Your app servers are at 90% CPU. Before adding more servers, what should you check first?

5. Why can't you just vertically scale a database forever?
