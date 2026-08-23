# 01 — Networking & Protocols
> **Why this matters:** Every system you design communicates over a network. Choosing the wrong protocol costs you latency, reliability, or both. Interviewers ask "how does the client talk to the server?" in every single interview.

---

## 1. HTTP / HTTPS

### How HTTP Works
HTTP is a **request-response protocol** on top of TCP. The client sends a request, the server sends a response, done.

```
CLIENT                          SERVER
  │                               │
  │──── HTTP Request ────────────►│
  │     GET /feed HTTP/1.1        │
  │     Host: api.twitter.com     │
  │     Authorization: Bearer ... │
  │                               │
  │◄─── HTTP Response ────────────│
  │     200 OK                    │
  │     Content-Type: application/json
  │     { "tweets": [...] }       │
  │                               │
```

### HTTP Methods and When to Use Them

| Method | Purpose | Idempotent? | Body? |
|--------|---------|-------------|-------|
| GET | Read a resource | ✅ Yes | No |
| POST | Create a resource | ❌ No | Yes |
| PUT | Replace a resource entirely | ✅ Yes | Yes |
| PATCH | Partially update a resource | ❌ No | Yes |
| DELETE | Remove a resource | ✅ Yes | Optional |

> **Idempotent** = calling it multiple times has the same effect as calling once. Critical for retry logic.

### HTTP Status Codes You Must Know

```
2xx — Success
  200 OK               → request succeeded, response has body
  201 Created          → POST succeeded, new resource created
  204 No Content       → succeeded, no body (e.g. DELETE)

3xx — Redirection
  301 Moved Permanently → URL shortener redirect (cached by browser)
  302 Found            → temporary redirect (not cached)

4xx — Client Error
  400 Bad Request      → client sent garbage
  401 Unauthorized     → not authenticated
  403 Forbidden        → authenticated but no permission
  404 Not Found        → resource doesn't exist
  429 Too Many Requests → rate limited

5xx — Server Error
  500 Internal Server Error → server bug
  502 Bad Gateway      → upstream server failed
  503 Service Unavailable → overloaded or down
  504 Gateway Timeout  → upstream server too slow
```

### HTTPS = HTTP + TLS

```
CLIENT                              SERVER
  │                                   │
  │──── TCP Handshake ───────────────►│
  │◄─── TCP SYN-ACK ─────────────────│
  │                                   │
  │──── TLS ClientHello ─────────────►│  (supported cipher suites)
  │◄─── TLS ServerHello ─────────────│  (chosen cipher, certificate)
  │──── Verify certificate ──────────►│
  │──── Key exchange ────────────────►│
  │◄─── Finished ─────────────────────│
  │                                   │
  │══ All subsequent data encrypted ══│
```

**Key point:** TLS adds ~1–2 RTT (round trip times) to connection setup. This is why connection reuse (keep-alive) matters.

---

## 2. HTTP/1.1 vs HTTP/2 vs HTTP/3

### The Problem HTTP/1.1 Has: Head-of-Line Blocking

```
HTTP/1.1 — One request at a time per connection
────────────────────────────────────────────────
Connection 1: [Request A ──────────────►] [Response A ◄──] 
Connection 2:                [Request B ──►] [Response B ◄──]
Connection 3:                              [Request C ──►] [Response C ◄──]

Browsers open 6 connections max per domain → still a bottleneck
```

### HTTP/2 — Multiplexing Solves This

```
HTTP/2 — Multiple streams over ONE connection
─────────────────────────────────────────────
Single TCP Connection:
  Stream 1: [Request A ──►]  [Response A ◄──]
  Stream 2:  [Request B ───────────────────►] [Response B ◄──]
  Stream 3:   [Request C ──────►] [Response C ◄──]
  
All interleaved, no blocking between streams.
```

**HTTP/2 features:**
- Multiplexing — multiple requests over one connection
- Header compression (HPACK) — reduces overhead
- Server push — server can proactively send resources
- Binary framing — more efficient than text

### HTTP/3 — UDP Instead of TCP

HTTP/3 replaces TCP with **QUIC** (UDP-based):
- No TCP head-of-line blocking at the transport layer
- Built-in encryption (TLS 1.3)
- Better for mobile (handles IP address changes gracefully)
- Used by YouTube, Google, Cloudflare

```
Interview use:  HTTP/2 for most APIs.
                HTTP/3 for high-performance media streaming or mobile-heavy apps.
```

---

## 3. TCP vs UDP

This is one of the most fundamental tradeoffs in networking.

```
┌─────────────────────────────────────────────────────────────┐
│                         TCP                                  │
│  ┌────────┐  SYN  ┌────────┐                               │
│  │ Client │──────►│ Server │  3-way handshake before data  │
│  │        │◄──────│        │  Guaranteed delivery           │
│  │        │  ACK  │        │  Ordered packets               │
│  │        │──────►│        │  Flow + congestion control     │
│  └────────┘       └────────┘                               │
│  Use: HTTP, databases, file transfer, chat                  │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                         UDP                                  │
│  ┌────────┐       ┌────────┐                               │
│  │ Client │──────►│ Server │  No handshake                 │
│  │        │──────►│        │  No guarantee of delivery     │
│  │        │──────►│        │  No ordering                  │
│  │        │       │        │  But: FAST and LOW LATENCY    │
│  └────────┘       └────────┘                               │
│  Use: video streaming, gaming, DNS, VoIP, WebRTC            │
└─────────────────────────────────────────────────────────────┘
```

### TCP vs UDP Decision Table

| Scenario | Choose | Why |
|----------|--------|-----|
| User sends a payment | TCP | Can't lose or reorder data |
| Video call (Zoom) | UDP | Old frame is useless, prefer new |
| File upload | TCP | Every byte must arrive |
| Live game state | UDP | Slightly old state is fine |
| DNS lookup | UDP | Single small packet, fast |
| Database connection | TCP | Data integrity required |
| Video streaming (Netflix) | TCP (HTTP) | Buffered, can afford retries |
| WebRTC (voice) | UDP | Real-time, latency > reliability |

---

## 4. DNS — Domain Name System

DNS translates `api.twitter.com` → `104.244.42.1`

### The DNS Resolution Chain

```
Browser asks: "What is the IP of api.twitter.com?"

1. Check local cache → not found
2. Ask OS resolver → not found  
3. Ask Recursive Resolver (usually ISP or 8.8.8.8)
        │
        ├──► Ask Root Server "." → "Ask .com TLD server"
        │
        ├──► Ask .com TLD server → "Ask twitter.com nameserver"
        │
        └──► Ask twitter.com nameserver → "104.244.42.1" ✓

4. Cache result for TTL seconds (e.g. 300s = 5 minutes)
5. Return IP to browser
```

### DNS Record Types

| Record | Purpose | Example |
|--------|---------|---------|
| A | Maps domain → IPv4 | `api.twitter.com → 104.244.42.1` |
| AAAA | Maps domain → IPv6 | IPv6 address |
| CNAME | Alias to another domain | `www → twitter.com` |
| MX | Mail servers for domain | Used by email |
| TXT | Arbitrary text | Used for verification, SPF |
| NS | Nameserver for domain | `twitter.com → ns1.twitter.com` |

### GeoDNS — Routing Users to Nearest Data Center

```
User in India  ──DNS query──► GeoDNS returns: 203.0.113.1  (Mumbai DC)
User in US     ──DNS query──► GeoDNS returns: 104.244.42.1 (Virginia DC)
User in Europe ──DNS query──► GeoDNS returns: 185.45.6.7   (Ireland DC)
```

Used by: CloudFront, Akamai, every global CDN. Critical for latency.

### TTL Implications

Low TTL (e.g. 60s) → DNS changes propagate quickly, but more DNS queries
High TTL (e.g. 86400s) → Fewer DNS queries, but slow to update

**Interview tip:** When a company does failover (switching to backup DC), they need low TTL set in advance.

---

## 5. WebSockets

HTTP is request-response — the client always initiates. But what if the **server** needs to push data? (chat, live scores, notifications)

### WebSocket Handshake

```
1. Client sends HTTP Upgrade request:
   GET /chat HTTP/1.1
   Upgrade: websocket
   Connection: Upgrade
   Sec-WebSocket-Key: dGhlIHNhbXBsZQ==

2. Server responds:
   HTTP/1.1 101 Switching Protocols
   Upgrade: websocket
   Connection: Upgrade

3. TCP connection stays open.
   Now BOTH sides can send messages at any time.

CLIENT ◄────────────────────── SERVER   (server push, no request needed)
CLIENT ──────────────────────► SERVER   (client message)
CLIENT ◄────────────────────── SERVER   (server response)
```

### When to Use WebSockets

✅ Use WebSockets when:
- Bi-directional real-time communication (chat, multiplayer games)
- Server needs to push data frequently (live scores, trading dashboard)
- Low latency updates (collaborative docs, live cursor positions)

❌ Don't use WebSockets when:
- Simple request-response is enough (REST API)
- Server only needs to push occasionally → use SSE instead
- Clients are behind restrictive firewalls (WebSockets may be blocked)

### WebSockets in System Design

```
Client App
    │
    │ ws:// connection (persistent)
    │
WebSocket Gateway (load balanced)
    │
    ├── Connection Manager (tracks which user is on which server)
    │        └── Redis (stores user_id → server mapping)
    │
    ├── Message Router
    │
    └── Message Store (Cassandra / Redis)
```

---

## 6. Long Polling vs Short Polling vs SSE

```
Short Polling (naive, avoid this):
─────────────────────────────────
Client: "Anything new?"  → Server: "No"  (200ms later)
Client: "Anything new?"  → Server: "No"  (200ms later)
Client: "Anything new?"  → Server: "No"  (200ms later)
Client: "Anything new?"  → Server: "Yes! Here's a message"
Problem: Wasteful, high server load

Long Polling (clever HTTP trick):
──────────────────────────────────
Client: "Anything new?"
Server: [holds the connection open... waiting...]
         [30 seconds later, message arrives]
Server: "Yes! Here's a message" → closes connection
Client: immediately opens new long-poll connection
Good: lower server load, near-real-time. Bad: connection overhead

Server-Sent Events (SSE):
──────────────────────────
Client opens a one-way HTTP stream.
Server sends events whenever it has something.
Client cannot send data back over this stream.

GET /events HTTP/1.1
────────────────────────────
Server keeps connection open and sends:
  data: {"type": "tweet", "id": 123}

  data: {"type": "like", "post_id": 456}

  data: {"type": "notification", "msg": "..."}
Good: simple, works over HTTP, auto-reconnect built in
Bad: server → client only, no client → server
```

### Decision: Which to Use?

```
Need bidirectional?     → WebSocket
Server-to-client only?  → SSE (simpler, HTTP-native)
Can't use WebSockets?   → Long Polling
Infrequent updates?     → Short Polling (only for truly infrequent)
```

---

## 7. gRPC

gRPC is an RPC (Remote Procedure Call) framework by Google. Instead of calling a URL, you call a function on a remote server.

### REST vs gRPC Comparison

```
REST                          gRPC
──────────────────────────────────────────────────────
Format: JSON (text)           Format: Protobuf (binary, 5-10x smaller)
Protocol: HTTP/1.1            Protocol: HTTP/2 (multiplexed)
Schema: informal (OpenAPI)    Schema: strict .proto file
Browser-friendly: ✅          Browser-friendly: ❌ (needs proxy)
Human-readable: ✅            Human-readable: ❌ (binary)
Performance: moderate         Performance: high
Streaming: ❌                 Streaming: ✅ (all 4 patterns)
Best for: public APIs         Best for: internal microservices
```

### gRPC Streaming Modes

```
1. Unary (like REST):
   Client ──request──► Server ──response──► Client

2. Server streaming:
   Client ──request──► Server ──stream of responses──► Client
   (e.g., subscribe to live data feed)

3. Client streaming:
   Client ──stream of data──► Server ──response──► Client
   (e.g., uploading many records)

4. Bidirectional streaming:
   Client ◄──── stream ────► Server  (both sides stream simultaneously)
   (e.g., real-time collaboration)
```

### When to Use gRPC in System Design

- Internal service-to-service communication (microservices)
- When performance matters and payload size is large
- When you need streaming (not possible in REST/HTTP 1.1)
- NOT for public-facing APIs (browser can't call gRPC directly)

---

## 8. REST vs GraphQL

### REST — Resource-Based

```
GET  /users/123          → Get user 123
GET  /users/123/tweets   → Get tweets of user 123
POST /tweets             → Create a tweet
DELETE /tweets/456       → Delete tweet 456
```

**Problem: Over-fetching and Under-fetching**

```
GET /users/123 returns:
{
  "id": 123,
  "name": "Alice",
  "email": "alice@...",    ← I didn't need this
  "bio": "...",            ← I didn't need this
  "created_at": "...",     ← I didn't need this
  "follower_count": 5000   ← I only needed name + follower_count
}

GET /users/123/followers:  ← Need a second request for follower list
```

### GraphQL — Query-Based

```graphql
# Client requests EXACTLY what it needs in ONE query
query {
  user(id: 123) {
    name
    followerCount
    recentTweets(limit: 5) {
      text
      likeCount
    }
  }
}

# Returns:
{
  "user": {
    "name": "Alice",
    "followerCount": 5000,
    "recentTweets": [...]
  }
}
```

### REST vs GraphQL Decision

| Use REST when... | Use GraphQL when... |
|------------------|---------------------|
| Public API (simpler to understand) | Mobile apps (save bandwidth with exact fields) |
| Simple CRUD operations | Complex nested data requirements |
| Caching is important (GET is cacheable) | Multiple clients needing different shapes of data |
| Team is familiar with REST | Rapid iteration on data requirements |
| Microservices talking to each other | Single API serving web + mobile + partners |

---

## 9. Reverse Proxy vs Forward Proxy

```
Forward Proxy — sits in front of CLIENTS
─────────────────────────────────────────
Client A ─►┐                    ┌─► Server 1
Client B ─►│ Forward Proxy ─────┤
Client C ─►┘                    └─► Server 2
Use: corporate firewall, VPN, bypass geo-blocks
Clients know about proxy. Servers see proxy's IP.

Reverse Proxy — sits in front of SERVERS
──────────────────────────────────────────
                ┌─► App Server 1
Client ─────────► Reverse Proxy ─┤─► App Server 2
                └─► App Server 3
Use: load balancing, SSL termination, caching, security
Clients don't know about proxy. One IP, many servers behind it.
```

**What a reverse proxy does:**
- Terminates SSL (handles TLS so app servers don't have to)
- Load balances across servers
- Caches static responses
- Provides DDoS protection (rate limiting, filtering)
- Compresses responses
- Serves as API Gateway

Examples: NGINX, HAProxy, Cloudflare, AWS ALB

---

## 🔁 How Everything Connects

```
                    User types "twitter.com" in browser
                              │
                    ┌─────────▼──────────┐
                    │   DNS Resolution   │  ← GeoDNS sends to nearest DC
                    └─────────┬──────────┘
                              │  IP address
                    ┌─────────▼──────────┐
                    │   Reverse Proxy    │  ← SSL termination, rate limiting
                    │   (NGINX/Cloudflare)│
                    └─────────┬──────────┘
                              │
               ┌──────────────┼───────────────┐
               │              │               │
     ┌─────────▼───┐  ┌───────▼─────┐ ┌──────▼──────┐
     │  REST API   │  │  gRPC       │ │  WebSocket  │
     │  (HTTP/2)   │  │  (internal) │ │  (chat)     │
     └─────────────┘  └─────────────┘ └─────────────┘
```

---

## 📝 Key Interview Sentences

> *"For real-time bidirectional communication like chat, I'd use WebSockets over a WebSocket gateway. The gateway maintains a connection map in Redis so any server can route messages to the right user."*

> *"For internal microservice communication, I'd use gRPC — it's binary, faster than JSON, and supports streaming. For the public API, REST is better because browsers and third parties can call it directly."*

> *"The client connects over HTTPS with HTTP/2 multiplexing, so multiple API calls share one connection. For the live feed, instead of polling, we use Server-Sent Events since it's server-to-client only and simpler than WebSockets."*

---

## ✅ Self-Test Questions

1. A user opens Twitter and loads their feed, then sees a new tweet appear without refreshing. What protocols are involved from DNS lookup to the live tweet appearing?

2. Why does Zoom use UDP instead of TCP for video?

3. You're designing a stock trading dashboard that shows live price updates. Would you use WebSockets, SSE, or Long Polling? Why?

4. Your microservices are calling each other 10,000 times per second. REST or gRPC? Why?

5. What is the difference between a reverse proxy and a load balancer? (Hint: a load balancer IS a reverse proxy)
