# BookMyShow — Complete High-Level System Design
### Interview Reference Guide | End-to-End Architecture Walkthrough

**Topics Covered:** Introduction • Requirements • Capacity Estimation • API Design • Database Design • HLD Diagram Walkthrough • Component Deep-Dives • Scaling • Failure Scenarios • Security • Interview Summary

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Requirements](#2-requirements)
3. [Traffic Estimation & Capacity Planning](#3-traffic-estimation--capacity-planning)
4. [API Design](#4-api-design)
5. [Database Design](#5-database-design)
6. [HLD Architecture — Diagram Walkthrough](#6-hld-architecture--diagram-walkthrough)
7. [Seat Reservation — The Core Problem](#7-seat-reservation--the-core-problem)
8. [Booking Flow — End to End](#8-booking-flow--end-to-end)
9. [Real-Time Seat Map — WebSocket Design](#9-real-time-seat-map--websocket-design)
10. [Handling the Taylor Swift Problem — Thundering Herd](#10-handling-the-taylor-swift-problem--thundering-herd)
11. [Cancellation & Refund Flow](#11-cancellation--refund-flow)
12. [Search & Discovery](#12-search--discovery)
13. [Multi-Region Deployment](#13-multi-region-deployment)
14. [Trade-offs & Design Decisions](#14-trade-offs--design-decisions)
15. [Failure Scenarios & Mitigations](#15-failure-scenarios--mitigations)
16. [Security Considerations](#16-security-considerations)
17. [Scaling to 100×](#17-scaling-to-100)
18. [Comparison: BookMyShow vs Pastebin vs URL Shortener](#18-comparison-bookmyshow-vs-pastebin-vs-url-shortener)
19. [Interview Summary](#19-interview-summary)

---

## 1. Introduction

BookMyShow is an online ticket booking platform that allows users to browse events (movies, concerts, sports), select seats, and purchase tickets. When a user selects seats and completes payment, the system generates a confirmed booking with a unique reference (e.g., `BMS-aB3kZ9`).

The critical challenge here is fundamentally different from systems like Pastebin or a URL shortener. This is a **seat inventory management problem under extreme contention** — thousands of users simultaneously competing for the same finite seats when a popular show goes on sale.

> 🔑 **Key Insight:** Seats are **finite, stateful resources** that transition through states: `AVAILABLE → HELD → BOOKED`. The system must prevent double-booking with absolute correctness — a single race condition means two people with tickets for the same seat. This is not a distributed storage problem; it is a **distributed concurrency problem**.

### What makes BookMyShow uniquely hard?

- **Finite shared state:** Unlike Pastebin (unlimited storage), seats are a fixed, scarce resource. Every hold reduces what others can book.
- **External payment dependency:** Booking involves an external payment gateway that can fail, timeout, or retry — creating complex state transitions.
- **Extreme read/write asymmetry:** 500:1 seat map views to bookings normally, but during an event release this ratio collapses to near 1:1 — all reads and writes are hammering the same rows.
- **Instantaneous spikes:** Taylor Swift tickets go on sale at noon. Every user arrives at the exact same second. This is qualitatively different from gradual load growth.
- **Fairness requirements:** Users should not be able to hold seats indefinitely without paying. Holds must expire.

---

## 2. Requirements

### 2.1 Functional Requirements

- **Browse Events:** List movies/concerts/sports by city, date, genre, venue.
- **View Seats:** Show real-time seat map with availability (available, held, booked).
- **Reserve Seats:** Temporarily hold selected seats for a user (TTL: 10 minutes).
- **Book Tickets:** Confirm booking after payment success; generate booking ID + QR code.
- **Cancel Booking:** Release seats back to inventory; trigger refund.
- **Search:** Full-text search across events, venues, cities with geo-filtering.
- **Notifications:** Booking confirmation, cancellation, and reminders via email/SMS.
- **Payment Integration:** Third-party payment gateway (Stripe/Razorpay).
- **Waitlist (optional):** Queue users when seats are sold out; notify on cancellation.

### 2.2 Non-Functional Requirements

- **Strong Consistency for Bookings:** Two users must never book the same seat. This is non-negotiable.
- **High Availability:** 99.99% uptime — browsing must never fail; booking degrades gracefully under load.
- **Low Latency:** Seat map render < 200 ms; booking confirmation < 3 s (includes payment round-trip).
- **Scalability:** Handle 500K concurrent users during peak event releases (Taylor Swift, IPL finals).
- **Durability:** No confirmed booking lost, ever.
- **Fairness:** No user holds seats indefinitely without payment. Holds expire after 10 minutes.

> 💡 **Clarifying Questions to ask in interviews:** What's the max seats per venue? Do we need group booking (hold contiguous seats)? Is there a waitlist? How long is a seat hold valid? Do we need dynamic pricing? Is the seat map visual (with row/column layout) or just a list? Are there VIP sections with different rules?

---

## 3. Traffic Estimation & Capacity Planning

### 3.1 Assumptions

| Parameter | Value |
|-----------|-------|
| Daily active users | 5 million |
| Events listed per day | 10,000 (movies × cities × showtimes) |
| Bookings per day | 500,000 |
| Average seats per booking | 2 |
| Peak concurrent users (event release) | 500,000 |
| Seat map views : bookings ratio | 500 : 1 (reads dominate heavily) |
| Seat record size | ~50 bytes (seat_id, status, hold_expiry) |
| Booking record size | ~500 bytes |
| Largest single event | 100,000 seats (stadium concert) |
| Replication factor | 3× |

### 3.2 Calculations

| Metric | Calculation & Result |
|--------|----------------------|
| Bookings/sec (avg) | 500K ÷ 86,400 ≈ **5.8 writes/sec** |
| Peak bookings (10× during release) | **~58 writes/sec** (low absolute, but ALL targeting same rows) |
| Seat map reads/sec (avg) | 5.8 × 500 = **~2,900 reads/sec** |
| Seat map reads/sec (peak) | **~29,000 reads/sec** |
| Peak seat contention | 500K users → 50K-seat concert = **~10 users per seat competing** |
| Seat inventory storage | 10K events × 10K seats × 50 B = **~5 GB total** (fits in Redis!) |
| Bookings storage (5 years) | 500K/day × 365 × 5 × 500 B = **~456 GB** |
| Peak bandwidth | 29K reads/sec × 10 KB seat map = **~290 MB/sec** |

> 🔑 **Key Insight:** This is a **concurrency problem, not a storage problem.** Total data is tiny — 5 GB for all seat inventory worldwide. But 500K users contending for the same 50K seats in the same second is an extreme concurrency challenge that drives every architectural decision.

---

## 4. API Design

Style: REST + WebSocket, JSON, versioned at `/v1/`. WebSocket for real-time seat map updates.

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/v1/events` | GET | List/search events (city, date, category) |
| `/v1/events/{eventId}` | GET | Event details + show timings |
| `/v1/events/{eventId}/shows/{showId}/seats` | GET | Real-time seat map |
| `/v1/bookings` | POST | Initiate booking (reserve seats + create order) |
| `/v1/bookings/{bookingId}/confirm` | POST | Confirm payment, finalize booking |
| `/v1/bookings/{bookingId}` | GET | Booking details |
| `/v1/bookings/{bookingId}` | DELETE | Cancel booking |
| `/v1/users/{userId}/bookings` | GET | User's booking history |

### POST /v1/bookings — Initiate (Reserve Seats)

```json
// Request
{
  "show_id": "show_789",
  "seat_ids": ["A12", "A13"],
  "user_id": "user_456"
}

// Response 201 Created
{
  "booking_id": "BMS-aB3kZ9",
  "status": "RESERVED",
  "seats": ["A12", "A13"],
  "amount": 560.00,
  "currency": "INR",
  "payment_url": "https://pay.stripe.com/session_xyz",
  "expires_at": "2025-04-08T10:10:00Z"
}
```

### POST /v1/bookings/{bookingId}/confirm — Payment Confirmation

```json
// Response 200 OK
{
  "booking_id": "BMS-aB3kZ9",
  "status": "CONFIRMED",
  "booking_ref": "BMS-aB3kZ9",
  "qr_code_url": "https://cdn.bms.com/qr/BMS-aB3kZ9.png"
}
```

### WebSocket — Real-Time Seat Updates

```
wss://api.bms.com/v1/shows/{showId}/seats
```

Server pushes seat state changes to all connected clients viewing the same show:

```json
// Seat held by someone else
{ "type": "SEAT_HELD", "seat_id": "A12", "expires_in": 600 }

// Seat released (hold expired or cancellation)
{ "type": "SEAT_RELEASED", "seat_id": "A12" }

// Seat permanently booked
{ "type": "SEAT_BOOKED", "seat_id": "A12" }
```

### Rate Limiting

- **Booking initiations:** 5 per minute per user (Redis token bucket). Returns `429 Too Many Requests` with `Retry-After`.
- **Seat hold abuse:** Maximum 1 active hold per user per show. A user cannot hold all seats and never pay.
- **Peak show protection:** Token bucket per `show_id` limits booking initiations to 10,000/minute during the first 60 seconds of a sale.

---

## 5. Database Design

Three distinct storage needs, each requiring a different tool.

### 5.1 The Three Storage Layers

| Data | Store | Reason |
|------|-------|--------|
| Seat inventory | **Redis** | Sub-millisecond atomic ops; 5 GB fits in RAM entirely |
| Bookings & payments | **PostgreSQL** | ACID transactions required; no eventual consistency on money |
| Event catalog | **PostgreSQL + Elasticsearch** | Structured data + full-text/geo search |
| Sessions / rate limits | **Redis** | TTL-native, high throughput |
| QR codes / tickets | **S3** | Binary blobs, CDN-served |

### 5.2 Redis — Seat Inventory (The Hot Layer)

Each show's seat map stored as a Redis Hash:

```
Key:   seats:{showId}
Field: {seatId}
Value: AVAILABLE | HELD:{userId}:{expiryEpoch} | BOOKED
```

**Example:**
```
seats:show_789 → {
  "A12": "AVAILABLE",
  "A13": "HELD:user_456:1712567400",
  "A14": "BOOKED",
  "A15": "AVAILABLE"
}
```

**Atomic reservation via Lua script** — this is the most critical piece of the entire system:

```lua
-- Executed atomically in Redis (single-threaded, no interruption possible)
local show_id = ARGV[1]
local user_id = ARGV[2]
local expiry   = ARGV[3]

-- Phase 1: Check ALL requested seats are AVAILABLE (fail fast if any conflict)
for i = 1, #KEYS do
  local status = redis.call('HGET', 'seats:' .. show_id, KEYS[i])
  if status ~= 'AVAILABLE' then
    return 0  -- conflict — abort entire operation
  end
end

-- Phase 2: Hold ALL seats (only reached if all are AVAILABLE)
for i = 1, #KEYS do
  redis.call('HSET', 'seats:' .. show_id, KEYS[i],
             'HELD:' .. user_id .. ':' .. expiry)
end

return 1  -- success
```

Why Lua? Redis executes Lua scripts atomically — no other command runs in between. This makes the check-then-hold operation indivisible, eliminating the TOCTOU (Time-of-Check-Time-of-Use) race condition entirely.

**Redis persistence for seat data:**
- **AOF (Append-Only File):** Every write persisted to disk. Survives Redis restart.
- **RDB snapshots:** Periodic full-state snapshots as backup.
- **Source of truth fallback:** On Redis restart, confirmed `BOOKED` seats are rebuilt from PostgreSQL `bookings` table. `HELD` seats that weren't in PostgreSQL are treated as `AVAILABLE` (hold expired during downtime — correct behavior).

### 5.3 PostgreSQL — Bookings & Payments (The Durable Layer)

**Table: `bookings`**

| Column | Type | Description |
|--------|------|-------------|
| `booking_id` | UUID (PK) | Snowflake ID encoded as Base62, prefixed `BMS-` |
| `show_id` | UUID (FK) | Reference to show |
| `user_id` | UUID (FK) | Reference to user |
| `seat_ids` | text[] | Array of reserved seat IDs |
| `status` | enum | `RESERVED → PAYMENT_PENDING → CONFIRMED → CANCELLED → EXPIRED` |
| `total_amount` | numeric | Total ticket price |
| `payment_id` | varchar | External gateway transaction reference |
| `created_at` | timestamp | Booking initiation time |
| `confirmed_at` | timestamp | Payment confirmation time |
| `expires_at` | timestamp | Hold expiry (10 minutes from creation) |

**Table: `shows`**

| Column | Type | Description |
|--------|------|-------------|
| `show_id` | UUID (PK) | |
| `event_id` | UUID (FK) | Reference to event |
| `venue_id` | UUID (FK) | Reference to venue |
| `start_time` | timestamp | Show datetime |
| `total_seats` | int | Total capacity |
| `available_seats` | int | Denormalized counter — decremented on CONFIRM |
| `price_tiers` | JSONB | `{"A": 560, "B": 420, "pit": 800}` |

**Table: `payments`**

| Column | Type | Description |
|--------|------|-------------|
| `payment_id` | UUID (PK) | |
| `booking_id` | UUID (FK) | |
| `gateway` | varchar | `stripe` / `razorpay` |
| `status` | enum | `PENDING → SUCCESS → FAILED → REFUNDED` |
| `amount` | numeric | |
| `idempotency_key` | varchar (UNIQUE) | Prevents double-charge on gateway retry |
| `gateway_response` | JSONB | Full gateway payload for audit |
| `created_at` | timestamp | |

**Critical constraints:**
```sql
-- Prevents double-booking at the DB layer (secondary guard after Redis)
CREATE UNIQUE INDEX ON bookings (show_id, seat_id, status)
  WHERE status = 'CONFIRMED';

-- Ensures idempotent payment processing
CREATE UNIQUE INDEX ON payments (idempotency_key);
```

### 5.4 PostgreSQL — Event Catalog

**Table: `events`** — title, description, category, language, cast, images, rating  
**Table: `venues`** — name, city, address, coordinates (PostGIS point), capacity, seat_map_layout (JSONB)  
**Table: `seat_categories`** — category, price, color (for UI rendering), row ranges

PostgreSQL is the write master for catalog data. All writes go here first, then fan out to Elasticsearch via Kafka for search indexing.

### 5.5 Elasticsearch — Search & Discovery

Indexes all events with fields for full-text search, geo-search, and faceted filtering:

```json
{
  "event_id": "evt_123",
  "title": "Taylor Swift | The Eras Tour",
  "city": "Mumbai",
  "venue": "DY Patil Stadium",
  "category": "concert",
  "date": "2025-06-15",
  "price_min": 2000,
  "price_max": 15000,
  "available_seats": 42000,
  "location": { "lat": 19.044, "lon": 72.865 }
}
```

- **Geo-search:** Events within N km of user's coordinates, sorted by date.
- **Availability filter:** `available_seats > 0` — hides sold-out events from listings.
- **Auto-suggest:** Completion suggester for search-as-you-type (event names, venues, cities).
- **Update pattern:** PostgreSQL is the source of truth. Kafka `catalog_updated` events trigger Elasticsearch index updates asynchronously (eventual consistency on search — acceptable).

---

## 6. HLD Architecture — Diagram Walkthrough

### 6.1 The Big Picture

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                            THE BIG PICTURE                                   │
│                                                                              │
│  User (Browser) ──┐                                                          │
│                   │                                                          │
│  User (Mobile)  ──┼──▶ CDN ──▶ API Gateway ──▶ Load Balancer               │
│                   │    (static   (auth, rate       │                         │
│  User (WebSocket)─┘     assets)   limiting)        │                         │
│                                               ┌────┴─────────────────────┐  │
│                                               │                          │  │
│                                    ┌──────────▼──────┐  ┌───────────────▼┐ │
│                                    │  Seat Service   │  │ Booking Service│ │
│                                    │ (Redis Lua ops) │  │ (PostgreSQL)   │ │
│                                    └──────┬──────────┘  └───────┬────────┘ │
│                                           │                      │          │
│                          ┌────────────────▼──────────────────────▼────────┐ │
│                          │                                                 │ │
│              ┌───────────▼──────┐    ┌──────────────────┐                 │ │
│              │ Redis Key Pool   │    │    PostgreSQL     │                 │ │
│              │ (seat inventory) │    │  (bookings,       │                 │ │
│              │ Lua atomic holds │    │   payments,       │                 │ │
│              │ Redis Sentinel   │    │   shows, events)  │──▶ Read Replica │ │
│              └──────────────────┘    └──────────────────┘                 │ │
│                                                                             │ │
│              ┌──────────────────┐    ┌──────────────────┐                 │ │
│              │ Redis Metadata   │    │  Elasticsearch   │                 │ │
│              │ (sessions, rate  │    │  (event search,  │                 │ │
│              │  limits, queue)  │    │   geo-filter)    │                 │ │
│              └──────────────────┘    └──────────────────┘                 │ │
│                                                                             │ │
│              ┌──────────────────────────────────────────────────────────┐  │ │
│              │         WebSocket Server (Redis Pub/Sub fan-out)         │  │ │
│              └──────────────────────────────────────────────────────────┘  │ │
│                                      │                                      │ │
│                          ┌───────────▼──────────────────────────────────┐  │ │
│                          │         Kafka (Async Event Bus)              │  │ │
│                          └──────────────────────────────────────────────┘  │ │
│                    ┌──────────────────┬───────────────────┐                 │ │
│                    ▼                  ▼                   ▼                 │ │
│          ┌──────────────┐  ┌──────────────────┐  ┌───────────────────┐    │ │
│          │ Notification │  │  QR Code Worker  │  │  Hold Expiry      │    │ │
│          │  Worker      │  │  (generate → S3) │  │  Worker           │    │ │
│          └──────────────┘  └──────────────────┘  └───────────────────┘    │ │
└──────────────────────────────────────────────────────────────────────────────┘
```

### 6.2 Component-by-Component Explanation

#### ① CDN

Serves all static assets — seat map UI, event images, QR code PDFs. The CDN is **never involved in seat reservation logic**. Seat maps can be cached at the CDN for 5 seconds (short TTL for eventual consistency during browsing). This offloads the majority of the 29K peak reads/sec from the origin.

#### ② API Gateway

The entry point for all API traffic. Responsibilities:
- **JWT validation:** Verifies token signature and expiry before any request reaches a service.
- **Rate limiting:** Per-user and per-IP token bucket (backed by Redis). Rejects excess with `429`.
- **Routing:** Forwards to the correct microservice — Seat Service for inventory ops, Booking Service for booking CRUD.
- **Idempotency enforcement:** Strips and forwards `Idempotency-Key` headers to downstream services.

#### ③ Seat Service

The most critical service in the system. **All seat state transitions are owned exclusively by the Seat Service.** No other service touches seat state directly.

- **Reserve:** Executes Redis Lua script to atomically hold requested seats. Returns success or conflict.
- **Release:** Sets seat status back to `AVAILABLE` in Redis (on hold expiry or cancellation).
- **Confirm:** Transitions `HELD → BOOKED` in Redis after payment confirmation.
- **Read seat map:** Returns current seat hash from Redis (cached for 5s for browsing reads).
- **Publish changes:** After every state transition, publishes to Redis Pub/Sub channel `seats:{showId}` for WebSocket fan-out.

#### ④ Booking Service

Manages the lifecycle of booking records in PostgreSQL. It is the **orchestrator** of the booking flow.

- Creates `RESERVED` booking row after Seat Service confirms hold.
- Updates status through `PAYMENT_PENDING → CONFIRMED → CANCELLED / EXPIRED`.
- Calls Payment Service to initiate and verify payment.
- Publishes `booking_confirmed` / `booking_cancelled` events to Kafka for async workers.

#### ⑤ Payment Service

Wraps the external payment gateway (Stripe/Razorpay).

- Creates a payment session and returns a payment URL to the client.
- **Receives webhooks** from the gateway on payment success/failure. Verifies webhook signatures via HMAC-SHA256 before processing.
- Uses `idempotency_key` (from the gateway event ID) to prevent double-processing if Stripe retries the webhook.
- Publishes `payment_succeeded` / `payment_failed` events to Kafka.

#### ⑥ Redis — Seat Inventory

The in-memory seat state store. Every seat for every active show lives here as a Redis Hash. Key properties:

- **Atomic Lua scripts:** The only way to modify seat state. Guarantees correctness under concurrency.
- **5 GB total:** Entire worldwide seat inventory fits in RAM — sub-millisecond reads and writes.
- **AOF + RDB persistence:** Survives restarts. Confirmed bookings rebuilt from PostgreSQL on recovery.
- **Redis Sentinel:** Automatic failover if the primary node goes down (< 30 seconds).
- **Hold TTL management:** Holds store expiry timestamp in the value. The Hold Expiry Worker scans for expired holds and releases them.

#### ⑦ Redis — Metadata / Sessions

A separate Redis cluster (never mix with seat inventory) for:
- Rate limit token buckets (per user, per show_id, per IP).
- Virtual queue sorted sets (`ZADD queue:{showId} timestamp userId`).
- Session tokens and JWT blacklisting.
- WebSocket connection metadata.

#### ⑧ PostgreSQL + Read Replica

The durable, financial-grade source of truth.

- **Primary:** Handles all writes — booking inserts, status updates, payment records.
- **Read Replica:** Handles booking history reads, show listings, user profile queries.
- **ACID transactions:** Booking confirmation is a single transaction: update booking status + decrement `available_seats` counter on the show record.
- **Multi-AZ (RDS):** Automatic failover to standby in ~30 seconds on primary failure.

#### ⑨ WebSocket Server + Redis Pub/Sub

Real-time seat map updates to all connected clients.

- Each client viewing a show's seat map maintains a WebSocket connection.
- All WebSocket server instances subscribe to Redis Pub/Sub channel `seats:{showId}`.
- When Seat Service publishes a state change, **every WS server instance** fans it out to all its local connections for that show simultaneously.
- **Scaling:** WS servers are partitioned by `show_id` via consistent hashing at the load balancer. All clients for `show_789` land on the same set of WS servers, which all share the same Redis Pub/Sub subscription.

#### ⑩ Kafka — Async Event Bus

Decouples all non-critical-path work from the booking flow. Topics:

| Topic | Consumers |
|-------|-----------|
| `booking.confirmed` | Notification Worker, QR Code Worker, Analytics |
| `booking.cancelled` | Notification Worker, Waitlist Worker, Analytics |
| `seat.released` | WebSocket Server (additional fan-out), Analytics |
| `payment.succeeded` | Booking Service (trigger confirm flow) |
| `payment.failed` | Booking Service (trigger expiry flow) |
| `catalog.updated` | Elasticsearch indexer |

#### ⑪ Hold Expiry Worker

Monitors seat holds and releases them when TTLs expire.

- Listens to Redis keyspace events on the `expired` channel.
- On hold expiry: calls Seat Service to mark seat `AVAILABLE`; publishes `seat.released` to Kafka; triggers WebSocket broadcast.
- Also handles the edge case where payment is in-flight when hold expires (extends hold by 5 minutes once only).

#### ⑫ QR Code Worker

Triggered by `booking.confirmed` Kafka event:
1. Generates QR payload: `{booking_id, show_id, seat_ids, user_id}` signed with HMAC-SHA256.
2. Renders QR as PNG.
3. Uploads to S3 at `s3://bms-tickets/{booking_id}.png`.
4. Updates booking record with `qr_code_url`.
5. Venue scanners verify the HMAC signature at entry — no database call needed for fast entry scanning.

---

## 7. Seat Reservation — The Core Problem

This section explains why naive approaches fail and why the Redis Lua approach is the only correct solution at scale.

### 7.1 Why Naive Approaches Fail

**❌ Naive approach (Read → Check → Write):**

```
Thread A: Read seat A12 → status = "AVAILABLE"
Thread B: Read seat A12 → status = "AVAILABLE"   ← both read before either writes
Thread A: Write seat A12 → "HELD:userA"
Thread B: Write seat A12 → "HELD:userB"           ← overwrites A! DOUBLE BOOKING.
```

This is a classic **TOCTOU (Time-of-Check-Time-of-Use) race condition.** Even with database transactions, if two transactions read the seat status before either commits, both will see `AVAILABLE` and both will proceed.

**❌ Database row locks (SELECT FOR UPDATE):**

PostgreSQL `SELECT FOR UPDATE` is correct but slow. At 500K concurrent users all hitting the same rows, the DB becomes the bottleneck — connection queues fill up, timeouts cascade. PostgreSQL can handle ~500 serializable ops/sec on a hot row; Redis Lua handles ~500,000.

### 7.2 The Correct Solution — Redis Lua Atomic Script

Redis is **single-threaded for command execution.** A Lua script executes atomically — no other Redis command can interleave during script execution. This makes check-and-hold indivisible:

```lua
-- KEYS = list of seat_ids to hold
-- ARGV[1] = show_id, ARGV[2] = user_id, ARGV[3] = expiry_epoch

local show_key = 'seats:' .. ARGV[1]
local user_id  = ARGV[2]
local expiry   = ARGV[3]

-- Phase 1: Verify ALL seats are AVAILABLE (atomic check)
for i = 1, #KEYS do
  local current = redis.call('HGET', show_key, KEYS[i])
  if current ~= 'AVAILABLE' then
    return {0, KEYS[i]}  -- return conflict + which seat caused it
  end
end

-- Phase 2: Hold ALL seats (only executes if Phase 1 passes)
for i = 1, #KEYS do
  redis.call('HSET', show_key, KEYS[i], 'HELD:' .. user_id .. ':' .. expiry)
end

return {1, ''}  -- success
```

**What this guarantees:**
- User A and User B both try to book seat A12 simultaneously.
- Redis executes their Lua scripts sequentially (single-threaded).
- User A's script runs first → holds A12.
- User B's script runs → checks A12 → finds `HELD:userA:...` → returns conflict.
- User B gets a `409 Conflict` response. They must pick different seats.
- **Double-booking is mathematically impossible if all reservations go through this script.**

### 7.3 PostgreSQL as Secondary Guard

Even with Redis Lua, a secondary database-level constraint is added for defense in depth:

```sql
-- If somehow two CONFIRMED bookings for the same seat slip through,
-- this constraint catches it at the DB layer
CREATE UNIQUE INDEX ON bookings (show_id, seat_id)
  WHERE status = 'CONFIRMED';
```

This is a belt-and-suspenders approach. The Redis Lua script is the primary gate; the DB constraint is the emergency brake.

---

## 8. Booking Flow — End to End

### 8.1 Happy Path

```
1.  User selects seats A12, A13 → POST /v1/bookings
                                   │
2.  Seat Service:                  │
    - Execute Lua script on Redis  │
    - A12, A13 both AVAILABLE?     │
    - Yes → set HELD:user_456:+10m │
    - Return success               │
                                   │
3.  Booking Service:               │
    - INSERT INTO bookings         │
      (status=RESERVED,            │
       expires_at=now+10min)       │
    - Return booking_id +          │
      payment_url to client        │
                                   │
4.  Client redirects to            │
    Stripe checkout page           │
                                   │
5.  User completes payment         │
    on Stripe                      │
                                   │
6.  Stripe sends webhook →         │
    Payment Service                │
    - Verify HMAC signature        │
    - Check idempotency_key        │
    - INSERT payment record        │
    - Publish payment.succeeded    │
                                   │
7.  Booking Service (consumes):    │
    - UPDATE booking               │
      status=CONFIRMED             │
    - Decrement available_seats    │
      on show record               │
      (single PostgreSQL txn)      │
                                   │
8.  Seat Service:                  │
    - Update Redis: A12,A13        │
      HELD → BOOKED                │
                                   │
9.  Kafka: publish booking.confirmed
                                   │
10. Workers (async):               │
    - Send confirmation email/SMS  │
    - Generate QR code → S3        │
    - Update Elasticsearch         │
      available_seats count        │
    - Check waitlist               │
```

### 8.2 Payment Failure Path

```
User fails payment / abandons checkout
          │
          ▼
Booking remains RESERVED until expires_at
          │
          ▼
Hold Expiry Worker detects expiry:
  - Seat Service: A12, A13 → AVAILABLE in Redis
  - Booking Service: status → EXPIRED in PostgreSQL
  - Kafka: publish seat.released
  - WebSocket: broadcast SEAT_RELEASED to all viewers
```

### 8.3 Race — Payment Completes Just As Hold Expires

This is the most complex edge case:

```
T+9:58 — User's hold is 2 seconds from expiry
T+9:59 — User completes payment; Stripe processes
T+10:00 — Hold Expiry Worker triggers; sees expired hold
T+10:01 — Payment webhook arrives at Payment Service

Resolution:
- Payment Service checks booking status in PostgreSQL.
- If status = EXPIRED: issue immediate refund; notify user apologetically.
- If status = RESERVED (hold extended during active payment): process normally.
- Hold Expiry Worker checks for active payment session before releasing — extends hold 5 minutes (once only) if payment is in-flight.
```

### 8.4 Booking State Machine

```
                    ┌──────────────┐
                    │   RESERVED   │◀─── Initial state (POST /bookings)
                    └──────┬───────┘
                           │
              ┌────────────┴────────────┐
              │                         │
     Payment initiated           Hold expires (10 min)
              │                         │
              ▼                         ▼
    ┌──────────────────┐         ┌────────────┐
    │ PAYMENT_PENDING  │         │  EXPIRED   │
    └────────┬─────────┘         └────────────┘
             │
      ┌──────┴──────┐
      │              │
   Success         Failure
      │              │
      ▼              ▼
┌───────────┐  ┌───────────┐
│ CONFIRMED │  │  FAILED   │
└─────┬─────┘  └───────────┘
      │
  User cancels
      │
      ▼
┌───────────┐
│ CANCELLED │
└───────────┘
```

---

## 9. Real-Time Seat Map — WebSocket Design

### 9.1 Why WebSocket?

For a popular show, hundreds of thousands of users are viewing the same seat map simultaneously. Seats change state rapidly during a sale. Polling every 3 seconds would generate:

`500K clients × 1 poll / 3s = ~167K requests/sec` hitting the origin — unsustainable.

WebSocket maintains a persistent connection per client. The server pushes updates only when state changes. Bandwidth and server load are proportional to **the rate of seat changes**, not the number of viewers.

### 9.2 Architecture

```
Seat changes:
Seat Service → HSET Redis seats:show_789 A12 BOOKED
             → PUBLISH seats:show_789 '{"type":"SEAT_BOOKED","seat_id":"A12"}'
                                │
                     Redis Pub/Sub channel
                                │
              ┌─────────────────┼─────────────────┐
              ▼                 ▼                  ▼
         WS Server 1      WS Server 2        WS Server 3
        (20K clients)    (20K clients)       (20K clients)
         for show_789     for show_789        for show_789
              │                 │                  │
         Fan out to        Fan out to         Fan out to
         local 20K         local 20K          local 20K
         connections       connections        connections
```

Each WS server subscribes to `seats:{showId}` on Redis Pub/Sub. One publish from Seat Service triggers fan-out to **all connected clients globally** via all WS server instances.

### 9.3 Scaling WebSocket Connections

- WS servers are partitioned by `show_id` using **consistent hashing** at the load balancer.
- All clients for `show_789` land on the same shard of WS servers.
- Each WS server handles ~50K persistent connections.
- For 500K viewers: 10 WS servers, each with 50K connections.
- Each seat state change: each WS server does 50K message pushes — manageable (~50 MB/sec per server at 1 KB per message).

### 9.4 Reconnection Handling

If a WebSocket drops:
1. Client auto-reconnects (exponential backoff: 1s, 2s, 4s, max 30s).
2. On reconnection, client fetches full seat map via `GET /v1/events/.../shows/{showId}/seats` (REST).
3. Reconnects to WebSocket for further live updates.
4. Brief window of stale data during reconnect is acceptable — the reservation step is always strongly consistent.

---

## 10. Handling the Taylor Swift Problem — Thundering Herd

When a mega-event goes on sale, 500K users arrive **at the exact same second.** This is qualitatively different from gradual load. Three mitigations applied in layers:

### 10.1 Virtual Queue (Waiting Room) — The Primary Defense

Before the sale opens, users are directed to a waiting room. The queue is backed by a **Redis Sorted Set** (`ZADD`) where the score is the join timestamp (FIFO fairness):

```
ZADD queue:show_789 1712567400.123 user_001
ZADD queue:show_789 1712567400.456 user_002
ZADD queue:show_789 1712567400.789 user_003
...
```

The queue drainer service:
- Every second, `ZPOPMIN queue:show_789 5000` — admits 5,000 users into the booking flow.
- Admitted users get a time-limited "admission token" (Redis key with 5-minute TTL).
- Without an admission token, POST /v1/bookings returns `202 Accepted` (queued) with a queue position.
- `ZRANK queue:show_789 user_001` gives real-time queue position for the "Your position: 12,453" UI.

**Why this works:** Converts an instantaneous spike into a steady drain at a rate the system can handle (5,000/sec → 300K/minute). Users know their position and don't rage-quit. No thundering herd reaches the booking services.

### 10.2 Per-Show Rate Limiting — The Second Layer

For the first 60 seconds of a sale, a Redis token bucket per `show_id` limits booking initiations:

```
Bucket capacity: 10,000 tokens
Refill rate: 10,000 tokens/minute
Key: rate:show:show_789
```

Requests beyond the bucket are queued (not rejected), giving users a retry window rather than a hard failure.

### 10.3 Read Replica for Seat Maps — The Third Layer

During the spike:
- All seat map **reads** go to Redis read replicas (slave nodes). Replication lag < 50ms — acceptable for browsing.
- All seat **writes** (holds, confirms) go to the Redis master.
- CDN serves the static seat map UI assets. Even the initial seat map JSON is cached at CDN for 5 seconds, absorbing the first wave of reads entirely at the edge.

---

## 11. Cancellation & Refund Flow

```
1.  User requests cancellation → DELETE /v1/bookings/{bookingId}
                                  │
2.  Booking Service:              │
    - Verify user owns booking    │
    - Check cancellation policy   │
      (>24h before show: full refund)
    - >2h: partial refund         │
    - <2h: no refund              │
                                  │
3.  Seat Service:                 │
    - Redis: A12, A13 BOOKED → AVAILABLE
    - Publish seat.released to Pub/Sub
                                  │
4.  PostgreSQL:                   │
    - UPDATE booking status = CANCELLED
    - Increment available_seats on show
                                  │
5.  Payment Service:              │
    - Initiate refund via         │
      gateway API                 │
    - INSERT refund record        │
                                  │
6.  Kafka: publish booking.cancelled
                                  │
    ┌─────────────────────────────┤
    │                             │
    ▼                             ▼
Notification Worker         Waitlist Worker
(email/SMS: "Booking        - Check queue:show_789
 cancelled, refund in       - ZRANGE for first user
 3-5 business days")        - Send notification:
                              "Seats available!"
```

---

## 12. Search & Discovery

### 12.1 Elasticsearch Index

All events are indexed with fields enabling:

- **Full-text search:** `match` query on `title`, `cast`, `venue`, `description`.
- **Geo-search:** `geo_distance` query — "events within 50 km of my location."
- **Faceted filtering:** Filter by `category`, `language`, `date_range`, `price_range`.
- **Availability filter:** `available_seats > 0` — sold-out events excluded from listings.
- **Sorting:** By date, by distance, by price, by popularity (view count).

### 12.2 Auto-Suggest

Elasticsearch completion suggester powers search-as-you-type:

```json
GET /events/_search/suggest
{
  "event_suggest": {
    "prefix": "taylor",
    "completion": { "field": "title.suggest", "size": 5 }
  }
}
```

Returns: `["Taylor Swift | Eras Tour Mumbai", "Taylor Swift | Eras Tour Delhi", ...]`

### 12.3 Update Flow

```
Booking confirmed (PostgreSQL)
          │
          ▼
Kafka: catalog.updated event
          │
          ▼
Elasticsearch Consumer:
- Decrement available_seats in ES index
- If available_seats = 0: remove from search results
```

Eventual consistency (< 2 seconds) is acceptable for search — a user seeing a "sold out" event in search results and getting a 409 on attempting to book is a handled edge case, not a correctness failure.

---

## 13. Multi-Region Deployment

### 13.1 Geo-Routing

Route53 GeoDNS routes users to the nearest region — `IN-Mumbai`, `US-East`, `EU-West`.

### 13.2 Replication Strategy

**Active-Active for reads, Active-Passive for seat writes:**

| Component | Strategy | Reason |
|-----------|----------|--------|
| Event catalog | Active-Active (DynamoDB Global Tables or PG streaming) | Read-heavy, rarely changes |
| **Seat inventory (Redis)** | **Single-region per show** | Cross-region locking adds unacceptable latency |
| Bookings (PostgreSQL) | Primary in home region; read replicas elsewhere | ACID writes must be local |
| S3 (QR codes) | Cross-region replication | CDN-served globally |
| Elasticsearch | Replicated index per region | Low-latency search everywhere |

### 13.3 Single-Region Seat Routing — Key Decision

All seat operations for a given `show_id` are **pinned to one home region.** The home region is determined at show creation time (typically the region closest to the venue's country).

```
show_789 (Mumbai concert) → home region: IN-Mumbai
show_456 (London show)    → home region: EU-West

Indian user booking show_789 → routes to IN-Mumbai ✅ (same region)
UK user booking show_789    → routes to IN-Mumbai  ✅ (cross-region, slight latency)
```

**Why not cross-region seat operations?**

Cross-region distributed locking (Redlock across regions) adds 100–200ms round-trip latency per operation — unacceptable for a seat hold that must complete in < 50ms. Single-region routing keeps all seat contention within one Redis cluster where sub-millisecond Lua scripts work correctly.

**Trade-off:** If IN-Mumbai goes down during a sale, seat bookings for shows hosted there pause. Users get a clear error. Confirmed bookings (in PostgreSQL, replicated) are safe.

### 13.4 Regional Failover

1. Route53 health checks detect IN-Mumbai degradation.
2. Traffic for browsing and catalog routes to SG-Singapore within 60 seconds.
3. Seat booking for IN-Mumbai shows: return `503 Service Temporarily Unavailable` with `Retry-After`.
4. Once IN-Mumbai recovers, Redis seat state is rebuilt from PostgreSQL confirmed bookings.
5. **No data loss for confirmed bookings** — PostgreSQL multi-AZ + streaming replication.

---

## 14. Trade-offs & Design Decisions

| Decision | Trade-off |
|----------|-----------|
| **Redis Lua for seat holds vs PostgreSQL SELECT FOR UPDATE** | Redis: 500K ops/sec, sub-ms latency, eliminates TOCTOU. Trade-off: Redis is not the financial source of truth — PostgreSQL sync needed for durability. On Redis failure, fall back to PostgreSQL row locks (correct but 1000× slower). |
| **Single-region seat routing** | Eliminates distributed locking complexity. Trade-off: if the home region for a show fails mid-sale, bookings for that show pause. Confirmed bookings are safe in replicated DB. |
| **Virtual queue vs open sale** | Queue prevents thundering herd and ensures fairness. Trade-off: users hate waiting rooms — requires compelling "Your position: X" UX. Necessary for any mega-event. |
| **WebSocket vs polling for seat map** | WebSocket: true real-time, correct UX for competitive seat selection. Trade-off: stateful connections are harder to scale — requires Redis Pub/Sub fan-out architecture. Polling at 3s is a simpler fallback. |
| **5-second CDN cache for seat map reads** | Absorbs the browsing majority (99%) at CDN edge. Trade-off: users may see a seat as available that was just taken. The Lua reservation is the hard gate — they get a 409 at booking time, not a double-booking. |
| **External payment gateway (Stripe)** | Offloads PCI DSS compliance entirely. Trade-off: webhook latency + complex idempotency handling for retries. |
| **Kafka for all async work** | Decouples notifications, QR generation, waitlist from booking critical path — zero added latency to confirmation. Trade-off: email/SMS delivery is eventual (~2 seconds typically). |
| **Two-phase booking (RESERVE → CONFIRM)** | Decouples seat hold from payment. Allows payment to happen asynchronously while seat is locked. Trade-off: hold window (10 minutes) temporarily reduces available inventory — opportunity cost during high demand. |

---

## 15. Failure Scenarios & Mitigations

| Failure | Impact | Mitigation |
|---------|--------|------------|
| **Redis seat store failure** | Cannot reserve seats | Redis Sentinel auto-failover (< 30s); fall back to PostgreSQL `SELECT FOR UPDATE` at reduced throughput; circuit breaker limits load during fallback |
| **PostgreSQL primary failure** | Booking confirmations fail | RDS Multi-AZ automatic failover (~30s); in-flight RESERVED bookings timeout and expire; no data loss |
| **Payment gateway timeout** | User pays but booking not confirmed | Idempotent webhook retry; Booking Service polls gateway every 30s for status; dead-letter queue for manual review |
| **Duplicate webhook (Stripe retries)** | User charged twice | `idempotency_key` on payment record; `INSERT IF NOT EXISTS` — second insert silently ignored |
| **Hold expires during active payment** | User's seats released mid-checkout | Hold Expiry Worker checks for active payment session; extends hold 5 minutes (once only); push notification to user |
| **WebSocket server failure** | Clients lose real-time updates | Auto-reconnect; on reconnect, full seat map fetched via REST; seat state authoritative in Redis regardless |
| **Kafka consumer lag** | Notifications/QR delayed | Kafka replicated (3 brokers); notification delay tolerable (SLA: 5 min); alert on lag > 1 min |
| **DDoS on booking endpoint** | Legitimate users blocked | WAF + Cloudflare; rate limiting per IP and `user_id`; virtual queue absorbs spikes; CAPTCHA for anomalous patterns |
| **Seat oversell (defense in depth)** | Two users with same seat | Redis Lua is primary gate — mathematically impossible if all reservations go through it; PostgreSQL unique index `(show_id, seat_id) WHERE status=CONFIRMED` as secondary guard |
| **S3 failure (QR codes)** | QR codes unavailable | Re-generate on demand from booking record; venue scanners can fall back to manual booking reference lookup |
| **Accidental bulk cancellation** | Mass refund event | Soft-delete with 15-minute admin review window; bulk cancel requires 2-factor admin confirmation; PostgreSQL PITR for rollback |
| **Hold Expiry Worker failure** | Expired holds not released | Secondary: PostgreSQL batch job scans `bookings WHERE status=RESERVED AND expires_at < now()` hourly as backstop |

---

## 16. Security Considerations

- **Authentication:** JWT tokens (15-minute lifetime) with refresh tokens. API keys for B2B partner integrations with separate rate limits.
- **Authorization:** Users can only view/cancel their own bookings. Admin endpoints gated by separate role claims in JWT.
- **Payment security:** Never store raw card data — Stripe tokenization handles all PCI DSS scope. Webhook signatures verified via HMAC-SHA256 (`Stripe-Signature` header) before any processing.
- **Seat hold abuse:** Maximum 1 active hold per user per show. Hold also counts against user's session hold budget — cannot hold seats across multiple shows simultaneously beyond a configurable limit.
- **Bot prevention:** CAPTCHA on booking initiation during peak sales windows. Anomaly detection (same IP, 50 booking attempts/minute) → WAF auto-block. Virtual queue inherently throttles bot activity.
- **Scalper prevention:** Device fingerprinting + velocity checks (1 booking per device per popular show release). Credit card BIN analysis to detect multiple purchases from same card pool. Purchase limits per account enforced at Booking Service.
- **QR code security:** QR payload contains `{booking_id, show_id, seat_ids, user_id}` signed with HMAC-SHA256 using a venue-specific key. Venue scanners verify HMAC offline (no DB call). New signature issued 24 hours before show — limits long-term forgery window.
- **Data privacy:** PII (names, phone numbers, email) encrypted at rest in PostgreSQL. Masked in all logs. GDPR-compliant deletion: soft-delete → hard-delete after 30 days. Booking history retained 7 years for financial compliance (separate encrypted cold store).
- **HTTPS everywhere:** TLS 1.3 enforced on all endpoints. HSTS preloaded. WebSocket connections use `wss://` exclusively.
- **Database:** PostgreSQL behind VPC — not publicly accessible. App servers connect via private subnets only. IAM role-based access (no hardcoded credentials).

---

## 17. Scaling to 100×

### Bottleneck 1 — Seat Contention (100× concurrent bookings)

Partition shows across more Redis master nodes. Seat operations for different shows are already independent — 100× shows = 100× Redis shards. This is **horizontally trivial**. No cross-shard coordination needed.

For a single mega-show with 100× more users: the virtual queue is the answer. Drain the queue at the rate the Redis master can handle (~500K ops/sec). Users wait; correctness is maintained.

### Bottleneck 2 — WebSocket Fan-Out (100× connected viewers)

Shard WebSocket servers by `show_id`. Add more WS server instances. Redis Pub/Sub fan-out is the bottleneck — mitigate by upgrading to a dedicated fan-out service (Pusher, AWS API Gateway WebSockets, Ably) for shows with 1M+ concurrent viewers. These services specialize in last-mile message distribution.

### Bottleneck 3 — PostgreSQL Write Throughput (100× bookings)

- **Connection pooling:** PgBouncer in transaction mode handles connection exhaustion.
- **Partition by date/show_id:** Booking table partitioned so hot inserts land on recent partitions.
- **At extreme scale:** Migrate to CockroachDB or Vitess (MySQL sharding) for horizontal write scaling while maintaining ACID semantics.

### Bottleneck 4 — Elasticsearch Search (100× queries)

- Add more Elasticsearch data nodes (horizontal scaling is native to ES).
- Add more replica shards for read-heavy query patterns.
- Cache popular search queries in Redis (30-second TTL) — most users search the same city + date combinations.

---

## 18. Comparison: BookMyShow vs Pastebin vs URL Shortener

| Aspect | URL Shortener | Pastebin | BookMyShow |
|--------|--------------|----------|------------|
| **Core challenge** | Short key generation | Blob storage at scale | Seat contention + concurrency |
| **Consistency model** | Eventual (acceptable) | Eventual (acceptable) | **Strong (non-negotiable for bookings)** |
| **Primary bottleneck** | Read throughput | Storage bandwidth | **Write concurrency on shared state** |
| **Key data store** | NoSQL (DynamoDB) | S3 + DynamoDB | **Redis + PostgreSQL** |
| **Payment flow** | None | None | **Critical path — external gateway** |
| **Real-time requirement** | None | None | **WebSocket seat map** |
| **Load spike pattern** | Gradual / viral | Viral shares | **Instantaneous (event release)** |
| **Data size** | Tiny (URL rows) | Large (text blobs) | Tiny (seat records, 5 GB total) |
| **Expiry complexity** | Simple TTL | 3-layer TTL | Hold TTL + booking expiry + refund |
| **Failure consequence** | Redirect fails | Content unavailable | **Financial loss, duplicate charges** |
| **Fairness concern** | None | None | **Virtual queue, scalper prevention** |
| **Concurrency technique** | None needed | None needed | **Redis Lua atomic scripts** |

---

## 19. Interview Summary

### Key Design Decisions to Memorize

- **Redis Lua for atomic seat holds:** The foundational correctness guarantee. Redis single-threaded execution makes Lua scripts indivisible — the only way to eliminate TOCTOU without distributed locks.
- **Two-phase booking (RESERVE → CONFIRM):** Decouples seat hold from payment. Seat is held optimistically; payment collected asynchronously. Hold TTL ensures fairness.
- **Virtual queue for peak load:** Converts thundering herd (500K users/second) into a controlled drain (5K users/second). Essential for any limited-inventory, high-demand system.
- **Redis for inventory, PostgreSQL for bookings:** Correct tool per problem — in-memory atomics for contention, ACID transactions for financial records.
- **WebSocket + Redis Pub/Sub:** True real-time seat map updates across horizontally scaled WS servers. One publish reaches all clients regardless of which WS server they're connected to.
- **Single-region seat routing:** Simplest correct solution to cross-region seat contention. Avoids Redlock complexity and latency.
- **Kafka for all async work:** Notifications, QR generation, waitlist, Elasticsearch updates — all decoupled from the critical booking path with zero latency impact.

### 30-Second Perfect Interview Answer

> *"BookMyShow's hardest problem is preventing two users from booking the same seat. I solve this by keeping the entire seat inventory in Redis (only ~5 GB — fits in RAM) and using a Lua script to atomically check-and-hold seats in a single operation — since Redis is single-threaded, this eliminates all race conditions.*
>
> *The booking is a two-phase flow: seat hold first (10-minute TTL via Hold Expiry Worker), then payment via Stripe webhook, then confirmation in PostgreSQL (ACID transaction for financial durability).*
>
> *For event-release spikes (Taylor Swift going on sale at noon), a virtual queue backed by a Redis sorted set drains users at a controlled rate — converts thundering herd into steady load. The seat map uses WebSocket backed by Redis Pub/Sub to push real-time updates to all connected clients. For multi-region, seat operations for a given show are pinned to one home region to avoid distributed locking overhead. Async work — notifications, QR generation, waitlist — all flows through Kafka.*"

### Common Follow-up Questions

**Q: What if Redis goes down during a peak sale?**

Redis Sentinel triggers automatic failover to a replica in < 30 seconds. During those 30 seconds, the Seat Service falls back to PostgreSQL `SELECT FOR UPDATE` on the seats table — much slower (~500 ops/sec vs Redis's 500K ops/sec) but fully correct. A circuit breaker limits incoming booking traffic to what PostgreSQL can handle, returning `503 Service Temporarily Unavailable` with `Retry-After` for excess requests. Browsing continues unaffected (CDN-cached seat maps).

**Q: How do you handle the user who pays just as their hold expires?**

The Hold Expiry Worker checks for an active payment session before releasing a hold. If a payment is in-flight (booking status = `PAYMENT_PENDING`), the worker extends the hold by 5 minutes (once only) and sends a push notification: *"Hurry — your seats are reserved for 5 more minutes."* If the webhook arrives after the hold genuinely expired and seats were re-booked by someone else, the Booking Service triggers an immediate refund and notifies the user with an apology and priority access link.

**Q: How do you scale WebSocket for 500K concurrent viewers on a single show?**

WS servers are horizontally scaled and partitioned by `show_id` using consistent hashing at the load balancer. All servers handling `show_789` subscribe to Redis Pub/Sub channel `seats:show_789`. When a seat changes, Seat Service publishes once to Redis; every WS server instance fans it out to its local connections simultaneously. With 10 WS servers each holding 50K connections, each server does 50K message pushes per seat event. For truly extreme scale (1M+ viewers), upgrade to a dedicated fan-out service like Pusher or AWS API Gateway WebSockets.

**Q: Two users click "Book" at the exact same millisecond for the same seat. Walk me through what happens.**

Both POST /v1/bookings requests arrive at the Seat Service simultaneously. They're routed to the same Redis master (seat operations for a show are pinned to one region/node). Redis executes their Lua scripts sequentially — single-threaded. User A's script arrives first (by nanoseconds): checks A12 = `AVAILABLE`, holds it → returns `1` (success). User B's script executes next: checks A12 = `HELD:userA:...` — not `AVAILABLE` → returns `0` (conflict). User A proceeds to payment. User B receives `409 Conflict` with the message "Seat A12 is no longer available. Please select a different seat." No double-booking possible.

**Q: How do you prevent scalpers from buying all tickets using bots?**

Multiple layers: (1) Virtual queue randomizes position within the same join-second, preventing bots from gaining advantage by joining early. (2) CAPTCHA required at queue join for anomalous traffic patterns. (3) Purchase limit: max 4 tickets per account per event, enforced at Booking Service. (4) Device fingerprinting flags accounts using the same device with different user IDs. (5) Credit card BIN analysis detects multiple purchases from the same card issuer pool. (6) WAF rate limits by IP — 50+ booking attempts/minute triggers auto-block.

---

*End of Document — BookMyShow HLD Reference Guide*
