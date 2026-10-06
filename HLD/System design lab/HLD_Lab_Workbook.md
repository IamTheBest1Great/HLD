# HLD: Learn by Building - Complete Lab Workbook

**Source basis:** HLD Master Notes - Consolidated Index, supplied by the learner. **Project:** EventHub. **Format:** local implementation exercises plus four capstones.

## Contents

00. Lab Environment and Working Method
01. Networking & Protocols
02. Scalability
03. Consistency & CAP Theorem
04. Availability
05. Database
06. Caching
07. Messaging Queues & Async
08. Storage Systems
09. Fault Tolerance & Reliability
10. Reducing Latency
11. Durability
12. Security
13. Maintainability
14. API Design
15. Estimation Skills
16. HLD Architecture and Frameworks
17. Roadmaps and System Pattern Projects
18. Capstone - URL Shortener
19. Capstone - Pastebin
20. Capstone - BookMyShow
21. Capstone - Google Drive and Dropbox

---

# 00. Lab Environment and Working Method

## Scope and provenance

The supplied **HLD Master Notes - Consolidated Index** names 15 core concepts, Architecture & Frameworks, Roadmaps & Study Plans, and four case studies. It also lists 14 system-pattern families under the roadmap. The linked 21 Markdown chapters were not supplied. This workbook preserves those visible topic names and their ordering, but the projects, implementation steps, workloads, acceptance criteria, and study schedule below are newly authored exercises, not quotations or reconstructions of the unseen chapters.

Every top-level topic and every named roadmap pattern has an implementation assignment. A heading such as "Core Reliability Patterns" does not reveal its complete contents; consequently this workbook cannot establish one-to-one coverage of every strategy inside the missing chapter. Use the coverage checklist at the end to match the visible headings. Official implementation references are identified by short source IDs, resolved in REFERENCES.md.

## The project: EventHub

Build a multi-tenant event and workshop platform. Organizations publish events; users browse, book places, receive notifications, upload materials, and participate in live discussions. Later add link sharing, expiring pastes, numbered-seat reservations, and a file-sync client.

Start with a modular application, not a fleet of microservices. Extract a service only when a lab asks you to test a concrete scaling or ownership boundary.

Baseline request path:

```text
HTTP client -> NGINX -> api1 / api2 -> PostgreSQL
                                -> Redis (optional, disposable cache)
PostgreSQL outbox -> publisher -> RabbitMQ -> notification worker
                                            -> PostgreSQL dedup + notification
```

**Implemented in this starter:** HTTP API, fixture identities, tenant/owner-scoped reads, validated booking input, PostgreSQL conditional inventory update, transactional idempotency and outbox, cache-aside reads, NGINX balancing, publisher confirmations, consumer deduplication, metrics, request IDs, a load script, and a concurrency script.

**Assignments, not completed features:** production authentication, numbered-seat holds, real payments, search, file storage, replication/failover automation, full tracing, rate-limit middleware, GraphQL/gRPC servers, WebSockets, CRDT collaboration, and production disaster recovery. Some reusable algorithms are supplied in `src/domain.mjs`, but are not automatically wired into the API.

**Safety boundary:** run only on localhost or an isolated training environment with synthetic data. The included `lab-*` bearer values are public fixture identities, not secure authentication. Passwords in Compose are lab-only. Do not deploy this package publicly or use real customer records/payment credentials. Destructive exercises must use disposable lab databases or directories.

## Prerequisites and startup

Use Node.js 22 or later and Docker with Compose v2. On Windows, run the shell examples in a Linux shell such as WSL2; the Node scripts themselves are cross-platform. The SQL examples use PostgreSQL 17 syntax. The default app does not require a cloud account.

Choose one path:

```bash
# Zero external services: exercise domain logic and HTTP contracts.
node --test test/*.test.mjs
node src/demo.mjs
# Demo listens on 127.0.0.1:3000 and loses data when restarted.
```

```bash
# Main lab: real database + cache + two APIs + gateway.
docker compose up -d --build
docker compose exec api1 node scripts/seed.mjs
curl http://127.0.0.1:8080/health/ready

# Add the real broker, publisher, and worker when reaching Lab 07.
docker compose --profile async up -d --build
```

Compose waits for the explicitly declared database/API health checks during startup. This is not a runtime failover mechanism. See [COMPOSE]. The Redis dependency is intentionally optional for serving requests.

Use the checked-in dependency ranges initially. Docker's first build needs internet access. The authoring environment could not access npm and did not generate a lockfile. After a successful local `npm install`, retain `package-lock.json`, switch repeatable builds to `npm ci`, and record image digests. The listed image tags are exercise choices, not a claim that they are the newest or production-approved versions.

## First successful request

```bash
curl -i http://127.0.0.1:8080/v1/events \
  -H 'Authorization: Bearer lab-alice'

curl -i http://127.0.0.1:8080/v1/bookings \
  -H 'Authorization: Bearer lab-alice' \
  -H 'Content-Type: application/json' \
  -H 'Idempotency-Key: booking-first-001' \
  --data '{"eventId":"11111111-1111-4111-8111-111111111111","quantity":1}'
```

Repeat the booking with the same key and body. It should return the original booking ID. Reusing that key with `quantity:2` should return `409`. A successful replay is not another inventory decrement.

Fixtures: `lab-alice` and `lab-bob` belong to `org-a`; `lab-charlie` belongs to `org-b`. Event `11111111-1111-4111-8111-111111111111` begins with 100 places. Event ending `111112` begins with exactly one place. Seed is non-destructive: repeating it does not reset bookings or inventory.

## Workflow for every experiment

Create a branch and copy `evidence/TEMPLATE.md` to a lab-specific file. Write a hypothesis and invariant before coding. Run the naive/baseline implementation. Introduce a specific failure or workload. Make one change. Repeat the same test and record results. Commit the code, raw measurements, invariant query, and an architecture decision record.

An invariant is stronger than "the endpoint returned 200." Examples: no negative inventory; no duplicate effect for a replay; no cross-tenant data; every acknowledged file is retrievable; no published version points to missing bytes.

No latency target in this workbook is a measured result from your hardware. Workloads and thresholds are proposed test settings. Record the observed values, including failures.

## Reset and cleanup

To stop without deleting data:

```bash
docker compose --profile async down
```

For a **complete destructive reset of this project's lab data only**:

```bash
docker compose --profile async down -v
docker compose up -d --build
docker compose exec api1 node scripts/seed.mjs
```

Do not use that reset during a durability exercise: removing a volume is deliberate data deletion, not a crash. Save evidence outside container volumes.

## Definition of done

A lab is complete only when you can show the implementation, reproduce the original problem, pass its correctness assertions, explain a remaining failure case, and state the extra complexity or cost introduced by the solution.


---

# 01. Networking & Protocols

**Project increment:** expose EventHub through a gateway and implement alternate communication paths.

**Source alignment:** networking fundamentals, OSI layers, HTTP/HTTPS, TCP/UDP, DNS, load balancing/proxies, real-time communication, REST/gRPC/GraphQL, CDN/global networking. The final comparison and evidence review cover the index's pattern/interview-oriented headings.

### 01-A. Trace one request through the network

Start the baseline and request one event with `curl -v`. Identify the destination, port, request line, headers, status, and response body. Add a request ID to a client request and compare it with the server-generated `X-Request-ID`; the baseline deliberately generates its own ID rather than trusting arbitrary client text.

Run:

```bash
curl -s -o /dev/null \
  -H 'Authorization: Bearer lab-alice' \
  -w 'dns=%{time_namelookup} connect=%{time_connect} first_byte=%{time_starttransfer} total=%{time_total}\n' \
  http://127.0.0.1:8080/v1/events

docker compose exec api1 node --input-type=module -e \
  "import dns from 'node:dns/promises'; console.log(await dns.lookup('postgres',{all:true}))"
```

The curl timestamps are cumulative checkpoints, not independent durations to add. Derive phase deltas. A numeric loopback address does not exercise external DNS, so do not report it as an internet DNS measurement. The second command observes Docker service-name resolution.

**Build:** add a diagnostic page showing request ID, instance, status, payload bytes, and elapsed time. Map the actual request to application, transport, network, and local-link responsibilities. A browser capture alone cannot prove what happened at every OSI layer.

**Pass:** explain which parts of the path disappear when requesting api1 directly on port 3001 and which remain.

### 01-B. Add HTTPS and test certificate validation

Generate an isolated, short-lived local certificate:

```bash
mkdir -p infra/tls
openssl req -x509 -newkey rsa:2048 -nodes \
  -keyout infra/tls/key.pem -out infra/tls/cert.pem -days 2 \
  -subj '/CN=eventhub.test' \
  -addext 'subjectAltName=DNS:eventhub.test,IP:127.0.0.1'
```

Add a separate NGINX server listening on 443, mount the certificate/key read-only, and publish `127.0.0.1:8443:443`. Set `ssl_certificate` and `ssl_certificate_key` to their mounted paths. Preserve the original proxy locations. Do not commit the generated private key.

Test with `curl --cacert infra/tls/cert.pem --resolve eventhub.test:8443:127.0.0.1 https://eventhub.test:8443/health/live`. Repeat without the trusted certificate, then with a mismatched hostname. Record validation failures. Do not solve the exercise by permanently disabling verification.

**Pass:** the trusted, matching request succeeds; the untrusted/mismatched requests fail. Explain where TLS terminates and whether the gateway-to-API hop is encrypted. HTTP semantics are specified independently of these deployment choices. [HTTP]

### 01-C. Implement TCP framing and a lossy UDP telemetry sender

Create `transport/tcp-server.mjs`, `tcp-client.mjs`, `udp-server.mjs`, and `udp-client.mjs` using Node's `net` and `dgram` modules. Send `{sequence,sentAt,value}` records numbered 1-100.

For TCP, implement newline-delimited JSON with a receive buffer. Split one JSON record across two writes and put two records in one write. The receiver must still produce exactly 100 records. Add a maximum frame size and discard/close invalid frames.

For UDP, send one record per datagram. Deliberately discard every fifth datagram in your receiver to create a reproducible application-level loss experiment. Track missing sequences and duplicates; do not imply that this artificial loss is a measured network drop rate. Then add an acknowledgement/retransmission experiment with deduplication, or explicitly choose best-effort delivery for disposable telemetry.

**Pass:** booking commands use the reliable application request path; telemetry loss does not corrupt bookings. Explain why transport delivery alone does not prove a transaction committed.

### 01-D. Compare gateway balancing policies

Call `/health/live` 30 times and record the `X-Instance` header. Change the upstream from its default selection to `least_conn`, then add an artificial delay to one API instance. Run a concurrent workload, not only sequential curls.

Stop api1 while traffic continues. Separate requests that reached the failed node before failure detection from those routed after it. Restart api1 and observe recovery. The included `max_fails` and `fail_timeout` settings use upstream failure observations; they are not active application-health probes. [NGINX]

**Pass:** save per-instance counts, error counts, and recovery observations. Do not report "zero downtime" unless the measurements support that claim. Verify a sent booking POST is not blindly retried by the gateway with an unsafe policy.

### 01-E. Implement polling, SSE, and WebSocket versions of a live seat count

First build polling with `GET /v1/events/:id` every two seconds. Record request count and observed update delay. Next add `GET /v1/events/:id/stream` returning `text/event-stream`; send events with a monotonically increasing per-event version, heartbeat comments, and a reconnect cursor. Disable proxy buffering for that location.

Finally add a WebSocket gateway with authenticated subscriptions and server-side tenant checks. Use a dedicated Redis pub/sub connection for low-latency fan-out, but persist authoritative state in PostgreSQL. On a version gap or reconnect, fetch a new snapshot; Redis pub/sub is not your durable message history.

Test two clients attached to different API replicas. Disconnect one, make three bookings, reconnect, and verify it resynchronizes. Bound send buffers and disconnect/resync slow consumers rather than accumulating unlimited messages.

**Pass:** the displayed count converges after reconnection; displayed availability is never accepted as the authority for selling a seat. Explain which direction of communication each transport needs.

### 01-F. Expose the same catalog via three API styles

Keep `GET /v1/events/:id` as REST. Add a gRPC service with this contract:

```proto
syntax = "proto3";
package eventhub;
service Catalog { rpc GetEvent(GetEventRequest) returns (Event); }
message GetEventRequest { string id = 1; }
message Event { string id = 1; string title = 2; int32 available = 3; }
```

Resolve tenant identity from authenticated transport metadata, not from an unrestricted `tenantId` field. Generate/load descriptors and add client deadlines using the official Node gRPC workflow. [GRPC]

Create a GraphQL `event(id)` field with nested organizer details. Deliberately implement an N+1 resolver, count database calls, then batch organizer lookups using a request-scoped DataLoader. Never share its authorization-sensitive cache across users. [GRAPHQL]

**Pass:** all interfaces enforce the same ownership rules; compare bytes, errors, tooling effort, and latency on the same payload. Do not conclude one protocol is universally faster from a single local benchmark.

### 01-G. Build a local edge-cache experiment

Add a public, immutable event-image endpoint such as `/assets/<contentHash>.png`. Put a second caching proxy before the origin. Set a long cache lifetime only on those immutable public assets. Do not cache authenticated bookings or private file URLs under a public key.

Warm the asset, stop the origin, and observe what the edge can still serve. Change the image content and therefore its hash. For mutable metadata, set a short lifetime and implement invalidation separately. Add simulated origin delay to show the difference between cache hit and miss paths.

**Pass:** the new URL displays the new image, private responses do not leak, and you describe this as a local edge-cache simulation rather than a deployed global CDN. Carry the measured trade-off into Lab 02.


---

# 02. Scalability

**Project increment:** handle increasing catalog traffic without confusing capacity with correctness.

**Source alignment:** scaling fundamentals, core techniques, CDN, patterns, and decision frameworks.

### 02-A. Find the first bottleneck with a repeatable workload

Install k6 for your workstation, seed the catalog, and run `k6 run load/catalog.js`. Start at 50 requests/second, then repeat at 100 and 200 using `RATE`. Use three runs per level, including one cold-cache and one warm-cache run. Keep payload, event distribution, machine resources, and duration fixed.

```bash
RATE=50 k6 run load/catalog.js
RATE=100 k6 run load/catalog.js
RATE=200 k6 run load/catalog.js
```

The script uses an arrival-rate executor. Watch `dropped_iterations`: a load generator that cannot start scheduled work has not actually delivered the requested traffic. Closed-loop concurrency tests and open arrival-rate tests answer different questions. [K6]

Record client p50/p95/p99, completed throughput, error categories, CPU, memory, PostgreSQL connections, and cache behavior. Scrape each API's metrics directly on ports 3001 and 3002; scraping randomly through the gateway would mix different process counters.

**Pass:** identify a measured bottleneck, not merely the first component you recognize.

### 02-B. Vertical versus horizontal capacity

Stop api2 and establish a one-instance baseline. Give api1 a small CPU limit, then a larger one, using a temporary Compose override with `cpus` and `mem_limit`. Repeat the same workload. Restore the override before the next experiment.

Bring api2 back and repeat. Track total database pool capacity: two pools of 10 connections are not the same configuration as one pool of 10. Add enough replicas and watch whether PostgreSQL, the gateway, or the generator becomes the limiting component.

**Build:** a table with instance count, CPU budget, pool size per instance, achieved throughput, p95, error rate, and bottleneck.

**Pass:** explain diminishing gains and any regressions. A single hot event may still serialize its inventory updates even while catalog reads scale.

### 02-C. Remove hidden instance-local state

Create a deliberately naive `POST /lab/cart/items` route storing carts in a process-local map. Call through the gateway, then kill one API. Observe missing or inconsistent carts.

Move the cart state into an explicit shared store with TTL and ownership checks. Decide whether carts are disposable or require durable recovery; do not automatically treat Redis caching settings as adequate durability. Repeat across two replicas.

For file uploads, repeat the problem with a file saved in one container's writable layer. Replace it with shared, durable storage in Lab 08 rather than hoping sticky sessions will preserve the bytes.

**Pass:** any replica can serve the cart, and losing an API process does not lose authoritative booking state. Explain what sticky sessions would and would not fix.

### 02-D. Implement shard routing and a resharding exercise

Use the supplied `shardFor()` rendezvous-hashing function on 10,000 synthetic organization IDs. Map keys to shards A and B, add C, and count moved keys. The accompanying unit test checks that changed assignments move to the new node rather than needlessly between old nodes.

Create two isolated PostgreSQL databases with the same schema. Write a router that resolves `tenantId -> shard` before acquiring a connection. Keep one tenant's booking transaction on one shard. Do not split a single inventory transaction across independent databases accidentally.

To migrate a tenant, add a routing directory with `activeShard`, `migrationState`, and `routingVersion`. Copy a snapshot, replay changes, briefly fence writes for cutover, verify counts/checksums, then atomically change the directory. Route old clients through the directory rather than relying on a cached hash forever. Delete the old copy only after a verification window.

**Failure:** stop migration after the snapshot and before cutover. Repeat a booking. **Pass:** exactly one shard is authoritative, no accepted update is silently omitted, and rollback still has a clearly defined writer.

### 02-E. Protect the bottleneck rather than scaling blindly

Change traffic from uniform event reads to 90% of requests for one event. Then change it to a booking spike for a one-seat event. Compare cache hit rates, database contention, and rejected requests.

Implement a bounded admission queue for the booking operation, with an explicit maximum wait and a `429` or `503` contract depending on whether the rejection is a quota or overload decision. Add jitter to retries in the test client. Never let an unbounded in-memory queue convert an overload into a memory crash.

**Pass:** admitted operations preserve inventory correctness; rejected requests fail promptly and explicitly; the queue age remains bounded. Record which component would be scaled next and why.


---

# 03. Consistency & CAP Theorem

**Project increment:** make the booking invariant survive concurrency, lag, and partitions.

**Source alignment:** distributed-system foundations, CAP/PACELC, consistency models, replication/consensus, design decisions, and real-world patterns.

### 03-A. Reproduce a lost update before fixing it

Create a separate `inventory_lab(id integer primary key, available integer)` table and insert `(1,2)`. In two psql sessions, begin transactions and read `available`; both should read 2. In session A write the client-computed value 1 and commit. In session B also write 1 and commit.

The final value is 1 despite two intended decrements. Record the complete schedule. This is an application read-modify-write problem; running statements inside transactions does not by itself select the required isolation or locking behavior.

Replace the client-computed assignment with a conditional arithmetic update. Under PostgreSQL Read Committed, an UPDATE that waits for a concurrent updater rechecks its condition against the updated row. Use that behavior deliberately rather than relying on a stale application read. [PG-ISO]

**Pass:** explain the difference between an invariant-preserving conditional update, explicit row locking, optimistic version checks, and full serializable transactions.

### 03-B. Prove no overselling in the real API

Inspect `PgStore.book()` and identify every write within the same transaction. The key inventory statement is:

```sql
UPDATE events
SET available = available - $3, version = version + 1
WHERE tenant_id = $1 AND id = $2 AND available >= $3
RETURNING id;
```

The application must require one returned row before inserting the booking. The database also has an `available >= 0` check as a backstop. A constraint alone does not automatically implement an entire multi-step booking protocol.

Use a fresh seeded one-seat event, then run:

```bash
node scripts/race.mjs
```

The script launches 100 attempts with distinct idempotency keys. On a healthy, non-overloaded run, expect one `201` and the remaining attempts to report unavailability. If infrastructure timeouts occur, record them separately; they do not permit a second sale.

Verify authoritative state:

```sql
SELECT e.id,e.capacity,e.available,
       COALESCE(SUM(b.quantity) FILTER (WHERE b.status='confirmed'),0) AS sold
FROM events e LEFT JOIN bookings b ON b.event_id=e.id AND b.tenant_id=e.tenant_id
WHERE e.id='11111111-1111-4111-8111-111111111112'
GROUP BY e.id;
```

**Pass:** `sold=1`, `available=0`, and `sold+available=capacity`. Repeat with concurrent retries sharing one key and verify exactly one booking ID. Do not run the distinct-key and same-key cases on an already exhausted event without resetting or creating a fresh fixture.

### 03-C. Implement and measure read-your-writes

Add `event_projection(event_id primary key, version bigint, available integer, updated_at timestamptz)`. Update it asynchronously from committed domain events with an artificial delay. This is a read-model simulation of lag, not a physical replica.

Implement three read modes: primary reads, projection reads, and `minimumVersion` reads. In the last mode, use the projection only if its version is at least the client's last observed version; otherwise wait for a bounded interval or read the primary. Do not silently return an older version while claiming read-your-writes.

Deliver version 7 before version 6. Make the projection update conditional on `incomingVersion > storedVersion`. Add unique event IDs for duplicate detection; a version comparison and event deduplication address related but different problems.

**Pass:** the weak read can visibly lag, the read-your-writes mode never regresses below its promised minimum, and a delayed old event does not overwrite a newer projection.

### 03-D. Observe a real consensus cluster under failure and partition

Use `infra/compose.consensus.yaml` to run three etcd members. It follows etcd's static container bootstrapping model. [ETCD-CONTAINER]

```bash
docker compose -f infra/compose.consensus.yaml up -d
docker compose -f infra/compose.consensus.yaml exec e1 \
  etcdctl --endpoints=http://e1:2379,http://e2:2379,http://e3:2379 endpoint status --write-out=table
```

Write a key, stop one member, and write again. Then stop a second member and repeat with a finite command timeout. Restore all members.

For an actual partition experiment, disconnect e3 from the Compose network while leaving its process alive:

```bash
docker network disconnect eventhub-consensus_default \
  "$(docker compose -f infra/compose.consensus.yaml ps -q e3)"
```

Attempt a write inside e3 against its own loopback endpoint with `etcdctl --command-timeout=3s --endpoints=http://127.0.0.1:2379 put demo minority`. Attempt another through e1/e2. Restore e3 using `docker network connect --alias e3 eventhub-consensus_default <e3-container-id>`.

Etcd's majority side can make progress while the minority cannot commit conflicting writes. Leader transitions can still introduce temporary failures. [ETCD-FAILURES]

**Pass:** preserve the operation histories, distinguish a stopped server from a network partition, and explain the behavior of the specific read/write operation. Do not label the whole EventHub application simply "CP" or "AP."

### 03-E. Build an availability-first counter and a latency/consistency comparison

Implement two independently writable page-view replicas using a grow-only counter: each node increments its own component; merge by component-wise maximum; obtain the total by summing components. Disconnect, increment both, exchange state repeatedly, and verify convergence. This counter is a deliberately mergeable data type, not a safe replacement for seat inventory.

For the no-partition trade-off, add a controlled simulated remote-ack delay to a synchronous path and compare it with asynchronously propagated analytics. Mark it as a timing model, not an implementation of a new consensus protocol. Record response latency and observed staleness under both modes.

**Pass:** state which invariant makes merging acceptable for views and unacceptable for selling a unique seat. Explain CAP's partition case separately from the ordinary latency/consistency design question associated with PACELC.


---

# 04. Availability

**Project increment:** keep useful operations available during bounded failures and make outages measurable.

**Source alignment:** availability foundations, techniques, and patterns by system type.

### 04-A. Define an availability indicator before creating a dashboard

Define an eligible booking attempt and a successful service outcome. A correct "sold out" business response is not the same as an infrastructure failure. Measure both request-based availability and, separately, the duration of complete unavailability.

Create a probe sending one request per second for five minutes. Kill api1 halfway through. Save timestamps, status categories, and instance labels. Repeat after killing the gateway: the starter has one gateway and therefore does not eliminate that failure point.

**Pass:** publish the numerator and denominator, not only a percentage. Calculate the error budget for a lab target of your choice, and label it as a target rather than a achieved service-level commitment.

### 04-B. Separate liveness, readiness, and optional dependencies

The baseline liveness endpoint reports the process is serving. Readiness queries PostgreSQL. Stop Redis: catalog reads should fall back to PostgreSQL; the optional cache should not mark the entire application unusable. Stop PostgreSQL: readiness and authoritative writes should fail.

Add a draining state for deployment: stop accepting new work, mark the instance unready to a health-aware router, let bounded in-flight work finish, then exit. The provided NGINX setup does not actively poll readiness; implement an orchestrator/proxy integration or explicitly remove the instance from routing before stopping it.

**Pass:** optional recommendations/notifications can degrade without falsely confirming bookings; a liveness policy does not repeatedly restart all instances during a database outage.

### 04-C. Add a physical standby and perform a fenced manual failover

Use the replication setup recipe in `infra/REPLICATION.md`. Create a replication role on the lab primary, permit its replication connection in the lab-only `pg_hba.conf`, and initialize a standby with `pg_basebackup -R`. Query `pg_is_in_recovery()` and compare WAL positions.

Pause replay with `pg_wal_replay_pause()`, make a booking on the primary, and demonstrate a stale read from the standby. Resume replay and measure catch-up. Do not interpret a healthy TCP connection as proof the replica is current.

For failover, stop/fence the old primary before promoting the standby with `pg_promote()`. Repoint writers, run the booking invariant query, and compare committed records against your client history. Record any data loss under asynchronous replication. Do not bring the old primary back as another writer; rebuilding/rejoining it is a separate operation.

**Pass:** a single writer is enforced throughout, availability loss is measured, and the result includes an observed recovery-point gap rather than assuming replication means zero loss. See [PG-REPLICATION].

### 04-D. Deploy a backwards-compatible change without losing service

Add a nullable `attendee_note` column, deploy code that tolerates it being absent/null, then deploy writers that use it. Run continuous reads and bookings while replacing one API instance at a time. Roll the code back while leaving the additive column in place.

Next deliberately rename or remove a column while old code is still running on one instance in a disposable branch. Observe failures. Restore the schema and rewrite the migration as expand, transition, and later contract.

**Pass:** the compatible rollout preserves service and invariants; the incompatible experiment is captured as a failure case. Keep availability evidence separate from the durability proof in Lab 11.


---

# 05. Database

**Project increment:** make storage choices from access patterns, query plans, and transaction boundaries.

**Source alignment:** SQL/NoSQL foundations, replication/HA, partitioning/sharding, indexing, performance, scaling, and design patterns.

### 05-A. Model constraints before adding indexes

Inspect `db/001_init.sql`. Identify primary keys, the composite tenant/event foreign key, checks, and ownership indexes. Add organizations and users, then replace the fixture-only identifiers with foreign keys while keeping a clean migration path.

Write tests that attempt a negative quantity, a booking for another tenant's event, an orphaned event reference, and a duplicate idempotency key. Assert database and API behavior. Do not assume UUIDs are an authorization boundary.

Create a requirement that one organizer can assign many instructors to many events; implement a join table rather than comma-separated IDs. Then add optional display-only event metadata as JSONB and specify which fields are indexed or validated.

**Pass:** show which invariants are protected in the database, which in the application, and why both are needed.

### 05-B. Build a million-row query-tuning experiment

Create a disposable workload table instead of filling the real booking table with invalid historical data:

```sql
CREATE TABLE workload_orders AS
SELECT g::bigint AS id, (g%10)::integer AS tenant_id,
       (g%1000)::integer AS user_id,
       timestamptz '2026-01-01 00:00:00+00' + g*interval '1 second' AS created_at,
       repeat('x',100) AS payload
FROM generate_series(1,1000000) g;
ANALYZE workload_orders;

EXPLAIN (ANALYZE, BUFFERS)
SELECT id,created_at FROM workload_orders
WHERE tenant_id=7 AND user_id=7
ORDER BY created_at DESC,id DESC LIMIT 20;

CREATE INDEX workload_owner_time
ON workload_orders(tenant_id,user_id,created_at DESC,id DESC);
```

Repeat the plan and workload after adding the index. Compare rows visited, buffers, sort operations, index size, and write cost. Use the same data and parameters. Do not expect an index scan to be optimal for every query; test a query that returns a large fraction of the table too. [PG-EXPLAIN]

**Pass:** justify the index column order with the actual filter/sort pattern and demonstrate its cost on insert/update throughput.

### 05-C. Compare relational, key-value, and document-shaped access

Implement event lookup three ways: normalized PostgreSQL rows, a Redis key containing a disposable JSON projection, and a document representation containing an event with its display metadata. For the third path, first use PostgreSQL JSONB, then optionally repeat in a local MongoDB instance to compare an actual document database. JSONB support alone does not turn PostgreSQL into a separate NoSQL system.

Define workloads before comparing: point lookup, filter by organizer and date, update one embedded field, and join to an instructor. Create appropriate indexes for each. Measure duplication and the work required to update an instructor name appearing in many materialized documents.

**Failure:** interrupt the projection update halfway through. **Pass:** the authoritative data remains intact, stale projections can be rebuilt, and you choose storage per access pattern rather than declaring SQL or NoSQL universally superior.

### 05-D. Partition time-series data and distinguish partitioning from sharding

Create a telemetry table with monthly partitions:

```sql
CREATE TABLE telemetry (
 occurred_at timestamptz NOT NULL, event_id uuid NOT NULL,
 tenant_id text NOT NULL, value double precision NOT NULL,
 PRIMARY KEY(occurred_at,event_id)
) PARTITION BY RANGE(occurred_at);
CREATE TABLE telemetry_2026_01 PARTITION OF telemetry
FOR VALUES FROM ('2026-01-01') TO ('2026-02-01');
CREATE TABLE telemetry_2026_02 PARTITION OF telemetry
FOR VALUES FROM ('2026-02-01') TO ('2026-03-01');
```

Seed both months. Compare a January-only query with one missing a time predicate. Inspect partition pruning in EXPLAIN. PostgreSQL's partition pruning uses partition bounds; an index and a partition solve different parts of the access problem. [PG-PARTITION]

Test an out-of-range insert, add a controlled default/new-month strategy, and practice retiring a partition. Explain why a primary key on only `event_id` cannot simply be assumed globally enforced by this partitioned-table definition.

**Pass:** name the difference between partitions on one server and independent database shards from Lab 02. Show a retention operation without deleting unrelated months.

### 05-E. Tune pools, replicas, and transaction duration together

Compare API pool sizes 2, 10, and 30 while holding API count and workload constant. Log acquisition wait separately from query execution. Create a slow transaction holding an inventory row lock; inspect waiting sessions and set a finite lock timeout.

Route catalog reads to the standby from Lab 04, but keep booking decisions on the primary. Test replica lag and read-your-writes. Add query cancellation/deadline handling and release clients in `finally` blocks after commit/rollback.

**Pass:** no leaked connections, no assumption that more connections always mean more throughput, and no correctness decision taken from a lagging replica.


---

# 06. Caching

**Project increment:** accelerate catalog reads while making cache failure and staleness visible.

**Source alignment:** fundamentals, strategies, eviction, distributed caching, invalidation, common problems, and system patterns.

### 06-A. Instrument cache-aside instead of guessing its value

Read an event twice and compare `X-Cache`. Add counters for hit, miss, cache timeout, and database fallback. Run the catalog load script with `CACHE_ENABLED=false`, then with the cache enabled. Keep the data and workload identical.

Inspect the baseline path: check cache, fetch the authoritative row on miss, attempt a cache fill, and return. Redis failures are caught because it is optional. The HTTP response uses `no-store`; this application cache is distinct from browser/CDN caching.

**Build:** record hit ratio and database QPS together. A high hit ratio for one tiny hot object is not evidence that a large mixed workload will fit in memory.

**Pass:** disabling Redis does not invent successful bookings or lose authoritative data; the fallback's increased database load is visible.

### 06-B. Implement four strategy variants with explicit failure semantics

Create interchangeable `CatalogRepository` wrappers. In **cache-aside**, callers explicitly fill after misses. In **read-through**, the wrapper owns the loading logic, so the service does not know how fills occur. Compare code ownership as well as runtime behavior.

For **write-through**, have the wrapper synchronously write the database and then refresh Redis. Inject a Redis failure after the database commit. Decide whether to return the durable write result and invalidate/retry cache maintenance, or return an explicitly ambiguous response. Do not claim the two independent stores form one atomic transaction.

For **write-behind**, use only disposable page-view aggregation: accept into a bounded buffer, flush batches periodically, and kill the process before a flush. Measure loss. Then replace the volatile buffer with a durable queue to change the acknowledgement boundary. Do not use the naive volatile variant for inventory or payments.

**Pass:** produce a strategy/failure table showing who owns loading, when success is acknowledged, what can be lost, and how repair works.

### 06-C. Reproduce the stale-fill invalidation race

Add a deterministic test hook immediately after the database read and before the cache set. Start reader A and pause it there. Writer B changes the event and deletes the cache key. Resume A: it now fills the cache with the older row.

This demonstrates why delete-after-write alone is not a universal consistency proof. A five-second TTL limits retention after a fill, but an arbitrarily delayed stale fill does not establish a strict five-second freshness bound relative to the underlying update.

Implement one explicit remedy and test its assumptions: version-aware immutable cache keys plus a coherent version pointer; a generation/fencing token checked atomically before fill; or primary reads for operations requiring strict freshness. Keep booking decisions on PostgreSQL regardless.

**Pass:** the controlled interleaving no longer returns a version that violates the promised read contract. Document invalidation outages and delayed messages as remaining cases.

### 06-D. Collapse stampedes and handle negative-cache invalidation

Delete one hot cache entry and send 100 simultaneous reads. Count database fetches. Add an in-process map of in-flight load promises so concurrent local requests await the same fetch. Remove the promise in `finally` so a failed load does not poison the key forever.

Repeat across both API replicas. For cross-process coordination, experiment with a bounded lease acquired using `SET key token NX PX ttl`; release only if the token still matches. Use a unique token and a compare-and-delete Lua script. This is cache-fill coordination, not an authoritative seat lock. Redis's lock documentation explicitly discusses the safety limits of failover-based and expiring leases. [REDIS-LOCK]

Cache an absent object briefly, create it, and verify creation invalidates the negative cache. Apply TTL jitter to many keys expiring at once. Test hot-key expiry separately from a mass expiry wave.

**Pass:** measure collapsed work, specify behavior when the loader dies, and show a newly created object is not hidden indefinitely by a stale negative entry.

### 06-E. Compare eviction policies and isolate authoritative state

Use only the dedicated lab Redis. Populate more data than the configured cache budget. Compare `allkeys-lru`, `allkeys-lfu`, and `noeviction` under uniform and skewed reads. Inspect `INFO stats` and `INFO memory`, including evictions and rejected writes.

Redis exposes policy choices with different eviction behavior; its LRU/LFU implementations are approximations rather than a perfect record of every access. [REDIS-EVICT]

Now store a simulated rate-limit key in the same evicting instance and demonstrate why eviction can weaken quota enforcement. Move security/coordination state to a separately configured store with a documented failure policy. Do not simply add "important" key prefixes to an all-keys eviction policy.

**Pass:** cache size is based on measured bytes, not just object count; eviction never removes the only copy of a booking.

### 06-F. Add a two-level cache and test invalidation loss

Add a small per-process L1 cache before Redis L2. Include value version and expiry metadata. Broadcast invalidations, then disconnect one subscriber during an event change. Reconnect and observe whether it serves stale L1 data.

Implement bounded L1 lifetime and a resynchronization mechanism. Define which read contracts allow that bound. Compare L1 hit latency against the added invalidation, memory, and observability complexity.

**Pass:** state a precise freshness policy and its failure assumptions; never present best-effort pub/sub invalidation as guaranteed delivery.


---

# 07. Messaging Queues & Async

**Project increment:** deliver booking notifications without making a database commit depend on a healthy notification provider.

**Source alignment:** messaging systems, delivery guarantees, idempotency, async patterns, scaling, common failures, and real-world architectures.

### 07-A. Remove notification delivery from the synchronous transaction

Create a naive branch that writes a booking and then calls a fake email endpoint before returning. Add two seconds of delay and an intermittent error. Record booking latency and client retries.

Return to the baseline outbox design and start the async profile:

```bash
docker compose --profile async up -d --build
```

Create a booking and poll `/v1/notifications`. The starter records a simulated notification in PostgreSQL; it does not send an external email. Its transactional guarantee ends at that database effect.

**Pass:** broker/worker downtime delays notifications but does not erase the committed booking. The pending outbox is visible and eventually drains after recovery.

### 07-B. Inspect the transactional outbox and exercise both crash windows

`PgStore.book()` inserts inventory changes, the booking, an idempotency response, and an outbox event within one database transaction. `publisher.mjs` reads pending rows with `FOR UPDATE SKIP LOCKED`, publishes one durable message, waits for broker confirmation, and only then marks the row published.

First stop the publisher before creating a booking. Verify a pending row exists and is later delivered. Next set `PUBLISH_PAUSE_AFTER_CONFIRM_MS=10000`, recreate the publisher, create a fresh booking, and kill the publisher during that pause. Restart normally. The message may be delivered again because publish and database marking are separate transactions.

```bash
PUBLISH_PAUSE_AFTER_CONFIRM_MS=10000 \
  docker compose --profile async up -d --force-recreate publisher
# Create a fresh booking, then kill during the configured pause.
docker compose kill -s KILL publisher
PUBLISH_PAUSE_AFTER_CONFIRM_MS=0 \
  docker compose --profile async up -d --force-recreate publisher
```

**Pass:** every committed outbox event remains eligible until acknowledged publication, and duplicate delivery does not duplicate the effect. Broker publisher confirmations and consumer acknowledgements cover different stages; neither alone proves the other stage occurred. [RABBIT-CONFIRMS]

### 07-C. Make the consumer effect duplicate-safe

Inspect `worker.mjs`. It inserts the event ID into `processed_events` and creates the notification in the same transaction. If the ID already exists, it skips the effect. It acknowledges only after that transaction commits.

Set `WORKER_PAUSE_AFTER_COMMIT_MS=10000` and kill the worker after its database commit but before the broker acknowledgement. Restart without the pause. Query notifications by the event ID and require one row.

Repeat by deliberately resetting one outbox row's `published_at` to NULL in the isolated lab and allowing republishing. Use the same event ID, not a new ID for the retry.

**Pass:** one simulated effect per event ID. State the scope accurately: at-least-once delivery plus a transactional idempotent consumer, not magical end-to-end exactly-once delivery. For a real external email/payment provider, use the provider's idempotency support or reconcile ambiguous outcomes; a local dedup row cannot atomically commit a remote action.

### 07-D. Add bounded retries, a dead-letter queue, and a repair path

The starter sends malformed/unsupported events to a dead-letter route and exits on transient database errors instead of hot-looping immediate requeues. Extend it with attempt count, `nextAttemptAt`, error classification, exponential backoff, jitter, and a maximum attempt budget.

Create a poison message, a temporarily unavailable database, and a permanently invalid recipient. Verify each follows the intended policy. Add an authenticated redrive command that preserves event identity, records who requested repair, and does not bypass payload validation.

The starter's single-node durable classic queue and simple DLX are teaching defaults, not a complete highly available broker or guaranteed dead-letter delivery topology. In an advanced branch, configure replicated quorum queues and verify their documented dead-letter behavior before making stronger guarantees.

**Pass:** a bad message does not block all good messages, retry traffic is bounded, and operators can inspect and safely redrive failures.

### 07-E. Add ordering and backpressure deliberately

Create booking lifecycle events with `aggregateId`, `aggregateVersion`, and `eventId`. Deliver versions 3, 1, 2, 2 to the consumer. Decide whether to buffer gaps, fetch a fresh state snapshot, or enforce per-key ordering in the transport. Merely having a queue does not define the business ordering contract.

Run one, two, and four workers with different prefetch values. Measure throughput, oldest-message age, delivery duplicates, and database load. Use a slow fake dependency to show why more concurrent consumers can worsen overload.

**Pass:** the final lifecycle state is valid despite duplicates and tested reorderings, and consumption concurrency cannot grow without bound.

### 07-F. Compare competing consumers, fan-out, and replayable logs

Add separate notification and analytics subscribers. Give each its own queue bound to an event exchange so both receive the event; do not put both roles on one competing-consumer queue and expect both to receive every message.

For replay, retain an append-only event archive or introduce a log-oriented broker in a separate extension. Rebuild analytics into an empty table while live events continue. Checkpoint progress without losing events arriving during the rebuild.

**Pass:** explain the difference between distributing work to one worker, broadcasting to independent subscribers, and retaining history for replay. Keep event sourcing distinct from using an outbox: the baseline booking tables, not the outbox, remain the source of truth.


---

# 08. Storage Systems

**Project increment:** add large event materials and resumable downloads without storing every byte in the API process.

**Source alignment:** block/file/object storage, CDN/media delivery, large files, distributed storage concepts, decision frameworks, and system patterns.

### 08-A. Implement a storage interface and expose the failure boundaries

Define `put(key, stream)`, `get(key)`, `head(key)`, and `delete(key)`. Build a local filesystem adapter using generated, server-controlled keys under one configured root directory. Reject user-selected paths, `..`, and absolute path traversal. Separate display filename from physical key.

Store file metadata in PostgreSQL: tenant, owner, object key, size, checksum, content type, lifecycle status, and version. Do not store a path supplied by the browser as trusted storage identity.

Compare a container writable layer, a named volume, a shared filesystem mount, and an object-store API in an architecture decision record. A local directory-backed adapter is not an implementation of a distributed object store; a same-host shared mount is not evidence about NFS behavior across machines.

**Pass:** restarting an API cannot lose metadata; the persistence boundary for the bytes is explicitly identified and tested.

### 08-B. Stream uploads and validate ownership, size, and checksums

Implement `POST /v1/files/initiate` returning a server-generated upload ID and an allowed maximum size. Add a streaming upload endpoint. Use `stream.pipeline()` and a running hash rather than concatenating the full file into RAM. Enforce an actual byte limit even when Content-Length is missing or false.

Write to a temporary object, verify its length and checksum, then publish immutable bytes and commit metadata to `available`. If metadata commit fails after object creation, retain a recoverable orphan, not a published metadata pointer to missing bytes. Use an orphan-cleanup grace period so in-progress uploads are not deleted.

Test 1 MiB, 50 MiB, and a larger file suitable for your disk. Record API memory usage. Test a wrong checksum, early disconnect, and a user trying another user's upload ID.

**Pass:** failed or unauthorized uploads never become readable committed files; peak API memory does not scale with whole-file size.

### 08-C. Implement multipart/resumable upload as a state machine

Add these tables and endpoints:

```text
upload_sessions(id,tenant_id,user_id,expected_size,expected_hash,status,expires_at)
upload_parts(upload_id,part_number,size,checksum,object_key)
PRIMARY KEY(upload_id,part_number)
POST /v1/uploads
PUT  /v1/uploads/:id/parts/:partNumber
GET  /v1/uploads/:id/parts
POST /v1/uploads/:id/complete
DELETE /v1/uploads/:id
```

Divide a file into fixed-size parts. Upload out of order; use bounded parallelism. Retry the same part with the same bytes and require an idempotent outcome; reject a conflicting overwrite after a part is finalized. Complete only after checking the exact ordered manifest, total size, and a defined full-file checksum. Serialize concurrent completion calls and return the original completed result.

Interrupt halfway, list completed parts, and resume only missing parts. Expire abandoned sessions and delete their unreferenced bytes after the grace period.

For a real S3 extension, map initiate/upload/complete/abort onto the provider's multipart operations and use provider-supported checksums. Do not assume a multipart ETag is the checksum of the entire file. S3 allows independent part uploads and reuploading failed parts. [S3-MULTIPART]

**Pass:** byte-for-byte identical final download, safe repeated completion, no visible partial file, and no tenant escape.

### 08-D. Add authenticated downloads, ranges, and safe edge caching

Authorize access before issuing a download ticket. Scope the ticket to one object/version and a short expiry; never accept an arbitrary object key from a caller without authorization.

Implement byte-range responses in the local adapter and test the first, middle, last, and invalid ranges. Verify `Content-Range`, body length, and a reconstructed whole-file checksum. Rate-limit small-range abuse rather than allowing unlimited expensive tiny reads. HTTP range semantics are part of the HTTP contract. [HTTP]

Cache only public immutable content at the edge. A signed download URL can remain usable until it expires; immediate revocation requires an additional design, not merely deleting a sharing row in your app.

**Pass:** a resumed download is identical, private content does not become globally cached, and the revocation guarantee is stated honestly.

### 08-E. Simulate replication, corruption detection, and repair

Create two separate local storage roots to model copies. Store checksums, corrupt one copy of a synthetic file, and verify reads detect the mismatch. Repair from the verified copy, then corrupt both and prove detection does not imply recoverability.

Add a background integrity scanner with a bounded I/O budget. Record which metadata points to missing objects, which objects have no references, and which copies fail verification. Treat two directories on one disk as a functional simulation only, not independent failure domains.

**Pass:** the system never silently serves known-corrupt bytes; it can repair only when an intact authorized copy exists. Carry that distinction into the durability lab.


---

# 09. Fault Tolerance & Reliability

**Project increment:** tolerate slow and failing dependencies without amplifying an outage.

**Source alignment:** reliability foundations, core patterns, engineering techniques, and system playbooks.

### 09-A. Build a deterministic failing dependency

Add a local `fake-provider` service with configurable delay, failure probability, and a mode that performs its effect then drops the response. Record calls using a stable operation ID. Protect its configuration endpoint so it is not exposed outside the lab.

Call it from a deliberately naive notification or mock-payment path. Run load while changing delay from 20 ms to 2 seconds. Observe active calls, queue length, and total latency.

**Pass:** you can reproduce timeout, explicit failure, and ambiguous completion as three different outcomes.

### 09-B. Implement deadline-aware, bounded retries

Allocate an overall operation deadline, a maximum attempt count, and a per-attempt budget. Propagate cancellation with `AbortController` to outgoing HTTP calls. Do not confuse Node's inbound request-body timeout with a complete end-to-end handler deadline.

Retry only classified transient failures. Reuse the same idempotency key for a write retry. For exponential full jitter, choose delay uniformly between zero and `min(cap, base * 2**attempt)`, and stop if there is insufficient deadline remaining. Retry at one chosen layer rather than independently multiplying attempts in gateway, API, SDK, and worker.

The dependency may have committed even when the caller timed out. Use the fake provider's drop-after-effect mode to verify that a retry with a new key duplicates an effect while stable-key reconciliation does not. Bounded retries and jitter are meant to avoid turning partial failure into synchronized overload. [AWS-RETRY]

**Pass:** upper bounds exist for attempts, elapsed time, and in-flight work; permanent validation errors are not retried.

### 09-C. Wire in the supplied circuit breaker

Read `CircuitBreaker` in `src/domain.mjs` and its tests. Attach one breaker per dependency/operation class, not one global breaker covering unrelated functionality. Add counters for open rejection, attempted calls, failures, and recovery probes.

Set the provider to fail continuously. Verify the breaker opens after the configured threshold, rejects without calling the provider, admits a single half-open probe after the wait, and closes only after success. Test a slow old success arriving after the breaker opened; the supplied implementation uses an epoch to prevent that stale completion from incorrectly closing it.

**Pass:** optional features degrade predictably and do not consume every booking connection. Discuss tuning: a circuit is a policy with false positives/negatives, not proof the dependency is dead.

### 09-D. Add bulkheads, load shedding, and a safe fallback

Give reports/exports a separate concurrency semaphore or connection pool from interactive booking operations. Submit ten slow report jobs while booking traffic continues. Then remove the separation in a controlled branch and compare.

Limit waiting work; return an explicit overload response before memory is exhausted. For event recommendations, return an empty/popular list with a degraded flag. For inventory, return an error rather than cached "success."

**Pass:** report overload does not consume all booking capacity, and every fallback preserves the relevant business invariant.

### 09-E. Run a failure-injection matrix and write a runbook

In the isolated stack, separately stop Redis, stop the broker, kill a worker, pause PostgreSQL, kill api1, and stop the gateway. Restore the system between experiments. Apply a fixed workload and save failure onset, detection, recovery, lost/duplicate effects, and queue growth.

A process kill does not simulate all forms of packet loss or an entire region outage. For network-specific tests, add a TCP fault proxy and target only lab connections. Keep duration and blast radius bounded.

**Pass:** produce a runbook with a symptom, confirming signal, safe mitigation, rollback, and an invariant query. Include at least one case where the correct action is to reject work rather than pretend availability.


---

# 10. Reducing Latency

**Project increment:** improve a specific slow request using evidence, not a collection of optimizations.

**Source alignment:** latency fundamentals, core/advanced techniques, trade-offs, decision framework, and anti-patterns.

### 10-A. Measure the critical path and tail

Create `GET /v1/dashboard` that fetches an event, organizer, recommendations, and recent bookings. Add timestamps/spans around each call and around connection-pool acquisition. Build a baseline using sequential calls.

Report the latency distribution and errors, not only the average. Add a rare slow response to one dependency and observe its effect on p99. Do not add service p95 values or average instance p95 values and call the result end-to-end p95.

**Pass:** identify which work lies on the response's critical path, which is parallelizable, and which is optional.

### 10-B. Parallelize independent work and remove N+1 queries

Fetch independent dashboard components concurrently with separate deadlines. Return required data only when its contract is met; make optional recommendation failure explicit. Add a concurrency limit rather than blindly launching one promise per row.

Create 50 event cards with organizer names. Compare 51 queries with a join/batched lookup. Use a per-request DataLoader when implementing the GraphQL variant; preserve result ordering relative to input keys. [GRAPHQL]

**Pass:** lower database round trips without changing authorization or response semantics. Measure whether parallelism improved latency at the cost of greater instantaneous dependency load.

### 10-C. Optimize payload and connection work

Compare `SELECT *` with selecting only the fields rendered by the card. Add response compression for sufficiently large compressible payloads. Test a small JSON body, a large JSON body, and an already compressed image; measure bytes and CPU instead of assuming compression always helps.

Reuse outbound connections and database pools. Compare a new connection per request with reuse. Keep warm-cache and cold-start experiments separate. Move nonessential click analytics into the outbox path rather than blocking the user response.

**Pass:** your evidence identifies whether time was saved in transfer, connection establishment, query execution, or waiting.

### 10-D. Experiment with hedged reads without duplicating writes

Create two read-only replicas of a fake slow catalog endpoint with different tail behavior. Send the first read; only after a measured delay launch a second read if the first remains incomplete. Return the first acceptable result and cancel the other where possible.

Add a strict hedge budget so the experiment cannot double all traffic during overload. Compare p99 improvement, total backend requests, and divergence between replicas. Define the acceptable version before choosing the first response.

**Pass:** no write operation is hedged, extra traffic is bounded, and a faster stale reply is not mislabeled as meeting a stronger consistency contract.

### 10-E. Create and enforce a latency budget

Choose a lab objective, for example a 500 ms p95 for a stated local workload. Allocate tentative budgets to gateway, API work, pool wait, database, optional services, and transfer. These are design allocations, not guarantees that independent percentile values add.

Run the workload at 50%, 80%, and above the measured sustainable capacity. Observe queueing and compare latency with and without admission control. Document the point where a bounded rejection is preferable to an unbounded wait.

**Pass:** at least one optimization is accepted and one rejected based on measured benefit, resource cost, correctness, and operational complexity.


---

# 11. Durability

**Project increment:** recover acknowledged state and files from crashes and mistakes.

**Source alignment:** durability foundations/techniques, backup/recovery, distributed durability, decision framework, and anti-patterns.

### 11-A. Define what "acknowledged" means and test process crashes

Write a client-side append-only history of successful booking responses, including operation key, booking ID, and observed response time. Kill PostgreSQL after a stream of successes, without removing its volume. Restart and check every acknowledged booking ID against the database.

Compare the results with database durability settings such as `synchronous_commit` only in an isolated disposable instance. A successful process-crash test does not prove survival of a power loss, faulty disk, or loss of the entire host.

**Pass:** report exactly which fault was injected and which acknowledged records survived. Do not confuse an unacknowledged request that committed with acknowledged data loss; reconcile ambiguous outcomes by key.

### 11-B. Take a backup and prove restoration into a different database

Create a backup, store it outside the database volume, and restore to a distinct lab database:

```bash
mkdir -p backups
docker compose exec -T postgres pg_dump -U lab -d eventhub -Fc > backups/eventhub.dump
docker compose exec postgres createdb -U lab restore_check
docker compose exec -T postgres pg_restore -U lab -d restore_check --no-owner < backups/eventhub.dump
```

`createdb` is intended for a new empty restore target; choose another name if it already exists. Compare row counts, sampled record contents, inventory conservation, foreign keys, and outbox state. Do not point a restored outbox at real providers: its pending or replayed events can trigger effects again.

Delete a synthetic table in the original lab database only after the backup is verified. Restore the required data into a recovery environment and document the cutover procedure.

**Pass:** a usable restoration, measured recovery time, and a clear statement of how much newer data the backup lacks. "Backup job succeeded" alone is not enough.

### 11-C. Perform point-in-time recovery with a base backup and WAL

This is an advanced PostgreSQL exercise, distinct from `pg_dump`. Configure WAL archiving to a separate lab directory, verify archive success, take a physical base backup, and record a restore target immediately before a deliberate destructive transaction.

Restore the physical backup into a separate data directory. Configure `restore_command`, create `recovery.signal`, and set a recovery target that stops before the destructive commit. Start the recovery instance on a different port. Verify the pre-error row exists and legitimate earlier transactions are present.

PostgreSQL PITR combines a physical base backup with the relevant WAL history; a logical dump is not a substitute for that process. Follow the version-matched recovery instructions for filenames and target semantics. [PG-PITR]

**Pass:** restore the desired point, record the recovery timeline and measured RPO/RTO, and prove the archive contains every required segment. Keep backup and recovery credentials separate from ordinary application credentials.

### 11-D. Test the acknowledgement boundary for files and messages

For the local file adapter, distinguish "bytes copied to a process buffer," "written to the operating system," and the durability behavior of the chosen storage backend. Publish metadata only after the configured byte-durability boundary. On a local filesystem, practice file/directory synchronization where supported; do not assume a rename alone proves power-loss durability.

For the broker, compare sending without confirmations to confirmed publication. Kill the publisher and broker at controlled points and reconcile against the outbox and consumer tables. A single-node broker can still lose availability with its host.

**Pass:** every guarantee names both the acknowledgement point and the failure model it covers.

### 11-E. Distinguish replication from backup and corruption detection from recovery

Delete a synthetic row on the primary and watch the deletion replicate to the standby. Then recover it from an earlier backup. Use the two-copy corruption experiment from Lab 08 and demonstrate what happens when both copies are bad.

Create a retention policy for immutable backups, object versions, and event archives. Include a deletion/privacy policy so "keep everything forever" is not your default. Test restoring encrypted data with the correct key and failing without it; losing the only key can make intact bytes unusable.

**Pass:** the runbook covers accidental deletion, logical corruption, lost host, and missing encryption key as distinct scenarios, with explicit recoverable and unrecoverable cases.


---

# 12. Security

**Project increment:** replace teaching credentials with authenticated, tenant-isolated access and auditable operations.

**Source alignment:** authentication, authorization, data protection, application/network security, auditing, distributed security, decision frameworks, and anti-patterns. The exercises below are defensive tests against your own local application.

### 12-A. Replace fixture authentication without weakening authorization

The supplied `lab-alice`, `lab-bob`, and `lab-charlie` bearer strings are public development fixtures, not passwords or validated identity tokens. Do not deploy this configuration publicly.

Create an `IdentityVerifier` interface returning a verified subject and permitted tenant memberships. Integrate a local identity provider or an identity provider's development tenant. Use its maintained verification library and documented issuer/JWKS configuration. Verify signature, allowed algorithm, issuer, audience, expiry, and required claims; do not merely decode a token. Resolve active tenant membership on the server rather than trusting an arbitrary request-body `tenantId`.

Make a policy matrix for anonymous user, attendee, event organizer, and company administrator. Define permissions separately for viewing a public event, reading a private booking, managing an event, exporting attendees, and generating download tickets. An administrator of one tenant is not an administrator of every tenant.

**Experiment:** send a tampered token, expired token, wrong-audience token, valid token from the wrong tenant, and a valid attendee token attempting an organizer action. Test signing-key rotation and identity-provider unavailability under your chosen token-validation policy.

**Pass:** each negative case is rejected without leaking resource contents; provider failure never activates fixture authentication as a fallback. Existing records remain associated with stable internal user IDs.

### 12-B. Test object authorization at every entry point

Run the supplied HTTP tests, then write integration tests against the database-backed service. Alice's booking must remain inaccessible to Bob in the same tenant and Charlie in another tenant. Knowing a UUID is not permission to read the object. Object-level authorization is a specific OWASP API risk. [OWASP-BOLA]

Extend the same checks to list queries, exports, object downloads, WebSocket subscriptions, search results, cache keys, background jobs, and shared links. A secure `GET /booking/:id` does not compensate for an unfiltered `/export` endpoint.

Add a PostgreSQL row-level-security exercise using a **separate non-owner application role**, not the supplied `lab` bootstrap account. A representative policy is:

```sql
ALTER TABLE bookings ENABLE ROW LEVEL SECURITY;
CREATE POLICY tenant_boundary ON bookings
USING (tenant_id = current_setting('app.tenant_id', true))
WITH CHECK (tenant_id = current_setting('app.tenant_id', true));
```

Grant only needed table privileges to the new application role. Within a transaction, set context using `SELECT set_config('app.tenant_id', $1, true)` with a verified tenant value. Keep ownership checks for attendees in application policy or an additional RLS policy. Superusers, roles with `BYPASSRLS`, and ordinarily table owners can bypass RLS; changing only the policy while keeping a superuser connection does not demonstrate isolation. [PG-RLS]

**Pass:** reuse a pooled connection for different tenants and prove no previous transaction's tenant context persists. RLS is defense in depth, not a substitute for correct authorization.

### 12-C. Harden inputs, rendering, and resource consumption

Preserve parameterized SQL and the baseline's allowlist of booking fields. Add explicit schemas to every new endpoint, maximum pagination limits, bounded file sizes, allowed MIME types, and safe filenames generated by the server. Attempt to submit `status`, `tenantId`, and `userId` as unauthorized booking fields.

For Pastebin, render user text as text rather than executable HTML. Test a paste containing `<script>` and HTML attributes as inert content. For file delivery, set an appropriate content type and content disposition. Test traversal-style filenames without writing outside the dedicated lab directory.

For any URL-fetching feature, use only the local crawler fixture from Lab 17. Validate the final destination after every redirect and restrict protocols, hosts, ports, bytes, and duration. Do not permit arbitrary URLs to reach infrastructure metadata or internal services.

**Pass:** malformed requests produce controlled client errors, oversized requests are bounded, and input cannot modify tenant ownership or executable page content.

### 12-D. Separate secrets, network reachability, and audit evidence

Replace fixed development database credentials before any non-local deployment. Use a secrets mechanism appropriate to that deployment; do not commit secrets, private keys, database dumps, or `.env` files. Give the API, publisher, and worker distinct least-privilege roles as an exercise.

Remove host-exposed database/broker ports when they are unnecessary, and expose only the intended gateway. Add TLS from Lab 01, then document whether internal connections also need encryption for the assumed threat model. Encryption without key recovery procedures creates a different durability risk.

Emit structured audit records for permission changes, booking cancellation, data export, and share-link creation/revocation. Include actor, tenant, action, resource, outcome, and correlation ID, but not bearer tokens or full sensitive payloads. Restrict access to audit data and simulate rotation of a compromised signing key.

**Pass:** repository and log inspection reveal no real secrets; an unauthorized export attempt has a useful audit record; audit access is itself controlled.

### 12-E. Compare the four pillars using concrete incidents

Create a table with incidents as rows: API process down, acknowledged booking missing, repeated duplicate notifications, cross-tenant data exposure, and encryption key unavailable. Explain separately the effects on availability, reliability, durability, and security. These dimensions interact, but are not interchangeable.

Write a short incident playbook for each: detection, containment, recovery source, verification query, and customer-visible impact. Define deletion and retention rules for synthetic user data and backups.

**Pass:** recovery does not reintroduce revoked permissions or silently resurrect deleted data. State the remaining risks instead of declaring the application universally secure.


---

# 13. Maintainability

**Project increment:** make the system safe to change and understandable after something fails.

**Source alignment:** architecture, engineering practices, testing, CI/CD, observability, infrastructure, documentation, and common anti-patterns.

### 13-A. Preserve a modular monolith before extracting services

Separate HTTP parsing, identity verification, booking rules, persistence, messaging, and provider adapters. The starter already separates several of these concerns; improve the boundaries rather than introducing a network hop for each class.

Define a repository contract for `book`, `getBooking`, and `getEvent`. Run the same behavioral contract against `MemoryStore` and `PgStore`, while keeping PostgreSQL concurrency and rollback tests separate: a memory adapter is not proof of database behavior. Replace the fake notification adapter without editing the booking transaction.

**Pass:** changing an adapter does not change public API behavior. List the dependencies each module may import and fail a test or lint rule for forbidden dependencies.

### 13-B. Build a testing pyramid around invariants

Keep fast domain and HTTP tests for validation, authorization, idempotent replay, hashing, and circuit-breaker transitions. Add real PostgreSQL tests for concurrent guarded updates, transaction rollback, and uniqueness. Add broker integration tests for crash-before-ACK and replay after publish confirmation.

Create a property-style test that generates sequences of booking attempts and retries. After each sequence, check inventory bounds and conservation. Preserve the seed of any failing generated sequence so it can be replayed.

Add a contract test for the fake provider's timeout-after-effect behavior. Tests that assert only a mocked function was called cannot prove that retries are safe.

**Pass:** a deliberately removed inventory predicate, authorization filter, or deduplication key makes the corresponding test fail. The failure message identifies the violated invariant.

### 13-C. Automate a clean build and reversible deployment

Create a CI job that checks syntax, runs unit tests, builds containers, starts a disposable integration environment, seeds it, runs integration/race tests, and archives logs. Run each test job with its own Compose project name and database fixtures. Use secrets only where required and never upload sensitive dumps as public artifacts.

The starter has no generated dependency lockfile because registry access was unavailable in the authoring environment. On your first successful install, commit the generated lockfile, switch reproducible builds to `npm ci`, and record the image digests you validated. Version tags alone are not a reproducibility proof.

Practice the expand-contract migration from Lab 04. Deploy old and new API versions against the expanded schema before removing old fields. Roll application code back while retaining the compatible schema.

**Pass:** a fresh checkout can recreate the environment with documented commands, and a bad application release is rolled back without deleting the database.

### 13-D. Follow one request through asynchronous work

Propagate a request/trace correlation context from the HTTP request into the outbox message and worker log. Instrument database and broker spans, but redact sensitive attributes. OpenTelemetry's Node.js instructions explain initialization and the additional considerations for ESM instrumentation. [OTEL]

Expose bounded-label metrics for route, method, status, dependency, and worker outcome. Do not use raw user IDs, URLs, booking IDs, or request IDs as metric labels. Those belong in appropriately protected logs or traces, where needed.

**Experiment:** slow the database, kill the publisher, and trigger a duplicate message. Find the relevant request, outbox row, retry, and notification without searching every log manually.

**Pass:** distinguish API latency, queue wait, worker duration, and end-to-end completion latency. A fast `201` does not imply the asynchronous notification has already been produced.

### 13-E. Document decisions and operational ownership

Create an architecture decision record for PostgreSQL inventory, Redis as a disposable cache, outbox delivery, and the deliberate absence of microservices initially. Each record needs context, alternatives, decision, evidence, consequences, and a condition that would trigger reconsideration.

Write runbooks for database recovery, poison-message handling, key rotation, and schema rollback. Express infrastructure changes as reviewed configuration rather than unexplained manual console changes. Use an infrastructure-as-code tool only when it improves the target deployment; it is not needed to prove a local HTTP feature.

**Pass:** another learner can reproduce one failure and recovery using only the repository instructions. Remove any undocumented shell history dependency.


---

# 14. API Design

**Project increment:** make the EventHub API predictable under retries, concurrent updates, pagination, and asynchronous work.

**Source alignment:** REST principles, pagination, idempotency, versioning, security, performance, patterns, and anti-patterns.

### 14-A. Define contracts before adding endpoints

Write an OpenAPI contract for events, bookings, files, and asynchronous exports. Describe required fields, allowed fields, validation errors, authentication, and response headers. Keep successful resource creation distinct from an accepted background job. Use the HTTP specification to check method semantics, conditional requests, and status codes rather than inventing meanings. [HTTP]

Add event creation and update endpoints restricted to organizers. Use stable error codes with a human-readable message and request ID. Keep internal SQL errors and stack traces out of responses.

**Pass:** generated or hand-written contract tests reject missing fields, unknown fields, malformed identifiers, and unauthorized operations consistently. A documented `Location` resolves to the relevant resource or job.

### 14-B. Replace offset pagination with a stable cursor

Create enough bookings for many pages. Start with offset pagination, insert new rows between requests, and observe duplicates or omissions under the selected ordering. Then use a unique, deterministic ordering such as `(created_at, id)` and an index matching the tenant and sort order.

```sql
SELECT id, created_at, status
FROM bookings
WHERE tenant_id = $1 AND user_id = $2
  AND (created_at, id) < ($3::timestamptz, $4::uuid)
ORDER BY created_at DESC, id DESC
LIMIT $5;
```

Use a separate first-page query without a cursor. Fetch `limit + 1` rows to determine whether another page exists. Preserve PostgreSQL timestamp precision in the cursor rather than silently truncating microseconds through a JavaScript `Date`. Bind the cursor to the tenant, sort, and filters; validate its version and sign it where tampering matters.

Keyset pagination avoids some offset problems but does not automatically create a frozen snapshot while records are inserted or changed. Define snapshot versus live-feed semantics explicitly.

**Pass:** tied timestamps paginate without duplicates in an unchanged dataset, cross-tenant cursors are rejected, and maximum page size is enforced.

### 14-C. Test idempotency as a concurrent protocol

Inspect the starter's scope: `(tenant, user, route, key)`, plus a canonical request hash. The successful booking response is recorded in the same database transaction as inventory and outbox changes. Reusing the same key with a different valid payload is a conflict.

Run `node scripts/integration.mjs` against the container stack. Extend it to send the same key through both API replicas simultaneously. Drop the client connection after the server commit but before receipt of the response, then retry with the same key.

The teaching baseline stores successful responses indefinitely and does not cache failed attempts. Design retention, maximum storage, replay authorization, and API-version handling before changing that behavior. After a key is purged, a sufficiently late retry may become a new request; do not silently promise protection forever.

**Pass:** one logical request creates one booking and decrements inventory once. Equal payloads replay the documented outcome; different payloads never accidentally replay another operation.

### 14-D. Prevent lost updates with conditional writes

Expose an organizer-editable event representation with an authoritative version. Return a representation ETag and require `If-Match` for updates. Perform the version comparison in the database update itself, not in an earlier unprotected read:

```sql
UPDATE events
SET title = $4, version = version + 1
WHERE tenant_id = $1 AND id = $2 AND version = $3
RETURNING *;
```

Two clients read the same version and submit different changes. Exactly one may update that version; the other receives the documented precondition failure. HTTP conditional requests define `If-Match` behavior. [HTTP]

Add a compatible `/v2` or additive field migration and run old clients against it. Do not confuse URL versioning with concurrency versions.

**Pass:** stale writers cannot overwrite newer edits, and cache invalidation occurs only after the authoritative update succeeds.

### 14-E. Design asynchronous APIs and quotas

Add `POST /v1/exports`, returning a job resource and `202` while work is pending. Implement `GET /v1/jobs/:id` with tenant/owner authorization and states `queued`, `running`, `succeeded`, and `failed`. Define cancellation as best-effort or authoritative at explicit checkpoints, rather than assuming work disappears immediately.

Add the atomic shared rate limiter from Lab 17. Return a useful retry indication for rejected requests and preserve a separate availability policy for authenticated writes. Bound request size, page size, and concurrent export count as well as request rate.

**Pass:** job IDs are not authorization, duplicate creation can be made idempotent, and worker failure leaves a recoverable state instead of permanent unobservable progress.


---

# 15. Estimation Skills

**Project increment:** predict a workload, test what the local system can sustain, and revise the model.

**Source alignment:** traffic, storage, bandwidth, cache sizing, worked examples, estimation frameworks, system patterns, and common pitfalls. Every number below is a teaching assumption, not an observed capacity of the starter or a claim about a named company.

### 15-A. Turn product assumptions into arrival rates

Assume 100,000 daily active users, 20 catalog requests per user per day, and a peak factor of ten. Calculate:

```text
Daily catalog requests = 100,000 * 20 = 2,000,000
Average catalog RPS   = 2,000,000 / 86,400 = 23.15 approximately
Assumed peak RPS      = 23.15 * 10 = 231.5 approximately
```

Model writes separately: for example, 5,000 booking attempts/day with a different event-opening spike profile. Include retries, background workers, polling, and administrative exports rather than treating all traffic as user page loads.

**Pass:** every multiplier has a unit and an explicit assumption. Change one assumption by ten times and identify the first component likely to be affected.

### 15-B. Estimate storage from measured rows and retention

Seed representative rows, then inspect relation and index sizes with `pg_total_relation_size` and `pg_indexes_size`. Include bookings, idempotency responses, outbox events, notification records, indexes, WAL, replicas, backups, and retention. Do not multiply logical application bytes once and label that the whole storage bill.

For attachments, keep logical uploaded bytes, physical deduplicated bytes, versions, replica copies, and incomplete uploads separate. An average file size alone may hide a costly large-file tail.

**Pass:** produce a one-year storage table with separate assumptions and compare at least one estimated row/object size with an actual measurement.

### 15-C. Calculate bandwidth and cache requirements

Assume a 20 KiB compressed catalog response at the proposed peak. The application payload rate is approximately `231.5 * 20 * 1024` bytes/s, before protocol overhead. Report bytes/s and bits/s correctly.

At a 90% cache hit ratio for those reads, the nominal miss rate is about 23.15/s. That is not the entire database workload: writes, cache fills, invalidations, misses for other endpoints, and administrative operations remain.

Size Redis from measured entry size and the working-set distribution, plus overhead and headroom. Simulate a cold-cache restart and a popularity shift.

**Pass:** your model explains why a high average hit ratio does not prevent a database spike during mass expiry or recovery.

### 15-D. Estimate capacity and verify the bottleneck

Use a measured sustainable throughput per replica at your selected latency/error target. For illustration only, suppose one isolated replica supports 300 requests/s under a representative workload. At a deliberate 60% utilization allowance, use 180 requests/s per replica for planning:

```text
Required serving replicas = ceil(231.5 / 180) = 2
With one-replica-loss reserve = 3 total replicas
```

This simplified calculation assumes the database and other shared dependencies can support the total load. Measure that assumption; extra API replicas can otherwise just increase database contention.

For a stable stage, relate average concurrency to throughput times **mean** residence time. Do not substitute a p95 latency into an average-law calculation and call it exact. Under overload, model queue growth and rejection rather than assuming a stable queue.

**Pass:** record load-generator saturation and dropped iterations, not just server metrics. Distinguish a measured local result from a hypothetical regional deployment.

### 15-E. Write an estimation decision memo

Create a one-page memo containing workload assumptions, read/write mix, peak shape, data growth, bandwidth, cache budget, candidate bottleneck, proposed SLO, capacity headroom, and cost categories. Include a sensitivity table for ten-times more users, ten-times larger objects, and ten-times more retries.

Run one load experiment that could falsify the memo. Revise the estimate and explain why it changed. Do not memorize generic latency numbers when your actual workload can be measured.

**Pass:** another engineer can reproduce the arithmetic, identify uncertain inputs, and see exactly which measurements would improve the decision.


---

# 16. HLD Architecture and Frameworks

**Project increment:** connect the component-level experiments into an architecture that has a defensible purpose.

**Source alignment:** mental models, six-step and rapid interview frameworks, decision trees, architecture templates, archetypes, cross-concept links, anti-patterns, and walkthroughs. Your index names these frameworks but does not contain their actual steps or the 70-problem master map. The process below is a newly authored implementation review, not a reconstruction of those missing sections.

### 16-A. Produce six evidence-backed design artifacts

Create these six artifacts for EventHub: (1) functional requirements and explicit exclusions; (2) invariants, SLOs, and failure assumptions; (3) workload and capacity estimates; (4) API contracts and data model; (5) architecture with synchronous/asynchronous boundaries; and (6) failure experiments, tradeoffs, and operational plan.

Every box in the architecture should point to code, configuration, a contract, or a clearly labeled planned extension. Distinguish deployed components from things merely drawn in a diagram.

**Pass:** demonstrate one complete request from client to durable state and asynchronous effect, including the exact point at which success is acknowledged.

### 16-B. Use a decision tree that can remove technology

For each proposed component, ask: what measured or required problem does it solve; can an existing component solve it; what new failure mode does it add; how will it be tested; and when would it be removed?

Apply this to Redis, RabbitMQ, a search service, database sharding, and microservices. Compare the baseline modular monolith with a version where notification processing is independently deployed. Do not extract the inventory invariant across services merely to increase the number of services.

**Pass:** remove one unjustified component from a hypothetical overbuilt design and show which guarantees remain intact. Record the trigger that would justify adding it later.

### 16-C. Trace quality attributes through the same feature

Take `POST /v1/bookings`. Explain its networking path, scalability limit, consistency requirement, availability behavior, database transaction, cache policy, asynchronous effects, storage needs, retry behavior, latency budget, durability boundary, authorization, test coverage, API semantics, and capacity estimate.

Use the actual implementation as evidence. For example, Redis is not consulted to decide whether a seat can be sold; a stopped broker leaves outbox work pending; loss of the single PostgreSQL instance still affects the baseline's write availability.

**Pass:** no quality attribute is described only with a buzzword such as "highly available" or "eventually consistent." State which operation, what observation, and under which failure.

### 16-D. Rehearse the architecture as a technical review

Give yourself a constrained presentation window and explain the design without reading notes. Then have a reviewer introduce one change: global writers, tenfold spike, regional loss, strict deletion, collaborative editing, or payment-provider uncertainty.

Choose the matching family from Lab 17, identify what must change, and propose the next experiment. Do not claim the existing local tests prove a multi-region guarantee.

**Pass:** finish with a tradeoff sentence supported by evidence, such as: "We keep inventory on one authoritative database because the required no-oversell invariant is simpler to enforce there; this limits write availability during primary failure, so failover fencing is a separate tested requirement."


---

# 17. Roadmaps and System Pattern Projects

**Source alignment:** all fourteen named system-pattern families in the uploaded roadmap index. Its original 8-week and 120-day schedules are not attached. The projects and suggested sequence here are newly authored.

Complete these as branches or small services around EventHub. Do not make every subsystem a mandatory dependency of the basic booking application.

### 17-P01. CRUD / storage-based systems: organizer document manager

**Implement:** add `documents(id, tenant_id, owner_id, object_key, version, deleted_at)` and create/read/update/delete endpoints. Store bytes through the storage adapter from Lab 08 and metadata in PostgreSQL. Use generated object keys, authorization on every operation, and version-conditional updates.

**Build sequence:** first implement metadata CRUD; then stage object uploads before committing an available document; finally add soft deletion, retention, and an orphan-object collector. Define the response when bytes exist but metadata creation fails.

**Break it:** crash between object upload and metadata commit; concurrently edit the same version; delete a document while a download request is being authorized.

**Pass:** no cross-tenant document is exposed, a failed upload does not become a readable completed file, and orphan cleanup does not delete an object referenced by a committed live version. Record the retention and revocation window for already issued download tickets.

### 17-P02. Feed / timeline systems: followed-event activity feed

**Implement:** `follows(follower_id, organizer_id)`, immutable `posts`, and `feed_entries(user_id, post_id)` with a uniqueness constraint. Expose a cursor-paginated feed.

**Build sequence:** calculate the initial feed at read time from followed organizers; then fan out a post asynchronously into follower inboxes; finally use a hybrid where extremely high-follower organizers are merged at read time. Choose the fan-out threshold from measured write/read amplification, not a copied industry number.

**Break it:** replay a fan-out job, publish while a follow is added, remove access to a private post, and give one organizer most followers. Decide how edits and deletions propagate and how current authorization filters an old feed entry.

**Pass:** replay does not create duplicate feed entries, pagination uses a stable tie-breaker, and revoked private content is not exposed just because its ID remains in a materialized inbox. Compare storage, publish delay, and read latency across strategies.

### 17-P03. Real-time systems: event support chat

**Implement:** rooms, authorized memberships, and durable messages keyed by `(room_id, sequence)`, plus unique `(room_id, sender_id, client_message_id)`. Expose WebSocket send/subscribe and a history endpoint accepting `afterSequence`.

**Build sequence:** persist a message before acknowledging it; allocate a per-room sequence under a database lock in the same transaction; broadcast committed messages across API replicas; then implement reconnect, missed-message replay, duplicate suppression, and bounded slow-client buffers. Keep presence as separate TTL-based ephemeral state.

**Break it:** disconnect a client after persistence but before receipt of the ACK; resend its client message ID; move it to another replica; drop an intermediate broadcast. Pub/sub alone cannot recover a missed durable message.

**Pass:** one logical message has one durable identity, reconnect fills sequence gaps from storage, room authorization is checked, and a slow consumer cannot exhaust server memory. A per-room counter is a conscious contention tradeoff.

### 17-P04. Search systems: searchable event catalog

**Implement:** an initial PostgreSQL full-text index on normalized event text, with tenant and visibility filters. Inspect its query plan. PostgreSQL's text-search documentation describes document/query matching and indexing choices. [PG-SEARCH]

**Build sequence:** start with search over authoritative rows; add an asynchronously maintained projection containing event ID, version, searchable text, and deletion marker; then implement projection rebuild from a consistent source snapshot plus catch-up changes. An external search engine is an optional adapter, not a prerequisite.

**Break it:** deliver update version 8 before version 7; delete an event while rebuilding; pause the indexer; replay events. Keep permission checks current even while searchable text is stale.

**Pass:** older updates cannot resurrect deleted or newer content, index lag is measurable, and a rebuilt index is checked before a controlled cutover. State whether read-after-edit search freshness is guaranteed or explicitly eventual.

### 17-P05. Rate limiter / throttling: shared tenant quotas

**Implement:** token-bucket admission keyed by a verified tenant and operation. Start with the supplied single-process `TokenBucket`; observe that two replicas each granting a full quota violate a shared limit.

**Build sequence:** wire `infra/token_bucket.lua` through Redis `EVAL` with one key and arguments `capacity`, `refillTokensPerSecond`, and `cost`. The script uses Redis time, validates arguments before mutation, and atomically updates token state. Redis scripts execute atomically with respect to other commands; that does not mean arbitrary runtime errors roll back earlier writes. [REDIS-LUA]

Example, using a fresh dedicated quota key:

```bash
docker compose exec -T redis redis-cli --eval /dev/stdin quota:org-a:booking , 10 2 1 < infra/token_bucket.lua
```

The result is `{allowed, retryAfterMilliseconds, remainingWholeTokens}`. Add a bounded HTTP retry indication and record rejected requests. Move quota state out of an evicting general-purpose cache for any exercise requiring dependable enforcement.

**Break it:** concurrently request from both APIs, exhaust the burst, wait for refill, restart Redis, and trigger an outage. Define fail-open versus fail-closed behavior per operation. Cached reads and suspicious authentication attempts need not share a failure policy.

**Pass:** under the stated single-Redis failure model, concurrent requests respect burst/refill limits. Explicitly document quota reset, failover, clock, and storage-loss limitations.

### 17-P06. Logging / metrics / monitoring: a small telemetry pipeline

**Implement:** collect the starter's `/metrics`, structured logs, and trace context. Add request rate, error ratio, p95, database pool wait, outbox age, queue depth, and worker retry metrics.

**Build sequence:** scrape each replica separately, build a route-level dashboard, and define alerts tied to user symptoms and queue-age objectives. Keep high-cardinality IDs out of metric labels. Store diagnostic traces/logs separately with access control and retention.

**Break it:** label a disposable test metric with every booking ID and compare series growth; then remove that label. Stop the metrics collector and verify booking behavior remains independent. Cause a notification backlog while API latency stays normal.

**Pass:** an alert distinguishes request failures from asynchronous delay; collector loss does not take down booking; a runbook links symptoms to useful diagnostic queries rather than alerting on every CPU fluctuation.

### 17-P07. Distributed caching systems: sharded catalog cache

**Implement:** route event cache entries across two cache nodes using `shardFor` from the starter. Cache nodes remain disposable copies; PostgreSQL retains authority.

**Build sequence:** measure remapping when a node is added or removed; compare against modulo routing; add bounded per-process L1 caches and versioned invalidations; then introduce a hot-key replication or local coalescing strategy.

**Break it:** remove a node during a cold-cache period, delay invalidations, and make one key dominate traffic. Membership disagreement between clients must be observable, and a cache miss on one node must not be mistaken for authoritative absence.

**Pass:** cache node loss never loses a booking, fallback load is bounded or explicitly rejected, and you can quantify remapping, hit ratio, stale-read behavior, and hot-key load. Do not claim local cache experiments prove a production distributed-cache consistency protocol.

### 17-P08. Recommendation systems: relevant-event suggestions

**Implement:** synthetic user interactions, event tags, and a recommendation endpoint. Begin with a popularity baseline, then score tag overlap with a user's prior positive interactions. Persist model or ruleset version with each recommendation batch.

**Build sequence:** split synthetic history chronologically into training and evaluation sets; compare the personalized method with popularity using a chosen ranking metric; then add a fallback for new users and new events. Keep eligibility and tenant permissions outside the ranking score so recommendations cannot reveal forbidden events.

**Break it:** remove all history for a user, introduce a new tag, create a popularity spike, and replay duplicate interaction events. Check that future interactions never leak into training data for earlier evaluations.

**Pass:** the endpoint serves eligible items, duplicates do not inflate the chosen counters, cold-start behavior is explicit, and any quality claim is supported by your evaluation data rather than the label "AI."

### 17-P09. Payment / financial systems: a simulated payment ledger

**Implement:** a local fake payment provider, payment attempts, immutable ledger entries, and a reconciliation worker. Use integer minor currency units and keep currencies separate. Implement balanced debit/credit entries for each ledger transaction; reject unbalanced postings transactionally.

**Build sequence:** persist a payment request with a stable provider idempotency key; make the provider call outside long-held inventory transactions; process signed fake callbacks using raw request bytes; deduplicate provider event IDs; reconcile unknown outcomes by querying the provider's simulated records.

**Break it:** the provider records a charge but drops the response; it sends the callback twice or out of order; the booking expires before a late success; a refund request times out after taking effect.

**Pass:** one business payment is not accidentally charged twice by your own retries, ledger postings balance per currency, and ambiguous outcomes become reconciled states rather than an invented failure or success. Refunds are independently idempotent. This is a teaching simulator, not a payment-compliance or accounting certification.

### 17-P10. Large-scale processing: attendance analytics pipeline

**Implement:** generate synthetic newline-delimited JSON attendance records, partition work by event/date, and compute daily attendance aggregates. Give each input record a stable event ID and each batch a deterministic identity.

**Build sequence:** start with a bounded streaming parser; add checkpointed workers; make output and checkpoint updates atomic in the same database transaction where possible; then compare batch and incremental processing. Define how late-arriving and corrected events update previous aggregates.

**Break it:** kill a worker after output write but before checkpoint, duplicate a partition assignment, include malformed rows, and insert events older than the current processing window. Do not retain the entire input file in memory.

**Pass:** replay yields the same declared aggregate, invalid rows are accounted for, memory remains bounded, and an externally written output sink has a documented deduplication/commit strategy rather than an unsupported "exactly once" claim.

### 17-P11. Geospatial / location-based systems: nearby venue discovery

**Implement:** an optional PostGIS-enabled database with `geography(Point,4326)` venue locations and a GiST index. Generate fictional coordinates for testing, not personal location data. Store points in longitude/latitude order.

**Build sequence:** implement `GET /venues/nearby` with validated coordinates and a bounded radius; use `ST_DWithin` for filtering and a defined distance ordering; inspect the plan and boundary results. For geography values, PostGIS defines the distance parameter in meters. [POSTGIS]

Add synthetic courier or event-staff locations with timestamps and a freshness cutoff. Keep geographic candidate selection separate from the atomic assignment transaction.

**Break it:** swap latitude/longitude in a fixture, request an excessive radius, use stale location updates, and have two dispatchers assign the same worker concurrently.

**Pass:** nearby results respect units and tenant visibility; stale presence is labeled/excluded according to policy; one worker cannot receive conflicting active assignments under the chosen constraint.

### 17-P12. Collaborative systems: shared event-planning document

**Implement:** a local collaborative text editor using Yjs with your own local sync server and durable update storage. Start with the official editor/provider example, then add room authorization before using any private data. [YJS]

**Build sequence:** connect two clients, save updates, disconnect one, edit both independently, reconnect, and verify convergence. Add snapshots and update compaction only after replay from the original update log is verified. Maintain separate access-control state; conflict-free document merging does not decide who is allowed to edit.

**Break it:** duplicate and reorder updates, reload from storage, revoke a user's membership while connected, and attempt to join another tenant's room. Do not put private content into a public demonstration provider.

**Pass:** authorized replicas converge under the library's supported synchronization model, updates survive server restart, and revoked users cannot keep submitting accepted new edits. Explain how this differs from whole-file conflict copies in the Drive capstone.

### 17-P13. Infrastructure / system utilities: scheduled-job leases and IDs

**Implement:** `jobs(id, due_at, status, lease_owner, lease_until, fencing_token)` and competing schedulers. Claim due work in a transaction, incrementing a monotonic fencing token for every successful lease acquisition. Use database time for lease comparisons.

**Build sequence:** make job effects idempotent; require the current token when committing guarded local effects; add renewal and bounded retry; then test expired workers. A lease alone does not stop a paused process from resuming after another worker has taken over.

**Break it:** pause worker A until its lease expires, let B claim a newer token, then resume A. Its stale token must be rejected by the resource that commits the effect. External providers need their own idempotency/fencing mechanism; a database token cannot magically protect an unrelated remote write.

Add an ID-generator comparison: database sequence, random UUID, and a teaching time-based generator. Force clock rollback and process restarts in the time-based implementation; retain database uniqueness as a final check.

**Pass:** stale workers cannot commit protected effects, IDs remain unique under the tested assumptions, and you explain why this utility is not a home-built replacement for a consensus system.

### 17-P14. Web crawler / search indexing: local documentation search

**Implement:** a local fixture website containing linked pages, a redirect, duplicate content, `robots.txt`, a slow endpoint, and an oversized response. Crawl only this explicitly allowlisted local site.

**Build sequence:** persist a URL frontier with uniqueness and statuses; normalize URLs deliberately; apply per-host concurrency/delay limits; parse robots rules; stream bounded response bodies; compute a content hash; extract text; and feed the search projection from P04. The robots protocol communicates crawler rules, not access authorization. [ROBOTS]

**Break it:** create a link cycle, redirect toward a disallowed host/port, return repeated errors, duplicate the same page under two URLs, and interrupt a worker. Revalidate redirects and use bounded retries rather than expanding to arbitrary networks.

**Pass:** the crawler stays within its allowlist, terminates or checkpoints predictably, does not repeatedly index duplicate frontier jobs, and can rebuild a search index from its recorded synthetic documents.

### 17-A. Suggested execution sequence

Use this as a milestone-based sequence, not a promise that mastery takes a fixed number of hours. The week labels are an optional organization for your own pace.

| Phase | Build and measure | Evidence required before moving on |
|---|---|---|
| Week 1 | Baseline, networking, initial API contract, database model | First request trace, passing baseline tests, reproducible setup |
| Week 2 | Database races, consistency, idempotency, estimation | One-seat concurrency proof, workload assumptions |
| Week 3 | Caching, scalability, latency | Controlled before/after benchmark, stale-fill experiment |
| Week 4 | Messaging, availability, reliability | Crash-before-ACK recovery, outage behavior, bounded retries |
| Week 5 | Storage, durability, security, maintainability | Restore drill, tenant tests, CI/runbook evidence |
| Week 6 | URL Shortener and Pastebin capstones | Expiry, collision, orphan-cleanup and cache tests |
| Week 7 | BookMyShow and Drive/Dropbox capstones | Hold/payment race tests, offline sync conflict recovery |
| Week 8 | Selected pattern projects and architecture defense | Demonstration, measurements, tradeoffs, remaining limitations |

All fourteen pattern projects can be completed afterwards or interleaved where relevant. They are not realistically equivalent in effort. Work on one unfinished invariant at a time rather than starting fourteen incomplete systems.

For each topic, end with three interview questions: what guarantee did you implement; what failure invalidates it; and what evidence would justify a different design? Preserve your measured results and failed experiments in `evidence/`, not only screenshots of a successful UI.


---

# 18. Capstone - URL Shortener

**Project:** LinkHub, a tenant-managed short-link service with a public redirect endpoint.

**Source alignment:** the upload lists this case-study title but does not include its worked solution or detailed subsections. This is a newly authored capstone.

### 18-A. Implement a small correct service

Create `links(slug PRIMARY KEY, tenant_id, owner_id, target_url, version, expires_at, deleted_at, created_at)`. If the public redirect path is only `/s/:slug`, the slug must be unique globally, not merely within a tenant. Alternatively include a tenant-specific domain or path in the lookup identity.

Implement authenticated `POST /v1/links`, owner-authorized `GET/PATCH/DELETE /v1/links/:slug`, and public `GET /s/:slug`. Accept only the intended URL schemes, reject control characters, limit lengths, and define abuse handling. Do not make the server fetch arbitrary destination URLs merely to validate their existence.

Generate ten-character Base62 slugs using a cryptographically secure random generator with unbiased character selection; insert under the unique constraint and retry a bounded number of collisions. For comparison, implement sequence-to-Base62 allocation and document its enumeration properties. A short-link identifier is not an authorization token for confidential data.

**Pass:** forced collisions produce another slug rather than overwriting an existing destination. Access to the management API remains tenant/owner restricted.

### 18-B. Add caching, expiry, and safe management semantics

Cache public destination records with version and expiry metadata. Use temporary redirects during the lab and an explicit browser-cache policy, so a permanent cached redirect does not defeat later revocation experiments. Validate logical expiry on the read path; do not wait for a cleanup worker to hide expired links.

For an update, use `If-Match`/version-conditional writes. Test the stale-fill race from Lab 06 and state the maximum allowed staleness for a changed or disabled destination. Choose a stricter authoritative check when the stated revocation requirement demands it.

**Break it:** warm the cache, change the target, disable the link, delay invalidation, and request it through both API replicas.

**Pass:** outcomes match the documented expiry and revocation policy. Disabling a link cannot retroactively erase a destination already received by a browser; make that boundary explicit.

### 18-C. Decouple click analytics and compare key allocation

Persist or enqueue a click event with a chosen identity. Decide whether the metric counts HTTP redirect requests, deduplicated events, sessions, or estimated unique visitors; these are different numbers. Keep privacy-sensitive data out of the default payload.

Make analytics failure either a bounded best-effort loss or a durably acknowledged requirement. Do not claim both guaranteed lossless analytics and zero dependency on its durable acceptance path.

As an advanced allocation exercise, build a pre-generated key pool with transactional claims. Crash after claiming but before creating the link. Decide whether unused keys are permanently burned or safely recoverable under an explicit claim protocol. Never reassign a slug already visible to a client.

**Pass:** retries and worker replay follow the declared counting semantics; the redirect latency is measured with analytics healthy and unavailable.

### 18-D. Test scale and defend the architecture

Generate a read-heavy synthetic workload with one hot slug, a long tail, invalid slugs, and management writes. Compare no cache, shared cache, and local-plus-shared caching. Stop Redis and observe database load and admission behavior.

Required demonstrations: forced key collision; duplicate create request; expiry while cached; deletion during a read; cross-tenant management attempt; database outage; analytics replay; and hot-key traffic. Produce the API contract, schema, architecture, estimates, benchmark evidence, and one-page tradeoff memo.

**Completion gate:** no collision overwrites, no unauthorized management, no expired-link exposure beyond the declared read policy, and no unsupported claim of global scale from a laptop benchmark.


---

# 19. Capstone - Pastebin

**Project:** PasteHub, a text-sharing service with expiry, object storage, and optional access restrictions.

**Source alignment:** requirements, capacity, APIs, database design, key generation, multilayer caching, three-layer expiry, Bloom filters, multi-region choices, failure scenarios, security, and scaling. The design below implements these indexed themes; it is not the missing original worked solution.

### 19-A. Separate metadata from content with explicit states

Reuse LinkHub key allocation, but add `pastes(id, tenant_id, owner_id, object_key, bytes, sha256, visibility, expires_at, version, state)`. Define `uploading`, `available`, and `deleted` states. Expose create, read, owner delete, and optional metadata update endpoints.

Write content through the storage adapter with a byte limit and SHA-256 checksum. Commit available metadata only after the content object is verified. If upload succeeds but metadata creation fails, leave a tracked orphan eligible for later cleanup. Use an ownership check for private content; an unlisted URL is not equivalent to authenticated private access.

**Pass:** readers never receive a successfully advertised paste whose object was never completed; interrupted writes are recoverable without arbitrary deletion of shared live objects.

### 19-B. Implement three distinct expiry layers

For this lab, define the three layers explicitly: (1) logical expiry enforced by the read path; (2) cache lifetime bounded by the remaining expiry interval, with the expiry value also checked on cache hits; and (3) asynchronous physical cleanup after a grace interval.

Create a paste expiring shortly, warm every cache, stop the cleanup worker, and request it after the deadline. It must become logically unavailable even though bytes still exist. Then resume cleanup and verify physical removal according to retention policy.

Use an injectable test clock for deterministic boundary tests; define the deployed system's clock source and acceptable skew. Do not infer strict global expiry from synchronized laptop processes.

**Pass:** cleanup delays do not make an expired paste readable. A retrying cleaner does not delete a newly created or still-referenced object.

### 19-C. Add a Bloom filter without creating false 404s

Implement a membership filter for paste IDs and measure how many random nonexistent-ID requests can avoid a database lookup. Compare memory usage and false-positive rate with an exact set.

Then pause filter updates, create a paste in the database, and query it. This is the important distributed-system failure: a filter that has not seen the new ID can reject valid data even though the Bloom algorithm itself has no false negatives for inserted elements.

Build the filter from a snapshot plus a reliable catch-up stream, version its generation, and bypass negative decisions whenever completeness is not established. Cache negative lookups only with an explicit creation/invalidation policy.

**Pass:** no valid paste is rejected because the active filter is stale or partially rebuilt. False positives may cost an extra database lookup; an unsafe negative must not become an authoritative 404.

### 19-D. Render safely and test object/cache behavior

Serve paste text as inert text or safely escaped content. Test HTML/script content, large Unicode text, invalid encoding, oversized requests, unauthorized reads, deletion while cached, and a storage outage. Include rate limits for both creation and invalid-ID enumeration within your own lab.

Add a read-through cache wrapper and optionally a simulated edge cache for public, permitted content. Private authorization and strict revocation must not be bypassed by a shared public cache key.

**Pass:** text is not executed, private data is not cached for unrelated users, and checksum mismatch produces an observable failure rather than a silently altered paste.

### 19-E. Evaluate multi-region and 100-times growth as experiments

Model a home-writer region and a delayed read projection. Replicate available metadata, expiry, deletion markers, and object presence deliberately; do not publish remote metadata before its bytes are accessible under your read contract.

For strict deletion or permission revocation, choose an authoritative check or a fail-closed policy when freshness cannot be confirmed. Compare that with a deliberately eventual public-paste policy.

Multiply workload assumptions by 100, then identify which local subsystem you can actually stress: key generation, negative lookups, cache memory, object throughput, or cleanup lag. Report simulations and extrapolations separately from measurements.

**Completion gate:** demonstrate upload-orphan recovery, cached expiry, safe filter rebuild, duplicate creation, private access denial, deletion propagation, and a coherent capacity memo.


---

# 20. Capstone - BookMyShow

**Project:** SeatHub, reserved-seat booking with expiring holds and a simulated payment provider. This extends the baseline's quantity-based inventory; it is not already implemented by that baseline.

**Source alignment:** seat reservation, end-to-end booking, live seat maps, thundering herds, cancellation/refunds, search, regions, failures, security, and scaling.

### 20-A. Model seats and atomically acquire multi-seat holds

Create `shows`, `show_seats(show_id, seat_id, state, hold_id, hold_until, version)`, `holds`, `bookings`, and `payment_attempts`. The seat row's primary key is `(show_id, seat_id)`; add appropriate tenant relationships. Define a legal state-transition table before coding.

Implement `POST /shows/:id/holds` with a sorted, duplicate-free list of requested seat IDs. In one short database transaction, lock the relevant rows in a stable order, confirm every requested row exists, verify all are available or logically expired, and assign one unguessable hold ID and expiry. Roll back the entire request when any seat is unavailable.

Use database time and check expiry at the point protected by the row locks. PostgreSQL's `now()` is fixed at transaction start; for a boundary test after waiting on a lock, use a fresh clock value where the business rule requires it. Row locks and transaction isolation are described in the PostgreSQL references. [PG-LOCK] [PG-ISO]

**Pass:** two concurrent requests cannot hold the same seat; a three-seat request either gets all three or none; conflicting multi-seat requests do not leave partial holds.

### 20-B. Make expiry and ownership authoritative

Implement `GET /holds/:id`, owner release, and a background expiry cleaner. The cleaner is housekeeping, not the sole expiry enforcement mechanism. Every confirm/release operation must verify current hold identity and allowed state. A newer holder must not be affected by a delayed request carrying the old hold ID.

Add a version/fencing check where jobs act on an earlier seat state. Expiry cleanup should conditionally clear only the matching expired hold, not every row named in an old work item.

**Break it:** pause an expiry worker, allow the old hold to expire, acquire a new hold, then resume the old worker.

**Pass:** a stale cleanup or release cannot erase the new holder's reservation. Define what the UI displays when its cached seat map is behind the database.

### 20-C. Integrate fake payment without long-held inventory locks

Implement a local provider that records payments by a stable idempotency key and can deliberately time out after recording a successful charge. Create a payment-attempt record before calling it. Do not keep a seat row locked while waiting on a network request.

For this lab, choose a clear rule: a successful provider response/callback may confirm a booking only when the original hold still owns all seats and remains valid at the confirmation transaction. An alternative bounded payment-in-progress extension is possible, but must be explicitly modeled and tested rather than implicitly holding forever.

Deduplicate signed fake callbacks using provider event IDs. Handle `pending`, `succeeded`, `failed`, `unknown`, and refund states as durable state transitions. When a late successful payment arrives after the seat was legitimately reassigned, do not steal the seat from the new holder: record compensation/refund work with its own stable idempotency key.

**Pass:** duplicate callbacks do not create duplicate bookings; timeout-after-charge becomes reconciliation work; late success cannot confirm an expired reassigned hold; repeated refund processing does not duplicate the refund effect.

### 20-D. Broadcast committed changes and control a flash sale

Publish seat-map changes through the outbox after commit. Include show ID, seat IDs, and versions. On reconnect or detected version gaps, clients fetch an authoritative snapshot. A WebSocket message is a notification of state, not permission to buy a seat.

Add a waiting room with bounded admission and a documented fairness policy. Rate-limit expensive hold attempts independently of cheap catalog reads. Keep the admitted-work queue bounded and measure rejection/wait time, rather than simply accepting an unlimited backlog.

**Break it:** direct 100 clients at one seat, disconnect live updates, kill one API, and slow the database while admission continues.

**Pass:** at most one current valid hold/booking owns the seat, unavailable responses are distinguished from infrastructure failures, and resource usage remains bounded under the tested spike.

### 20-E. Add cancellation, refunds, and search projections

Define cancellation policy for this teaching system. For example, a confirmed booking may transition to cancelled and release its seats in a transaction that also creates refund-pending work. A delayed provider refund does not require pretending the cancellation never happened; expose both states accurately.

Make cancellation idempotent and check ownership. Publish seat availability only from committed transitions. Index show discovery asynchronously while keeping hold decisions on the authoritative database.

Assign each show a home writer for the multi-region exercise. Route all competing seat mutations for that show to the same authority unless you deliberately implement and test a stronger distributed coordination design. Separate read availability from write availability.

**Completion gate:** retain evidence for simultaneous holds, multi-seat rollback, expired-hold replacement, stale cleanup, duplicate payment callback, timeout-after-charge, late payment success, duplicate cancellation/refund, live-update gap recovery, and overload admission. No real payment service or money is needed.


---

# 21. Capstone - Google Drive and Dropbox

**Project:** FileHub, tenant-isolated file storage with resumable uploads, versions, sharing, and offline synchronization.

**Source alignment:** capacity, APIs, data model, component design, regions, tradeoffs, failure scenarios, security, and interview review. This is an implementation assignment inspired by the indexed case-study category, not a reproduction of Google or Dropbox's internal design.

### 21-A. Build metadata and immutable content manifests

Create `files(id, tenant_id, parent_id, name, current_version, deleted_at)`, `file_versions`, `chunks`, `manifest_entries(version_id, ordinal, chunk_id)`, upload sessions, access-control entries, and a change feed. Validate folder ownership and prevent parent cycles.

Start with a streaming whole-file adapter, then split large files into fixed-size chunks for the exercise. Compute SHA-256 for each chunk and record its exact byte count. A manifest stores ordered chunks and the file length; verify reassembled bytes, not just the existence of chunk names.

Deduplicate within a tenant initially to avoid introducing cross-tenant existence/privacy concerns. Keep logical user quota separate from physically deduplicated storage. Define version retention explicitly.

**Pass:** the reconstructed file matches the original byte-for-byte, a missing/corrupt chunk is detected, and file access remains authorized independently of knowledge of a chunk hash.

### 21-B. Implement resumable uploads and atomic publication

Expose create-upload, upload-part, inspect-upload, and complete-upload endpoints. Bind sessions to tenant and owner. Identify each part by session and ordinal; reject a retry that changes the content for an already accepted part unless your API explicitly supports replacement.

For completion, validate the entire manifest, expected size, and checksums, then publish the new version and update metadata in one database transaction. Bytes uploaded before completion remain staged, not visible as a completed file. Completing the same session twice must return the same published version.

**Break it:** disconnect after several chunks, restart the client, resend a part, kill the API before metadata commit, and complete concurrently from two clients.

**Pass:** upload resumes without restarting valid parts, completion is idempotent, incomplete files are not advertised, and abandoned sessions can be cleaned without deleting referenced chunks.

### 21-C. Synchronize clients using a commit-safe change cursor

Implement a change feed scoped to a tenant, with tombstones for deletion and the file version associated with each change. For the teaching implementation, serialize sequence allocation using a tenant counter row locked and incremented in the **same transaction** that mutates metadata and inserts the change record.

Do not casually use a standalone `BIGSERIAL` value as a guaranteed commit-order cursor. One transaction can allocate a lower sequence and commit after another transaction whose higher sequence was already consumed. A client that advances past the higher value can then miss the late lower record. The counter-row lock approach deliberately serializes same-tenant commits; document its throughput tradeoff.

Build a consistent initial snapshot/cursor handoff, followed by paginated incremental changes. Define a response for a cursor older than retained history, such as an explicit full-resynchronization requirement.

**Pass:** delaying one metadata transaction cannot make another client's cursor permanently skip its change. Replaying the same feed page is harmless.

### 21-D. Handle offline edit conflicts without losing data

Create two client working directories strictly beneath a dedicated lab path. Keep local metadata mapping file IDs, known versions, and pending operations. Do not point the exercise at your real documents folder.

Use a base-version condition when uploading an edit. If both clients edit the same file offline, choose an explicit conflict policy: preserve both versions and create a conflict copy, or present a merge workflow. Do not silently overwrite the later server state. Whole-file synchronization is different from the CRDT text editor in Lab 17.

Test rename versus edit, delete versus edit, move into a deleted parent, and stale client replay. Use stable file IDs so a rename is not necessarily a delete-and-reupload operation.

**Pass:** both conflicting contents remain recoverable, tombstones propagate, and repeated synchronization converges according to your documented policy.

### 21-E. Add sharing, revocation, and safe garbage collection

Support owner-controlled ACL changes and narrowly scoped share/download tickets. Authorize metadata, content, and change-feed access consistently. Test revocation with warm caches and previously issued tickets. Explain whether ticket expiry creates a bounded revocation delay or whether each download rechecks current permission.

Garbage collection must account for retained versions, in-progress uploads, and concurrent publication. Use a documented transactional reference or mark-and-sweep protocol with a grace period and final eligibility check. A count observed earlier is not permission to delete bytes currently being published.

**Break it:** run cleanup while another client completes an upload referencing an existing chunk; retry deletion jobs; restore metadata from backup; and revoke an active shared user.

**Pass:** cleanup never deletes a chunk referenced by a live retained version, permissions are not accidentally restored from stale cache, and restore procedures include the referenced bytes and required keys.

### 21-F. Exercise recovery and region tradeoffs

Restore metadata and object data into an isolated environment, then verify manifests, versions, permissions, and incremental-sync behavior. Do not allow restored background jobs to produce real external effects.

Model a home writer and a delayed remote replica. Document what a client sees when metadata has arrived but chunks have not, or when a regional failure loses recently acknowledged data under the chosen replication policy. Local simulated delays are useful experiments, not a proof of a real provider's regional durability.

**Completion gate:** demonstrate interrupted-upload resume, repeated completion, checksum mismatch, conflict preservation, deletion propagation, safe cursor behavior, cross-tenant denial, share revocation, GC/publication races, and verified restore. Deliver a working two-client demonstration plus the same architecture and evidence package used in earlier capstones.


---

# Coverage and Completion Checklist

The source supplied is an index only. This mapping establishes coverage of its visible top-level topics and fourteen named roadmap patterns, not the complete unpublished contents of every linked chapter.

| Source area | Implementation coverage |
|---|---|
| Files 01-15: core HLD concepts | Labs 01-15, with baseline changes, failure experiments, and pass criteria |
| File 16: Architecture & Frameworks | Lab 16, newly authored six-artifact review and architecture defense |
| File 17: Roadmaps & Study Plans | Lab 17, all 14 named pattern projects and a new milestone-based schedule |
| File 18: URL Shortener | Lab 18: keys, CRUD, redirects, expiry, caching, analytics, failures |
| File 19: Pastebin | Lab 19: object/metadata states, expiry, Bloom safety, security, growth |
| File 20: BookMyShow | Lab 20: holds, concurrency, simulated payment, cancellation, live updates |
| File 21: Drive/Dropbox | Lab 21: manifests, resumable upload, versions, safe sync cursors, sharing, GC |
| Interview-oriented notes, decision frameworks, anti-patterns | Evidence template, decision records, failure explanations, and Lab 16 review |
| Hidden detailed strategies, original 70-problem map, original day-by-day roadmap | Not available in the uploaded index; not represented as recovered content |

For each experiment, record **not started / built / failure reproduced / assertion passed / tradeoff explained**. An attractive UI is not the completion criterion.

The fourteen pattern projects are explicitly numbered 17-P01 through 17-P14. Their grouping follows the supplied index, without inventing the unseen original explanations.


---

# Implementation References

The uploaded index supplies the curriculum organization. These official references support the implementation semantics used in newly authored exercises. Reference pages may evolve; the package does not assert its teaching image versions are the latest. Technical verification was performed during preparation on 6 October 2026.

## [PG-ISO] PostgreSQL transaction isolation

`https://www.postgresql.org/docs/17/transaction-iso.html`

## [PG-LOCK] PostgreSQL explicit locking

`https://www.postgresql.org/docs/current/explicit-locking.html`

## [PG-PARTITION] PostgreSQL table partitioning

`https://www.postgresql.org/docs/17/ddl-partitioning.html`

## [PG-EXPLAIN] PostgreSQL using EXPLAIN

`https://www.postgresql.org/docs/17/using-explain.html`

## [PG-REPLICATION] PostgreSQL warm standby and replication

`https://www.postgresql.org/docs/17/warm-standby.html`

## [PG-PITR] PostgreSQL continuous archiving and recovery

`https://www.postgresql.org/docs/17/continuous-archiving.html`

## [PG-RLS] PostgreSQL row-level security

`https://www.postgresql.org/docs/17/ddl-rowsecurity.html`

## [PG-SEARCH] PostgreSQL text search

`https://www.postgresql.org/docs/17/textsearch-intro.html`

## [REDIS-LOCK] Redis distributed-lock patterns and limitations

`https://redis.io/docs/latest/develop/clients/patterns/distributed-locks/`

## [REDIS-EVICT] Redis eviction policies

`https://redis.io/docs/latest/develop/reference/eviction/`

## [REDIS-LUA] Redis scripting with Lua

`https://redis.io/docs/latest/develop/programmability/eval-intro/`

## [RABBIT-CONFIRMS] RabbitMQ publisher confirms and consumer acknowledgements

`https://www.rabbitmq.com/docs/confirms`

## [COMPOSE] Docker Compose startup ordering and health dependencies

`https://docs.docker.com/compose/how-tos/startup-order/`

## [NGINX] NGINX upstream configuration

`https://nginx.org/en/docs/http/ngx_http_upstream_module.html`

## [HTTP] RFC 9110: HTTP Semantics

`https://www.rfc-editor.org/rfc/rfc9110.html`

## [K6] k6 open and closed workload models

`https://grafana.com/docs/k6/latest/using-k6/scenarios/concepts/open-vs-closed/`

## [S3-MULTIPART] Amazon S3 multipart upload overview

`https://docs.aws.amazon.com/AmazonS3/latest/userguide/mpuoverview.html`

## [ETCD-FAILURES] etcd failure behavior

`https://etcd.io/docs/v3.6/op-guide/failures/`

## [ETCD-CONTAINER] etcd container deployment

`https://etcd.io/docs/v3.6/op-guide/container/`

## [OWASP-BOLA] OWASP API1: Broken Object Level Authorization

`https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/`

## [OTEL] OpenTelemetry Node.js getting started

`https://opentelemetry.io/docs/languages/js/getting-started/nodejs/`

## [AWS-RETRY] AWS Architecture: exponential backoff and jitter

`https://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/`

## [GRPC] gRPC Node.js basics

`https://grpc.io/docs/languages/node/basics/`

## [GRAPHQL] GraphQL.js N+1 and DataLoader

`https://www.graphql-js.org/docs/n1-dataloader/`

## [YJS] Yjs collaborative editor

`https://docs.yjs.dev/getting-started/a-collaborative-editor`

## [POSTGIS] PostGIS ST_DWithin

`https://postgis.net/docs/ST_DWithin.html`

## [ROBOTS] RFC 9309: Robots Exclusion Protocol

`https://www.rfc-editor.org/rfc/rfc9309.html`


---

# Validation Record

## Executed successfully in the authoring environment

- Node.js v22.16.0: `node --test test/*.test.mjs` - **36 tests passed, 0 failed**.
- `node --check` on every `.mjs` file under `src/`, `scripts/`, and `test/` - passed.
- Started the actual memory-demo HTTP server, checked liveness, created a booking, and repeated its idempotency key - same booking ID returned.
- Parsed the main Compose YAML and both optional infrastructure YAML files - syntactically valid YAML with the expected service names. This is not equivalent to Docker Compose runtime validation.
- Checked workbook code-fence balance, named experiment count, reference labels, and package contents.

The raw unit/HTTP test output is in `evidence/author-validation-tests.txt`.

## Not executed here

Docker, PostgreSQL, and Redis executables were unavailable. npm registry access did not complete, so dependencies were not installed here and no dependency lockfile was generated. The database/broker adapters, PostgreSQL race tests, optional replica/etcd stacks, Redis Lua script, and k6 workload require local execution and have not been runtime-verified in this environment.

The passing HTTP tests use the memory adapter. Its single-process behavior is **not** a proof of PostgreSQL isolation, broker delivery, or multi-node correctness. Production authentication, payment processing, storage/sync, advanced pattern services, and cloud deployment are learner-built extensions rather than completed starter features.

No throughput, latency, recovery-time, or cloud-scale performance figures are claimed as measured results. Numeric targets in the workbook are proposed experiment settings or explicitly labeled arithmetic examples.

## Suggested local verification order

1. Run the 36 fast tests.
2. Build the Compose environment, seed fixtures, and inspect readiness.
3. Run `node scripts/integration.mjs`.
4. Run the one-seat race on its unused fixture and check database conservation.
5. Enable the asynchronous profile and perform the outbox/consumer crash experiments.
6. Execute optional infrastructure recipes and each advanced lab independently.

Keep any failures and environment changes in `evidence/`; do not edit a benchmark report to imply a test passed when it was not run.
