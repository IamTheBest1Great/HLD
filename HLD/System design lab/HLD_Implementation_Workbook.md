<a id="start"></a>
# Build System Design: Implementation Lab Workbook


**189 implementation labs / 21 source chapters / 14 pattern families / 70 named variants**


Build one evolving application, reproduce its failure modes, implement a strategy, and prove the relevant invariant. This workbook is paired with the reference code and configurations in the ZIP package. It is a learning implementation environment, not a production-certified system.


## Read this first


The supplied earlier workbook was index-based. This edition expands the named repository strategies into concrete exercises and provides a fresh standalone reference project. Each lab includes source-topic alignment, required files, starting commands, implementation steps, failure injection and a completion gate.


**Coverage is not the same as implemented-product status:** reference code, integration recipes, models and learner-built extensions are labeled separately. Read the validation record before treating any infrastructure path as verified.


## Navigation


[Environment and quickstart](#environment) / [Validation](#validation) / [Operational playbooks](#playbooks) / [70 variants](#variants) / [Pattern overlays](#overlays) / [Study plan](#study) / [Source interpretation](#interpretation) / [References](#references)


- [01. Networking & Protocols](#chapter-01) - 14 labs

- [02. Scalability](#chapter-02) - 17 labs

- [03. Consistency & CAP Theorem](#chapter-03) - 10 labs

- [04. Availability](#chapter-04) - 7 labs

- [05. Database](#chapter-05) - 10 labs

- [06. Caching](#chapter-06) - 10 labs

- [07. Messaging Queues & Async](#chapter-07) - 9 labs

- [08. Storage Systems](#chapter-08) - 9 labs

- [09. Fault Tolerance & Reliability](#chapter-09) - 8 labs

- [10. Reducing Latency](#chapter-10) - 8 labs

- [11. Durability](#chapter-11) - 10 labs

- [12. Security](#chapter-12) - 14 labs

- [13. Maintainability](#chapter-13) - 8 labs

- [14. API Design](#chapter-14) - 7 labs

- [15. Estimation Skills](#chapter-15) - 6 labs

- [16. HLD Architecture and Frameworks](#chapter-16) - 6 labs

- [17. Roadmaps and Study Plans](#chapter-17) - 15 labs

- [18. Case Study: URL Shortener](#chapter-18) - 4 labs

- [19. Case Study: Pastebin](#chapter-19) - 4 labs

- [20. Case Study: BookMyShow](#chapter-20) - 6 labs

- [21. Case Study: Google Drive / Dropbox](#chapter-21) - 7 labs


<a id="environment"></a>


# 00. Environment and working method

A source-aligned implementation curriculum built around **189 labs, 21 chapters, 14 system-pattern projects, and 70 named architecture variants**. The labs combine reusable reference implementations with explicit engineering assignments, workload experiments, failure injection, recovery procedures, and acceptance gates.

**Start here:** `WORKBOOK.html ` for the navigable workbook, ` WORKBOOK.md ` for the full text, ` docs/COVERAGE.md ` for topic-to-lab mapping, and ` VALIDATION.md` for what was actually tested.

## What you are building

EventHub is a synthetic multi-tenant event platform. Catalog browsing, inventory, reservations, notifications, uploads, activity feeds, search and synchronization provide one evolving domain instead of unrelated toy examples. Four capstones extend it into a URL shortener, Pastebin, numbered-seat ticket booking, and a Drive/Dropbox-style file service.

```text
Client -> NGINX -> stateless API instances -> PostgreSQL (authority)
                       |                         |
                       +-> Redis (cache)         +-> transactional outbox
                                                        |
                                                confirmed publication
                                                        v
                                                    RabbitMQ
                                                        |
                                               idempotent consumer
                                                        v
                                               notification record

Independent experiments:
  primary -> standby1 + standby2       directory -> shard A/B/C
  Kafka / Cassandra / etcd             Kubernetes HPA/VPA / sandbox ASG
```

The default app is a modular service. Do not turn every optional lab into a mandatory runtime dependency.

## Fastest start: no packages or containers required

Install Node.js 22 or a compatible newer release. From this directory:

```bash
node --test test/*.test.mjs
node experiments/run.mjs list
node experiments/run.mjs sharding
node experiments/run.mjs all
node src/demo.mjs
```

In a second terminal:

```bash
BASE_URL=http://127.0.0.1:3000 node scripts/smoke.mjs
BASE_URL=http://127.0.0.1:3000 node scripts/race.mjs
BASE_URL=http://127.0.0.1:3000 SAME_KEY=true node scripts/race.mjs
BASE_URL=http://127.0.0.1:3000 RATE=20 SECONDS=10 node scripts/load.mjs
```

The memory demo loses state when stopped. Its passing tests do not validate PostgreSQL isolation or broker delivery.

## Database-backed track

Use Docker Engine/Desktop with Compose v2. Linux or a Linux shell in WSL2 is assumed for shell commands. Container image downloads and dependency installation require internet access.

```bash
docker compose up -d --build --wait --scale api=2
docker compose exec api node scripts/seed.mjs
node scripts/smoke.mjs
node scripts/race.mjs
SAME_KEY=true node scripts/race.mjs
docker compose exec -T postgres psql -U lab -d eventhub < db/assert_invariants.sql
```

The gateway is `http://127.0.0.1:8080 `. Direct API endpoints are not published to the host. ` docker compose exec api ` selects one instance; use `--index N` when testing a particular replica.

```bash
curl -i http://127.0.0.1:8080/v1/events \
  -H 'Authorization: Bearer lab-alice'
```

Add asynchronous processing only when studying messaging:

```bash
docker compose --profile async up -d --build
docker compose logs --tail=50 publisher worker
```

**Credentials are public fixtures**, not production authentication: `lab-alice `, ` lab-bob `, ` lab-charlie`. Database and broker passwords are disposable lab values. Bind only to localhost. Never expose this stack or reuse these credentials on a public host.

## Resource tracks

These are planning suggestions, not measured minimums. Run one heavyweight cluster at a time.

| Track | Suggested host allocation | What it demonstrates |
|---|---|---|
| Dependency-free | 2 CPU / 2 GB available memory | Algorithms, local HTTP, storage, deterministic failures |
| Core Compose | 4 CPU / 6-8 GB available memory | PostgreSQL, two APIs, cache, gateway, optional broker |
| One distributed cluster | 6-8 CPU / 12-16 GB available memory | Replication, Kafka, Cassandra or Kubernetes, separately |
| Cloud / hardware | Explicit sandbox budget / suitable hardware | Real instance scaling, regional boundaries or disk capabilities |

Do not use a simulated region or several directories on the same disk as evidence of independent failure domains.

## Status labels

- **Run reference:** reusable implementation exists. Some reference tests are dependency-free; database-backed paths still need integration execution.
- **Container recipe:** configuration is supplied, but must be started and verified in your environment.
- **Build extension:** implement the described feature using the reference modules. A starter command may exercise only a prerequisite, not the completed feature.
- **Cloud / hardware / model:** explicit external requirements or a simulation. These are not automatically equivalent to production deployments.

`VALIDATION.md` is the authoritative execution record. An example command or an acceptance criterion is not a claim of successful execution.

## Repeatable dependencies

No lockfile was generated here because external package installation was unavailable. On a network-enabled workstation, run `npm install `, review the dependency tree, commit ` package-lock.json `, then use ` npm ci`. Pin tested image digests for repeated experiments. Image tags and dependency ranges in this package are teaching selections, not a newest-version or security-approval claim.

## Working method

Create a branch, copy `evidence/TEMPLATE.md`, run a baseline, inject a failure, implement one change, repeat the same workload, and commit the raw results. Complete a lab only after its invariant, failure case, recovery and tradeoff are documented. Do not invent benchmark numbers.

## Stop versus reset

```bash
# Preserve volumes and evidence.
docker compose --profile async --profile balance down

# DESTRUCTIVE: only after saving evidence, and only for this disposable project.
# docker compose --profile async --profile balance down -v
```

Each auxiliary Compose file has its own project and named volumes. Stop it with the same `-f` file you used to start it. Cloud resources require explicit deletion; closing a terminal does not stop billing.

## Source and interpretation

Curriculum source: `IamTheBest1Great/HLD `, ` HLD/HLD_Master_Notes `, pinned commit ` 5982a3b5491e4cd1c36902e09fc54c6f0080f7b5 `. The chapter organization and named topics come from that repository. Lab scenarios, commands, implementation choices and acceptance tests are newly authored expansions. See ` docs/SOURCE_INTERPRETATION.md ` for scope, reviewed material and technical qualifications, and ` REFERENCES.md` for official implementation documentation.

The two supplied earlier workbooks are not overwritten. Their references to a different starter are not silently assumed to be present here. This package has its own code, paths and validation record.



<a id="validation"></a>


# Validation and implementation status

## Executed in this authoring environment

| Check | Result | Evidence |
|---|---|---|
| Dependency-free Node test suite | **112 passed, 0 failed, 0 skipped** | `evidence/author-tests.txt` |
| Node source syntax | 43 files passed `node --check` | `evidence/author-static-checks.json` |
| POSIX shell syntax | 6 scripts passed `sh -n` | `evidence/author-static-checks.json` |
| YAML parsing | 23 configuration files parsed | `evidence/author-static-checks.json` |
| Live memory-demo HTTP smoke test | Passed | `evidence/author-http-integration.json` |
| 100 distinct-key attempts for one seat | 1 successful booking identity; conservation checked through API | `evidence/author-http-integration.json` |
| 100 same-key attempts | 1 successful booking identity across replayed responses | `evidence/author-http-integration.json` |
| Bounded local HTTP load smoke | Ran 20 requests/second for 3 seconds | `evidence/author-local-load.json` |
| Deterministic algorithm experiment runner | All listed experiments completed | `evidence/author-experiments.jsonlog` |

The HTTP runs above use the **single-process memory adapter**, not PostgreSQL. Their concurrency results prove only that implementation under that tested failure model. The short load run is a functional smoke test, not a representative performance benchmark. Its latency numbers are not capacity recommendations.

YAML parsing is not Docker Compose schema/runtime validation, Kubernetes admission, CloudFormation validation or deployment success. Shell/Node syntax checks are not dependency integration tests.

Workbook structure was also checked:189 lab anchors,70 variants, valid internal links and JavaScript syntax. Visual browser validation could not be completed because the browser administrator policy blocked local-file and localhost navigation. The raw structural report is in `evidence/author-workbook-checks.json`.

## Not executed here

Docker/Compose, PostgreSQL, Redis, RabbitMQ, Kafka, Cassandra, MongoDB, etcd, Kubernetes, AWS resources and hardware-specific exercises were not run. Those executables/services were unavailable in the authoring environment. Network package installation did not complete, so there is no generated npm lockfile. Container-facing database/broker adapters, replica bootstrap, shard migration, PITR and cloud templates require local integration validation.

No real regional outage, disk power-loss test, payment transaction, public traffic defense test or production deployment occurred. No throughput, latency, durability SLA or zero-downtime claim is established by these local tests.

## What is supplied versus what the learner implements

**Reference code supplied:** core HTTP application with memory and PostgreSQL adapters; fixture tenancy/ownership; guarded booking transaction; transactional idempotency/outbox; optional cache; publisher/inbox consumer; placement algorithms; cache/limiter/reliability models; consensus/CRDT teaching models; checksummed WAL and local object/chunk handling; erasure codec; search/counting/ID algorithms; bounded load/race scripts; database schemas; replication/shard-migration scripts; protocol sample servers and infrastructure configurations.

**Learner extensions:** production OIDC/session integration; complete numbered-seat HTTP/payment workflows; full file-upload/sync APIs; durable distributed chat; full recommendation/search/crawler products; automated safe failover; online CDC resharding; production telemetry/security/secret operations; the full 70 variant systems; real cloud/hardware validation. Their lab instructions and acceptance gates are supplied, but these are not all finished services.

## First local integration checklist

1. Run `node --test test/*.test.mjs`.
2. Install dependencies on a network-enabled workstation, review and retain the lockfile.
3. Run `docker compose config`, build the core stack, wait for readiness, then run smoke/race scripts.
4. Execute `db/assert_invariants.sql` against the actual PostgreSQL database.
5. Start async services and reproduce publication/effect crash windows.
6. Run each isolated replication/sharding/PITR/cluster stack separately and save failures as well as successes.
7. Exercise Kubernetes/cloud/hardware only after confirming prerequisites, target context/account and cleanup budget.

Keep observed failures visible in `evidence/`. Do not relabel an unexecuted recipe as a passing integration test.



## Evidence contract for every lab


### Evidence template

Lab ID / source topic:
Date / code commit:
Host OS / CPU / RAM / Docker / Node / image digests:
Track: reference / extension / model / container / cloud / hardware

## Hypothesis and invariant
What changes? What must remain true? What would falsify the claim?

## Exact setup
Commands, fixture generation, credentials classification, dependency versions, resource limits and initial state.

## Baseline
Workload mix, arrival model, requested and delivered RPS, duration, warm/cold state, total requests, errors by category, p50/p95/p99, CPU, RSS, database pool wait, queue age.

## Failure and recovery
Injection target, allowed blast radius, duration, stop condition, chronology, recovery command, lost/duplicate/ambiguous operations. Save operation IDs in logs, not metric labels.

## Assertions
SQL or test command; actual output; expected outcome; passed/failed/not run. Include ownership, conservation, version, replay or checksum checks as appropriate.

## Tradeoff and cleanup
Additional cost/complexity, remaining failure mode, rejected alternative, rollback, resource cleanup, next experiment.

## Completion status
Not started / implemented / failure reproduced / assertion passed / reviewed.



<a id="chapter-01"></a>
# 01. Networking & Protocols

**Source chapter:** [01_Networking_and_Protocols.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/01_Networking_and_Protocols.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Core quickstart for gateway work; Node-only tests for transports; install dependencies for gRPC, GraphQL and WebSockets.


<a id="lab-01-TRACE"></a>
## 01-TRACE - Trace DNS, TCP, TLS and the seven OSI layers

**Track:** Run reference. **Source topics:** request path; OSI; latency; throughput; network hops.

**Files to inspect or extend:** `src/http.mjs`, `scripts/load.mjs`.

### Start / reproduce

```bash
curl -s -o /dev/null -H 'Authorization: Bearer lab-alice' -w 'dns=%{time_namelookup} connect=%{time_connect} first_byte=%{time_starttransfer} total=%{time_total}
' http://127.0.0.1:8080/v1/events
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Start the main stack and issue an authenticated request with curl -v. Save timestamps, headers, payload size and X-Instance.
2. Use the timing command below, subtract cumulative checkpoints rather than adding them. Repeat direct-to-API from a container and through the gateway.
3. Capture only the isolated lab interface with tcpdump or Wireshark; identify application framing, transport ports, IP routing and link encapsulation. Record which layers cannot be inferred from HTTP alone.

**Inject this failure:** Add a slow API instance and distinguish server processing delay from connect or DNS time. A loopback IP bypasses external DNS.

**Completion gate:** An annotated request timeline links each measured delay to a component and explains OSI responsibilities without claiming all layers are visible in a browser.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-01-DNS"></a>
## 01-DNS - Authoritative DNS, record types, TTL and failover

**Track:** Container recipe. **Source topics:** DNS recursion; authoritative DNS; A; AAAA; CNAME; MX; TXT; NS; TTL; GeoDNS.

**Files to inspect or extend:** `infra/network/dns.yaml`, `infra/network/Corefile`, `infra/network/eventhub.test`.

### Start / reproduce

```bash
docker compose -f infra/network/dns.yaml up -d
dig @127.0.0.1 -p 1053 eventhub.test A
dig @127.0.0.1 -p 1053 api.eventhub.test CNAME
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the supplied authoritative eventhub.test zone on port 1053. Query A, AAAA, CNAME, MX, TXT and NS explicitly with dig.
2. Change the zone serial and A answer, restart the isolated DNS service, and inspect old answers through a caching resolver until TTL expiry.
3. Add two named origin addresses and a health-driven answer switch; repeat with a client retaining an existing TCP connection. For GeoDNS, select the answer using a synthetic client-region fixture and document that this is not real geographic routing.

**Inject this failure:** Stop the preferred origin while the client has a cached answer. Record the window where DNS failover has not changed the request destination.

**Completion gate:** TTL, resolver caching, connection reuse and health detection appear as separate contributors to recovery time.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-01-HTTP"></a>
## 01-HTTP - HTTP semantics, statelessness, headers and conditional requests

**Track:** Run reference. **Source topics:** HTTP methods; status codes; headers; cookies; statelessness; ETag; If-Match.

**Files to inspect or extend:** `src/http.mjs`, `docs/openapi.yaml`.

### Start / reproduce

```bash
node --test test/http.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Exercise GET, POST and PATCH through the core API. Record Location, ETag, no-store, request ID and replay headers.
2. Add OPTIONS and a method allowlist. Use 201 for creation, 202 for a queued job, 204 only without a response body, and distinct validation, authentication, authorization and conflict codes.
3. Create two organizer clients reading v1; PATCH from both with If-Match. Contrast a resource version with an API version.

**Inject this failure:** Reuse an idempotency key with another quantity; submit an expired representation version and an unsupported method.

**Completion gate:** Only one edit to the same representation version succeeds; responses follow the documented API contract.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-01-TLS"></a>
## 01-TLS - HTTPS, certificate validation and mTLS

**Track:** Container recipe. **Source topics:** TLS; HTTPS; certificates; TLS termination; mTLS; TLS session reuse.

**Files to inspect or extend:** `infra/network/caddy.yaml`, `infra/network/Caddyfile`.

### Start / reproduce

```bash
docker compose -f infra/network/caddy.yaml up -d --build
# Export the local CA, then use curl --cacert with --resolve. See playbooks/NETWORK.md.
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run Caddy with its isolated local CA. Export its root certificate from the named caddy volume and trust only that certificate for curl tests; use --resolve eventhub.test:8443:127.0.0.1.
2. Check the matching hostname, an untrusted CA and a wrong hostname. Document that Caddy-to-API is plaintext in this teaching topology.
3. Create a separate client certificate signed by a lab CA and require it at an internal listener; accept a valid client and reject missing, expired and wrong-CA certificates. Keep generated private keys out of Git.

**Inject this failure:** Remove the trusted CA or change the hostname. Do not hide failure with a permanent -k flag.

**Completion gate:** Server identity is validated, the termination boundary is documented, and internal mTLS requires an independently authorized client identity.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-01-VERSIONS"></a>
## 01-VERSIONS - Compare HTTP/1.1, HTTP/2 and HTTP/3

**Track:** Container recipe. **Source topics:** HTTP/1.1; HTTP/2; multiplexing; head-of-line blocking; HTTP/3; QUIC.

**Files to inspect or extend:** `infra/network/caddy.yaml`.

### Start / reproduce

```bash
curl -V
# Version-specific requests are shown in playbooks/NETWORK.md.
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use the same Caddy origin, certificate and static payload for each protocol. Check curl -V before selecting --http2 or --http3-only; unsupported builds are a prerequisite failure, not protocol evidence.
2. Run concurrent small and large transfers. Introduce bounded packet delay/loss only in the lab namespace and observe stream completion times.
3. Record connection counts, transferred bytes, CPU, errors and tail latency. Distinguish application multiplexing from transport-level loss behavior.

**Inject this failure:** Block UDP on the isolated HTTP/3 path; distinguish negotiated fallback from an HTTP/3-only failure.

**Completion gate:** The client proves the negotiated version and does not attribute TLS/cache/workload differences to the HTTP version.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-01-TCP"></a>
## 01-TCP - TCP framing, flow control and backpressure

**Track:** Run reference. **Source topics:** TCP; byte streams; framing; flow control; congestion; keep-alive.

**Files to inspect or extend:** `src/transport.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="TCP" test/storage.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use jsonLineDecoder and tcpServer, sending one record split across writes and several records in one write.
2. Extend the server with a length-prefixed framing option, a frame-size limit and read deadline; preserve split UTF-8 bytes.
3. Pause a receiving client. Stop writing when socket.write returns false and resume on drain. Track buffered bytes rather than treating TCP delivery as a business commit.

**Inject this failure:** Send malformed JSON, an oversized frame and an early disconnect halfway through a frame.

**Completion gate:** Each valid complete frame appears once; partial or malformed frames do not become bookings; memory remains bounded for a slow receiver.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-01-UDP"></a>
## 01-UDP - UDP loss, ordering and application acknowledgments

**Track:** Run reference. **Source topics:** UDP; datagrams; loss; ordering; deduplication.

**Files to inspect or extend:** `src/transport.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="UDP" test/storage.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use udpServer to send numbered synthetic telemetry datagrams; every fifth record is deliberately discarded by the application.
2. Add an optional ACK/retry client with a finite retry count and a received-sequence set. Duplicate and reorder datagrams deliberately.
3. Compare reliable command delivery with best-effort presence/telemetry. Keep inventory mutations on a transactional request path.

**Inject this failure:** Drop ACKs after receipt, send the same sequence twice, and restart the receiver with and without persisted deduplication.

**Completion gate:** The report separates injected application loss from actual packet loss, and states whether delivery evidence survives a receiver restart.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-01-LB"></a>
## 01-LB - Round-robin, weighted, least-connections and IP-hash balancing

**Track:** Run reference + container. **Source topics:** round-robin; weighted round-robin; least connections; IP hash; sticky sessions.

**Files to inspect or extend:** `src/algorithms/load-balancer.mjs`, `infra/network/nginx.conf`.

### Start / reproduce

```bash
node experiments/run.mjs balancing
docker compose --profile balance up -d --build
RATE=50 SECONDS=20 node scripts/load.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the deterministic policy model, then reproduce each policy in NGINX. Use a slow upstream with SLOW_MS=200 and concurrent traffic, not only sequential curl.
2. For weighted routing, give one upstream twice the weight. For least_conn, hold connections open on the slow node. For ip_hash, use several isolated client source addresses.
3. Stop an upstream mid-test; record selection before and after failure detection. Contrast stickiness with shared durable state.

**Inject this failure:** A sticky session points at a dead node, and the remaining nodes cannot see a process-local cart.

**Completion gate:** Per-instance request counts, active requests and failure counts explain the policy choice; IP affinity is never presented as durable state replication.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-01-PROXY"></a>
## 01-PROXY - L4 versus L7, reverse/forward proxies and gateway policies

**Track:** Build extension. **Source topics:** L4; L7; reverse proxy; forward proxy; NGINX; HAProxy; Envoy.

**Files to inspect or extend:** `infra/network/nginx.conf`, `src/algorithms/load-balancer.mjs`.

### Start / reproduce

```bash
node scripts/smoke.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Route the same API through a TCP-mode HAProxy listener and an HTTP-mode listener. In HTTP mode add path-based routing and a request-size bound; show why encrypted passthrough cannot inspect the path without termination.
2. Use NGINX as the reverse proxy and a separate allowlisted forward proxy for a local client; demonstrate who chooses the upstream in each case.
3. Repeat a routing contract in Envoy or Caddy: health decision, timeout, forwarded identity, retry policy and drain behavior. Keep exact config versions in evidence.

**Inject this failure:** Configure a deliberately overbroad retry on a POST in an isolated branch and show why the idempotency boundary matters.

**Completion gate:** Routing, TLS termination and application authorization remain distinct; the gateway cannot silently turn an ambiguous write into a second business effect.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-01-POLL"></a>
## 01-POLL - Short polling, long polling and SSE

**Track:** Build extension. **Source topics:** short polling; long polling; SSE; reconnect cursors.

**Files to inspect or extend:** `src/realtime.mjs`.

### Start / reproduce

```bash
npm install
node src/realtime.mjs
# In another terminal:
curl -N -H "Authorization: Bearer lab-alice" http://127.0.0.1:3010/events
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Build a seat-count page using a two-second GET loop; count empty responses and update delay.
2. Add long polling with a 20-second bounded wait, returning immediately on a new version. On timeout, reconnect with jitter.
3. Run the SSE reference with Last-Event-ID, heartbeats and bounded history. Move history into a tenant-scoped durable stream for restart-safe replay; disable proxy buffering.

**Inject this failure:** Disconnect after three updates, reconnect with an old cursor, then restart the server. When history is unavailable, send an explicit resync instruction.

**Completion gate:** Reconnect fills a gap or explicitly requests a snapshot; no silent loss is mistaken for an up-to-date seat map.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-01-WS"></a>
## 01-WS - WebSockets, heartbeat, room authorization and slow consumers

**Track:** Build extension. **Source topics:** WebSocket; full-duplex; heartbeats; presence; fan-out.

**Files to inspect or extend:** `src/realtime.mjs`.

### Start / reproduce

```bash
node src/realtime.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Connect two Node or command-line WS clients with fixture Authorization headers to realtime.mjs. Do not put bearer tokens in URL query logs.
2. Add durable room messages with client_message_id and per-room sequence. Authorize every subscription and send, then broadcast committed messages across replicas.
3. Implement ping/pong expiry, bounded send buffers, reconnect history and a distinct TTL-based presence record. A browser client should use an authorized cookie or short-lived handshake ticket rather than unsupported custom WS headers.

**Inject this failure:** Pause one reader, revoke room membership and reconnect on another API replica after losing a broadcast.

**Completion gate:** Slow clients are disconnected without unbounded buffers; revoked users cannot submit accepted new messages; reconnect uses persisted history.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-01-GRPC"></a>
## 01-GRPC - All four gRPC interaction shapes

**Track:** Build extension. **Source topics:** REST; gRPC; Protocol Buffers; unary; server streaming; client streaming; bidirectional streaming.

**Files to inspect or extend:** `src/catalog.proto`, `src/grpc-server.mjs`.

### Start / reproduce

```bash
node src/grpc-server.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the supplied Catalog protobuf service. Generate/load the same contract on a client, supplying fixture metadata and a finite deadline.
2. Call GetEvent, ListEvents, CountEvents and WatchRequests. Compare JSON/REST payload bytes with protobuf for equivalent data.
3. Add TLS, stream backpressure and request-scoped authorization. For cancellation, stop work rather than continuing to compute an unused response.

**Inject this failure:** Cancel mid-stream, exceed the stream item limit, request another tenant event, and remove required authorization metadata.

**Completion gate:** The four shapes have distinct tests; tenant isolation, ordering and bounded resource usage survive cancellation.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-01-GRAPHQL"></a>
## 01-GRAPHQL - GraphQL queries, N+1, batching and query cost

**Track:** Build extension. **Source topics:** GraphQL; REST vs GraphQL; N+1; DataLoader; overfetching.

**Files to inspect or extend:** `src/graphql-server.mjs`, `docs/openapi.yaml`.

### Start / reproduce

```bash
node src/graphql-server.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the GraphQL reference and query the same catalog as REST. Add organizer and instructor fields with a deliberately naive resolver.
2. Record database calls for 50 events, then replace the N+1 lookup with a request-scoped batch loader preserving input-key order.
3. Add AST depth/field count limits, a cost budget, allowed pagination bounds and tenant-aware field policies. Never share a user-sensitive loader cache globally.

**Inject this failure:** Send a deeply nested query and reuse a cached organizer result across tenants in a failing test branch.

**Completion gate:** The optimized response preserves authorization while reducing database round trips; rejected expensive queries consume bounded resources.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-01-EDGE"></a>
## 01-EDGE - CDN pull/push/hybrid, origin shielding and edge compute

**Track:** Build extension. **Source topics:** CDN; pull; push; hybrid; origin shield; dynamic caching; edge compute; Geo routing; ESI; cache poisoning.

**Files to inspect or extend:** `infra/network/edge.conf`, `src/storage/chunks.mjs`.

### Start / reproduce

```bash
# Add the public immutable asset endpoint, then use infra/network/edge.conf.
node --test --test-name-pattern="chunk|storage" test/storage.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Add an immutable public /assets/<sha256> endpoint. Apply edge.conf to a second proxy and confirm HIT/MISS behavior; authenticated/private routes must bypass shared caching.
2. For pull, populate on a miss. For push, prefetch a manifest of asset URLs. For hybrid, prewarm popular assets and pull the long tail. Add a shield proxy to collapse cross-edge origin misses.
3. Implement a small region-specific edge response and an ESI-like page assembly exercise using only public fragments. Vary all content-affecting headers safely; strip untrusted host overrides before cache-key construction.

**Inject this failure:** Stop the origin after warming one asset, mutate the asset using a new hash URL, and attempt to cache a personalized response under another user's key.

**Completion gate:** Immutable content remains correct, private data does not leak, and the local topology is described as a CDN model, not a worldwide deployment.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-02"></a>
# 02. Scalability

**Source chapter:** [02_Scalability.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/02_Scalability.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Core quickstart plus a saved baseline. Use the dedicated Kubernetes/cloud/sharding playbooks when indicated.


<a id="lab-02-BASELINE"></a>
## 02-BASELINE - Find the bottleneck before choosing a strategy

**Track:** Run reference + container. **Source topics:** throughput; capacity; load tests; open workloads; hotspots; headroom.

**Files to inspect or extend:** `scripts/load.mjs`, `scripts/race.mjs`.

### Start / reproduce

```bash
node scripts/smoke.mjs
node scripts/race.mjs
RATE=50 SECONDS=30 OUT=evidence/baseline-50.json node scripts/load.mjs
docker stats --no-stream
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Warm the main stack, create a repeatable dataset and choose an explicit read/write mix. Capture a no-load correctness baseline with smoke and race tests.
2. Run the same arrival-rate workload at 20, 50, 100 and 200 requests/s, three trials each. Record completed throughput, p50/p95/p99, HTTP outcomes, CPU, RSS, pool waits and dropped generator work.
3. Repeat cold-cache, warm-cache, uniform tenant, hot-tenant and one-seat contention cases. Keep generator capacity and host resource budget visible.

**Inject this failure:** Increase offered traffic above measured service capacity. Distinguish generator saturation from server rejection.

**Completion gate:** A bottleneck is supported by measurements and a falsifiable next experiment, not an assumed technology limit.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-VERTICAL"></a>
## 02-VERTICAL - Scale CPU, RAM and process parallelism independently

**Track:** Container recipe. **Source topics:** vertical scaling; scale up; scale down; CPU; memory; multi-core; downtime; cost ceiling.

**Files to inspect or extend:** `compose.yaml`, `src/server.mjs`, `src/http.mjs`.

### Start / reproduce

```bash
API_CPUS=0.25 API_MEMORY=128m docker compose up -d --build --scale api=1
PATHNAME="/lab/cpu?n=20000" RATE=10 SECONDS=20 node scripts/load.mjs
API_CPUS=2 API_MEMORY=256m docker compose up -d --force-recreate --scale api=1
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run exactly one API replica with API_CPUS=0.25 and API_MEMORY=128m. Benchmark /lab/cpu?n=20000 and the database-backed catalog separately.
2. Increase CPU to 1 and 2 while holding memory and data constant; then vary memory independently. Repeat with WORKERS=1 and WORKERS=2: one JavaScript event loop does not automatically use all extra CPU cores.
3. Record restart downtime, RSS, throttling and connection pools per worker. Compare equal total CPU budgets before attributing an improvement to vertical versus horizontal scaling.

**Inject this failure:** Use an intentionally small memory limit with a bounded synthetic working set; observe OOM/restart. Never run a host-level memory exhaustion program.

**Completion gate:** The evidence distinguishes CPU allocation, actual parallel work and memory capacity; a larger instance is not automatically reported as faster.

**Additional constraint:** Detailed commands and comparison table are in playbooks/SCALING.md.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-HORIZONTAL"></a>
## 02-HORIZONTAL - Scale out stateless application instances

**Track:** Container recipe. **Source topics:** horizontal scaling; stateless services; stateful services; shared sessions; service discovery.

**Files to inspect or extend:** `compose.yaml`, `src/server.mjs`, `infra/network/nginx.conf`.

### Start / reproduce

```bash
docker compose up -d --build --scale api=4
RATE=100 SECONDS=30 node scripts/load.mjs
node scripts/race.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Hold per-instance CPU constant and scale the api service from one to two to four replicas. Confirm NGINX resolves the changing Docker service addresses.
2. Track database connection capacity as replicas times workers times pool size. Set the same total connection budget for a fair experiment.
3. Create a naive process-local cart, observe inconsistent results across replicas, then move it to a tenant/owner-scoped shared repository with a stated durability requirement.

**Inject this failure:** Kill one replica during read and booking traffic. Repeat with sticky sessions to show they do not preserve a dead process's state.

**Completion gate:** Any surviving replica can serve authoritative data; scale-out measurements include the shared database and gateway bottlenecks.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-HPA"></a>
## 02-HPA - Reactive autoscaling with Kubernetes HPA

**Track:** Cluster recipe. **Source topics:** reactive autoscaling; CPU utilization; requests; warmup; cooldown; HPA; readiness.

**Files to inspect or extend:** `infra/k8s/workload.yaml`, `infra/k8s/hpa.yaml`.

### Start / reproduce

```bash
kubectl apply -f infra/k8s/workload.yaml
kubectl -n hld-lab wait --for=condition=complete job/eventhub-seed --timeout=180s
kubectl apply -f infra/k8s/hpa.yaml
kubectl -n hld-lab get hpa,pods -w
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create a disposable kind cluster, load hld-eventhub:lab, and apply workload.yaml. Wait for PostgreSQL and the seed job, then apply the HPA.
2. Install a version-compatible Metrics Server and verify kubectl top pods works before judging HPA. CPU utilization is relative to the container request, not its limit.
3. Run an in-cluster CPU workload through the Service, watch current/desired replicas and stabilization, and observe pending pods when the cluster lacks spare nodes.

**Inject this failure:** Remove CPU requests or stop metrics collection in a disposable branch. Distinguish missing metrics from insufficient traffic.

**Completion gate:** HPA scales from 2 toward 8 only with usable metrics and available resources; recovery and scale-down stabilization are captured.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-SCHEDULED"></a>
## 02-SCHEDULED - Scheduled autoscaling and prewarming

**Track:** Cluster recipe. **Source topics:** scheduled autoscaling; known events; prewarming; CronJob; scheduled ASG actions.

**Files to inspect or extend:** `infra/k8s/scheduled-floor.yaml`, `scripts/patch-hpa.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs autoscaling
kubectl apply -f infra/k8s/scheduled-floor.yaml
kubectl -n hld-lab create job --from=cronjob/eventhub-peak-floor prewarm-once
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use a predictable event-opening schedule. Compare immediate reactive scaling with capacity reserved before the spike.
2. Apply scheduled-floor.yaml, which starts suspended. Trigger a one-off Job from its CronJob to set the HPA floor to six; restore minReplicas to two after the experiment.
3. For AWS, enable the optional morning/evening scheduled actions in the isolated CloudFormation stack. Keep min/max bounds and a clear time zone.

**Inject this failure:** Run the schedule in the wrong time zone in a test configuration and measure the wasted capacity or missed spike. Do not leave a paid fleet scheduled indefinitely.

**Completion gate:** The schedule changes the HPA floor rather than racing its controller; observed ready capacity exists before traffic arrives.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-PREDICTIVE"></a>
## 02-PREDICTIVE - Predictive scaling, forecast error and control-loop safety

**Track:** Model + cloud recipe. **Source topics:** predictive autoscaling; forecasting; capacity forecasts; AWS predictive scaling; forecast-only.

**Files to inspect or extend:** `src/algorithms/autoscale.mjs`, `infra/aws/autoscaling.yaml`.

### Start / reproduce

```bash
node experiments/run.mjs autoscaling
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run reactive, known-schedule and EWMA models against the same trace. The EWMA forecaster must only see past values; a known business schedule is a separate input.
2. Fit a daily-seasonal or moving-average predictor using an earlier training period, evaluate forecast error on a later period, and convert forecast load into capacity using measured per-instance service capacity.
3. The AWS template starts in ForecastOnly with HonorMaxCapacity. Accumulate the provider-required historical metrics, inspect forecasts, and only then explicitly switch to ForecastAndScale with alarms and rollback.

**Inject this failure:** Introduce a sudden nonseasonal event, missing metrics and a popularity shift; compare underprovisioned intervals and wasted instance-minutes.

**Completion gate:** Prediction is compared with a naive baseline, does not peek into future data, and is not claimed to work from a five-minute brand-new cloud deployment.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-ASG"></a>
## 02-ASG - Cloud VM scaling, health replacement and vertical instance changes

**Track:** Cloud recipe. **Source topics:** AWS Auto Scaling Group; target tracking; instance refresh; multi-AZ; scale-in protection.

**Files to inspect or extend:** `infra/aws/autoscaling.yaml`.

### Start / reproduce

```bash
aws cloudformation validate-template --template-body file://infra/aws/autoscaling.yaml
# Deployment requires your explicit sandbox account, region and AllowedClientCidr.
# Full creation, observation and cleanup commands: playbooks/SCALING.md.
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Inspect the CloudFormation template and cost boundary. Deploy only in your sandbox account with your public IPv4 /32; it creates a static NGINX fleet, not the fixture-authenticated EventHub app.
2. Use bounded SSM CPU load on only the stack's instance IDs. Observe target-tracking scale-out, health replacement and graceful target deregistration.
3. Change InstanceType from t3.small to t3.medium, create a new launch-template version and perform an instance refresh. Changing the template alone does not resize existing instances.

**Inject this failure:** Terminate one member from the sandbox fleet and compare health-check grace time, boot time and recovery. Keep ASG maximum at four.

**Completion gate:** The exercise separates instance count from instance type; the stack is deleted and its deletion status verified when finished.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-VPA"></a>
## 02-VPA - Vertical pod recommendations and stateful scaling limits

**Track:** Cluster recipe. **Source topics:** VPA; vertical pod autoscaling; stateful workloads; resource requests; control-loop conflicts.

**Files to inspect or extend:** `infra/k8s/vpa.yaml`.

### Start / reproduce

```bash
kubectl apply -f infra/k8s/vpa.yaml
kubectl -n hld-lab describe vpa eventhub
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Install the version-compatible VPA controller and apply vpa.yaml in recommendation-only mode. Run both CPU-bound and memory-bound workloads.
2. Inspect recommended requests, then manually roll out one change and compare against the same baseline. Do not enable two autoscalers fighting over CPU utilization on the same deployment.
3. Scale the PostgreSQL StatefulSet only after replacing it with a real replication topology; show why replicas:3 by itself creates independent databases, not replicated state.

**Inject this failure:** Raise resource requests beyond node allocatable capacity and observe pending pods rather than fabricated capacity gains.

**Completion gate:** VPA recommendations, scheduling limits and actual workload throughput are separate observations.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-HASH"></a>
## 02-HASH - Modulo hash and consistent/rendezvous hashing

**Track:** Run reference. **Source topics:** hash sharding; modulo; consistent hashing; virtual nodes; rendezvous hashing.

**Files to inspect or extend:** `src/algorithms/sharding.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs sharding
node --test --test-name-pattern="ring|rendezvous|hash shard" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Route 10,000 tenant keys across A/B, then A/B/C. Compare moved-key fraction, balance and membership-order stability.
2. For modulo, compute H(key)%N. For the ring, create 128 virtual tokens per node and find the first token clockwise. For rendezvous, choose the node with the highest H(key,node).
3. Use the router only for new-tenant placement; persist the resulting route in the directory so membership changes do not silently move live data.

**Inject this failure:** Add C without copying data in a disposable naive router. The resulting false 404s demonstrate why placement is not migration.

**Completion gate:** Ring/rendezvous additions move keys to the new node rather than arbitrarily between old nodes; live tenant routes stay authoritative until migration completes.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-RANGE"></a>
## 02-RANGE - Range-based sharding and range split

**Track:** Build extension. **Source topics:** range sharding; ordered keys; range split; hot partitions.

**Files to inspect or extend:** `src/algorithms/sharding.mjs`, `infra/sharding/compose.yaml`.

### Start / reproduce

```bash
node experiments/run.mjs sharding
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Partition synthetic event IDs or booking dates into exclusive upper-bound ranges. Route boundary keys with the supplied rangeShard function.
2. Create separate physical shard tables/databases for each range, then issue a time-bounded query only to intersecting ranges.
3. Split a hot range by copying a bounded interval, fencing its writer, verifying rows and atomically publishing a new versioned range map. Include an open-ended final range.

**Inject this failure:** Use monotonic IDs so all new writes hit the last range. Then query a missing/unassigned interval and ensure it is an explicit routing failure.

**Completion gate:** Boundary keys map to exactly one authority; range queries avoid unrelated shards; a split is not performed by changing metadata alone.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-DIRECTORY"></a>
## 02-DIRECTORY - Directory-based tenant routing

**Track:** Container recipe. **Source topics:** directory sharding; tenant isolation; route lookup; fencing tokens; epochs.

**Files to inspect or extend:** `src/stores/sharded.mjs`, `src/stores/pg.mjs`, `infra/sharding/catalog.sql`.

### Start / reproduce

```bash
docker compose -f infra/sharding/compose.yaml up -d --build
docker compose -f infra/sharding/compose.yaml exec router node scripts/seed-shards.mjs
BASE_URL=http://127.0.0.1:8089 node scripts/smoke.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Launch the catalog plus three shard databases, seed owned tenants, and run the HTTP smoke/race tests through port 8089.
2. Read tenant_routes before acquiring the shard transaction. Verify the per-shard tenant_fences row with FOR SHARE and the expected epoch inside every protected transaction.
3. Keep unknown or moving routes fail-closed. Add bounded directory caching only after designing invalidation, TTL and stale-epoch rejection.

**Inject this failure:** Pause a request after directory lookup, migrate its tenant, then resume it with the old epoch. Also stop the directory database.

**Completion gate:** A stale router cannot commit to the former owner; directory loss never invokes an unsafe hash fallback.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-GEO"></a>
## 02-GEO - Geographic/home-region sharding

**Track:** Build extension. **Source topics:** geo sharding; data residency; home writer; regional routing.

**Files to inspect or extend:** `src/algorithms/sharding.mjs`, `src/stores/sharded.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs sharding
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Assign each synthetic tenant an explicit home region, mapped to a physical shard. Route writes to its home authority, not the nearest available replica.
2. Create a region-specific read projection and add artificial propagation delay. Distinguish latency-optimized reads from operations requiring current permissions or inventory.
3. Migrate a tenant's home region only through the fenced migration protocol; include bytes, metadata, keys and retention policy in the move plan.

**Inject this failure:** Lose the home-region connection while a remote replica is reachable. Compare fail-closed writes with explicitly eventual public catalog reads.

**Completion gate:** No two regions sell the same seat independently; residency choices and simulated versus real geographic effects are documented.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-MIGRATE"></a>
## 02-MIGRATE - Resharding with a resumable fenced tenant migration

**Track:** Container recipe. **Source topics:** resharding; rebalancing; snapshot; checksums; cutover; rollback; CDC extension.

**Files to inspect or extend:** `scripts/migrate-tenant.mjs`.

### Start / reproduce

```bash
docker compose -f infra/sharding/compose.yaml exec -e FAIL_AFTER_STAGE=copied router node scripts/migrate-tenant.mjs org-a c
docker compose -f infra/sharding/compose.yaml exec router node scripts/migrate-tenant.mjs org-a c
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create bookings for org-a on shard A, then migrate it to C with the provided migration controller. The reference intentionally uses a bounded tenant outage, not zero-downtime CDC.
2. The controller marks moving, fences source transactions, copies all tenant-owned tables in dependency order, verifies counts/hashes, enables a new target epoch and performs a directory compare-and-set cutover.
3. Inject failures after fenced, copied and target-enabled stages; rerun the same command. Retain the old copy fenced. A post-cutover rollback is a reverse migration, not a blind route flip.

**Inject this failure:** Kill the controller at each persisted checkpoint and issue concurrent old-epoch writes. Disable independent publishers/workers during the migration reference.

**Completion gate:** Exactly one shard accepts the active epoch; acknowledged pre-fence bookings survive; retries converge to one target and all six table checksums match.

**Additional constraint:** Online snapshot+CDC and tenant-write fencing are specified separately in playbooks/SHARDING.md.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-HOTKEY"></a>
## 02-HOTKEY - Hot tenants, hot keys and load shedding

**Track:** Build extension. **Source topics:** hotspot mitigation; hot key replication; tenant isolation; admission control; bounded queues.

**Files to inspect or extend:** `src/algorithms/reliability.mjs`, `src/algorithms/cache.mjs`.

### Start / reproduce

```bash
node scripts/race.mjs
node --test --test-name-pattern="semaphore|singleflight" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Send 90% of reads to one event, then send competing booking writes to one seat. Show that cache replication helps hot reads but cannot remove the inventory serialization requirement.
2. Split a large tenant by a secondary key only after identifying transactions that must remain co-located. Add per-tenant bulkheads and a dedicated hot-key L1 cache for stale-tolerant reads.
3. Implement a bounded admission queue and return quota 429 separately from overload 503; use retry budgets and jitter.

**Inject this failure:** Remove cache nodes during the hot-key run. Avoid allowing the miss storm to consume the entire database pool.

**Completion gate:** Admitted writes preserve no-oversell, rejection is bounded and explicit, and unaffected tenants retain a measurable share of capacity.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-READS"></a>
## 02-READS - Read replicas, partitioning, denormalization and pools

**Track:** Container recipe. **Source topics:** read scaling; read replicas; partitioning; denormalization; connection pooling.

**Files to inspect or extend:** `src/stores/pg.mjs`, `db/partition_lab.sql`, `infra/replication/compose.yaml`.

### Start / reproduce

```bash
sh scripts/replication.sh up
docker compose exec -T postgres psql -U lab -d eventhub < db/partition_lab.sql
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Route catalog reads to a physical standby while keeping booking decisions on the primary. Use minVersion for read-your-writes fallback.
2. Partition telemetry by month and add an organizer summary projection. Measure query pruning and materialized-view freshness separately from replication.
3. Vary pool sizes with a fixed total connection budget; report connection-acquisition wait and lock wait separately from execution time.

**Inject this failure:** Pause replica replay, hold a long transaction and refresh the projection late.

**Completion gate:** The read contract explicitly describes staleness and primary fallback; more connections are not assumed to mean more throughput.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-ASYNC"></a>
## 02-ASYNC - Scale background work independently

**Track:** Container recipe. **Source topics:** asynchronous processing; worker pools; queue depth; backpressure; microservices.

**Files to inspect or extend:** `src/async/publisher.mjs`, `src/async/worker.mjs`, `compose.yaml`.

### Start / reproduce

```bash
docker compose --profile async up -d --build --scale worker=4
node scripts/smoke.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Start the outbox publisher and notification worker. Scale workers from one to four while holding the database pool budget fixed.
2. Measure oldest-message age as well as queue length; configure prefetch/concurrency so a slow downstream cannot create unlimited in-flight work.
3. Extract the worker as a separately deployable service with its own health, metrics and contract. Do not split the inventory transaction merely to create more microservices.

**Inject this failure:** Stop RabbitMQ while bookings continue, then restart it and watch the outbox drain without duplicate notification rows.

**Completion gate:** Worker capacity is tuned against downstream limits and per-event effects remain duplicate-safe.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-02-DECIDE"></a>
## 02-DECIDE - Scaling strategy decision and anti-pattern review

**Track:** Design + experiment. **Source topics:** scaling decision framework; CDN; cache; microservices; cost; tradeoffs; anti-patterns.

**Files to inspect or extend:** `src/algorithms/autoscale.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs estimation
node experiments/run.mjs autoscaling
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create separate read-heavy, write-heavy, predictable-spike and unpredictable-spike workload profiles.
2. Select the first intervention using the measured bottleneck: CPU/RAM, replicas, cache/CDN, read replicas, partitioning, sharding, asynchronous work or admission control.
3. For each rejected intervention, state what evidence would make it appropriate later. Include an intentionally overbuilt microservices/sharding design and simplify it.

**Inject this failure:** Increase one multiplier by ten: tenants, payload bytes, retries, hot-key concentration or worker latency.

**Completion gate:** The decision memo includes before/after evidence, operational cost categories, rollback and an invariant that the optimization may not weaken.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-03"></a>
# 03. Consistency & CAP Theorem

**Source chapter:** [03_Consistency_and_CAP_Theorem.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/03_Consistency_and_CAP_Theorem.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Core database track for transactional proofs. Start Cassandra or etcd separately for the corresponding cluster labs.


<a id="lab-03-ISOLATION"></a>
## 03-ISOLATION - Lost updates, write skew and isolation levels

**Track:** Container recipe. **Source topics:** Read Committed; Repeatable Read; Serializable; lost update; write skew; locking; MVCC.

**Files to inspect or extend:** `src/stores/pg.mjs`, `db/seat_holds.sql`.

### Start / reproduce

```bash
node scripts/race.mjs
docker compose exec -T postgres psql -U lab -d eventhub < db/invariants.sql
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use two psql sessions to read available=2 and both write the client-computed value 1. Save the interleaving and the lost decrement.
2. Compare guarded arithmetic UPDATE, SELECT FOR UPDATE, optimistic version comparison and SERIALIZABLE with a bounded whole-transaction retry on SQLSTATE 40001.
3. Create a two-row on-call roster invariant and demonstrate write skew under snapshot-style isolation; show the serializable alternative without calling every transaction level equivalent.

**Inject this failure:** Hold locks in opposite orders to reproduce a deadlock in a disposable table, then enforce sorted lock order.

**Completion gate:** The no-oversell and multi-row invariants are tested independently; retry logic handles aborted whole transactions, not a single final statement.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-03-MODELS"></a>
## 03-MODELS - Strong, sequential, session and eventual consistency

**Track:** Run reference. **Source topics:** linearizability; sequential consistency; eventual consistency; session consistency; tunable consistency.

**Files to inspect or extend:** `src/algorithms/consistency.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs consistency
node --test --test-name-pattern="history checker" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Record invocation/response times for a small register history and run the supplied exhaustive linearizability checker.
2. Construct a history that preserves each client's program order but violates real-time order; explain why sequential consistency and linearizability differ.
3. Add delayed read projections to EventHub and assign contracts per endpoint: authoritative booking, session-aware confirmation and eventually updated analytics.

**Inject this failure:** Deliver a stale read after an acknowledged write from another client; compare with overlapping operations that can be legally reordered.

**Completion gate:** The explanation names the observation guaranteed by each model rather than labeling an entire database or application CP/AP.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-03-SESSION"></a>
## 03-SESSION - Read-your-writes, monotonic reads/writes and causal dependencies

**Track:** Build extension. **Source topics:** read-your-writes; monotonic reads; monotonic writes; causal consistency; version vectors.

**Files to inspect or extend:** `src/stores/pg.mjs`, `src/algorithms/consistency.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="future minimumVersion|vector clocks" test/*.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Return eventVersion from a successful booking, carry it in the next GET minVersion, and read the primary if the replica cannot meet it.
2. Track the highest observed version per client/event so a later read cannot regress below that minimum. Serialize writes from a client or include a predecessor version and reject an out-of-order mutation.
3. For causal comments, carry the referenced post's version; hold or fetch the dependency before displaying the comment. Use vector clocks only where independent writers require partial-order tracking.

**Inject this failure:** Route one session between a current and lagging replica and deliver a reply before its parent post.

**Completion gate:** The session never sees a lower promised version; delayed dependencies are explicit rather than silently missing.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-03-QUORUM"></a>
## 03-QUORUM - Leaderless replication and tunable quorums

**Track:** Container recipe. **Source topics:** leaderless replication; Cassandra; N R W; quorums; read repair; hinted handoff.

**Files to inspect or extend:** `infra/cassandra.yaml`, `db/cassandra.cql`.

### Start / reproduce

```bash
docker compose -f infra/cassandra.yaml up -d
docker compose -f infra/cassandra.yaml exec -T c1 cqlsh < db/cassandra.cql
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Launch three Cassandra nodes with replication factor three. Run the same workload at ONE, QUORUM and ALL and record acknowledged writes and observed reads.
2. Stop one node, then two, with a finite client deadline. Inspect the difference between availability and the number of required acknowledgments.
3. Restore nodes, inspect convergence and repair, and compare an ordinary last-write-wins row with an IF NOT EXISTS lightweight transaction for a unique alias.

**Inject this failure:** Inject conflicting explicit write timestamps in the lab to expose LWW clock assumptions; do not use this counter as ticket inventory.

**Completion gate:** R+W>N is discussed under its assumptions, not sold as a universal linearizability proof; LWT and ordinary writes are measured separately.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-03-LEADERS"></a>
## 03-LEADERS - Leader-follower, multi-leader and replication modes

**Track:** Build extension. **Source topics:** leader-follower; multi-leader; sync replication; async replication; logical replication.

**Files to inspect or extend:** `infra/replication/compose.yaml`, `src/algorithms/consistency.mjs`.

### Start / reproduce

```bash
sh scripts/replication.sh up
node experiments/run.mjs consistency
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use the physical PostgreSQL stack for single-leader replication and separately create two writable logical-origin tables for a multi-leader experiment.
2. Keep origin node, globally unique event ID and per-origin sequence. Replicate immutable events to a merge consumer rather than assuming two ordinary PostgreSQL primaries automatically reconcile conflicts.
3. Compare synchronous acknowledgment latency with asynchronous visibility and demonstrate a conflicting field update requiring an explicit merge or manual resolution.

**Inject this failure:** Partition the two writers, update the same title differently and reconnect; preserve both input events before resolving.

**Completion gate:** The chosen conflict policy is explicit and suitable only for its data type; nonmergeable seat ownership remains single-writer.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-03-RAFT"></a>
## 03-RAFT - Consensus, elections and minority behavior with etcd

**Track:** Container recipe. **Source topics:** Raft; etcd; majority; leader election; network partitions; split-brain.

**Files to inspect or extend:** `infra/consensus.yaml`.

### Start / reproduce

```bash
docker compose -f infra/consensus.yaml up -d
docker compose -f infra/consensus.yaml exec e1 etcdctl --endpoints=http://e1:2379,http://e2:2379,http://e3:2379 endpoint status -w table
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Launch three etcd members and record endpoint status and leader identity. Commit a key, stop one member and commit again.
2. Disconnect one member from the Compose network without stopping its process. Attempt a timed write against its loopback endpoint, then write through the majority side.
3. Restore connectivity, compare values and log election/recovery duration. Include a complete two-member outage and distinguish unavailable writes from stale local reads.

**Inject this failure:** Run a minority-side write while the majority commits another value. Never create a second independent cluster over the same logical data.

**Completion gate:** The minority cannot commit conflicting state, the majority recovers with one authority, and the test names the exact read/write operation used.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-03-PAXOS"></a>
## 03-PAXOS - Paxos promises, accepted values and coordination services

**Track:** Run model. **Source topics:** Paxos; prepare; accept; ZooKeeper; coordination; leader leases.

**Files to inspect or extend:** `src/algorithms/paxos.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs paxos
node --test --test-name-pattern="Paxos" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the single-decree Paxos message model. Let a proposer choose X using two acceptors, then let a higher-ballot proposer request Y from an overlapping majority.
2. Observe that the second proposer must adopt the highest accepted value it learns. Partition prepare/accept recipient sets to reproduce minority failure.
3. For a service implementation, use etcd or ZooKeeper for session/lease-backed coordination, but require monotonically increasing fencing tokens at the protected resource. Durable acceptor state and a replicated log are additional work beyond this model.

**Inject this failure:** Pretend an acceptor forgets its promise after restart in a failing branch; demonstrate why volatile state is not a safe consensus deployment.

**Completion gate:** Chosen-value safety is tested under the model, and the workbook never presents a synchronous in-memory simulator as production Paxos.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-03-MERGE"></a>
## 03-MERGE - Conflict resolution, CRDTs and anti-entropy

**Track:** Build extension. **Source topics:** last-write-wins; vector clocks; G-counter; PN-counter; OR-set; application merge; anti-entropy.

**Files to inspect or extend:** `src/algorithms/consistency.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs consistency
node --test --test-name-pattern="counter|observed-remove|LWW" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Partition two counters, update both, merge in both orders and replay the same state repeatedly. Repeat with positive/negative counters and observed-remove sets.
2. Compare LWW field resolution with vector-clock detection of concurrent edits and a user-visible conflict copy. Preserve tombstones long enough for returning replicas.
3. Build a Merkle-tree or range-hash comparison over sorted records and transfer only mismatched ranges for repair; keep authoritative deletion metadata in the comparison.

**Inject this failure:** A clock-skewed old update wins under LWW, and premature tombstone deletion resurrects a removed item.

**Completion gate:** CRDT merges converge for the tested type; application merge preserves both meaningful edits; convergence is not confused with the no-oversell invariant.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-03-CAP"></a>
## 03-CAP - CAP, PACELC and operation-specific tradeoffs

**Track:** Design + experiment. **Source topics:** CAP; PACELC; partition tolerance; latency-consistency; hybrid consistency.

**Files to inspect or extend:** `infra/consensus.yaml`, `src/algorithms/consistency.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs consistency
node experiments/run.mjs paxos
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use etcd for a consistency-first key and the mergeable counter for availability-first independent updates during a partition.
2. For the no-partition case, add a controlled remote-ack delay to synchronous writes and compare response latency against asynchronously propagated views.
3. Write a per-operation matrix for inventory, chat presence, profile edits, billing ledger and analytics; assign a consistency/read contract and partition response to each.

**Inject this failure:** Keep a process alive while severing the network; compare that with a clean process crash.

**Completion gate:** The matrix distinguishes the CAP partition choice from PACELC's ordinary latency/consistency choice and avoids universal CP/AP product labels.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-03-DISTRIBUTED-TX"></a>
## 03-DISTRIBUTED-TX - Two-phase commit versus sagas and CQRS boundaries

**Track:** Build extension. **Source topics:** 2PC; prepared transactions; saga orchestration; choreography; CQRS; event sourcing.

**Files to inspect or extend:** `src/algorithms/seat-holds.mjs`, `src/async/publisher.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs booking
# Full participant/coordinator and saga recipe: playbooks/MESSAGING.md.
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. On two isolated PostgreSQL participants, enable max_prepared_transactions and prepare matching local updates with stable transaction IDs. Persist the coordinator decision before issuing COMMIT PREPARED.
2. Stop the coordinator after both participants prepare; inspect pg_prepared_xacts and blocked conflicting work. Resolve only using the durable coordinator decision, never by guessing.
3. Build the equivalent reservation/payment saga with persisted steps and idempotent compensation, then create a CQRS projection from committed events. Distinguish using an outbox from making the event log the source of truth.

**Inject this failure:** Crash after one participant commits, or deliver a late successful payment after the saga began compensation.

**Completion gate:** 2PC blocking and saga intermediate states are measured; neither is labeled universally correct for every cross-service operation.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-04"></a>
# 04. Availability

**Source chapter:** [04_Availability.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/04_Availability.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Core quickstart; physical replication for failover. Record an SLI and the exact injected fault before testing.


<a id="lab-04-SLO"></a>
## 04-SLO - Availability SLIs, SLOs and error budgets

**Track:** Build extension. **Source topics:** availability; SLI; SLO; SLA; error budgets; MTBF; MTTR.

**Files to inspect or extend:** `scripts/load.mjs`.

### Start / reproduce

```bash
RATE=10 SECONDS=60 OUT=evidence/availability.json node scripts/load.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Define eligible attempts and successful business outcomes before computing availability. A correct sold-out result is not a database outage.
2. Probe reads and fresh synthetic booking attempts independently. Record exact numerator, denominator, failure onset and restoration times.
3. Calculate a proposed request-based budget and a separate time-based outage budget; keep these teaching targets distinct from contractual SLAs.

**Inject this failure:** Kill an API, the database and the sole gateway in separate runs, restoring the baseline between failures.

**Completion gate:** Availability reports separate business rejection, infrastructure errors, monitoring gaps and user-visible downtime.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-04-REDUNDANCY"></a>
## 04-REDUNDANCY - Active-passive versus active-active serving

**Track:** Build extension. **Source topics:** redundancy; active-passive; active-active; SPOF; multi-AZ.

**Files to inspect or extend:** `compose.yaml`, `infra/aws/autoscaling.yaml`.

### Start / reproduce

```bash
docker compose up -d --scale api=2
docker compose stop gateway
docker compose start gateway
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run two API replicas active-active, then configure one standby receiving no normal traffic and switch traffic after a health failure.
2. Create a component dependency graph showing the single gateway, primary database, identity provider and broker. Redundant API replicas alone do not remove these failure points.
3. Repeat the topology across separate machines or availability zones for the advanced track, with anti-affinity and independent storage/network dependencies.

**Inject this failure:** Stop the only gateway while both APIs remain healthy; then simulate a complete host loss rather than only a process kill.

**Completion gate:** The runbook names residual single points of failure and distinguishes same-host container redundancy from independent failure domains.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-04-PROBES"></a>
## 04-PROBES - Liveness, readiness, startup and graceful drain

**Track:** Container recipe. **Source topics:** health checks; liveness; readiness; startup probes; draining; optional dependencies.

**Files to inspect or extend:** `src/http.mjs`, `infra/k8s/workload.yaml`.

### Start / reproduce

```bash
curl http://127.0.0.1:8080/health/ready
docker compose stop redis
node scripts/smoke.mjs
docker compose start redis
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Check the core endpoints: liveness tests the serving process; readiness checks the required database. Redis is optional and must not cause restart storms.
2. Stop Redis and confirm database fallback. Stop PostgreSQL and ensure writes fail rather than fabricate success.
3. During rollout, remove a pod from routing, stop admitting new work and let bounded in-flight operations finish before shutdown. Explain that Compose dependency health gates startup, not continuous failover.

**Inject this failure:** Introduce a slow-starting process and a database outage. Demonstrate the effect of confusing liveness with downstream readiness.

**Completion gate:** An optional dependency failure degrades only optional work; a required dependency outage is visible without cascading process restarts.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-04-FAILOVER"></a>
## 04-FAILOVER - Physical replica promotion with writer fencing

**Track:** Container recipe. **Source topics:** failover; primary promotion; replica lag; split-brain prevention; RPO; RTO.

**Files to inspect or extend:** `scripts/replication.sh`, `infra/replication/compose.yaml`.

### Start / reproduce

```bash
sh scripts/replication.sh up
sh scripts/replication.sh inspect
CONFIRM_LAB_FAILOVER=yes sh scripts/replication.sh promote
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create several acknowledged probe records and compare primary flush LSN with both replicas. Run both asynchronous and remote_apply modes.
2. Fence the old primary first, choose the most suitable candidate using observed replay/flush positions and promote it. Repoint all writer clients explicitly.
3. Reconcile the client acknowledgment history against the promoted database; rebuild or rewind the old primary before rejoining it as a standby.

**Inject this failure:** Pause the candidate before writing, then force failover. Record any asynchronous loss instead of assuming replicas imply zero RPO.

**Completion gate:** Only one writer is reachable, recovery time is measured and the acknowledgment-loss boundary is stated for the chosen candidate.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-04-ROLLOUT"></a>
## 04-ROLLOUT - Rolling, blue-green and canary deployments

**Track:** Build extension. **Source topics:** rolling deployment; blue-green; canary; expand-contract; rollback.

**Files to inspect or extend:** `infra/k8s/workload.yaml`, `docs/openapi.yaml`.

### Start / reproduce

```bash
kubectl -n hld-lab rollout status deployment/eventhub
kubectl -n hld-lab rollout undo deployment/eventhub
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Add an optional nullable database column, deploy readers tolerant of both versions, backfill in bounded batches, then deploy writers.
2. Roll one API at a time with readiness and drain. For blue-green, provision the second version against the compatible schema and switch a gateway upstream. For canary, route a small, explicit cohort or weight to the new version.
3. Keep code rollback independent from destructive schema rollback; remove old fields only after all clients and workers have migrated.

**Inject this failure:** Deploy a failing canary and test the rollback threshold. Rename a column too early in a disposable branch to expose the compatibility failure.

**Completion gate:** Continuous synthetic traffic preserves inventory and old-client contracts throughout the compatible deployment; rollback uses observed errors rather than intuition.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-04-DEGRADE"></a>
## 04-DEGRADE - Graceful degradation, feature flags and fail-safe policy

**Track:** Build extension. **Source topics:** graceful degradation; fallbacks; feature flags; retry; timeouts; circuit breakers.

**Files to inspect or extend:** `src/algorithms/reliability.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs reliability
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Put recommendations and notification delivery behind separate dependency policies. Return a labeled default recommendation list when its provider fails.
2. Add runtime feature flags with a safe default and an audited kill switch. Keep booking success dependent on the authoritative commit, not a cache or fallback.
3. Combine a deadline, one retry layer, a breaker and an admission bound; test their interaction instead of treating each mechanism as independent.

**Inject this failure:** Fail optional recommendations, then fail the booking database. The expected response must differ.

**Completion gate:** Optional features degrade without masking a failed sale; flags and cached fallback data never bypass current authorization.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-04-REGIONS"></a>
## 04-REGIONS - Multi-region failover and global health routing

**Track:** Regional extension. **Source topics:** multi-region; global load balancing; GeoDNS; active-active; active-passive; disaster recovery.

**Files to inspect or extend:** `infra/aws/autoscaling.yaml`, `infra/replication/compose.yaml`.

### Start / reproduce

```bash
node experiments/run.mjs consistency
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Draw a home-writer topology and a replicated regional read path, including identity, storage bytes, metadata, queues and key management.
2. Model remote latency and failover locally first. For real regions, deploy independently scoped stacks and record DNS/connection TTL, replication lag, client retry and key availability.
3. Practice failback only after reconciling divergent or missing data. Use active-active writes only for explicitly conflict-safe data or partitioned ownership.

**Inject this failure:** Lose an entire region while a client retains an old DNS answer and an in-flight write has an unknown outcome.

**Completion gate:** The failover plan includes fencing, acknowledgment reconciliation, region-specific dependencies and a tested failback path.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-05"></a>
# 05. Database

**Source chapter:** [05_Database.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/05_Database.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** A disposable PostgreSQL database, synthetic fixtures and backup before schema experiments. Optional database types run separately.


<a id="lab-05-MODEL"></a>
## 05-MODEL - Relational schema, constraints and normalized relationships

**Track:** Container recipe. **Source topics:** SQL; relational modeling; primary/foreign keys; ACID; normalization; joins.

**Files to inspect or extend:** `db/001_schema.sql`, `src/stores/pg.mjs`.

### Start / reproduce

```bash
docker compose exec -T postgres psql -U lab -d eventhub < db/invariants.sql
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Inspect the core schema and list the tenant, owner, inventory and idempotency invariants. Add organizations/users as parent tables and event_instructors as a join table.
2. Implement foreign keys, uniqueness and check constraints before adding caches. Compare application validation with database enforcement.
3. Add optional JSONB metadata only for flexible display attributes; retain relational columns for constrained business state.

**Inject this failure:** Attempt an orphaned booking, negative inventory, duplicate idempotency key and cross-tenant event reference directly through SQL.

**Completion gate:** The database rejects structural invariant violations even when application checks are bypassed; UUID knowledge does not grant access.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-05-INDEX"></a>
## 05-INDEX - B-tree, composite, covering, hash and secondary indexes

**Track:** Container recipe. **Source topics:** B-tree; hash index; composite index; covering index; secondary index; query plans.

**Files to inspect or extend:** `db/query_lab.sql`.

### Start / reproduce

```bash
docker compose exec -T postgres psql -U lab -d eventhub < db/query_lab.sql
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Load the million-row query fixture. Capture EXPLAIN ANALYZE BUFFERS for tenant/user/time filtering before and after the composite covering index.
2. Add an equality-only hash index in a disposable table and compare with B-tree equality/range/order workloads. Test a secondary lookup that requires fetching the base row.
3. Measure index bytes and insert/update throughput; include a broad scan where a sequential plan is rational.

**Inject this failure:** Use a filter missing the leading index columns and an ORDER BY incompatible with the index. Do not force index scans just to make a screenshot.

**Completion gate:** The chosen index order is justified by the access pattern; write amplification and cache working set are recorded alongside read latency.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-05-LSM"></a>
## 05-LSM - LSM trees, memtables, SSTables and compaction

**Track:** Build extension. **Source topics:** LSM tree; memtable; SSTable; WAL; compaction; read amplification; write amplification.

**Files to inspect or extend:** `src/storage/wal.mjs`, `src/algorithms/bloom.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs storage
node experiments/run.mjs bloom
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Start from WalStore: every accepted mutation is appended and optionally synchronized before entering the map.
2. Add a bounded memtable flush to an immutable sorted SSTable with a sparse key index and Bloom filter. Read newest memtable/SSTables first; tombstones must hide older values.
3. Compact two sorted tables by key/version into a new immutable file, fsync it, atomically publish a manifest and only later reclaim old files. Retain WAL until the checkpoint/manifest proves replay safety.

**Inject this failure:** Crash after writing a compacted table but before publishing its manifest; restart without losing the old referenced tables.

**Completion gate:** Recovery returns the latest value, tombstones are not dropped prematurely, and measured reads/writes include compaction work.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-05-DOCUMENT"></a>
## 05-DOCUMENT - Document databases, embedding and replica sets

**Track:** Container recipe. **Source topics:** document store; MongoDB; JSONB; embedding; referencing; schema evolution.

**Files to inspect or extend:** `infra/datastores.yaml`.

### Start / reproduce

```bash
docker compose -f infra/datastores.yaml --profile mongo up -d
docker compose -f infra/datastores.yaml exec mongo1 mongosh --eval 'rs.initiate({_id:"rs0",members:[{_id:0,host:"mongo1:27017"},{_id:1,host:"mongo2:27017"},{_id:2,host:"mongo3:27017"}]})'
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the MongoDB replica-set profile, initiate rs0 using the three Docker hostnames and wait for a primary.
2. Represent an event with embedded display metadata, then compare an instructor embedded in many events with a normalized reference. Index tenant and event date for the same query as PostgreSQL.
3. Test majority write concern and selected read concern/preference under primary loss. Compare JSONB in PostgreSQL with a real document engine rather than treating them as identical products.

**Inject this failure:** Update an instructor name halfway through a duplicated-document backfill; fail the primary during a majority write.

**Completion gate:** The access-pattern comparison includes update fan-out, schema evolution, acknowledgment semantics and observed stale reads.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-05-KEYVALUE"></a>
## 05-KEYVALUE - Key-value and wide-column access patterns

**Track:** Build extension. **Source topics:** key-value; Redis; DynamoDB; wide-column; Cassandra; HBase; partition keys.

**Files to inspect or extend:** `infra/cassandra.yaml`, `db/cassandra.cql`.

### Start / reproduce

```bash
docker compose -f infra/cassandra.yaml exec -T c1 cqlsh < db/cassandra.cql
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Implement event point lookup as a Redis hash and compare an authoritative SQL row with a disposable cache projection.
2. In Cassandra, create query-specific tables partitioned by tenant/event or tenant/day, with clustering keys for ordered access. Bound partition size and compare a hot single partition with many distributed partitions.
3. For DynamoDB or HBase, repeat the same contract in a sandbox using documented partition/row keys and consistency settings; record provider-specific behavior rather than assuming SQL versus NoSQL determines consistency.

**Inject this failure:** Choose a constant partition key and replay a skewed workload, then introduce a secondary access pattern the original schema does not support.

**Completion gate:** The selected schema serves declared queries without hidden cross-partition scans and names the cost of a hot partition or secondary index.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-05-GRAPH"></a>
## 05-GRAPH - Graph traversal and geospatial indexes

**Track:** Build extension. **Source topics:** graph database; Neo4j; relationships; traversal; geospatial; GiST; PostGIS.

**Files to inspect or extend:** `infra/datastores.yaml`, `src/algorithms/processing.mjs`.

### Start / reproduce

```bash
docker compose -f infra/datastores.yaml --profile graph up -d
node --test --test-name-pattern="geo distance" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use synthetic attendees, follows and event interests. Build a two-hop friend suggestion query in Neo4j and compare a relational join implementation.
2. Create a PostGIS geography(Point,4326) venue table, a GiST index and ST_DWithin filtering in metres; keep longitude/latitude ordering explicit.
3. Separate candidate discovery from a transactional worker/seat assignment, and apply current tenant/visibility filters after index lookup.

**Inject this failure:** Create a high-degree graph node, swap latitude/longitude, and query stale staff locations.

**Completion gate:** Query plans and candidate counts explain performance; stale or unauthorized locations are not accepted for an atomic assignment.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-05-PARTITION"></a>
## 05-PARTITION - Horizontal, vertical, time and functional partitioning

**Track:** Container recipe. **Source topics:** table partitioning; range; list; hash; vertical partitioning; functional partitioning; partition pruning.

**Files to inspect or extend:** `db/partition_lab.sql`.

### Start / reproduce

```bash
docker compose exec -T postgres psql -U lab -d eventhub < db/partition_lab.sql
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the monthly telemetry partition fixture and compare queries with and without a time bound. Add a default/new-month strategy before the next month arrives.
2. Create list partitions by region and hash partitions by tenant in separate disposable tables. Explain why these still share one server unless independently deployed.
3. Split large rarely used event content from hot metadata for vertical partitioning, and separate catalog from analytics ownership for functional partitioning. Measure added joins and transaction boundaries.

**Inject this failure:** Insert outside known ranges, remove the time predicate and attempt to assume a global unique key that omits the partition key.

**Completion gate:** Pruning and retention operations are measured; partitioning on one host is distinguished from independent database sharding.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-05-SHARDS"></a>
## 05-SHARDS - Shard keys, cross-shard queries and global constraints

**Track:** Build extension. **Source topics:** database sharding; shard-key choice; global uniqueness; cross-shard joins; scatter-gather.

**Files to inspect or extend:** `src/stores/sharded.mjs`, `scripts/migrate-tenant.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs sharding
BASE_URL=http://127.0.0.1:8089 node scripts/race.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Reuse all six routing approaches from chapter 02: modulo hash, range, directory, geo, consistent ring and rendezvous. Score candidate keys by skew, query locality and transaction locality.
2. Keep one tenant's booking transaction on one shard. Add a separate global slug registry or globally unique random IDs where a cross-tenant namespace requires uniqueness.
3. Implement a bounded scatter-gather reporting query returning per-shard status and a defined partial-result policy; perform global ORDER BY/top-K at the aggregator rather than concatenating locally sorted pages.

**Inject this failure:** Time out one shard, duplicate a global alias across two shards in a naive branch, and migrate a tenant while a report is running.

**Completion gate:** Cross-shard limitations are explicit; ordinary SQL transactions are not represented as atomic across independent servers.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-05-PERFORMANCE"></a>
## 05-PERFORMANCE - Pools, N+1, denormalization and materialized views

**Track:** Build extension. **Source topics:** connection pools; N+1; denormalization; materialized views; query cancellation; lock timeout.

**Files to inspect or extend:** `src/stores/pg.mjs`, `db/query_lab.sql`.

### Start / reproduce

```bash
node scripts/load.mjs
node --test test/http.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Compare pool sizes 2, 10 and 30 at fixed traffic. Instrument acquisition wait, query duration and transaction duration separately.
2. Implement an organizer dashboard naively with one query per card, then batch/join the lookup. Add a materialized summary with version/freshness metadata.
3. Keep transactions short and release clients in finally. Propagate a request budget to query/lock timeouts and record canceled versus completed work.

**Inject this failure:** Hold a row lock while the pool fills; stop a caller without canceling the underlying query in a failing branch.

**Completion gate:** No connection leak remains after errors, and fewer round trips are not achieved by removing authorization filters.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-05-REPLICATE"></a>
## 05-REPLICATE - Physical versus logical replication and schema coordination

**Track:** Build extension. **Source topics:** physical replication; logical replication; read replicas; failover; schema changes.

**Files to inspect or extend:** `infra/replication/compose.yaml`, `src/stores/pg.mjs`.

### Start / reproduce

```bash
sh scripts/replication.sh inspect
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use the supplied physical standby for byte-level WAL replay and read scaling.
2. Create a logical publication/subscription for a dedicated projection table with a replica identity. Demonstrate that selected-table replication and schema/DDL rollout require explicit coordination.
3. Add an expand-contract migration to publisher and subscriber sides, monitor lag and slot retention, and clean up unused slots after the experiment.

**Inject this failure:** Pause the subscriber while writes continue and monitor retained WAL disk budget. Break compatibility with an early column removal only in a disposable branch.

**Completion gate:** Physical and logical replication are not conflated; routing, visibility, DDL and retention responsibilities are documented.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-06"></a>
# 06. Caching

**Source chapter:** [06_Caching.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/06_Caching.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Core database/cache track and an authoritative version contract. Reference cache algorithms can be tested without Redis.


<a id="lab-06-LAYERS"></a>
## 06-LAYERS - Browser, gateway, CDN, application and database caches

**Track:** Build extension. **Source topics:** browser cache; HTTP cache; CDN; application cache; database buffers; Redis; Memcached.

**Files to inspect or extend:** `src/cache-client.mjs`, `infra/network/edge.conf`.

### Start / reproduce

```bash
node experiments/run.mjs cache
docker compose -f infra/datastores.yaml --profile cache up -d
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Trace one request through browser/edge/application/database layers and label each cache's key, ownership, lifetime and invalidation responsibility.
2. Compare Cache-Control no-store on private core APIs with immutable public assets at the edge and a tenant-scoped Redis application cache.
3. Run the same point-lookup contract in Memcached and Redis, recording missing-key semantics, value serialization and memory overhead; do not assume either is the authoritative database.

**Inject this failure:** Cache a private response without tenant identity in a disposable test and demonstrate the cross-user leak before fixing the key/policy.

**Completion gate:** Each layer has a separate freshness and authorization contract; clearing one cache is not assumed to invalidate all others.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-06-ASIDE"></a>
## 06-ASIDE - Cache-aside and read-through

**Track:** Run reference. **Source topics:** cache-aside; lazy loading; read-through; cache misses; loader ownership.

**Files to inspect or extend:** `src/algorithms/cache.mjs`, `src/cache-client.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs cache
node --test --test-name-pattern="five cache|singleflight" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Read the same event twice and inspect X-Cache. Count database loads, cache hits, misses, timeouts and fallbacks.
2. Move the miss loader from the service into a CatalogRepository wrapper to implement read-through. Keep the comparison about ownership and behavior, not an assumed different storage guarantee.
3. Cache absent data only with a short, explicit negative-cache lifetime and creation invalidation.

**Inject this failure:** Stop Redis during a miss and observe the database fallback load; make the loader fail and ensure a later retry can succeed.

**Completion gate:** Both strategies preserve the authoritative database result, and the comparison explains who performs the fill.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-06-THROUGH"></a>
## 06-THROUGH - Write-through cache with a non-atomic second store

**Track:** Run reference. **Source topics:** write-through; synchronous cache maintenance; dual-write failure.

**Files to inspect or extend:** `src/algorithms/cache.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="write-through" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Write the database first, then update the cache synchronously using the reference wrapper. Return the durable result separately from cache maintenance status.
2. Inject a cache failure after database commit and add a durable repair/invalidation event. Do not roll back the already committed database by pretending two stores are one transaction.
3. Test a retry with the original idempotency key and observe that it cannot repeat the booking effect.

**Inject this failure:** Crash after the database commit and before the cache set; then serve a warmed stale cache entry.

**Completion gate:** The API states the commit outcome accurately and recovery repairs or invalidates stale cache without losing the durable write.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-06-BEHIND"></a>
## 06-BEHIND - Write-back/write-behind with explicit loss boundaries

**Track:** Build extension. **Source topics:** write-back; write-behind; batching; durable queue; acknowledgment boundary.

**Files to inspect or extend:** `src/algorithms/cache.mjs`, `src/storage/wal.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs cache
node experiments/run.mjs storage
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use the bounded volatile writeBehind buffer for synthetic page views, acknowledge acceptance and kill the process before flush.
2. Replace the buffer with a durable queue or WAL and acknowledge only after the chosen durability boundary. Batch by key while retaining original event IDs for deduplication.
3. Measure throughput, flush delay, loss, replay and shutdown behavior. Keep this strategy away from authoritative inventory until its semantics meet that invariant.

**Inject this failure:** Crash before flush, after database batch commit but before checkpoint, and while the queue is full.

**Completion gate:** The volatile version's loss is demonstrated; the durable version has a tested replay/deduplication protocol rather than an unsupported exactly-once claim.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-06-AROUND"></a>
## 06-AROUND - Write-around and read pollution

**Track:** Run reference. **Source topics:** write-around; write-heavy workload; cache pollution; invalidation.

**Files to inspect or extend:** `src/algorithms/cache.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="five cache|generation fence" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Write rare catalog changes directly to the database and invalidate any existing cached representation instead of eagerly caching each write.
2. Compare this with write-through on a write-heavy workload where few written records are later read.
3. Run the stale-fill interleaving: a reader obtains old data, a writer commits and invalidates, then the reader attempts to fill the old value.

**Inject this failure:** Allow a delayed old fill after invalidation in a naive branch, then reject it with a generation or version fence.

**Completion gate:** Write-around avoids populating never-read data while honoring the declared freshness contract.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-06-EVICTION"></a>
## 06-EVICTION - LRU, LFU, FIFO, random and TTL policies

**Track:** Run reference + container. **Source topics:** LRU; LFU; FIFO; random; TTL; noeviction; working set.

**Files to inspect or extend:** `src/algorithms/cache.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs cache
node --test --test-name-pattern="LRU|LFU|TTL|cache capacity" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the same deterministic hot/long-tail trace through every supplied policy. Keep capacity identical and record hits, misses and evictions.
2. Repeat with a dedicated Redis cache using allkeys-lru, allkeys-lfu and noeviction; measure actual bytes and Redis's approximate behavior rather than equating it with the exact teaching map.
3. Put a quota or lease key into an allkeys-evicting instance in a failing branch and show why authoritative coordination state requires a separate failure policy.

**Inject this failure:** Shift the hot set, exceed the memory budget and create a mass TTL expiry.

**Completion gate:** Policy selection is backed by workload evidence; an evicted cache item is never the only copy of a booking.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-06-INVALIDATE"></a>
## 06-INVALIDATE - TTL, event-driven and versioned invalidation

**Track:** Build extension. **Source topics:** TTL invalidation; event invalidation; versioned keys; generation fencing; stale-fill race.

**Files to inspect or extend:** `src/algorithms/cache.mjs`, `src/async/publisher.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="generation fence" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create a deterministic pause after database read and before cache fill. Commit an update and invalidate while the reader is paused.
2. Run the GenerationCache reference to reject the old fill; implement the same comparison atomically in the shared cache for a multi-process exercise.
3. Compare TTL-only, outbox-driven invalidation and immutable versioned keys with a coherent version pointer. Define what happens when invalidation delivery is delayed or lost.

**Inject this failure:** Replay an old invalidation after a newer fill and disconnect one invalidation subscriber.

**Completion gate:** The test names the precise version/freshness promise; a TTL after an arbitrarily delayed fill is not presented as a strict bound from the original database update.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-06-STAMPEDE"></a>
## 06-STAMPEDE - Stampede prevention and refresh-ahead

**Track:** Build extension. **Source topics:** cache stampede; singleflight; lease; refresh-ahead; probabilistic refresh; TTL jitter.

**Files to inspect or extend:** `src/algorithms/cache.mjs`, `infra/redis/release-lease.lua`.

### Start / reproduce

```bash
node --test --test-name-pattern="singleflight" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Delete one hot key and issue 100 simultaneous misses. Count authoritative fetches, then collapse same-process loads with SingleFlight.
2. Across replicas, use a bounded unique-token Redis lease for cache loading, release through compare-and-delete Lua and serve an explicitly allowed stale value or wait within a deadline.
3. Add refresh-ahead and probabilistic early refresh, then TTL jitter across many keys. Keep hot-key expiry distinct from mass-expiry avalanches.

**Inject this failure:** Kill the loader while holding a lease and resume it after another loader acquired a newer lease.

**Completion gate:** Load duplication is measured, waiters are bounded, and an expired cache lease is never used as the final inventory lock.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-06-PENETRATION"></a>
## 06-PENETRATION - Negative caching and safe Bloom-filter rebuilds

**Track:** Run reference + extension. **Source topics:** cache penetration; negative caching; Bloom filter; false positives; filter generation.

**Files to inspect or extend:** `src/algorithms/bloom.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs bloom
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Load known IDs into the Bloom filter and measure random absent-ID requests, memory and false positives.
2. Pause filter updates, create a new database item and demonstrate that an incomplete generation cannot safely return a final 404.
3. Build from a consistent snapshot plus a catch-up stream, publish a generation only after completeness is established, and bypass negative decisions while uncertain. Invalidate negative cache entries on creation.

**Inject this failure:** Restart mid-build and query a recently created item before catch-up finishes.

**Completion gate:** Inserted items are not rejected by the algorithm, and distributed completeness—not the Bloom formula alone—governs authoritative negative decisions.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-06-DISTRIBUTED"></a>
## 06-DISTRIBUTED - Redis Cluster, Sentinel and multi-level caches

**Track:** Container recipe. **Source topics:** Redis Cluster; hash slots; Sentinel; replication; distributed cache; L1; L2.

**Files to inspect or extend:** `infra/redis/cluster.yaml`, `infra/redis/compose.yaml`, `infra/redis/sentinel.conf`.

### Start / reproduce

```bash
docker compose -f infra/redis/cluster.yaml up -d
docker compose -f infra/redis/cluster.yaml exec r1 redis-cli --cluster create r1:6379 r2:6379 r3:6379 r4:6379 r5:6379 r6:6379 --cluster-replicas 1 --cluster-yes
docker compose -f infra/redis/compose.yaml up -d
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the six-node Redis Cluster and create three primaries plus one replica each. Route a key and inspect MOVED/ASK behavior using a cluster-aware client.
2. Separately run the primary/two-replica/three-Sentinel stack and observe primary discovery after failure; Sentinel is HA discovery, not automatic sharding.
3. Add a bounded local L1 before distributed L2. Include versions/expiry and a reconnect resynchronization policy because pub/sub invalidation is not durable history.

**Inject this failure:** Lose a primary, disconnect an L1 invalidation listener and execute a multi-key command with keys in different hash slots.

**Completion gate:** Cluster sharding, Sentinel failover and L1 freshness are explained separately; asynchronous cache replication is not advertised as a durable inventory store.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-07"></a>
# 07. Messaging Queues & Async

**Source chapter:** [07_Messaging_Queues_and_Async.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/07_Messaging_Queues_and_Async.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Core schema and idempotency tests; enable the async profile or the separate broker/log topology as indicated.


<a id="lab-07-TOPOLOGY"></a>
## 07-TOPOLOGY - Work queues, pub/sub fan-out and replayable logs

**Track:** Container recipe. **Source topics:** point-to-point; competing consumers; publish-subscribe; fan-out; replayable logs; RabbitMQ; Kafka.

**Files to inspect or extend:** `src/async/topology.mjs`, `infra/messaging/kafka.yaml`.

### Start / reproduce

```bash
docker compose --profile async up -d --build
docker compose -f infra/messaging/kafka.yaml up -d
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Publish booking events to a topic exchange and give notification and analytics their own queues. Scale two consumers within each queue.
2. Compare this with placing both logical subscribers on one queue, where they compete instead of both receiving each event.
3. Create a Kafka topic and independent consumer groups for durable replay; distinguish message removal/acknowledgment from retained-log offset advancement.

**Inject this failure:** Stop analytics while notifications continue, then replay only analytics without sending notifications again.

**Completion gate:** The topology delivers each event to the intended logical subscribers and distributes work within—not between—those subscribers.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-07-OUTBOX"></a>
## 07-OUTBOX - Transactional outbox and ambiguous publish outcomes

**Track:** Container recipe. **Source topics:** transactional outbox; publisher confirms; atomic local transaction; duplicate publication.

**Files to inspect or extend:** `src/stores/pg.mjs`, `src/async/publisher.mjs`.

### Start / reproduce

```bash
PUBLISH_PAUSE_AFTER_CONFIRM_MS=5000 docker compose --profile async up -d --force-recreate publisher
# Create a booking; wait for publish-confirmed, then:
docker compose kill -s KILL publisher
PUBLISH_PAUSE_AFTER_CONFIRM_MS=0 docker compose --profile async up -d publisher
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Inspect the core booking transaction: guarded inventory update, booking row, successful idempotency response and outbox event commit together.
2. Run the publisher with a stable event ID and persistent mandatory publication. Mark published_at only after broker confirmation; retain the row until then.
3. Set PUBLISH_PAUSE_AFTER_CONFIRM_MS=5000, create a booking and kill the publisher after its confirmation log but before marking the row. Restart it normally.

**Inject this failure:** Publication succeeds but the database mark does not; the restart must be allowed to publish the same event again.

**Completion gate:** No committed outbox event is silently forgotten; duplicate publication is an expected tested state, not a supposed impossible error.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-07-INBOX"></a>
## 07-INBOX - Consumer deduplication and effect-before-ACK

**Track:** Container recipe. **Source topics:** idempotent consumer; inbox; at-least-once; at-most-once; exactly-once scope.

**Files to inspect or extend:** `src/async/worker.mjs`, `db/invariants.sql`.

### Start / reproduce

```bash
WORKER_PAUSE_AFTER_COMMIT_MS=5000 docker compose --profile async up -d --force-recreate worker
docker compose exec -T postgres psql -U lab -d eventhub < db/invariants.sql
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use the worker's transaction to insert processed_events and the notification effect together, then ACK only after commit.
2. Kill the worker during WORKER_PAUSE_AFTER_COMMIT_MS=5000 and allow redelivery. Check the unique tenant/source_event_id result.
3. Compare at-most-once ACK-before-effect in a failing branch and show the loss window. For external email/payment effects, use provider idempotency or reconciliation rather than a local dedup row alone.

**Inject this failure:** The same event ID arrives twice with the same payload, then with a conflicting payload hash.

**Completion gate:** One simulated database effect exists per event ID; payload conflicts are quarantined and delivery semantics are scoped honestly.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-07-RETRY"></a>
## 07-RETRY - Backoff, dead letters, poison messages and redrive

**Track:** Build extension. **Source topics:** retry queues; exponential backoff; jitter; DLQ; poison messages; redrive.

**Files to inspect or extend:** `src/async/worker.mjs`, `src/async/topology.mjs`.

### Start / reproduce

```bash
docker compose --profile async logs worker
node experiments/run.mjs reliability
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Classify validation/permanent failures separately from transient dependency errors. Bound attempts, elapsed time and concurrent retry work.
2. Add nextAttemptAt and durable retry metadata, using delay queues or a scheduled retry table rather than immediate requeue loops.
3. Build an authenticated DLQ inspection/redrive command preserving event identity and recording the operator. For quorum queues, configure and verify the intended dead-letter delivery policy explicitly.

**Inject this failure:** Inject invalid JSON, an unknown event type, a temporarily offline database and a permanently invalid recipient.

**Completion gate:** Good events continue, retry traffic remains bounded, and redrive does not duplicate prior effects or bypass validation.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-07-ORDER"></a>
## 07-ORDER - Partition ordering, versions and backpressure

**Track:** Build extension. **Source topics:** ordering; aggregate versions; partitions; prefetch; consumer groups; backpressure.

**Files to inspect or extend:** `src/async/worker.mjs`, `infra/messaging/kafka.yaml`.

### Start / reproduce

```bash
docker compose --profile async up -d --scale worker=4
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Add aggregateId, aggregateVersion and eventId. Deliver versions 3,1,2,2 and choose a documented gap policy: buffer, resync snapshot or transport partition ordering.
2. Partition Kafka events by aggregate ID and preserve per-key ordering; do not assume global order across partitions or independent queues.
3. Compare worker counts and prefetch values against a slow database/provider, recording throughput, oldest-message age, active calls and duplicate delivery.

**Inject this failure:** Kill a consumer during rebalance and let an old worker finish after a new owner starts.

**Completion gate:** The final state respects aggregate version rules and an old worker cannot commit an invalid later effect merely because it still runs.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-07-KAFKA"></a>
## 07-KAFKA - Kafka replication, ISR, producer idempotence and transactions

**Track:** Container recipe. **Source topics:** Kafka; KRaft; ISR; acks; retention; offsets; idempotent producer; transactions; read_committed.

**Files to inspect or extend:** `infra/messaging/kafka.yaml`.

### Start / reproduce

```bash
docker compose -f infra/messaging/kafka.yaml up -d
docker compose -f infra/messaging/kafka.yaml exec k1 /opt/kafka/bin/kafka-topics.sh --bootstrap-server k1:9092 --create --topic booking.events --partitions 3 --replication-factor 3 --config min.insync.replicas=2
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Launch the three-node KRaft stack, create a three-partition topic with replication factor three and min.insync.replicas=2, and use a producer with acks=all.
2. Stop one broker and then a second; record producer errors and ISR changes. Create two independent consumer groups and replay one from an earlier offset.
3. Build a consume-transform-produce transaction committing produced records and consumed offsets together; consume with read_committed. Keep an external database/email effect outside the claimed transaction boundary.

**Inject this failure:** Crash after producing output but before committing the transaction, then restart with the same stable transactional identity where required.

**Completion gate:** Replay, committed visibility and quorum behavior match the declared broker contract; end-to-end external exactly-once is not inferred.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-07-BROKER-HA"></a>
## 07-BROKER-HA - RabbitMQ quorum queues and alternative brokers

**Track:** Build extension. **Source topics:** RabbitMQ quorum; Redis Streams; NATS JetStream; SQS; Pub/Sub; Pulsar; broker selection.

**Files to inspect or extend:** `infra/messaging/rabbit.yaml`, `infra/redis/compose.yaml`, `infra/datastores.yaml`.

### Start / reproduce

```bash
docker compose -f infra/messaging/rabbit.yaml up -d
docker compose -f infra/messaging/rabbit.yaml exec r1 rabbitmqctl cluster_status
docker compose -f infra/datastores.yaml --profile nats up -d
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the three-node RabbitMQ cluster and declare a quorum queue only after cluster membership is healthy. Verify queue members and leader distribution.
2. Repeat a common durable-consumer contract using Redis Streams (consumer group, XACK, pending reclamation) and NATS JetStream (durable consumer, explicit ACK, replay).
3. For SQS, Google Pub/Sub or Pulsar sandbox tracks, implement the same contract tests for visibility/ack deadlines, ordering scope, dedup scope, retry and dead letters. Provider names do not imply interchangeable semantics.

**Inject this failure:** Lose the broker leader, crash a consumer before ACK and reclaim an abandoned pending message.

**Completion gate:** The comparison table records observed delivery, retention, ordering and failure behavior for each actually run adapter, leaving others explicitly unexecuted.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-07-SAGA"></a>
## 07-SAGA - Saga orchestration and choreography

**Track:** Build extension. **Source topics:** saga; orchestration; choreography; compensating actions; state machine.

**Files to inspect or extend:** `src/algorithms/seat-holds.mjs`, `src/async/publisher.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs booking
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Persist saga_id, current_step, status, version and the next command. Orchestrate reserve seats -> request payment -> confirm -> notify, with stable per-step idempotency keys.
2. Implement compensation as new durable commands: release only the matching hold, refund only the matching payment, and record compensation failure for operator repair.
3. Rebuild the same flow as choreography with explicit events and correlation/causation IDs; compare observability and cyclic dependency risk.

**Inject this failure:** Payment succeeds after the hold expires, a refund callback is duplicated, or the orchestrator restarts during compensation.

**Completion gate:** Late success never steals a newer holder's seat; compensation is retriable and visible, not an imaginary rollback of external time.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-07-CQRS"></a>
## 07-CQRS - CQRS, event sourcing, snapshots and projection rebuild

**Track:** Build extension. **Source topics:** CQRS; event sourcing; append-only events; projections; snapshots; replay; schema evolution.

**Files to inspect or extend:** `src/storage/wal.mjs`, `src/algorithms/processing.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs storage
node experiments/run.mjs search
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create an immutable event stream with aggregate ID, sequence, event ID and schema version; choose whether it or relational state is the authority.
2. Build a projection using a transactional checkpoint and conditional aggregate-version update. Rebuild into a new table while capturing changes after the snapshot boundary.
3. Snapshot the aggregate state at a known sequence and replay the suffix. Upcast old events through versioned transformations without rewriting audit history silently.

**Inject this failure:** Duplicate an event, replay out of order, crash after projection write but before checkpoint and change an event schema.

**Completion gate:** Rebuild produces the same declared state, gaps are visible and the event log is not confused with a disposable notification outbox.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-08"></a>
# 08. Storage Systems

**Source chapter:** [08_Storage_Systems.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/08_Storage_Systems.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Node local storage tests first; use disposable upload directories and a sandbox object store for provider-backed work.


<a id="lab-08-TYPES"></a>
## 08-TYPES - Block, file and object storage are different interfaces

**Track:** Run reference + storage extension. **Source topics:** block storage; EBS; SAN; Azure Disk; file storage; NFS; SMB; object storage; S3; GCS; Azure Blob.

**Files to inspect or extend:** `src/storage/chunks.mjs`.

### Start / reproduce

```bash
node --test test/storage.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Write the same synthetic attachment through a container writable layer, a named filesystem volume and the FileStore adapter; record which boundary survives a container restart.
2. For a shared file lab, mount an isolated NFS/SMB export on two clients and test permissions, concurrent rename and server loss. For block storage, attach a disposable volume in a sandbox VM and create a filesystem only on that known empty device.
3. Implement an object adapter with put/get/head/delete and immutable keys, keeping metadata separately. Compare atomic rename, append, range read and concurrent-write semantics instead of assuming a shared POSIX filesystem.

**Inject this failure:** Restart the container, detach the disposable block volume and interrupt a shared filesystem connection; never run filesystem tools against an unidentified host device.

**Completion gate:** The decision record names interface, durability boundary, failure domain, access control and cost categories for each storage type.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-08-UPLOAD"></a>
## 08-UPLOAD - Streamed uploads and durable publication

**Track:** Build extension. **Source topics:** streaming upload; size limits; checksums; temporary objects; metadata publication; orphans.

**Files to inspect or extend:** `src/storage/chunks.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="upload|checksum|storage path" test/storage.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use FileStore.put with a streaming source, byte limit and expected checksum. Measure memory against 1 MiB and 50 MiB synthetic files.
2. Create file metadata states uploading, available and deleted. Publish available metadata only after verified bytes cross the chosen storage durability boundary.
3. Keep orphaned bytes recoverable when metadata commit fails; add a grace-period collector with a final reference check.

**Inject this failure:** Send an early disconnect, a false Content-Length, an incorrect checksum and an upload ID owned by another tenant.

**Completion gate:** No incomplete or unauthorized object becomes a published file; memory does not grow with the whole upload in the streaming adapter.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-08-MULTIPART"></a>
## 08-MULTIPART - Multipart uploads, resumability and manifest validation

**Track:** Build extension. **Source topics:** multipart upload; resumable upload; bounded parallelism; part checksums; S3 multipart.

**Files to inspect or extend:** `src/storage/chunks.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="chunk manifest|manifest rejects|empty file" test/storage.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Add upload_sessions and upload_parts keyed by session/ordinal, with tenant, owner, expected size/hash, state and expiry.
2. Upload fixed-size chunks out of order with bounded parallelism. Retrying identical bytes is idempotent; a conflicting finalized part is rejected.
3. Complete in one metadata transaction after validating the exact ordered manifest, all part checksums and full-file checksum. For S3 map initiate/upload/list/complete/abort explicitly and do not treat a multipart ETag as a universal file checksum.

**Inject this failure:** Interrupt at 50%, restart the client, submit concurrent completion calls and expire a session while a late part arrives.

**Completion gate:** Resume sends only missing parts; repeated completion returns the same version; partial bytes never masquerade as a complete file.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-08-DIRECT"></a>
## 08-DIRECT - Direct-to-object-store upload and signed download authorization

**Track:** Build extension. **Source topics:** presigned URLs; direct upload; signed downloads; ownership; expiry; revocation.

**Files to inspect or extend:** `src/storage/chunks.mjs`, `docs/openapi.yaml`.

### Start / reproduce

```bash
# Implement provider calls against a disposable bucket; no cloud upload is performed by this package.
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Authorize initiate-upload before issuing a narrowly scoped ticket for one object key, method, expiry and maximum policy-defined size.
2. Have the client upload directly to the object provider, then verify object identity, size/checksum and session ownership before publishing metadata.
3. For downloads, authorize each issuance and state whether existing tickets remain valid until expiry or require an additional per-request revocation check.

**Inject this failure:** Reuse a ticket for a different key, try another user's session and revoke sharing after a ticket has already been issued.

**Completion gate:** Ticket knowledge grants only its documented scope; deleting a sharing row is not presented as instantly revoking all previously issued URLs.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-08-RANGE"></a>
## 08-RANGE - Range downloads, resume and cache-safe media delivery

**Track:** Build extension. **Source topics:** HTTP ranges; Content-Range; 206; 416; resumable downloads; CDN.

**Files to inspect or extend:** `src/storage/chunks.mjs`, `infra/network/edge.conf`.

### Start / reproduce

```bash
node --test --test-name-pattern="range|corrupt" test/storage.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use the local range reference for correctness, then replace its full-object read with bounded streaming for large objects. Verify first, middle, last, suffix and invalid ranges.
2. Return correct Content-Range and length, and reconstruct the complete file from several chunks to verify SHA-256.
3. Cache only public immutable versions; authorize private ranges and bound many tiny-range requests. Decide how full-file integrity is established while streaming ranges.

**Inject this failure:** Download a stale version range after a file update, corrupt one chunk and request overlapping or impossible ranges.

**Completion gate:** Reassembled bytes match the selected immutable version; invalid ranges are explicit and memory remains bounded in the extended streaming path.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-08-DEDUP"></a>
## 08-DEDUP - Fixed-size versus content-defined chunking and deduplication

**Track:** Build extension. **Source topics:** chunking; delta sync; fixed chunks; content-defined chunks; deduplication; hashes.

**Files to inspect or extend:** `src/storage/chunks.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs storage
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run chunkBuffer on repeated synthetic content and compare logical chunk count with unique hash count.
2. Insert bytes near the beginning and measure changed fixed-size chunks. Add content-defined boundaries using a rolling fingerprint with minimum/average/maximum chunk sizes and compare upload deltas.
3. Deduplicate within a tenant first, maintaining authorized references, logical quota and physical byte accounting separately. Treat cross-tenant dedup as a separate privacy-sensitive design.

**Inject this failure:** Attempt to read a chunk merely by guessing its hash, and run GC while a second file version begins referencing it.

**Completion gate:** Dedup saves measured bytes without granting access by hash; no referenced chunk is collected during concurrent publication.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-08-ERASURE"></a>
## 08-ERASURE - Replication versus Reed-Solomon erasure coding

**Track:** Run reference. **Source topics:** replication; erasure coding; k+m; checksums; repair; failure domains.

**Files to inspect or extend:** `src/algorithms/erasure.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs erasure
node --test --test-name-pattern="erasure" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Encode an attachment into three data and two parity shards using the supplied GF(256) teaching implementation.
2. Remove every possible pair of shards and reconstruct from the remaining verified fragments. Corrupt one fragment and reject it using its checksum before decoding.
3. Compare encoded bytes, metadata, CPU and reconstruction reads against three full replicas. Place real fragments on distinct hosts only in the advanced track.

**Inject this failure:** Lose or corrupt more shards than the code can recover; also alter trusted metadata in a negative test.

**Completion gate:** All tested recoverable combinations return identical bytes; unrecoverable cases fail loudly, and the teaching codec is not labeled a hardened production storage library.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-08-LIFECYCLE"></a>
## 08-LIFECYCLE - Tiered storage, integrity scans and cleanup

**Track:** Build extension. **Source topics:** hot storage; warm storage; cold storage; archive; lifecycle; integrity scans; orphan cleanup.

**Files to inspect or extend:** `src/storage/chunks.mjs`, `src/algorithms/erasure.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="corrupt|checksum" test/storage.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Classify objects by age and access count using synthetic metadata. Move eligible immutable versions between hot/warm/archive adapters without publishing a pointer before the destination is verified.
2. Create a bounded integrity scanner checking missing objects, mismatched hashes and unreferenced bytes. Record repair sources and unrecoverable cases.
3. Define retrieval delay, minimum retention, deletion requests and encrypted-key retention for archive tiers; use provider lifecycle policy only after reviewing its current semantics.

**Inject this failure:** Interrupt a tier migration, corrupt both replicas and restore metadata referring to archived bytes.

**Completion gate:** Tier changes preserve retrievability under the declared contract, and a checksum detects corruption without falsely promising recovery.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-08-VIDEO"></a>
## 08-VIDEO - Async transcoding, HLS/DASH and adaptive bitrate delivery

**Track:** Build extension. **Source topics:** media storage; transcoding; thumbnails; HLS; DASH; adaptive bitrate; CDN distribution.

**Files to inspect or extend:** `src/async/worker.mjs`, `infra/network/edge.conf`.

### Start / reproduce

```bash
# Requires ffmpeg on your workstation. Use synthetic media, not copyrighted streaming content.
ffmpeg -f lavfi -i testsrc=size=1280x720:rate=24 -t 10 -pix_fmt yuv420p /tmp/hld-synthetic.mp4
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Generate a short synthetic test video locally with ffmpeg. Store the original, publish a processing event and return processing status instead of blocking the upload response.
2. Have idempotent workers create 360p/720p renditions, thumbnails and HLS/DASH manifests in immutable versioned paths. Publish ready metadata only after all manifest references exist.
3. Use a local player and constrained bandwidth to observe rendition selection, buffer length and startup time. Put segment delivery behind the public edge cache while protecting private entitlement checks.

**Inject this failure:** Kill the transcoder midway, publish a manifest with a missing segment in a failing branch and retry the same job.

**Completion gate:** Duplicate jobs do not produce conflicting ready versions; the player never receives a successful manifest pointing at incomplete required output.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-09"></a>
# 09. Fault Tolerance & Reliability

**Source chapter:** [09_Fault_Tolerance_and_Reliability.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/09_Fault_Tolerance_and_Reliability.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Core workload plus a deterministic local dependency that can delay, fail or drop a response after its effect.


<a id="lab-09-DEADLINES"></a>
## 09-DEADLINES - Timeouts, propagated deadlines and ambiguous outcomes

**Track:** Build extension. **Source topics:** connect timeout; read timeout; deadline; cancellation; unknown outcome.

**Files to inspect or extend:** `src/algorithms/reliability.mjs`, `src/stores/pg.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs reliability
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create a fake dependency with normal, delayed, explicit-failure and effect-then-drop-response modes. Record every effect under a stable operation key.
2. Allocate an end-to-end deadline, a smaller attempt budget and connection/read budgets. Pass remaining time and AbortSignal to each downstream operation.
3. On a timeout after a possible commit, query by operation key before retrying. Distinguish an inbound body timeout from a complete handler deadline.

**Inject this failure:** Let the fake payment commit and drop its response; compare retrying with the same key versus a new key.

**Completion gate:** Upper bounds exist for attempt count, waiting time and in-flight work; a timeout is not falsely reported as proof no side effect occurred.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-09-RETRIES"></a>
## 09-RETRIES - Exponential backoff, full jitter and retry budgets

**Track:** Run reference. **Source topics:** retry; exponential backoff; jitter; retry budget; idempotency; overload amplification.

**Files to inspect or extend:** `src/algorithms/reliability.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="retry" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the retry helper with transient and permanent failures, keeping a stable write idempotency key.
2. Use full jitter in [0,min(cap,base*2^attempt)], stop when the deadline cannot accommodate another attempt and honor bounded Retry-After information.
3. Put retries in one chosen layer. Compare one three-attempt retry loop with independent three-attempt loops at gateway, API and worker.

**Inject this failure:** Recover the dependency while many callers share the same failure onset; compare synchronized retries with jitter.

**Completion gate:** The failure storm cannot multiply requests without a bound, and validation or authorization failures are not retried.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-09-CIRCUIT"></a>
## 09-CIRCUIT - Circuit breaker closed/open/half-open transitions

**Track:** Run reference. **Source topics:** circuit breaker; failure threshold; half-open probe; recovery; stale completions.

**Files to inspect or extend:** `src/algorithms/reliability.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="circuit|half-open|stale successful" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Attach a separate CircuitBreaker to each dependency/operation class, with metrics for allowed calls, failures, open rejections and recovery probes.
2. Force consecutive failures, verify the breaker opens without calling the provider, then allow only one half-open probe after the reset interval.
3. Complete a slow earlier success after the circuit has opened; the epoch mechanism must prevent it from incorrectly closing the newer circuit state.

**Inject this failure:** Race an old successful completion with a new failed probe and simulate oscillating downstream health.

**Completion gate:** Transitions are observable and deterministic; optional fallback is explicit and a breaker is not treated as proof a remote service is permanently dead.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-09-BULKHEAD"></a>
## 09-BULKHEAD - Bulkheads, priority admission and load shedding

**Track:** Build extension. **Source topics:** bulkhead; semaphore; bounded queue; priority; adaptive load shedding; fallback.

**Files to inspect or extend:** `src/algorithms/reliability.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="semaphore" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Assign expensive reports a separate concurrency semaphore or database pool from interactive bookings.
2. Run ten slow report jobs beside booking traffic. Compare with an intentionally shared unbounded queue and observe wait time/RSS.
3. Bound queue length and age, reserve capacity for critical operations and reject overload early. Add an adaptive threshold using recent latency/error signals with hysteresis, not a noisy instantaneous trigger.

**Inject this failure:** The report dependency becomes slow and its queue reaches the limit while bookings continue.

**Completion gate:** Background overload cannot exhaust all critical capacity; rejected work is visible and no fallback invents a successful booking.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-09-LIMITERS"></a>
## 09-LIMITERS - All five rate-limiting algorithms

**Track:** Run reference. **Source topics:** token bucket; leaky bucket; fixed window; sliding window log; sliding window counter.

**Files to inspect or extend:** `src/algorithms/rate-limit.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs rate-limit
node --test --test-name-pattern="bucket|window|sliding" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the same burst trace through every supplied algorithm, including requests immediately before and after a window boundary.
2. Implement per-user and per-tenant buckets with separate route classes. Token bucket permits bounded bursts; leaky bucket reserves departures that the worker must actually honor.
3. Compare fixed-window boundary bursts, exact sliding logs and approximate sliding counters. Validate cost, clock behavior, memory usage and Retry-After.

**Inject this failure:** Regress the clock, send a large weighted request, switch user IDs to try bypassing tenant quotas and let the limiter store fail.

**Completion gate:** Each strategy has an explicit acceptance/denial trace and a named precision/memory tradeoff; a shaper is not represented as immediate admission.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-09-DISTRIBUTED-LIMIT"></a>
## 09-DISTRIBUTED-LIMIT - Atomic distributed limiters and lease fencing

**Track:** Container recipe. **Source topics:** Redis Lua; distributed rate limit; server clock; atomicity; fail-open; fail-closed; leases.

**Files to inspect or extend:** `infra/redis/token-bucket.lua`, `infra/redis/sliding-log.lua`, `infra/redis/release-lease.lua`.

### Start / reproduce

```bash
docker compose exec -T redis redis-cli --eval /dev/stdin quota:org-a , 10 5 1 < infra/redis/token-bucket.lua
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the token-bucket Lua script atomically on a dedicated non-evicting Redis instance, using server time and a tenant/route-scoped key.
2. Run the sliding-log script with a unique request nonce so simultaneous requests do not overwrite the same sorted-set member. For Redis Cluster, co-locate related keys using hash tags.
3. Test fail-open for disposable recommendation traffic versus fail-closed for expensive administrative exports. Use compare-and-delete lease release, and require a resource-side fencing token where stale owners could cause authoritative writes.

**Inject this failure:** Run 100 simultaneous requests through two API replicas, then stop Redis and expire a lease while its original owner is paused.

**Completion gate:** The chosen quota does not double merely because there are two APIs; outage policy and the limits of asynchronous Redis failover are documented.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-09-HEDGING"></a>
## 09-HEDGING - Hedged reads, watchdogs and redundant paths

**Track:** Build extension. **Source topics:** hedged requests; timeouts; heartbeats; watchdog; redundancy; failover.

**Files to inspect or extend:** `src/algorithms/reliability.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="circuit|semaphore" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create two read-only providers with rare slow responses. Send a second request only after a measured delay and only within an explicit hedge budget.
2. Accept the first response satisfying the minimum version, cancel the loser where supported and count extra backend work.
3. Add a watchdog for a deliberately stuck background worker, but use leases plus fencing so restarting it cannot permit overlapping authoritative writers.

**Inject this failure:** The fastest replica is stale; an old worker resumes after its replacement acquires ownership.

**Completion gate:** Hedging never duplicates writes and cannot exceed the configured extra-work budget; a fast stale answer is not mislabeled consistent.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-09-CHAOS"></a>
## 09-CHAOS - Failure injection, recovery matrices and incident runbooks

**Track:** Build extension. **Source topics:** chaos engineering; fault injection; checkpoint recovery; observability; incident response.

**Files to inspect or extend:** `compose.yaml`, `db/invariants.sql`.

### Start / reproduce

```bash
node scripts/smoke.mjs
docker compose --profile async logs --no-color
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. For each component define a steady-state invariant, a bounded blast radius, abort criteria and a restoration command.
2. Run one failure at a time: API kill, Redis outage, broker outage, worker crash, DB pause, gateway stop, network delay, disk-budget exhaustion in a dedicated volume and a corrupt synthetic file.
3. Capture detection time, service impact, queue growth, duplicate/lost effects and recovery query results. Add checkpoints for long-running batch work and resume from the last committed unit.

**Inject this failure:** Restart too early while the dependency is still broken and observe a retry/restart storm in the failing configuration.

**Completion gate:** Every tested failure has a symptom, confirming signal, safe mitigation, rollback and invariant check; untested region/power faults remain explicitly unproven.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-10"></a>
# 10. Reducing Latency

**Source chapter:** [10_Reducing_Latency.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/10_Reducing_Latency.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** A fixed workload and measurable baseline. Every optimization must preserve authorization and response semantics.


<a id="lab-10-CRITICAL"></a>
## 10-CRITICAL - Critical-path latency and percentile-safe measurement

**Track:** Build extension. **Source topics:** network latency; disk latency; processing latency; queueing; lock wait; p50; p95; p99.

**Files to inspect or extend:** `scripts/load.mjs`, `src/http.mjs`.

### Start / reproduce

```bash
RATE=50 SECONDS=30 node scripts/load.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Build GET /dashboard with event, organizer, recommendations and recent-booking calls. Add spans or monotonic timing around pool acquisition, query, external call and serialization.
2. Begin with sequential work and rare injected slow responses. Record complete client latency distributions and error counts, not only average server handler time.
3. Identify the critical path and separate exclusive durations from cumulative curl checkpoints. Never add independent component p95s or average per-instance p95s as an end-to-end percentile.

**Inject this failure:** Add one 2-second response in every 100 calls and increase load until queue time dominates.

**Completion gate:** The report identifies what actually dominates the tail and preserves rejected/time-out samples rather than removing them from the latency story.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-10-PARALLEL"></a>
## 10-PARALLEL - Parallel fan-out, batching and asynchronous work

**Track:** Build extension. **Source topics:** parallel calls; fan-out/fan-in; async I/O; batching; background jobs; N+1.

**Files to inspect or extend:** `src/async/publisher.mjs`, `src/algorithms/reliability.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs reliability
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run independent dashboard queries concurrently with per-call deadlines and an overall budget; keep required and optional results distinct.
2. Replace 51 organizer queries for 50 cards with one join or batch lookup. Bound both result size and concurrent work.
3. Move nonessential analytics to the outbox and return a job handle for expensive reports, while preserving eventual completion/error visibility.

**Inject this failure:** One optional fan-out branch stalls and a batch contains a missing/unauthorized item.

**Completion gate:** Fewer round trips and critical-path duration are measured without changing access rules or silently dropping required data.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-10-CACHE"></a>
## 10-CACHE - Cache placement, query optimization and shard locality

**Track:** Build extension. **Source topics:** browser cache; CDN; app cache; database buffers; indexes; read replicas; sharding; co-location.

**Files to inspect or extend:** `db/query_lab.sql`, `src/cache-client.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs cache
docker compose exec -T postgres psql -U lab -d eventhub < db/query_lab.sql
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Replay one slow catalog query with no cache, Redis hit, L1 hit and edge hit where public semantics allow. Keep the payload and authorization contract fixed.
2. Use the query fixture to distinguish an index improvement from a warm database-buffer effect; include cold and warm trials.
3. Move tenant-scoped queries to one shard and co-locate the API with its home writer in a latency model. Record cross-region calls that remain unavoidable.

**Inject this failure:** Add a remote hop to an otherwise local synchronous chain and lose the cache during peak traffic.

**Completion gate:** The accepted optimization reduces measured work or distance, and its staleness/failure contract is explicitly retained.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-10-PREFETCH"></a>
## 10-PREFETCH - Prefetching, predictive loading and precomputed feeds

**Track:** Build extension. **Source topics:** browser prefetch; predictive loading; feed precomputation; recommendation prefetch; wasted work.

**Files to inspect or extend:** `src/algorithms/cache.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs cache
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Add a next-page prefetch after the first page becomes idle, with cancellation on navigation and an authorization-aware cache key.
2. Precompute a feed and recommendation candidate set asynchronously using a versioned snapshot. Serve an explicitly bounded-age result and refresh in the background.
3. Compare measured hit benefit with unused bytes, CPU and database reads. Restrict prefetch to a budget instead of fetching the entire catalog.

**Inject this failure:** Change the user's next action distribution and expire authorization while prefetched data is cached.

**Completion gate:** Prefetch reduces observed wait for useful work without causing unbounded speculative traffic or exposing stale private permissions.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-10-PAYLOAD"></a>
## 10-PAYLOAD - Gzip/Brotli, media compression and binary serialization

**Track:** Build extension. **Source topics:** gzip; Brotli; payload size; compression CPU; media compression; protobuf.

**Files to inspect or extend:** `src/catalog.proto`, `src/grpc-server.mjs`.

### Start / reproduce

```bash
node src/grpc-server.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create small JSON, large repetitive JSON and already compressed media fixtures. Measure source bytes, encoded bytes, compression time and transfer time.
2. Enable compression only where it benefits the payload, with correct content negotiation and Vary handling. Compare protobuf with the same logical fields over gRPC.
3. Select only the fields needed by the card instead of compressing unused columns; keep response schemas compatible.

**Inject this failure:** Compress a tiny response at high quality during CPU saturation and compare the latency regression with byte savings.

**Completion gate:** The decision includes CPU, wire bytes and end-to-end latency; no universal compression speedup is asserted.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-10-CONNECTIONS"></a>
## 10-CONNECTIONS - Connection pools, keep-alive and TLS session reuse

**Track:** Build extension. **Source topics:** connection pooling; persistent connections; TCP keep-alive; TLS session resumption; HTTP/2; HTTP/3.

**Files to inspect or extend:** `infra/network/caddy.yaml`, `src/stores/pg.mjs`.

### Start / reproduce

```bash
curl -V
node scripts/load.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Compare one connection per request against persistent HTTP and database pools. Capture connection setup separately from service time.
2. Repeat HTTP/1.1, HTTP/2 and HTTP/3 against the same Caddy origin, then test TLS session reuse with a client supporting it.
3. Bound idle lifetimes and stale connection retries; a pooled connection whose upstream died must not be retried unsafely for an ambiguous POST.

**Inject this failure:** Close an upstream idle connection just before reuse and simulate stale DNS addresses after failover.

**Completion gate:** The improvement is attributed to avoided setup rather than different cache state; reconnection preserves idempotency.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-10-COMPUTE"></a>
## 10-COMPUTE - Algorithm, batching, SIMD and storage-hardware experiments

**Track:** Hardware-aware extension. **Source topics:** CPU optimization; algorithm complexity; batching; SIMD; SSD; HDD; in-memory DB; WAL tuning; hot data.

**Files to inspect or extend:** `src/algorithms/processing.mjs`, `src/storage/wal.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs search
node experiments/run.mjs storage
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Benchmark a deliberately quadratic ranking pass against a bounded heap/top-K approach on the same synthetic data. Separate warmup from steady-state measurements.
2. Compare scalar numeric processing with a vetted vectorized/native or WebAssembly implementation only where the CPU and toolchain actually support the path; verify equal results first.
3. On available dedicated storage, compare random/sequential I/O and fsync batches; otherwise keep SSD/HDD/NVDIMM results as unexecuted hardware tasks. Never fabricate hardware differences from two directories on the same disk.

**Inject this failure:** Increase batch size until interactive latency worsens, or move hot data into an undersized cache.

**Completion gate:** Correctness is unchanged, hardware capabilities are recorded and tuning does not silently weaken the acknowledged durability contract.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-10-TAIL"></a>
## 10-TAIL - Tail-latency hedging, admission and service budgets

**Track:** Build extension. **Source topics:** tail latency; hedging; timeouts; jitter; adaptive load shedding; latency budget; tradeoffs.

**Files to inspect or extend:** `src/algorithms/reliability.mjs`.

### Start / reproduce

```bash
node scripts/load.mjs
node experiments/run.mjs reliability
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Set an explicit lab latency objective for a specified workload, then allocate tentative budgets to gateway, queueing, application, database and optional dependencies.
2. Run at 50%, 80% and above measured sustainable capacity. Compare admission control and bounded read hedging against the baseline.
3. Accept one improvement and reject another using latency, error rate, extra backend work, consistency, durability and operational cost.

**Inject this failure:** Cause correlated slowness across both hedge targets and verify the extra-request budget prevents overload amplification.

**Completion gate:** The report includes successful and rejected requests, remaining tail cases and a rollback threshold; component percentiles are not added as a proof.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-11"></a>
# 11. Durability

**Source chapter:** [11_Durability.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/11_Durability.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Client history outside disposable storage, verified backups and an explicit destructive-test scope.


<a id="lab-11-WAL"></a>
## 11-WAL - Append-only WAL, fsync, replay and partial-tail recovery

**Track:** Run reference. **Source topics:** WAL; append-only logs; fsync; crash recovery; replay; checkpoint.

**Files to inspect or extend:** `src/storage/wal.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs storage
node --test --test-name-pattern="WAL" test/storage.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run WalStore with checksummed sequential records. Append put/delete operations, synchronize before acknowledgment and reopen the store.
2. Write a snapshot to a temporary file, synchronize bytes, rename it and synchronize the parent directory. The reference retains and replays WAL; safe truncation is an additional manifest/checkpoint exercise.
3. Compare sync=true and unsynchronized writes only in the disposable directory, recording which fault model the test covers.

**Inject this failure:** Append a partial tail and corrupt a complete record. The partial tail can be discarded; a complete checksum mismatch must fail loudly.

**Completion gate:** Acknowledged records survive the tested close/reopen and process cases; this does not by itself prove survival of power loss or defective hardware.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-11-ACK"></a>
## 11-ACK - Database and broker acknowledgment boundaries

**Track:** Build extension. **Source topics:** ACID durability; synchronous_commit; remote_write; remote_apply; publisher confirms; commit logs.

**Files to inspect or extend:** `infra/replication/compose.yaml`, `src/async/topology.mjs`.

### Start / reproduce

```bash
sh scripts/replication.sh inspect
node scripts/race.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Record a client-side ledger of successful booking IDs, keys and response times. Kill PostgreSQL without deleting its volume, restart and verify each acknowledged ID.
2. Compare on, remote_write, remote_apply and asynchronous commit on an isolated replication stack, documenting whether each acknowledgment means local flush, remote OS write, remote flush or replay visibility.
3. For Rabbit/Kafka, compare unconfirmed sends, confirmed publication and committed consumer effects as different boundaries.

**Inject this failure:** Kill the process after commit but before the client receives its response; reconcile by key rather than classifying this as an acknowledged data loss.

**Completion gate:** The report names acknowledgment point, failure domain, recoverable uncertainty and observed losses for each experiment.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-11-BACKUP"></a>
## 11-BACKUP - Full, incremental, differential and snapshot backups

**Track:** Build extension. **Source topics:** full backup; incremental backup; differential backup; snapshots; restore verification.

**Files to inspect or extend:** `scripts/backup.sh`, `db/invariants.sql`.

### Start / reproduce

```bash
sh scripts/backup.sh
# Restore the resulting dump into a fresh database; see playbooks/DURABILITY.md.
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Take a logical full backup with backup.sh and restore into a distinct new database. Verify rows, foreign keys, inventory conservation and pending outbox state.
2. For incremental backups, capture a base plus changes since the previous backup; for differential, capture changes since the base. Use PostgreSQL-version-compatible physical tooling rather than pretending pg_dump --incremental exists.
3. For volume snapshots, coordinate database consistency and all required volumes/keys. Restore every chain type and measure recovery work and missing-time windows.

**Inject this failure:** Remove an intermediate incremental component, corrupt one backup and simulate a successful backup job whose restore is unusable.

**Completion gate:** Every claimed backup is restored and checked; snapshot existence and backup-job success alone do not count as recovery proof.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-11-PITR"></a>
## 11-PITR - Point-in-time recovery with physical backup and archived WAL

**Track:** Container recipe. **Source topics:** PITR; base backup; WAL archive; recovery target; RPO; RTO.

**Files to inspect or extend:** `infra/pitr/compose.yaml`, `infra/pitr/recover.sh`.

### Start / reproduce

```bash
docker compose -f infra/pitr/compose.yaml up -d
docker compose -f infra/pitr/compose.yaml --profile tools run --rm backup
# Record a primary timestamp, make the synthetic deletion, archive WAL, then restore per playbook.
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Launch the separate PITR stack with a verified archive directory. Take and verify a physical base backup before the deliberate destructive change.
2. Record a database timestamp immediately before deleting the synthetic recovery_probe row, force a WAL switch and wait for successful archival.
3. Restore to the separate empty recovery volume using recovery_target_time and exclusive target semantics. Verify earlier legitimate records exist and the unwanted deletion does not.

**Inject this failure:** Remove a required archived segment in a disposable copied archive; recovery must stop rather than silently invent success.

**Completion gate:** The recovered database reaches the intended point, with a measured RPO/RTO and no overwrite of the running original.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-11-REPLICATION"></a>
## 11-REPLICATION - Replication is not backup; consensus is not independent storage

**Track:** Build extension. **Source topics:** replication; consensus; Raft; Paxos; multi-region; deletion replication; logical corruption.

**Files to inspect or extend:** `infra/replication/compose.yaml`, `infra/consensus.yaml`.

### Start / reproduce

```bash
sh scripts/replication.sh up
node experiments/run.mjs paxos
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Delete a synthetic primary row and observe the deletion replicate to a healthy standby. Recover it from an older backup rather than promoting another copy of the deletion.
2. Repeat an etcd majority write under member loss and compare consensus availability with the durability of each member's persistent storage.
3. Create a failure-domain map covering disk, host, zone, region, credentials and operator error. Replicas sharing a failure domain cannot prove independence.

**Inject this failure:** Delete the same object through the authoritative API or lose the common host containing every local replica.

**Completion gate:** The recovery source is matched to the incident: replica for one-node failure, historical backup/version for propagated deletion, independent domain for host loss.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-11-RAID"></a>
## 11-RAID - RAID 0, 1, 5, 6 and 10 with explicit device safety

**Track:** Hardware/VM recipe. **Source topics:** RAID 0; RAID 1; RAID 5; RAID 6; RAID 10; rebuild; storage redundancy.

**Files to inspect or extend:** `src/algorithms/erasure.mjs`.

### Start / reproduce

```bash
# Device-creation commands are intentionally gated by the disposable-VM checklist in playbooks/DURABILITY.md.
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use a disposable VM and loop devices backed by newly created lab image files; record lsblk, backing-file paths and the exact device allowlist before any mdadm command.
2. Build each RAID layout separately, write checksum fixtures and fail only one of its listed loop devices. Compare usable capacity, tolerated failures and rebuild I/O.
3. For RAID 0 demonstrate no redundancy; for mirrors/parity test both tolerated and excessive failures. Never substitute production disk names or run these commands on an unverified workstation device.

**Inject this failure:** Fail more members than the selected layout tolerates and compare with a controller/host loss that affects the entire array.

**Completion gate:** Recovered files match checksums; the report distinguishes array redundancy, backup, independent-host replication and irreversible excess loss.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-11-NVM"></a>
## 11-NVM - NVDIMM, battery-backed RAM and persistent-memory boundaries

**Track:** Hardware-dependent recipe. **Source topics:** non-volatile memory; NVDIMM; battery-backed RAM; persistent memory; flush ordering.

**Files to inspect or extend:** `src/storage/wal.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs storage
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Inventory actual hardware and firmware guarantees before choosing a persistence API. Do not label ordinary process memory non-volatile.
2. On a suitable test host, use the vendor-supported persistent-memory library to write a checksummed record and durable commit marker in the documented order.
3. Run only an approved controlled crash/power-loss procedure on expendable hardware, compare recovery with normal RAM, and record battery/controller health dependencies.

**Inject this failure:** Interrupt before and after the durable commit marker; include the case where a battery or persistence domain is unavailable.

**Completion gate:** Only actually tested hardware guarantees are claimed. Without suitable hardware this remains a documented unexecuted lab, not a fake local simulation result.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-11-INTEGRITY"></a>
## 11-INTEGRITY - Checksums, scrubbing, erasure recovery and repair sources

**Track:** Build extension. **Source topics:** end-to-end checksums; silent corruption; erasure coding; scrubbing; repair.

**Files to inspect or extend:** `src/algorithms/erasure.mjs`, `src/storage/chunks.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs erasure
node --test --test-name-pattern="corrupt|checksum" test/*.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Calculate checksums at upload, persistent write, read and restored download. Verify which component authenticates metadata as well as bytes.
2. Use the 3+2 erasure model and two full-copy roots; corrupt one copy, repair from a verified source, then corrupt all usable sources.
3. Add a bounded scrubber that logs object/version/copy identity and rate-limits I/O so repair does not starve interactive traffic.

**Inject this failure:** A stored checksum is altered together with bytes, or both replicas share the same corrupted source.

**Completion gate:** Integrity checks distinguish accidental corruption detection from authenticity and from recoverability; insufficient verified fragments fail explicitly.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-11-IMMUTABLE"></a>
## 11-IMMUTABLE - Immutable object versions, retention and key recovery

**Track:** Build extension. **Source topics:** object versioning; immutable backups; snapshot retention; ransomware resilience; encryption keys.

**Files to inspect or extend:** `src/security.mjs`, `infra/pitr/compose.yaml`.

### Start / reproduce

```bash
node --test test/security.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create immutable content versions and store backup manifests outside the application's ordinary write/delete role. Test retrieving a prior object version after accidental replacement.
2. Define finite retention and deletion workflows, including what happens to legally required removals in backups. For provider object-lock features, verify the selected mode before enabling irreversible retention.
3. Encrypt a backup using a separate data key, preserve the key-recovery path and restore with the correct key. Rotate wrapping keys without losing old ciphertext access.

**Inject this failure:** Lose the only key, compromise an application credential and attempt to delete its protected backup copy.

**Completion gate:** The recovery drill restores bytes, metadata and keys together, and does not claim immutable copies eliminate every corruption or credential risk.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-11-DR"></a>
## 11-DR - Disaster recovery planning, drills and failback

**Track:** Build extension. **Source topics:** regional failure; RPO; RTO; DR; recovery runbook; data residency; failback.

**Files to inspect or extend:** `db/invariants.sql`, `infra/pitr/compose.yaml`.

### Start / reproduce

```bash
node scripts/smoke.mjs
node scripts/race.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Choose lab RPO/RTO targets for bookings, attachments and analytics separately. Identify a recovery source for each and the latest confirmed recovery boundary.
2. Rebuild the service into a separate environment from infrastructure definitions, backup data, event history and recovered keys. Isolate restored external-effect workers until reconciliation completes.
3. Measure detection, restore, catch-up, validation, traffic cutover and failback independently; record remaining unavailable dependencies.

**Inject this failure:** Restore an old outbox whose notifications or payments may already have occurred; preserve stable provider keys and reconcile before redrive.

**Completion gate:** The DR claim includes a completed restoration and invariant checks, not only a diagram or scheduled backup.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-12"></a>
# 12. Security

**Source chapter:** [12_Security.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/12_Security.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Synthetic identities/data only. Keep fixture authentication local; use the isolated identity provider for integration.


<a id="lab-12-THREAT"></a>
## 12-THREAT - CIA, defense in depth, least privilege and failure policy

**Track:** Design + defensive test. **Source topics:** CIA triad; defense in depth; least privilege; zero trust; secure defaults; separation.

**Files to inspect or extend:** `src/http.mjs`, `db/001_schema.sql`.

### Start / reproduce

```bash
node --test test/http.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Draw trust boundaries for browser, gateway, API, database, broker, object storage and identity provider. Classify synthetic data and actors.
2. For each entry point record authentication, resource authorization, input limit, tenant scope, audit event and dependency-outage behavior.
3. Create a negative-test matrix covering anonymous, wrong tenant, same-tenant wrong owner, attendee versus organizer and compromised background service.

**Inject this failure:** Remove one defensive layer in a test branch and verify what remaining layers still enforce; do not claim defense in depth when every layer trusts the same client tenant field.

**Completion gate:** The threat model maps confidentiality, integrity and availability risks to concrete controls and tests, with residual risk stated.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-12-IDENTITY"></a>
## 12-IDENTITY - Password login, OAuth 2.0, OIDC and SSO

**Track:** Build extension. **Source topics:** password authentication; OAuth 2.0; OIDC; SSO; identity provider.

**Files to inspect or extend:** `infra/security/compose.yaml`, `src/security.mjs`.

### Start / reproduce

```bash
docker compose -f infra/security/compose.yaml up -d
node --test test/security.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run local Keycloak on port 8180 with development-only bootstrap credentials. Create an EventHub realm, public browser client and confidential service client.
2. Use authorization-code flow with PKCE for browser login. Validate issuer, audience, signature, algorithm, expiry and required claims using a maintained verifier; map subject to server-side memberships.
3. Replace fixture authentication behind an explicit adapter. Disable the fixture path entirely in the identity-enabled deployment rather than falling back to it when the provider fails.

**Inject this failure:** Submit an expired, tampered or wrong-audience token and simulate signing-key rotation or identity-provider outage.

**Completion gate:** Only verified identities reach authorization checks; OAuth authorization and OIDC identity are not treated as synonyms.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-12-MFA"></a>
## 12-MFA - MFA, passkeys and biometric-backed authentication

**Track:** Identity/browser extension. **Source topics:** MFA; WebAuthn; passkeys; biometrics; account recovery.

**Files to inspect or extend:** `infra/security/compose.yaml`.

### Start / reproduce

```bash
# Configure the identity provider and a supported browser/authenticator. Do not implement custom biometric matching.
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Enable a second factor and WebAuthn/passkey authentication in the local identity provider. Keep biometric verification on the user's authenticator rather than collecting biometric templates in EventHub.
2. Register two recovery methods and require fresh/step-up authentication for administrative exports or permission changes.
3. Test device loss, expired challenges, wrong origin/RP ID and an account recovery flow with its own authorization/audit policy.

**Inject this failure:** Attempt to replay a previous WebAuthn assertion or use a valid assertion for another origin.

**Completion gate:** The application receives verified authentication evidence, not raw biometric data; recovery is tested rather than bypassing the second factor silently.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-12-SESSIONS"></a>
## 12-SESSIONS - Access tokens, refresh rotation and secure cookies

**Track:** Build extension. **Source topics:** JWT; access token; refresh token; session expiration; HttpOnly; Secure; SameSite; CSRF.

**Files to inspect or extend:** `src/http.mjs`, `infra/security/compose.yaml`.

### Start / reproduce

```bash
node --test test/http.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use short-lived access tokens and server-side or provider-managed refresh-token rotation. Persist only the verification/session state needed for revocation and reuse detection.
2. For cookie sessions, set appropriate HttpOnly, Secure and SameSite settings and add CSRF protection to state-changing requests. Bearer and cookie threat models differ.
3. On logout or membership removal, define when already issued access tokens cease to authorize sensitive operations; recheck current permissions where immediate revocation matters.

**Inject this failure:** Reuse a rotated refresh token, send a cross-site form mutation and race logout with a cached permission decision.

**Completion gate:** The session lifecycle handles expiration, rotation, revocation and CSRF according to a written contract.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-12-AUTHZ"></a>
## 12-AUTHZ - RBAC, ABAC, ownership and row-level security

**Track:** Build extension. **Source topics:** RBAC; ABAC; fine-grained permissions; object authorization; RLS; tenant isolation.

**Files to inspect or extend:** `db/rls_lab.sql`, `src/stores/pg.mjs`.

### Start / reproduce

```bash
docker compose exec -T postgres psql -U lab -d eventhub < db/rls_lab.sql
node --test --test-name-pattern="ownership|tenant" test/http.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create a role/action/resource matrix, then add attribute rules such as event organizer ownership and tenant membership.
2. Apply rls_lab.sql and test using the non-owner app_limited role. Set tenant context transaction-locally from a verified identity and prove pooled connections do not retain a previous tenant.
3. Repeat authorization for search, exports, download tickets, WebSocket rooms, background jobs and list endpoints; securing a single detail route is insufficient.

**Inject this failure:** Bob requests Alice's booking, Charlie requests org-a data, and a superuser bypasses RLS in a comparison test.

**Completion gate:** Negative tests fail at every entry point; table-owner/superuser bypass is understood and attendee ownership remains an additional rule beyond tenant RLS.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-12-CRYPTO"></a>
## 12-CRYPTO - Symmetric/asymmetric encryption, hashes and signatures

**Track:** Run reference + key-management extension. **Source topics:** AES-GCM; asymmetric keys; hashing; digital signatures; password hashing; field encryption; envelope encryption.

**Files to inspect or extend:** `src/security.mjs`.

### Start / reproduce

```bash
node --test test/security.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run authenticated-encryption tests using independent random nonces and tenant/resource AAD. Modify ciphertext, tag, key and AAD separately.
2. Contrast a checksum, keyed MAC, digital signature and password hash by the property each provides; never store passwords with an unsalted fast checksum.
3. Implement envelope encryption: generate a data key per object/tenant, wrap it under a managed master key and store key-version metadata. Rotate wrapping keys and test old-data recovery.

**Inject this failure:** Decrypt using another tenant context, reuse incorrect key metadata or lose the only wrapping key.

**Completion gate:** Integrity/authenticity failures are rejected, nonce/key lifecycle is explicit and encrypted bytes remain recoverable under the tested key-retention policy.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-12-PROTECTION"></a>
## 12-PROTECTION - Disk/database/object encryption, tokenization and masking

**Track:** Build extension. **Source topics:** encryption at rest; disk encryption; database encryption; object encryption; PII; masking; tokenization.

**Files to inspect or extend:** `src/security.mjs`, `infra/aws/autoscaling.yaml`.

### Start / reproduce

```bash
node --test test/security.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Encrypt a disposable cloud/block volume and object bucket using the platform's supported key mechanism; record who can decrypt independently from who can read storage bytes.
2. Tokenize synthetic sensitive fields into a separately protected lookup service and mask them in logs/UI according to role.
3. Add field-level encryption to an export and verify search/index/backup implications. A disk encryption setting alone does not stop an authorized but overprivileged database query.

**Inject this failure:** Export as a lower-privilege role, inspect application logs for sensitive values and restore an encrypted backup without its key.

**Completion gate:** Protection is tested at storage, query, presentation and backup boundaries rather than represented by one global encryption checkbox.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-12-INPUT"></a>
## 12-INPUT - Injection prevention, bounded input and output encoding

**Track:** Build extension. **Source topics:** SQL injection; XSS; command injection; SSRF; input validation; output escaping; resource limits.

**Files to inspect or extend:** `src/http.mjs`, `src/stores/pg.mjs`, `src/storage/chunks.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="invalid|malformed|traversal|oversized" test/*.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Keep parameterized SQL, route-specific schemas and allowlisted fields. Reject client-controlled tenant/user/status overrides.
2. Render paste content as text, not executable HTML; invoke fixed tools using argument arrays rather than shell concatenation. For upload filenames, separate display names from generated storage keys.
3. A crawler must validate scheme, host, resolved address and every redirect against an allowlist, with byte/time/redirect limits; use only local synthetic targets in the lab.

**Inject this failure:** Submit script text, traversal filenames, shell metacharacters, oversized JSON and a redirect to an internal-only fixture.

**Completion gate:** Inputs remain inert data, resource consumption is bounded and no untrusted URL can reach arbitrary internal services.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-12-HEADERS"></a>
## 12-HEADERS - Security headers, TLS and certificate rotation

**Track:** Build extension. **Source topics:** CSP; HSTS; X-Frame-Options; nosniff; Referrer-Policy; TLS; mTLS; certificate rotation.

**Files to inspect or extend:** `infra/network/Caddyfile`, `src/http.mjs`.

### Start / reproduce

```bash
docker compose -f infra/network/caddy.yaml up -d
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Add a restrictive Content-Security-Policy to the actual browser UI, frame restrictions, nosniff and a suitable referrer policy; verify no required asset silently violates the policy.
2. Use HSTS only on the intended HTTPS host after understanding its browser persistence. Validate server hostname and CA; add mTLS for sensitive internal service calls.
3. Rotate a test certificate/key pair with overlap and monitor handshake failures, without disabling verification as a workaround.

**Inject this failure:** Inject a disallowed script source in a local page, embed the page in an unauthorized frame and present an expired certificate.

**Completion gate:** The browser/network tests show enforcement and legitimate functionality remains usable; certificate trust is not bypassed.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-12-NETWORK"></a>
## 12-NETWORK - Private networks, DMZ, service accounts, IAM and service mesh

**Track:** Build extension. **Source topics:** private subnets; DMZ; segmentation; security groups; IAM; temporary credentials; service accounts; service mesh.

**Files to inspect or extend:** `infra/k8s/scheduled-floor.yaml`, `infra/aws/autoscaling.yaml`.

### Start / reproduce

```bash
kubectl -n hld-lab auth can-i patch horizontalpodautoscalers --as=system:serviceaccount:hld-lab:hpa-scheduler
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Draw edge/public, application and data network zones, allowing only the required flows. Remove host database/broker ports when not needed.
2. Give API, publisher and worker separate least-privilege identities. Use workload/service-account identity and temporary cloud credentials instead of embedding long-lived admin keys.
3. Add mTLS identity and authorization to service-to-service traffic using an explicit proxy or mesh experiment; verify that the selected CNI actually enforces any Kubernetes NetworkPolicy.

**Inject this failure:** Compromise a synthetic worker credential and attempt a schema change, unrelated tenant export or direct public database connection.

**Completion gate:** The credential and network allowlists constrain the blast radius; a manifest existing in Git is not mistaken for enforced network policy.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-12-WAF"></a>
## 12-WAF - Firewalls, WAF, quotas and defensive traffic filtering

**Track:** Build extension. **Source topics:** firewall; WAF; traffic filtering; DDoS protection; CDN scrubbing; rate limiting; load shedding.

**Files to inspect or extend:** `src/algorithms/rate-limit.mjs`, `infra/network/nginx.conf`.

### Start / reproduce

```bash
RATE=20 SECONDS=15 MAX_INFLIGHT=50 node scripts/load.mjs
node experiments/run.mjs rate-limit
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use a local reverse proxy/WAF rule set to reject disallowed methods, oversized payloads and known test signatures while measuring false positives.
2. Combine per-identity quotas with global admission control. Run only bounded synthetic load against your own isolated endpoint; do not generate attack traffic at public targets.
3. Compare network firewall rules, HTTP inspection and upstream CDN/scrubbing services by what they can observe and where traffic is dropped.

**Inject this failure:** Overload an expensive report endpoint with a capped local workload and submit a legitimate payload that resembles a rule signature.

**Completion gate:** Filtering preserves legitimate requests and prevents resource exhaustion under the tested bound; a laptop load test is not claimed to prove internet-scale DDoS protection.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-12-SECRETS"></a>
## 12-SECRETS - Secrets management and rotation without public leakage

**Track:** Build extension. **Source topics:** secrets vault; API keys; secret rotation; environment variables; key recovery.

**Files to inspect or extend:** `src/security.mjs`, `compose.yaml`.

### Start / reproduce

```bash
node --test test/security.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Inventory every lab credential and mark it synthetic. Before a non-local deployment, replace fixtures through an approved secret store and separate bootstrap/admin from runtime roles.
2. Create two overlapping credential versions, deploy readers accepting the transition, rotate writers and then revoke the old credential. Test connection-pool reconnection after rotation.
3. Scan repository, build logs, traces and backups for accidental credentials; do not assume an environment variable is automatically secret from process introspection or dumps.

**Inject this failure:** Revoke the old key during a long-lived connection and simulate loss of the only backup decryption credential.

**Completion gate:** Rotation has a tested rollback and no real secret appears in source or evidence artifacts.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-12-AUDIT"></a>
## 12-AUDIT - Audit logs, detection, incident response and governance evidence

**Track:** Governance + implementation extension. **Source topics:** audit logging; immutable logs; SIEM; anomaly detection; incident response; GDPR; HIPAA; PCI-DSS; SOC 2.

**Files to inspect or extend:** `src/http.mjs`, `src/security.mjs`.

### Start / reproduce

```bash
node --test test/http.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Emit actor, tenant, resource, action, outcome and correlation ID for authentication, permission changes, exports and administrative operations; redact tokens and sensitive payloads.
2. Create local detection rules for repeated authorization failures and unexpected exports, then route alerts into a synthetic incident workflow with containment, recovery and postmortem steps.
3. Build an evidence/control inventory for applicable governance frameworks using official requirements and qualified review. These exercises do not certify compliance or decide legal applicability.

**Inject this failure:** Try to delete audit evidence with an ordinary application credential and replay an old administrative request.

**Completion gate:** The incident can be reconstructed without exposing secrets; control evidence, current legal obligations and formal certification are kept distinct.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-12-MESSAGE-SECURITY"></a>
## 12-MESSAGE-SECURITY - Signed webhooks, replay protection and secure queues

**Track:** Build extension. **Source topics:** message authentication; encryption; broker ACLs; webhook signatures; replay protection.

**Files to inspect or extend:** `src/security.mjs`, `src/async/worker.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="webhook" test/security.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use the raw-body HMAC verification helper with timestamp tolerance; parsing and reformatting JSON before signature verification changes the signed bytes.
2. Persist event IDs with the resulting business effect so a valid replay does not repeat it. Timestamp freshness alone does not provide business deduplication.
3. Give producers and consumers separate broker permissions, encrypt transport and validate schema/tenant ownership before applying an event.

**Inject this failure:** Alter one byte, replay a valid message within the time window and send a correctly signed event about another tenant's booking.

**Completion gate:** Authentication, freshness, authorization and deduplication are separately tested; a valid signature is not unlimited permission.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-13"></a>
# 13. Maintainability

**Source chapter:** [13_Maintainability.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/13_Maintainability.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** A working core application, automated correctness tests and at least one captured failure/recovery experiment.


<a id="lab-13-BOUNDARIES"></a>
## 13-BOUNDARIES - Modular monolith, DDD boundaries and service extraction

**Track:** Build extension. **Source topics:** modularity; cohesion; loose coupling; DDD; bounded contexts; microservices; separation of concerns.

**Files to inspect or extend:** `src/stores/memory.mjs`, `src/stores/pg.mjs`, `src/async/worker.mjs`.

### Start / reproduce

```bash
node --test test/http.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Map HTTP parsing, identity, policy, booking rules, persistence, messaging and storage into explicit modules. Keep the booking transaction in one boundary initially.
2. Define import rules and behavioral contracts for store/provider interfaces; run memory and PostgreSQL adapters against the same contract plus separate real-database concurrency tests.
3. Extract notification delivery or media processing only when independent deployment/scaling is demonstrated, adding versioned messages and failure budgets.

**Inject this failure:** Change a storage adapter or provider API and verify business callers do not import its transport details.

**Completion gate:** Boundaries support an actual change without splitting atomic invariants across unnecessary network calls.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-13-PATTERNS"></a>
## 13-PATTERNS - Factory, strategy, observer, repository and dependency injection

**Track:** Build extension. **Source topics:** factory; strategy; observer; repository; dependency injection; SOLID; refactoring.

**Files to inspect or extend:** `src/http.mjs`, `src/algorithms/cache.mjs`, `src/server.mjs`.

### Start / reproduce

```bash
node --test test/*.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use a factory to construct the selected store, inject dependencies into createApp and expose a repository contract instead of direct SQL in routes.
2. Use interchangeable cache/rate-limit strategies and an event observer boundary for noncritical effects. Compare testability before and after removing global mutable dependencies.
3. Refactor one tightly coupled path with characterization tests first; keep only patterns that solve an observed change/testing problem.

**Inject this failure:** Replace a cache with a failing adapter and a notification provider with a recorder without modifying the inventory transaction.

**Completion gate:** Tests prove equivalent behavior after the refactor; adding a design-pattern name without a concrete benefit is not counted as implementation.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-13-TESTS"></a>
## 13-TESTS - Unit, property, integration, contract and end-to-end tests

**Track:** Run reference + container. **Source topics:** unit tests; integration tests; E2E; contract tests; property tests; regression tests.

**Files to inspect or extend:** `test/algorithms.test.mjs`, `test/http.test.mjs`, `test/storage.test.mjs`, `test/security.test.mjs`.

### Start / reproduce

```bash
node --test test/*.test.mjs
node scripts/smoke.mjs
node scripts/race.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the dependency-free test suite, then run the real PostgreSQL smoke/race workflow separately. A memory model does not prove database locking behavior.
2. Turn invariants into property tests: key placement, erasure reconstruction, merge convergence, inventory conservation, tenant isolation and idempotency.
3. Add provider contract tests and a browser happy/failure journey. Keep deterministic seeds, fake clocks and persisted failure histories for reproducibility.

**Inject this failure:** Randomize message duplication/reordering and shrink a failing operation history into a deterministic regression case.

**Completion gate:** Each guarantee has a test at the layer where it could fail, with integration tests clearly separated from unit models.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-13-CI"></a>
## 13-CI - CI, dependency locking and reproducible builds

**Track:** Build extension. **Source topics:** CI; CD; dependency management; lockfiles; static analysis; artifact signing; supply chain.

**Files to inspect or extend:** `.github/workflows/ci.yml`, `Dockerfile`, `package.json`.

### Start / reproduce

```bash
node --test test/*.test.mjs
# After network-enabled dependency installation: npm ci
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the provided GitHub Actions workflow on a branch you control. It separates local tests from a Docker-backed integration job.
2. Generate and retain package-lock.json after a successful npm install, use npm ci, record image digests and pin reviewed action versions for stronger reproducibility.
3. Add formatting/linting, schema compatibility, secret/dependency scans and image checks. Sign/restrict release artifacts according to the deployment trust model.

**Inject this failure:** Introduce a failing test, incompatible schema or accidental synthetic-secret marker and ensure the release gate stops.

**Completion gate:** CI outputs raw test/log evidence and cannot label an unrun integration test as passing.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-13-OBSERVABILITY"></a>
## 13-OBSERVABILITY - Logs, metrics, tracing and meaningful alerts

**Track:** Build extension. **Source topics:** structured logs; metrics; distributed tracing; OpenTelemetry; Prometheus; alerting; cardinality.

**Files to inspect or extend:** `src/http.mjs`, `infra/observability/prometheus.yml`, `infra/observability/compose.yaml`.

### Start / reproduce

```bash
docker compose -f compose.yaml -f infra/observability/compose.yaml up -d
curl http://127.0.0.1:8080/metrics
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Enable structured request logs and scrape each API instance directly using DNS service discovery, not random gateway scraping.
2. Add spans for cache, pool wait, database, outbox and worker processing with propagated trace context. Keep request IDs as log fields, not unbounded metric labels.
3. Build alerts on errors, latency, saturation, oldest outbox/message age and stalled replication; include dashboards and a runbook for each alert.

**Inject this failure:** Break one worker while request success remains high and demonstrate why endpoint availability alone misses delayed effects.

**Completion gate:** A single operation can be followed across synchronous and asynchronous boundaries, and metrics remain bounded in cardinality.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-13-DEPLOY"></a>
## 13-DEPLOY - Deployment strategies, feature flags and safe rollback

**Track:** Build extension. **Source topics:** rolling; blue-green; canary; feature flags; A/B testing; kill switch; rollback.

**Files to inspect or extend:** `src/algorithms/processing.mjs`, `infra/k8s/workload.yaml`.

### Start / reproduce

```bash
node --test --test-name-pattern="stable experiment" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Apply the availability chapter's rolling, blue-green and canary procedures to a backwards-compatible feature.
2. Use a deterministic experiment assignment and separate rollout safety flags from analytical A/B assignments. Keep a safe default and an audited kill switch.
3. Rollback code without reversing already published irreversible data transformations; use expand-contract migrations and forward repair where needed.

**Inject this failure:** Canary errors increase only for one tenant cohort, and a flag service becomes unavailable.

**Completion gate:** The rollout can be stopped using explicit metrics and preserves old clients, tenant isolation and data invariants.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-13-IAC"></a>
## 13-IAC - Infrastructure as code, immutable images and configuration

**Track:** Build extension. **Source topics:** IaC; Terraform; CloudFormation; containers; Kubernetes; immutable infrastructure; config; secrets.

**Files to inspect or extend:** `compose.yaml`, `infra/k8s/workload.yaml`, `infra/aws/autoscaling.yaml`.

### Start / reproduce

```bash
aws cloudformation validate-template --template-body file://infra/aws/autoscaling.yaml
# No cloud resources are created by this validation command.
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Bring up the same lab from clean Compose/Kubernetes definitions and compare it with a manually modified environment.
2. Create a Terraform or CloudFormation plan for your chosen sandbox topology, separate state and secrets from source, and review drift before apply.
3. Build immutable application images and replace instances/pods rather than hand-editing their running filesystem. Document environment-specific configuration and migration ordering.

**Inject this failure:** Make a manual security-group or resource-limit change, detect it as drift and reconcile deliberately rather than blindly overwriting.

**Completion gate:** The environment can be reproduced and destroyed within its scope; no production account or existing resource is silently modified.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-13-DOCS"></a>
## 13-DOCS - ADRs, API documentation, ownership and technical debt

**Track:** Documentation + implementation exercise. **Source topics:** ADRs; runbooks; API docs; code ownership; version control; technical debt; onboarding.

**Files to inspect or extend:** `docs/openapi.yaml`.

### Start / reproduce

```bash
cp evidence/TEMPLATE.md evidence/my-lab.md
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Write an ADR for each accepted architecture change including context, invariant, alternatives, evidence, decision and consequences.
2. Keep the API schema, event schema, migration runbook, dependency map and incident instructions next to the implementation. Give each component and alert an owner.
3. Track a small technical-debt backlog with measurable failure/change cost, then retire one item using characterization tests and a staged rollout.

**Inject this failure:** Ask another developer to recover the broker backlog or run a tenant migration using only the repository documentation.

**Completion gate:** The recovery and onboarding tasks complete without unwritten knowledge, and ADRs link to raw evidence rather than unsupported claims.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-14"></a>
# 14. API Design

**Source chapter:** [14_API_Design.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/ 14_API_Design.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Core HTTP tests, an API contract and a declared retry/pagination/versioning policy.


<a id="lab-14-REST"></a>
## 14-REST - Resource modeling, method semantics and stable error contracts

**Track:** Run reference. **Source topics:** REST; resources; HTTP methods; status codes; idempotency; error schema; API gateway.

**Files to inspect or extend:** `docs/openapi.yaml`, `src/http.mjs`.

### Start / reproduce

```bash
node --test test/http.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Model events, bookings, uploads and jobs as resources, with explicit request/response schemas and finite payload/page limits.
2. Implement method-specific authorization and stable machine-readable error codes. Keep authentication failures, unavailable resources, conflicts and overload distinct.
3. Test safe/idempotent HTTP semantics separately from application idempotency keys; document unknown write outcomes and retry guidance.

**Inject this failure:** Issue malformed JSON, unsupported fields, wrong methods, missing credentials and a valid-but-conflicting request.

**Completion gate:** Clients can distinguish correction, retry and reconciliation actions without inspecting internal stack traces.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-14-PAGINATION"></a>
## 14-PAGINATION - Offset, page, cursor and keyset pagination

**Track:** Build extension. **Source topics:** offset pagination; page pagination; cursor pagination; keyset pagination; stable sort; snapshot.

**Files to inspect or extend:** `db/query_lab.sql`.

### Start / reproduce

```bash
docker compose exec -T postgres psql -U lab -d eventhub < db/query_lab.sql
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Add list endpoints ordered by created_at and id. Implement offset/page variants first, then keyset predicates using both fields for a deterministic tie-breaker.
2. Encode an opaque cursor containing tenant, filter fingerprint and last tuple; authenticate it if tampering could alter scope. Validate limits and reject a cursor from another query or tenant.
3. Compare a live-list contract with an explicit snapshot token. Preserve database timestamp precision rather than converting microseconds to JavaScript milliseconds and skipping rows.

**Inject this failure:** Insert/delete records between page requests and create several rows with the same timestamp.

**Completion gate:** Pagination follows its declared live/snapshot contract without cross-tenant access or skipped same-timestamp rows.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-14-IDEMPOTENCY"></a>
## 14-IDEMPOTENCY - Transactional request deduplication and conditional updates

**Track:** Run reference + container. **Source topics:** idempotency keys; request fingerprints; scope; retention; ETag; If-Match; optimistic concurrency.

**Files to inspect or extend:** `src/stores/pg.mjs`, `src/http.mjs`.

### Start / reproduce

```bash
SAME_KEY=true node scripts/race.mjs
node --test --test-name-pattern="key|If-Match" test/http.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use the supplied booking endpoint with a key scoped by tenant, user and route. Persist the payload fingerprint and response in the same transaction as the effect.
2. Run same-key races and conflicting-payload retries; return the original logical booking rather than decrement inventory again.
3. Use If-Match for editing a representation and define how long idempotency responses are retained. Expiring a key may permit later reuse, so align retention with business/provider guarantees.

**Inject this failure:** Lose the HTTP response after commit and retry; then retry the same key with quantity changed.

**Completion gate:** One key produces one effect under the stated retention window; version conflict and retry deduplication remain distinct mechanisms.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-14-VERSION"></a>
## 14-VERSION - URI, query, header and content-negotiated versioning

**Track:** Build extension. **Source topics:** URI versioning; query versioning; header versioning; media type versioning; deprecation; compatibility.

**Files to inspect or extend:** `docs/openapi.yaml`, `src/http.mjs`.

### Start / reproduce

```bash
node --test test/http.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Expose one backwards-compatible change additively and one intentionally breaking change through an explicit new version.
2. Implement thin routing adapters for /v2, a version query parameter, an Accept-Version header and a versioned media type in separate branches; preserve one internal domain contract.
3. Add compatibility tests for an old client, document a deprecation/sunset process and count usage before removing the old path.

**Inject this failure:** Cache two header-selected versions under an incomplete key and let an old consumer encounter a removed field.

**Completion gate:** Version selection and cache variation are explicit, and client removal is based on a measured migration plan.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-14-JOBS"></a>
## 14-JOBS - Asynchronous APIs, webhooks and callback security

**Track:** Build extension. **Source topics:** 202 Accepted; job status; polling; webhooks; retry; callback signatures; replay.

**Files to inspect or extend:** `src/security.mjs`, `src/async/worker.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="webhook" test/security.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Implement POST /exports returning 202 and a Location for a tenant/owner-scoped job. Persist queued/running/succeeded/failed/cancelled states and result identity.
2. Allow polling with bounded intervals and an optional webhook subscription verified before use. Sign exact raw bytes and include event ID/timestamp.
3. Retry delivery with the same event ID and expose delivery attempts to operators; keep the job completion contract separate from webhook delivery success.

**Inject this failure:** Complete the job while the callback recipient is unavailable, duplicate a callback and revoke the recipient's permission.

**Completion gate:** Job state is authoritative and retrievable even if webhooks fail; duplicate callbacks do not repeat the consumer effect.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-14-PROTOCOLS"></a>
## 14-PROTOCOLS - REST, GraphQL, gRPC and gateway composition

**Track:** Build extension. **Source topics:** REST; GraphQL; gRPC; protocol buffers; gateway aggregation; BFF; binary serialization.

**Files to inspect or extend:** `src/graphql-server.mjs`, `src/grpc-server.mjs`, `src/catalog.proto`.

### Start / reproduce

```bash
node src/graphql-server.mjs
# In a separate terminal: node src/grpc-server.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Expose the same tenant-scoped catalog using the three supplied interfaces and compare contract/tooling effort, payload and errors.
2. Add a gateway aggregation endpoint with independent downstream deadlines and explicit optional fields; keep authorization at both the edge and owning service.
3. Exercise all four gRPC stream shapes and GraphQL query limits. Browser compatibility and internal-service use are part of the decision, not only a microbenchmark.

**Inject this failure:** One downstream is slow, one nested GraphQL field is unauthorized and a streaming client cancels early.

**Completion gate:** Interface choice preserves the same business and access invariants; no single local result is generalized as universally fastest.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-14-CACHE-SECURITY"></a>
## 14-CACHE-SECURITY - API cache controls, compression, auth and quotas

**Track:** Build extension. **Source topics:** ETag; Cache-Control; conditional GET; gzip; Brotli; API keys; OAuth; JWT; rate limits.

**Files to inspect or extend:** `infra/network/edge.conf`, `src/http.mjs`, `infra/redis/token-bucket.lua`.

### Start / reproduce

```bash
node experiments/run.mjs rate-limit
node --test test/http.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Add conditional GET with If-None-Match for public versioned catalog representations while keeping private booking responses no-store.
2. Negotiate compression and content type correctly, including Vary behavior where needed. Bind cache keys to any content-affecting identity or avoid shared caching entirely.
3. Apply verified identity, object authorization and endpoint-specific quota policies before expensive query execution.

**Inject this failure:** Reuse one user's cached response for another, ignore Vary on a version/compression dimension or bypass quota through a second API replica.

**Completion gate:** Cache, protocol and security behavior are validated together rather than optimized independently into a leak.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-15"></a>
# 15. Estimation Skills

**Source chapter:** [15_Estimation_Skills.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/15_Estimation_Skills.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Record assumptions with units; substitute measured row/payload/workload values where available.


<a id="lab-15-UNITS"></a>
## 15-UNITS - Units, powers, assumptions and measured reference latencies

**Track:** Run model. **Source topics:** DAU; MAU; QPS; TPS; bytes; bits; decimal; binary; latency reference numbers.

**Files to inspect or extend:** `experiments/run.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs estimation
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create an assumptions worksheet recording source, unit, time window and confidence for every input. Distinguish bytes/s from bits/s and GiB from decimal GB.
2. Measure local memory, disk, network and handler timings only for this machine; retain source reference figures as educational approximations, not your hardware's results.
3. Rewrite the source-style statement 1,000 users upload 1 GB as a volume first, then divide by an explicit time window before deriving bandwidth.

**Inject this failure:** Change one seconds/minutes unit or count payload replicas twice and show how the final capacity estimate changes.

**Completion gate:** Every output includes units and provenance, and no latency or hardware capacity is presented as measured unless a test produced it.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-15-TRAFFIC"></a>
## 15-TRAFFIC - Traffic, peak factors, fan-out and retry amplification

**Track:** Run model + workload. **Source topics:** traffic estimation; read/write ratio; peak factor; fan-out; retry amplification; concurrency.

**Files to inspect or extend:** `scripts/load.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs estimation
node scripts/load.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Derive average request rate from active users and actions/day, then apply a justified peak factor and read/write mix.
2. Account separately for user requests, downstream fan-out, cache misses, retries and asynchronous messages; an API request can create many internal operations.
3. Use Little's-law-style steady-state reasoning with mean time and clearly stated assumptions to estimate in-flight work, then compare it with measured concurrency.

**Inject this failure:** A celebrity event increases fan-out and a dependency failure multiplies retries at several layers.

**Completion gate:** External QPS and internal work are not conflated, and the hot-key case is not hidden by a uniform average.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-15-STORAGE"></a>
## 15-STORAGE - Database, media, replica, index and backup capacity

**Track:** Build extension. **Source topics:** storage estimation; retention; indexes; metadata; WAL; replication; backup; compression; dedup; erasure.

**Files to inspect or extend:** `db/query_lab.sql`, `src/algorithms/erasure.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs erasure
node experiments/run.mjs storage
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Measure row/table/index bytes on the fixture and extrapolate using an explicit growth/retention horizon. Add metadata, WAL/log retention and maintenance headroom.
2. Estimate attachments from upload rate, size distribution and retained versions, with dedup/compression factors labeled measured or assumed.
3. Compare three replicas with k+m erasure shards and backup chains without double-counting parity as full replicas.

**Inject this failure:** Raise retention from 30 days to a year, disable dedup and pause a replica so WAL accumulates.

**Completion gate:** The storage budget includes operational overhead and sensitive assumptions, not only logical payload bytes.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-15-NETWORK"></a>
## 15-NETWORK - Bandwidth, media bitrate, CDN hits and internal traffic

**Track:** Build extension. **Source topics:** bandwidth estimation; ingress; egress; CDN hit ratio; replication traffic; media bitrate.

**Files to inspect or extend:** `scripts/load.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs estimation
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Calculate ingress/egress separately using payload distributions and concurrency. Include TLS/protocol overhead as an explicit assumption or measurement.
2. Add object replication, interservice calls and analytics traffic; a high edge-hit ratio reduces origin traffic but not the user's total delivered bytes.
3. For video, estimate selected bitrate times concurrent streams, plus segment/manifest overhead and rendition processing/storage.

**Inject this failure:** Drop CDN hit ratio and move the home writer to another region; identify the changed origin and cross-region traffic.

**Completion gate:** The model labels bytes versus bits and distinguishes edge, origin and replication bandwidth.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-15-CACHE"></a>
## 15-CACHE - Cache sizing, working set and hot-key concentration

**Track:** Run reference. **Source topics:** working set; cache capacity; hit ratio; hot keys; Pareto; object overhead.

**Files to inspect or extend:** `src/algorithms/cache.mjs`, `src/algorithms/bloom.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs cache
node experiments/run.mjs bloom
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Measure serialized value size and actual Redis memory usage, including keys and per-entry overhead.
2. Replay uniform, skewed and shifting-hot-set traces. Estimate working-set capacity from observed reuse rather than multiplying all stored rows by a guessed percentage.
3. Relate misses to authoritative database QPS and evaluate what happens when the cache disappears.

**Inject this failure:** The hot set changes rapidly or a cache node fails, invalidating the original hit-ratio assumption.

**Completion gate:** Capacity and miss amplification estimates are backed by traces, with one hot key analyzed separately from aggregate cache size.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-15-CAPACITY"></a>
## 15-CAPACITY - Servers, headroom, N+1 and sensitivity analysis

**Track:** Build extension. **Source topics:** server count; CPU; memory; disk; network; headroom; N+1; growth; sensitivity; cost.

**Files to inspect or extend:** `scripts/load.mjs`, `src/algorithms/autoscale.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs estimation
node experiments/run.mjs autoscaling
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use measured sustainable per-instance capacity at the required error/latency objective, not a theoretical peak, to estimate instance count.
2. Apply operational utilization and one-failure headroom; then recompute after removing a node. Evaluate CPU, memory, connection and storage limits independently.
3. Build low/base/high scenarios for DAU, peak factor, average payload, cache hits and provider latency. Use current sandbox pricing only when making a cost decision.

**Inject this failure:** Double the peak factor or lose the largest node while a rebuild consumes I/O.

**Completion gate:** The design includes a bottleneck-driven capacity plan, uncertainty ranges and the next measurement needed before procurement.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-16"></a>
# 16. HLD Architecture and Frameworks

**Source chapter:** [16_HLD_Architecture_and_Frameworks.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/16_HLD_Architecture_and_Frameworks.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Completed critical-path labs and evidence. Architectural boxes must reference actual code, configuration or an explicit planned extension.


<a id="lab-16-REQUIREMENTS"></a>
## 16-REQUIREMENTS - Requirements, non-functional goals and the design frameworks

**Track:** Design + implementation review. **Source topics:** four design questions; six-step framework; five-step rapid framework; requirements; NFRs.

**Files to inspect or extend:** `docs/openapi.yaml`.

### Start / reproduce

```bash
node experiments/run.mjs estimation
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use EventHub as a client brief: identify core actors/actions, scope exclusions, read/write mix, consistency-sensitive operations, realtime needs and expected scale.
2. Walk the source's clarify -> estimate -> API/data -> high-level diagram -> deep dive -> bottleneck/tradeoff sequence, with one testable requirement at every step.
3. Repeat as a short five-step review, retaining links to actual code and evidence rather than rehearsed technology names.

**Inject this failure:** The stakeholder changes availability, privacy or growth assumptions after the first diagram.

**Completion gate:** The revised design traces every important component to a requirement and identifies what evidence would invalidate a decision.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-16-LAYERS"></a>
## 16-LAYERS - Seven-layer architecture and synchronous/asynchronous boundaries

**Track:** Design + container. **Source topics:** client edge; entry/load balancing; application; cache; persistence; async; analytics.

**Files to inspect or extend:** `compose.yaml`, `src/stores/pg.mjs`, `src/async/worker.mjs`.

### Start / reproduce

```bash
docker compose config
node scripts/smoke.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Draw the actual deployed lab with seven layers and label every arrow synchronous, asynchronous or internal/monitoring.
2. Trace one booking through each relevant component, showing the transaction boundary, response acknowledgment and later notification path.
3. Remove any box that has no implemented responsibility. Identify the control plane separately from the data plane and document which layer owns auth, quotas and retries.

**Inject this failure:** Stop one component and predict then measure which arrows fail and which operations remain available.

**Completion gate:** The diagram matches real processes, ports and schemas instead of an aspirational diagram disconnected from the code.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-16-ARCHETYPES"></a>
## 16-ARCHETYPES - Read-heavy, write-heavy, realtime, financial, media, search and geo archetypes

**Track:** Design + reference experiments. **Source topics:** seven archetypes; decision trees; polyglot persistence; architecture selection.

**Files to inspect or extend:** `src/algorithms/processing.mjs`, `src/algorithms/seat-holds.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs all
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create one workload/invariant brief for each of the seven source archetypes and select the dominant access path.
2. Build a minimal slice using the relevant prior labs: feed projection, async ingest, realtime room, balanced ledger, chunk/transcode pipeline, inverted index or location search.
3. Compare alternatives under the same brief and change one assumption to force a different decision. Keep all transactions, keys and failure policies explicit.

**Inject this failure:** Apply a read-heavy eventual-cache design to a one-seat booking in a failing model, or a global synchronous transaction to disposable analytics.

**Completion gate:** The choice is driven by the operation's invariant and workload, not an arbitrary universal stack.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-16-MIGRATION"></a>
## 16-MIGRATION - Design for 100x growth without unsafe big-bang migration

**Track:** Build extension. **Source topics:** 100x scaling; service extraction; schema migration; resharding; multi-region; compatibility.

**Files to inspect or extend:** `scripts/migrate-tenant.mjs`.

### Start / reproduce

```bash
node scripts/migrate-tenant.mjs org-a c
# Run inside the isolated sharding router, not against an arbitrary DATABASE_URL.
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the measured baseline through the source's next-level scaling questions and identify the first actual bottleneck.
2. Plan a phased migration with dual-compatible readers, a backfill/catch-up boundary, explicit writer ownership, validation and rollback. Use the tenant migration as the concrete reference.
3. Scale only the necessary layer; include failure cases where a migration controller, queue or permission service becomes unavailable.

**Inject this failure:** Crash after copying data but before cutover and let an old client continue with a stale route or schema.

**Completion gate:** The new design preserves acknowledged writes and old-client compatibility, with a reversible boundary documented before cutover.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-16-MAP70"></a>
## 16-MAP70 - Implement the 70-problem master map as concrete variants

**Track:** Project variant assignments. **Source topics:** 70-problem master map; traffic control; global infrastructure; feeds; communication; media; geo; search; finance; concurrency; storage; big data; productivity.

**Files to inspect or extend:** `docs/VARIANTS_70.md`.

### Start / reproduce

```bash
node experiments/run.mjs list
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use VARIANTS_70.md: every named source problem maps to an implementation base, a distinct code/schema extension and a failure acceptance gate.
2. Pick one variant from each of the twelve source categories and build its unique increment, not just a renamed EventHub endpoint.
3. For each completed variant include API/data model, routing key, acknowledgment boundary, load profile, failure evidence and a decision memo.

**Inject this failure:** Carry the wrong invariant from a neighboring problem, such as allowing duplicate wallet effects because duplicate analytics were acceptable.

**Completion gate:** The artifact set distinguishes 70 specified project variants from 70 already-built production systems; each implemented variant has its own test evidence.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-16-REVIEW"></a>
## 16-REVIEW - Architecture reviews, decision matrices and source qualifications

**Track:** Review exercise. **Source topics:** tradeoffs; anti-patterns; decision matrix; staff-level design; CLEAR; review.

**Files to inspect or extend:** `docs/SOURCE_INTERPRETATION.md`.

### Start / reproduce

```bash
node --test test/*.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Review one design against requirements, capacity, APIs/data, critical path, failures, security, operations and evolution.
2. Use the source decision frameworks but record explicit qualifications where examples are simplifications: replication is not backup; quorum overlap alone is not every consistency guarantee; a Redis lease is not final seat ownership.
3. Complete the chapter's self-test by explaining one accepted strategy, one rejected alternative and one remaining failure case using observed evidence.

**Inject this failure:** A reviewer removes a simplifying assumption such as bounded clock skew, durable dedup retention or independent failure domains.

**Completion gate:** The design explains the impact and repair path without inventing guarantees that the implementation does not provide.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-17"></a>
# 17. Roadmaps and Study Plans

**Source chapter:** [17_Roadmaps_and_Study_Plans.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/17_Roadmaps_and_Study_Plans.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Complete the corresponding foundational labs before each pattern; do not deploy all patterns as default dependencies.


<a id="lab-17-CRUD"></a>
## 17-CRUD - Pattern 1: CRUD and storage platform

**Track:** Build extension. **Source topics:** CRUD; URL shortener; Pastebin; metadata; permissions; durable storage.

**Files to inspect or extend:** `src/stores/pg.mjs`, `src/storage/chunks.mjs`.

### Start / reproduce

```bash
node scripts/smoke.mjs
node --test test/storage.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Build tenant-scoped create/read/update/delete for events and files using the core repository and optimistic versions.
2. Add immutable attachment storage with metadata states, object authorization and a retention/deletion workflow.
3. Run the URL-shortener and Pastebin capstones as specialized storage paths, comparing cacheable public data with private records.

**Inject this failure:** Crash between object upload and metadata commit, then attempt another tenant's read.

**Completion gate:** There is no published missing object, no cross-tenant access and every acknowledged durable mutation is recoverable under the tested fault model.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-17-FEED"></a>
## 17-FEED - Pattern 2: Feed/timeline push, pull and hybrid fan-out

**Track:** Build extension. **Source topics:** news feed; fan-out on write; fan-out on read; hybrid; celebrity; ranking; pagination.

**Files to inspect or extend:** `src/async/worker.mjs`, `src/algorithms/processing.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs search
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Add follows and immutable posts. Implement pull by querying followed authors, push by writing post references into follower inboxes, and hybrid by excluding high-fanout authors from push.
2. Deduplicate inbox entries on follower/post, build with a checkpointed worker and merge celebrity posts at read time.
3. Use stable ranking tie-breakers and a declared live/snapshot pagination contract. Recheck visibility when serving a previously materialized reference.

**Inject this failure:** A celebrity posts during worker backlog, a user unfollows, or a private post was already fanned out before permissions changed.

**Completion gate:** Feed references are duplicate-safe, stale permissions do not leak content and fan-out cost is measured under skew.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-17-REALTIME"></a>
## 17-REALTIME - Pattern 3: Chat, presence and notifications

**Track:** Build extension. **Source topics:** chat; WebSocket; presence; offline delivery; read receipts; notification fan-out.

**Files to inspect or extend:** `src/realtime.mjs`, `src/async/worker.mjs`.

### Start / reproduce

```bash
node src/realtime.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Persist room messages with room sequence and client_message_id before acknowledging send; use an outbox to fan out committed messages.
2. Add reconnect from sequence, bounded gap recovery and offline notifications in a separate subscriber.
3. Presence uses heartbeat TTL and is not message durability; read receipts and delivered acknowledgments have distinct meanings.

**Inject this failure:** Disconnect after commit but before send ACK, then reconnect to a different replica and resend the same client ID.

**Completion gate:** The sender sees one logical message, offline clients can recover history and presence loss does not erase messages.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-17-SEARCH"></a>
## 17-SEARCH - Pattern 4: Search, autocomplete and indexing

**Track:** Build extension. **Source topics:** inverted index; autocomplete; full-text; CDC; ranking; ACL filtering.

**Files to inspect or extend:** `src/algorithms/processing.mjs`, `db/query_lab.sql`.

### Start / reproduce

```bash
node experiments/run.mjs search
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Build a versioned inverted index from committed catalog events; reject old updates and preserve deletion tombstones.
2. Add prefix suggestions with a bounded trie/top-K candidate cache, and compare with PostgreSQL full-text search or a search engine adapter.
3. Rebuild into a new index, catch up from a durable boundary, verify counts/sample queries and atomically switch the search alias.

**Inject this failure:** Deliver deletion before an older update, rebuild during live writes and request an unauthorized document via search.

**Completion gate:** Rebuilds do not resurrect deletions, old events cannot replace newer versions and search never bypasses current access rules.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-17-LIMITER"></a>
## 17-LIMITER - Pattern 5: Distributed rate-limiting service

**Track:** Build extension. **Source topics:** distributed limiter; five algorithms; tenant budgets; API gateway; fairness.

**Files to inspect or extend:** `infra/redis/token-bucket.lua`, `infra/redis/sliding-log.lua`.

### Start / reproduce

```bash
node experiments/run.mjs rate-limit
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Expose an internal check/reserve API using a selected algorithm and server-side identity, with a separate administrative configuration plane.
2. Implement token bucket and sliding-log admission in Redis Lua; compare the other three local models under the same trace before integrating them.
3. Add tenant and per-route quotas, bounded configuration propagation and an explicit degraded policy when the store is unavailable.

**Inject this failure:** Two gateways race on the same quota key and one instance loses access to Redis.

**Completion gate:** The global quota is not multiplied by gateway count and store failure behavior is documented and tested.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-17-OBSERVE"></a>
## 17-OBSERVE - Pattern 6: Logging, metrics and monitoring pipeline

**Track:** Build extension. **Source topics:** logs; metrics; monitoring; time series; sampling; cardinality; alerts.

**Files to inspect or extend:** `infra/observability/prometheus.yml`, `src/http.mjs`.

### Start / reproduce

```bash
docker compose -f compose.yaml -f infra/observability/compose.yaml up -d
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Emit structured records from API and workers, buffer them with bounded backpressure and ingest into a separate observability store.
2. Separate high-cardinality logs from bounded metric labels; define retention, redaction and tenant access policies.
3. Build availability, latency, backlog-age and replication-lag alerts with runbooks and test them under a controlled fault.

**Inject this failure:** The observability sink fails or a user-controlled path creates millions of potential labels in a naive branch.

**Completion gate:** Business operations do not block indefinitely on telemetry, and the alert system detects failures outside HTTP availability.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-17-CACHE"></a>
## 17-CACHE - Pattern 7: Distributed cache service

**Track:** Build extension. **Source topics:** distributed cache; hash ring; eviction; replication; invalidation; hot keys.

**Files to inspect or extend:** `src/algorithms/sharding.mjs`, `src/algorithms/cache.mjs`, `infra/redis/cluster.yaml`.

### Start / reproduce

```bash
node experiments/run.mjs sharding
node experiments/run.mjs cache
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Expose get/set/delete through a router using consistent hashing and virtual nodes, then compare with Redis Cluster's actual slot routing.
2. Implement the five cache strategies, bounded memory/TTL, a fill-coordination policy and an explicit replication/failover contract.
3. Test node addition/removal without routing existing authoritative data blindly; cache contents may be refilled, but quota/lease state needs a separate design.

**Inject this failure:** Remove a hot-key node during a miss storm and lose an invalidation message.

**Completion gate:** Memory and backend fetches remain bounded, and failover staleness/loss assumptions are stated.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-17-RECOMMEND"></a>
## 17-RECOMMEND - Pattern 8: Recommendation pipeline and online serving

**Track:** Build extension. **Source topics:** recommendations; candidate generation; ranking; features; batch; stream; fallback.

**Files to inspect or extend:** `src/algorithms/processing.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs search
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create synthetic user/event interaction records with event-time and consent/retention attributes.
2. Build offline popularity/co-occurrence candidates, materialize versioned features and rank a bounded candidate list online with a strict timeout.
3. Use a safe default list when features are missing or the model is unavailable. Log model/feature version for comparison and a deterministic experiment assignment.

**Inject this failure:** Delay features, introduce a popularity shift and remove an event's visibility after it entered the candidate cache.

**Completion gate:** Recommendations meet the response budget, respect current visibility and can be traced to model/feature versions without fabricated quality metrics.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-17-PAYMENT"></a>
## 17-PAYMENT - Pattern 9: Payment, wallet and financial ledger

**Track:** Build extension. **Source topics:** payment gateway; double-entry ledger; wallet; idempotency; reconciliation; saga.

**Files to inspect or extend:** `src/algorithms/seat-holds.mjs`, `src/security.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs booking
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use mock money only. Represent amounts in integer minor units and post balanced per-currency journal entries with unique operation keys.
2. Add reserve/capture/refund state machines and a fake provider that can commit before dropping its response.
3. Reconcile provider history with local payment attempts before repeating an unknown operation; compensate through new entries/events rather than rewriting journal history.

**Inject this failure:** Duplicate a webhook, time out after provider success and crash after journal commit but before acknowledgment.

**Completion gate:** Entries balance, duplicate keys cannot repeat the economic effect and ambiguous outcomes remain visible until reconciled.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-17-PROCESSING"></a>
## 17-PROCESSING - Pattern 10: Large-scale batch and stream processing

**Track:** Build extension. **Source topics:** batch; streaming; ETL; MapReduce; watermarks; checkpoints; Top K; late events.

**Files to inspect or extend:** `src/algorithms/processing.mjs`, `infra/messaging/kafka.yaml`.

### Start / reproduce

```bash
node experiments/run.mjs search
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Ingest immutable synthetic click events with IDs, event times and partition keys. Build a batch aggregation and an incremental stream projection.
2. Persist checkpoints with output effects, deduplicate event IDs and define event-time windows, lateness allowance and correction behavior.
3. Implement exact Top-K for a bounded dataset and a Count-Min approximate candidate path, measuring error against the exact result.

**Inject this failure:** Replay a partition, deliver late events after a watermark and crash between output write and offset checkpoint.

**Completion gate:** Reprocessing is deterministic under the declared window policy and approximate counts are not reported as exact.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-17-GEO"></a>
## 17-GEO - Pattern 11: Nearby search, matching and location updates

**Track:** Build extension. **Source topics:** geospatial; GeoHash; spatial index; nearby search; matching; routing; location TTL.

**Files to inspect or extend:** `src/algorithms/processing.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="geo distance" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Store synthetic venue/worker points with update timestamps and versions. Query candidates using Redis GEO, PostGIS or a bounded geohash-neighbor search.
2. Expand into neighboring cells and apply an exact distance filter; geohash equality alone is not a radius query.
3. Assign a selected worker with a compare-and-set transaction and expire stale locations. Use a graph shortest-path model separately from proximity.

**Inject this failure:** Two requests select the same worker, a worker crosses a cell boundary or a stale location arrives after a newer one.

**Completion gate:** Only one assignment wins, nearby results use the intended distance units and stale location versions cannot replace new ones.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-17-COLLAB"></a>
## 17-COLLAB - Pattern 12: OT/CRDT collaboration and conflict handling

**Track:** Build extension. **Source topics:** collaboration; OT; CRDT; offline edits; convergence; snapshots; tombstones.

**Files to inspect or extend:** `src/algorithms/consistency.mjs`, `src/realtime.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="counter|observed-remove" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Run the supplied CRDT counter/set models to establish merge properties, then integrate a maintained collaborative-text library such as Yjs for actual text rather than pretending a counter is a document editor.
2. Persist authenticated updates with stable document identity, reconnect replay and versioned snapshots.
3. Compare a file conflict-copy policy with OT/CRDT merging for an editable document; enforce current access on submissions and snapshot downloads.

**Inject this failure:** Partition two editors, perform concurrent inserts/deletes, reconnect repeatedly and revoke one editor while offline.

**Completion gate:** The selected algorithm converges on the tested edit history and revoked users cannot commit accepted new edits after the chosen revocation boundary.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-17-UTILITIES"></a>
## 17-UTILITIES - Pattern 13: IDs, schedulers, leases and infrastructure utilities

**Track:** Build extension. **Source topics:** Snowflake; ID allocation; distributed scheduler; leader election; leases; fencing.

**Files to inspect or extend:** `src/algorithms/ids.mjs`, `infra/consensus.yaml`, `src/stores/sharded.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs ids
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use Snowflake with unique node ownership, rollback detection and persisted last timestamp/sequence; compare with random IDs and leased database ID blocks.
2. Create a due-job table and claim work with a lease/version using a short transaction, recording a stable execution ID.
3. Use coordination leases for ownership, but check a fencing epoch at the stateful sink to reject an expired worker that resumes late.

**Inject this failure:** Reuse a node ID, move the clock backward, expire a job lease and let the old owner return after reassignment.

**Completion gate:** IDs stay unique under the stated lease/restart assumptions and duplicate job delivery cannot duplicate the protected effect.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-17-CRAWLER"></a>
## 17-CRAWLER - Pattern 14: Bounded crawler and indexing frontier

**Track:** Build extension. **Source topics:** crawler; URL normalization; deduplication; frontier; per-host politeness; robots; SSRF.

**Files to inspect or extend:** `src/algorithms/bloom.mjs`, `src/algorithms/processing.mjs`.

### Start / reproduce

```bash
# Run only against the local allowlisted fixture created for this lab.
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create a local fixture website with cycles, duplicate URLs, redirects, slow pages and explicit robots rules. Restrict the crawler to that fixture host/network.
2. Normalize URLs, maintain a durable frontier and fetched-generation record, and apply per-host concurrency and delay budgets.
3. Validate every redirect destination and resolved address, limit bytes/time/content type and emit content-hash/versioned indexing events.

**Inject this failure:** A fixture redirects to an unapproved internal address, creates an infinite URL space or never completes its response.

**Completion gate:** Only approved targets are fetched, cycles do not cause unbounded work and indexing replay is duplicate-safe.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-17-STUDY"></a>
## 17-STUDY - Roadmaps, study plans, review trackers and cross-pattern mastery

**Track:** Study workflow. **Source topics:** 8-week plan; 120-day plan; top-20; top-30; four-sheet tracker; nine pillars; NFR glossary; CLEAR.

**Files to inspect or extend:** `docs/STUDY_PLAN.md`, `evidence/TEMPLATE.md`.

### Start / reproduce

```bash
node experiments/run.mjs list
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use the source's eight-week and 120-day structures as scheduling templates, not proof that every learner needs the same number of hours.
2. Track four views: topic coverage, implementation evidence, project variants and review mistakes. The HTML workbook stores local completion checkboxes; export evidence files separately.
3. For each study cycle, build one baseline, break it, improve it and explain the tradeoff. Revisit the source's prioritized interview sets and nine-pillar/NFR glossary using concrete evidence from completed labs.

**Inject this failure:** A topic is marked studied but has no running artifact or failure test; move it back to implementation pending.

**Completion gate:** Completion is based on evidence and review quality, not merely reading a chapter or ticking an unchecked code recipe.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-18"></a>
# 18. Case Study: URL Shortener

**Source chapter:** [18_Case_Study_URL_Shortener.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/18_Case_Study_URL_Shortener.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Core API/database, key allocation, cache and asynchronous processing labs.


<a id="lab-18-CREATE"></a>
## 18-CREATE - URL shortener: key generation and durable create path

**Track:** Build extension. **Source topics:** URL shortener; random keys; Base62; hash collision; sequential IDs; Snowflake; key-generation service; custom alias.

**Files to inspect or extend:** `src/algorithms/ids.mjs`, `src/common.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs ids
node --test --test-name-pattern="Base62|random slugs|Snowflake" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create links(slug PRIMARY KEY,tenant_id,owner_id,target_url,expires_at,version,status) and a create-idempotency table. Validate only approved URL schemes; never fetch targets during creation.
2. Implement random Base62 with unique-constraint retry, sequence-to-Base62, hash-prefix with collision handling and Snowflake-to-Base62 variants. Compare enumeration risk and key-service dependence.
3. For a preallocated KGS, lease nonoverlapping ranges transactionally, persist ownership and never recycle a possibly issued range. Create custom aliases with one global uniqueness authority.

**Inject this failure:** Force a collision with a tiny test alphabet, race two custom aliases and crash a KGS after handing out IDs but before acknowledgment.

**Completion gate:** Unique links remain unique, retries return one logical link and leased key blocks do not overlap after restart.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-18-REDIRECT"></a>
## 18-REDIRECT - Redirect path, expiry, caching and hot keys

**Track:** Build extension. **Source topics:** 301; 302; redirect; TTL; cache-aside; CDN; hot link; Bloom filter.

**Files to inspect or extend:** `src/algorithms/cache.mjs`, `src/algorithms/bloom.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs cache
node experiments/run.mjs bloom
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Implement GET /:slug with a bounded cache lookup, authoritative fallback, owner-controlled deletion and a server-side logical expiry check on every representation.
2. Choose 301 versus 302 deliberately: permanent client caches complicate mutable targets and revocation. Key cached values by slug/version and never retain beyond link expiry.
3. Add hot-link replication/L1 caching and a Bloom filter only when its generation is complete; reject malformed slugs early without hiding valid new ones.

**Inject this failure:** Expire a link while it is cached, update its target after a permanent redirect and lose a hot cache node.

**Completion gate:** Expired/deleted links obey the declared contract and traffic spikes do not turn into unbounded origin queries.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-18-ANALYTICS"></a>
## 18-ANALYTICS - Asynchronous click analytics, abuse controls and privacy

**Track:** Build extension. **Source topics:** click analytics; async events; rate limiting; abuse prevention; privacy; retention.

**Files to inspect or extend:** `src/async/worker.mjs`, `src/algorithms/processing.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs rate-limit
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Emit a bounded click event after the redirect decision, keeping analytics out of the critical redirect dependency chain.
2. Aggregate by slug/time bucket with deduplication where needed and a documented at-most/at-least event collection contract.
3. Apply per-creator quotas, an abuse-report/takedown workflow and limited/redacted client metadata retention; do not build an unrestricted URL-fetching scanner inside the redirect service.

**Inject this failure:** Stop analytics, redeliver click batches and revoke a malicious synthetic link while it is hot-cached.

**Completion gate:** Redirect correctness does not depend on analytics availability and takedown propagation is measured rather than assumed instant.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-18-SCALE"></a>
## 18-SCALE - Global shortener, sharding, replication and recovery

**Track:** Build extension. **Source topics:** read-heavy scaling; geo routing; sharding; replication; global alias uniqueness; capacity.

**Files to inspect or extend:** `src/stores/sharded.mjs`, `infra/replication/compose.yaml`.

### Start / reproduce

```bash
node experiments/run.mjs sharding
node experiments/run.mjs estimation
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use the source's traffic estimates only as scenario inputs; derive required capacity from your local benchmark and explicit growth assumptions.
2. Shard immutable/random-key records and route custom-alias creation through an authority that can guarantee global uniqueness. Define region-specific read staleness and deletion/expiry propagation.
3. Run cold-cache, hot-slug, primary loss, reshard and backup-restore drills, recording acknowledgment histories and degraded behavior.

**Inject this failure:** Two regions claim the same custom alias while partitioned, or a cached redirect outlives its authoritative expiry.

**Completion gate:** The final design explains create and redirect consistency independently and recovers durable link records without silent alias conflicts.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-19"></a>
# 19. Case Study: Pastebin

**Source chapter:** [19_Case_Study_Pastebin.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/19_Case_Study_Pastebin.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** URL key allocation, object/metadata publication, expiry, authorization and cache safety labs.


<a id="lab-19-STORE"></a>
## 19-STORE - Pastebin: bytes, metadata and publication state machine

**Track:** Build extension. **Source topics:** Pastebin; large text; object storage; metadata; KGS; checksums; atomic publication.

**Files to inspect or extend:** `src/storage/chunks.mjs`, `src/algorithms/ids.mjs`.

### Start / reproduce

```bash
node --test test/storage.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create paste metadata with tenant, owner, slug, visibility, version, expiry, object key, size/hash and uploading/available/deleted states.
2. Validate a stated text-size bound, stream bytes into immutable storage and publish available metadata only after verification; reuse the URL-shortener key strategies where appropriate.
3. Keep orphan cleanup separate from metadata commit and expose retries through a stable upload/create operation ID.

**Inject this failure:** Crash after byte upload but before metadata commit, then retry the same creation and interrupt cleanup.

**Completion gate:** An available paste never points to missing/unverified bytes and abandoned uploads are eventually reclaimed without deleting active work.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-19-EXPIRE"></a>
## 19-EXPIRE - Three-layer expiry and cache-safe deletion

**Track:** Build extension. **Source topics:** logical expiry; cache TTL; physical TTL cleanup; deletion; object lifecycle.

**Files to inspect or extend:** `src/algorithms/cache.mjs`, `src/async/worker.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="TTL|generation fence" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Enforce expires_at in the read path even while physical cleanup is delayed. Set each cached representation's TTL no later than its remaining lifetime.
2. Use a durable expiry/cleanup worker to delete or tombstone metadata and later reclaim unreferenced bytes with a grace period.
3. Separate logical unavailability, cache invalidation and physical deletion; provider TTL systems may remove records asynchronously, so background deletion is not the access-control boundary.

**Inject this failure:** Keep the cleanup worker stopped beyond expiry, warm the cache before expiry and race extension/deletion with an old cleanup task.

**Completion gate:** Expired content is not served while physical bytes may still await cleanup; version fencing prevents a stale task deleting a renewed version.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-19-VISIBILITY"></a>
## 19-VISIBILITY - Public, unlisted and private pastes

**Track:** Build extension. **Source topics:** public; unlisted; private; authorization; XSS; syntax highlighting; search.

**Files to inspect or extend:** `src/algorithms/processing.mjs`, `src/http.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs search
node --test --test-name-pattern="ownership" test/http.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Implement distinct visibility semantics: public may be indexed, unlisted is discoverable only by capability/URL but is not equivalent to private, and private requires current authorization.
2. Serve paste text as inert text or escaped markup; isolate syntax highlighting and disallow active HTML execution.
3. Add public full-text search with versioned deletion propagation and no private/unlisted indexing unless the product contract explicitly allows it.

**Inject this failure:** Paste script markup, guess a private slug and deliver a stale search update after deletion.

**Completion gate:** Text cannot execute in the viewer and search/cache/download paths enforce the same current visibility contract.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-19-RECOVER"></a>
## 19-RECOVER - Pastebin growth, abuse limits and disaster recovery

**Track:** Build extension. **Source topics:** read/write scaling; CDN; rate limits; multi-region; backup; consistency; retention.

**Files to inspect or extend:** `src/storage/chunks.mjs`, `infra/pitr/compose.yaml`.

### Start / reproduce

```bash
node experiments/run.mjs storage
node experiments/run.mjs estimation
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Benchmark small/hot and large/rare pastes separately; use the source's growth scenarios as explicit assumptions, not measured demand.
2. Scale metadata reads independently from blob transfer and apply per-user storage/creation limits and bounded range/download work.
3. Restore a consistent metadata/byte/key snapshot, verify sample and full manifest hashes, and replay post-backup changes before cutover.

**Inject this failure:** Restore metadata without its corresponding objects, lose a cache region and replay an old deletion after a version change.

**Completion gate:** The recovery drill checks content and permissions, not only metadata row counts; global freshness and deletion guarantees remain explicit.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-20"></a>
# 20. Case Study: BookMyShow

**Source chapter:** [20_Case_Study_Book_My_Show.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/20_Case_Study_Book_My_Show.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Database concurrency, atomic idempotency, outbox, bounded admission and mock payment/ledger foundations.


<a id="lab-20-CATALOG"></a>
## 20-CATALOG - BookMyShow: catalog, search, shows and numbered-seat schema

**Track:** Build extension. **Source topics:** movie/event catalog; shows; venues; geosearch; seat inventory; read model.

**Files to inspect or extend:** `db/seat_holds.sql`, `src/algorithms/processing.mjs`.

### Start / reproduce

```bash
docker compose exec -T postgres psql -U lab -d eventhub < db/seat_holds.sql
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Model venues, shows and show_seats with a unique (tenant,show,seat) key. Build a read-optimized search/catalog projection separate from authoritative sale state.
2. Add price/version metadata and current show eligibility rules; display a seat map with a version, not an authoritative client-side promise.
3. Use geo/full-text indexes for venue discovery and keep hot-show reads cached with an explicit freshness contract.

**Inject this failure:** Show a stale seat map and submit an offer using an old price/show version.

**Completion gate:** Browse/search remain scalable while the booking transaction revalidates availability and applicable terms against current authority.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-20-HOLDS"></a>
## 20-HOLDS - Atomic multi-seat holds and expiration fencing

**Track:** Build extension. **Source topics:** AVAILABLE; HELD; BOOKED; row locks; hold expiry; fencing; atomic multi-seat booking.

**Files to inspect or extend:** `db/seat_holds.sql`, `src/algorithms/seat-holds.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs booking
node --test --test-name-pattern="hold|late payment" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use lab_acquire_hold to lock requested seats in a stable order and sample clock_timestamp only after lock acquisition.
2. Acquire all requested seats or none, recording owner, hold ID, expiry and version. Releasing an old hold must match its identity so it cannot release a later holder.
3. Build hold-confirm and expiry-sweeper transactions using the same lock order and server-time recheck. Redis may throttle admission but does not replace the authoritative seat invariant.

**Inject this failure:** Race 100 buyers for one seat, overlap two multi-seat requests and resume an expired holder after replacement.

**Completion gate:** At most one valid owner exists for a seat, no request receives a partial hold and stale release/expiry actions cannot free a new hold.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-20-PAYMENT"></a>
## 20-PAYMENT - Mock payments, webhooks and late-success compensation

**Track:** Build extension. **Source topics:** payment idempotency; unknown outcome; signed callbacks; refund; saga; reconciliation.

**Files to inspect or extend:** `src/algorithms/seat-holds.mjs`, `src/security.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="late payment|ledger" test/algorithms.test.mjs
node --test --test-name-pattern="webhook" test/security.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create a payment attempt associated with the hold and a stable provider operation key; do not hold database row locks across the network call.
2. Persist provider callbacks by event ID with the payment transition, verify raw signatures and reject mismatched hold/amount/currency ownership.
3. On success, re-lock/recheck the matching current hold before booking. If the hold expired or was replaced, record refund-pending compensation rather than taking someone else's seat.

**Inject this failure:** Time out after provider success, receive duplicate/out-of-order callbacks and fail a refund request temporarily.

**Completion gate:** One payment effect is reconciled to one booking or a visible compensation; ambiguous outcomes do not disappear as generic errors.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-20-FLASH"></a>
## 20-FLASH - Waiting room, flash-sale admission and realtime seat maps

**Track:** Build extension. **Source topics:** flash sale; waiting room; fairness; admission control; WebSocket; backpressure; hot key.

**Files to inspect or extend:** `src/realtime.mjs`, `src/algorithms/rate-limit.mjs`, `db/seat_holds.sql`.

### Start / reproduce

```bash
node scripts/race.mjs
node experiments/run.mjs rate-limit
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Introduce a bounded waiting-room token queue ahead of the hot show, with signed admission tokens, expiry and a per-tenant/event capacity budget.
2. Broadcast committed seat-version changes through durable events and realtime fan-out; clients detecting a gap must fetch a snapshot.
3. Measure admitted versus rejected attempts, queue age and database contention. Displayed availability is advisory until the hold transaction succeeds.

**Inject this failure:** Lose a realtime message, reconnect to another node, replay an expired admission token and overload the show beyond sustainable capacity.

**Completion gate:** No overselling occurs, queue age stays bounded and stale client displays cannot bypass authoritative checks.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-20-CANCEL"></a>
## 20-CANCEL - Cancellations, refund accounting and ticket validation

**Track:** Build extension. **Source topics:** cancellation; refund idempotency; ticket identity; validation; reconciliation; audit.

**Files to inspect or extend:** `src/algorithms/seat-holds.mjs`, `src/security.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs booking
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Add a booking cancellation transition with a stable operation key and a business-specific eligibility policy. Release seats and write the refund/outbox instruction in one local transaction.
2. Use balanced journal entries for mock capture/refund amounts, preserving immutable history instead of mutating a single balance silently.
3. Issue a signed/opaque ticket identity and atomically record first validation; a replay should produce an explicit already-used outcome, not a second admission.

**Inject this failure:** Duplicate cancellation/refund requests and scan the same ticket concurrently at two mock gates.

**Completion gate:** Inventory, ledger and ticket-use invariants remain consistent under duplicates; refund failure stays visible and repairable.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-20-FAILOVER"></a>
## 20-FAILOVER - Home-show sharding, regional failover and final sale proof

**Track:** Build extension. **Source topics:** show sharding; home writer; replication; fenced failover; backups; RPO; RTO.

**Files to inspect or extend:** `infra/replication/compose.yaml`, `db/seat_holds.sql`, `db/invariants.sql`.

### Start / reproduce

```bash
sh scripts/replication.sh inspect
node scripts/race.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Assign each show a single home writer/shard and keep all seats in a multi-seat transaction co-located.
2. Test read-only catalog replicas and explicit session/version fallback, then perform fenced writer failover with acknowledged booking reconciliation.
3. Restore a backup into an isolated environment, disable external payment effects, reconcile provider keys and run inventory/ledger/hold-state checks before reopening sales.

**Inject this failure:** The primary disappears during a sold-out flash sale with some client responses missing.

**Completion gate:** The final evidence proves no duplicate seat ownership, no duplicate mock payment and a documented treatment of every ambiguous operation.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="chapter-21"></a>
# 21. Case Study: Google Drive / Dropbox

**Source chapter:** [21_Case_Study_Google_Drive_Dropbox.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/ 21_Case_Study_Google_Drive_Dropbox.md). The following implementations and acceptance tests are newly authored.

**Prerequisites:** Storage/checksums, tenant authorization, version-conditional updates, commit-safe cursors and restore foundations.


<a id="lab-21-MODEL"></a>
## 21-MODEL - Drive/Dropbox: stable file identity, versions, folders and ACLs

**Track:** Build extension. **Source topics:** file metadata; folders; stable IDs; versions; permissions; quotas; metadata vs bytes.

**Files to inspect or extend:** `src/storage/chunks.mjs`, `docs/openapi.yaml`.

### Start / reproduce

```bash
node --test test/storage.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Model stable file IDs separately from names/paths, immutable file versions, parent folders, current-version pointers, ACLs and logical quotas.
2. Define create, move, rename, delete, list, initiate-upload and complete-upload APIs with optimistic versions and tenant/owner checks.
3. Prevent folder cycles in the metadata transaction and treat a move as metadata change, not an unnecessary physical byte rewrite.

**Inject this failure:** Move and rename a file concurrently, move a folder into its descendant and exceed logical quota using deduplicated bytes.

**Completion gate:** Identity survives rename/move, no folder cycle is committed and quota counts the chosen logical contract independently from physical dedup savings.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-21-CHUNK"></a>
## 21-CHUNK - Resumable chunk upload, manifests and durable commit

**Track:** Build extension. **Source topics:** chunk upload; resume; manifest; checksum; multipart; dedup; atomic version publication.

**Files to inspect or extend:** `src/storage/chunks.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs storage
node --test --test-name-pattern="chunk|manifest" test/storage.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create owner-scoped upload sessions and content-addressed chunk references. Verify each chunk's size/hash and return the already uploaded set for resume.
2. Complete by validating the full ordered manifest and expected file checksum, then publish the immutable version and current-version pointer under a metadata compare-and-set.
3. Deduplicate within authorized scope and track references transactionally; a guessed hash is not permission to obtain an existing chunk.

**Inject this failure:** Interrupt after half the chunks, retry completion twice, mutate a part after completion and fail metadata commit after durable byte upload.

**Completion gate:** Final download is byte-identical, repeated completion returns the same version and incomplete manifests never become current files.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-21-SYNC"></a>
## 21-SYNC - Commit-safe change cursors, tombstones and offline catch-up

**Track:** Build extension. **Source topics:** delta sync; change feed; commit order; cursor; tombstones; offline client; snapshot.

**Files to inspect or extend:** `db/sync_cursor.sql`.

### Start / reproduce

```bash
docker compose exec -T postgres psql -U lab -d eventhub < db/sync_cursor.sql
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Use the tenant counter-row lock pattern in sync_cursor.sql to allocate a change sequence within the same metadata transaction. A plain BIGSERIAL allocation can commit out of order and skip an unseen lower sequence.
2. Provide /changes?after=cursor with bounded pages, a history-retention floor and a snapshot-required response for an expired cursor.
3. Persist client cursor only after applying the whole received page. Include deletes/moves as tombstones/changes and recheck access for returned metadata and bytes.

**Inject this failure:** Delay transaction A after allocating a sequence while B attempts a later change, then crash the client before persisting its cursor.

**Completion gate:** A client never advances past a committed change it could permanently miss; reapplying a page is idempotent and old cursors trigger explicit resynchronization.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-21-CONFLICT"></a>
## 21-CONFLICT - Offline conflict copies, OT/CRDT and simultaneous moves

**Track:** Build extension. **Source topics:** optimistic version; offline edits; conflict copy; OT; CRDT; merge; rename conflict.

**Files to inspect or extend:** `src/algorithms/consistency.mjs`, `src/storage/chunks.mjs`.

### Start / reproduce

```bash
node --test --test-name-pattern="observed-remove|counter" test/algorithms.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Upload a new file version against an expected base version. If it changed, preserve the offline user's bytes as a conflict copy or explicit pending resolution rather than overwriting silently.
2. For a collaborative text product, integrate a maintained OT/CRDT library with persisted operations and authenticated updates; ordinary binary file sync need not merge arbitrary bytes.
3. Test simultaneous rename/move/edit/delete cases and decide which operations can commute or require a user-visible conflict.

**Inject this failure:** Two offline clients edit the same base, one deletes the folder and the other uploads a later version.

**Completion gate:** No meaningful user edit is silently discarded under the documented policy and merges converge where the chosen data type supports them.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-21-SHARE"></a>
## 21-SHARE - Sharing, revocation, search and signed download scope

**Track:** Build extension. **Source topics:** sharing; ACLs; capability tokens; revocation; private search; signed URLs.

**Files to inspect or extend:** `src/algorithms/processing.mjs`, `src/security.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs search
node --test --test-name-pattern="ownership|tenant" test/http.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Create scoped sharing records with recipient/role, optional expiry and immutable object-version scope for tickets.
2. Recheck permission before issuing download credentials, serving search results or accepting sync updates. Define whether existing tickets remain usable until expiry or pass through an immediate-revocation gateway.
3. Index filenames/content asynchronously with tenant/version/deletion metadata; filter search results through current authorization.

**Inject this failure:** Revoke access while a client is offline with cached metadata or an unexpired download ticket, then try a new upload under the old permission.

**Completion gate:** The revocation boundary is explicit, new unauthorized writes are rejected and private search does not leak document titles/snippets.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-21-GC"></a>
## 21-GC - Reference-safe garbage collection and version retention

**Track:** Build extension. **Source topics:** garbage collection; reference counts; mark-sweep; grace period; tombstones; retention.

**Files to inspect or extend:** `src/storage/chunks.mjs`, `db/sync_cursor.sql`.

### Start / reproduce

```bash
node --test test/storage.test.mjs
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Track immutable chunk references per committed version and active upload session. Choose transactional reference counts or a consistent mark/sweep manifest with generation markers.
2. Use a grace period longer than the maximum publication/retry window, then recheck references immediately before physical deletion.
3. Apply version retention and deletion requests to metadata, object versions, backups and keys according to a documented policy; never equate a name deletion with immediate byte reclamation.

**Inject this failure:** Run cleanup while a concurrent upload begins referencing an existing chunk, or restore metadata older than the latest GC cycle.

**Completion gate:** No live or in-progress authorized version loses a required chunk and restored metadata is validated against actually retained bytes.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.


<a id="lab-21-GLOBAL"></a>
## 21-GLOBAL - Metadata/byte replication, integrity and global recovery

**Track:** Build extension. **Source topics:** metadata replication; object replication; multi-region; checksums; erasure; RPO; RTO.

**Files to inspect or extend:** `infra/replication/compose.yaml`, `src/algorithms/erasure.mjs`, `src/storage/chunks.mjs`.

### Start / reproduce

```bash
node experiments/run.mjs erasure
node experiments/run.mjs storage
```

The command is the entry point or prerequisite. It does not automatically complete the implementation steps below.

### Implement

1. Replicate metadata and bytes as distinct pipelines, recording what visibility is allowed before remote bytes become available.
2. Use checksummed manifests and background repair, comparing replicated copies with erasure-coded fragments and their failure domains.
3. Restore metadata, immutable chunks, change cursors, permissions and encryption keys together, then resume clients from a valid synchronization boundary.

**Inject this failure:** Allow metadata to arrive before its object, corrupt one remote chunk and fail the original region during a sync burst.

**Completion gate:** Clients receive an explicit pending/retry state rather than a successful pointer to missing content; recovery verifies end-to-end checksums and current permissions.

**Record and restore:** save the exact commands, raw measurements, authoritative-state assertion and recovery result in a lab-specific evidence file. Restore the isolated fixture/topology before changing strategy; do not delete persistence during a crash-survival test.



<a id="playbooks"></a>
# Operational playbooks


These playbooks make the key infrastructure experiments self-contained. Read the matching lab first; all destructive and paid operations remain explicitly scoped.



<a id="playbook-scaling"></a>


# Operational playbook: all scaling strategies

Applies to 02-BASELINE through 02-DECIDE. **Local resource changes are executable configurations, not measured improvements.** First run the database-backed quickstart. Use a fresh evidence file for every comparison.

## 1. Baseline and experimental control

```bash
docker compose up -d --build --wait --scale api=1
docker compose exec api node scripts/seed.mjs
RATE=30 SECONDS=30 OUT=evidence/one-api.json node scripts/load.mjs
docker stats --no-stream
```

Record completed throughput, errors and generator drops. Repeat cold and warm cache experiments separately. CPU-bound work uses `/lab/cpu?n=20000`; catalog reads may be I/O-bound. Do not attribute an unrelated database limit to API CPU.

## 2. Vertical CPU, RAM and workers

```bash
API_CPUS=0.25 API_MEMORY=128m WORKERS=1 docker compose up -d --force-recreate --scale api=1
PATHNAME='/lab/cpu?n=20000' RATE=10 SECONDS=30 OUT=evidence/cpu-small.json node scripts/load.mjs
API_CPUS=2 API_MEMORY=256m WORKERS=1 docker compose up -d --force-recreate --scale api=1
PATHNAME='/lab/cpu?n=20000' RATE=10 SECONDS=30 OUT=evidence/cpu-large.json node scripts/load.mjs
API_CPUS=2 API_MEMORY=256m WORKERS=2 docker compose up -d --force-recreate --scale api=1
PATHNAME='/lab/cpu?n=20000' RATE=10 SECONDS=30 OUT=evidence/cpu-workers.json node scripts/load.mjs
```

Changing a limit recreates the container; it is not an in-place zero-downtime upgrade. One JavaScript worker does not automatically use all cores for synchronous CPU work. `WORKERS` uses Node cluster and adds an independent database pool per worker. Memory experiments must allocate a bounded synthetic working set and observe GC/RSS/OOM separately; do not assume a larger RAM limit helps a tiny catalog.

**Pass:** show resource configuration, the bottleneck that changed, and cases with no improvement. Restore `WORKERS=1` before comparing API replica count.

## 3. Horizontal application scaling and statelessness

```bash
API_CPUS=1 API_MEMORY=256m WORKERS=1 DB_POOL_MAX=5 docker compose up -d --force-recreate --scale api=4
RATE=100 SECONDS=30 OUT=evidence/four-api.json node scripts/load.mjs
node scripts/race.mjs
SAME_KEY=true node scripts/race.mjs
docker compose exec -T postgres psql -U lab -d eventhub < db/assert_invariants.sql
```

Compare both **equal resources per instance** and **equal total resource budget**. Four pools of five connections create up to 20 connections; adjust intentionally. NGINX uses Docker DNS to discover API addresses. Record `X-Instance` counts and restart one instance during load. Sticky routing is not durability: intentionally place a cart in process memory, demonstrate inconsistency, then move it to a tenant-scoped shared store with an explicit persistence contract.

## 4. Reactive, scheduled and predictive controls

Run `node experiments/run.mjs autoscaling` to compare deterministic workload models. These models demonstrate controller behavior; they do not deploy an autoscaler.

For real pods use `playbooks/KUBERNETES.md`: HPA uses CPU requests and Metrics Server; VPA recommendations are separate; the scheduled controller raises the HPA floor rather than fighting its desired replicas. For asynchronous workers, implement a queue-age or lag metric adapter and scale the worker deployment independently, bounded by database/provider capacity.

Forecast tests must include a missed spike and a false-positive spike. Compare warmup delay, rejected requests, queue age and unused capacity. AWS predictive scaling uses observed history; the supplied ASG policy is intentionally `ForecastOnly`, so it does not silently start paying for forecast-driven capacity. See official references [AWS-PREDICT] and [K8S-HPA].

## 5. Real VM / cloud autoscaling: explicit opt-in

This creates billed resources. Use a **dedicated sandbox account**, configured AWS CLI, a supported region, and your current public IPv4 `/32`. The template serves only a synthetic NGINX health page, not EventHub or private data. Never change the allowed CIDR to the whole internet to fix connectivity.

```bash
export AWS_REGION=us-east-1
export STACK=hld-scaling-lab
export ALLOWED_CLIENT_CIDR='203.0.113.10/32'  # REPLACE with your actual public IPv4/32.
aws sts get-caller-identity
aws cloudformation validate-template --template-body file://infra/aws/autoscaling.yaml
aws cloudformation deploy --stack-name "$STACK" \
  --template-file infra/aws/autoscaling.yaml --capabilities CAPABILITY_IAM \
  --parameter-overrides AllowedClientCidr="$ALLOWED_CLIENT_CIDR" \
  --region "$AWS_REGION"
aws cloudformation describe-stacks --stack-name "$STACK" --query 'Stacks[0].Outputs'
```

Obtain the ASG name from the output. Save initial instance IDs, desired capacity, launch template version and health states. Inspect target tracking, CPU metrics and scaling activities. The static health page alone is not an adequate CPU stress workload: through **SSM Session Manager**, run a bounded CPU task on one lab instance, watch it scale, then stop the task. For application-level results, deploy a reviewed synthetic CPU endpoint rather than exposing the fixture-authenticated core API.

For vertical instance changes update the template parameter to its other allowed instance size, inspect the launch template, and start a rolling instance refresh. A changed launch template does not prove existing instances already changed size. Maintain spare capacity and ALB health throughout.

For scheduled scaling enable the template's schedule parameter after reviewing its UTC windows. For predictive scaling observe `ForecastOnly ` forecasts after sufficient representative history, compare errors, and only then explicitly switch to ` ForecastAndScale` in a reviewed template change. Record maximum capacity and a cost stop condition.

```bash
aws cloudformation delete-stack --stack-name "$STACK" --region "$AWS_REGION"
aws cloudformation wait stack-delete-complete --stack-name "$STACK" --region "$AWS_REGION"
```

Verify stack deletion and separately inspect retained resources, log groups, snapshots or manually created resources. Do not leave the lab running while waiting days for predictive history without a budget.

## 6. Data, cache, messaging and geographic scaling

Horizontal API scaling does not scale a single contended inventory row. Use:

| Bottleneck | Implemented lab path | Counter-test |
|---|---|---|
| Repeated reads | Cache-aside, L1/L2, read replicas | Cold cache, stale data, cache outage |
| Write/data volume | Hash/range/directory/geo shards | Hot tenant, migration interruption, cross-shard query |
| Time retention | Time partitioning | Missing partition, pruning absent, retention correctness |
| Expensive aggregates | Denormalization/CQRS | Rebuild, out-of-order events, deletion propagation |
| Background work | Independent consumers, partitioned log | Queue age, hot partition, duplicate effect |
| Global latency | Edge cache, home-region routing | Expired content, stale authorization, regional partition |
| Overload | Admission, quota, load shedding | Retry storm, bounded queue, business rejection accuracy |

The dedicated sharding, replication, caching and messaging labs contain the implementation/failure cases. Finish with an ADR choosing **one measured next action**, not every technology at once.



<a id="playbook-kubernetes"></a>


# Operational playbook: HPA, VPA, scheduling and rollout

**Prerequisites:** an isolated Kubernetes cluster with a default StorageClass, working container networking and Metrics Server. `kind `, ` kubectl` and Docker are convenient locally. Install controllers using their official, version-matched releases. The package does not install or validate your cluster automatically.

## Build and load the local image

```bash
docker build -t hld-eventhub:lab .
kind create cluster --name hld-lab
kind load docker-image hld-eventhub:lab --name hld-lab
kubectl config current-context  # Must be your disposable kind-hld-lab context.
kubectl apply -f infra/k8s/workload.yaml
kubectl -n hld-lab wait --for=condition=complete job/eventhub-seed --timeout=180s
kubectl -n hld-lab rollout status deployment/eventhub --timeout=180s
kubectl -n hld-lab top pods
```

If `top` fails or HPA reports unknown metrics, fix Metrics Server before interpreting the experiment. Local clusters may need specifically scoped certificate configuration; do not copy insecure kubelet settings into production. Pending PVCs require a working StorageClass. Inspect seed-job logs rather than repeatedly applying a completed Job with changed immutable fields.

## Exercise traffic through a Service

A host port-forward is convenient for smoke tests, but a long-lived port-forward may pin traffic to one selected pod. For a fair scaling workload, run the provided load script **inside the cluster against the Service**:

```bash
kubectl -n hld-lab run hld-load --restart=Never --image=hld-eventhub:lab \
  --env=BASE_URL=http://eventhub:3000 --env=RATE=50 --env=SECONDS=60 \
  --env='PATHNAME=/lab/cpu?n=20000' --command -- node scripts/load.mjs
kubectl -n hld-lab logs -f pod/hld-load
kubectl -n hld-lab delete pod hld-load
```

## Reactive HPA

```bash
kubectl apply -f infra/k8s/hpa.yaml
kubectl -n hld-lab get hpa,pods -w
kubectl -n hld-lab describe hpa eventhub
```

Run baseline, steady load, spike and recovery. Record desired/current replicas, ready count, stabilization windows and dropped load. CPU utilization is relative to the configured **CPU request**, not the physical host's total CPU. Scale-down stabilization and startup readiness prevent some oscillation, not every overload. More replicas can still overwhelm PostgreSQL; the supplied StatefulSet is one database, not HA.

## Scheduled prewarming without a competing replica controller

```bash
kubectl apply -f infra/k8s/scheduled-floor.yaml
kubectl -n hld-lab create job --from=cronjob/eventhub-peak-floor prewarm-once
kubectl -n hld-lab logs -f job/prewarm-once
kubectl -n hld-lab get hpa eventhub
# Restore the normal floor after the experiment.
kubectl -n hld-lab patch hpa eventhub --type=merge -p '{"spec":{"minReplicas":2}}'
```

The CronJob is supplied suspended. Unsuspend only after reviewing its schedule, timezone behavior, service account and cluster version. Its role can patch only the intended HPA. Add a complementary scheduled floor decrease before enabling recurring prewarming; a completed one-off job does not automatically undo its change.

## VPA, rolling changes and resilience

Install the VPA CRDs/controllers first, then apply `infra/k8s/vpa.yaml `. Its update mode is ` Off`: inspect recommendations without automatic eviction. Test recommendations on a copy and assess OOM, scheduling and pool budgets. Avoid two controllers adjusting the same signal in conflicting ways.

The Deployment has readiness, liveness, startup probes and graceful termination. Run continuous requests during `kubectl rollout restart deployment/eventhub -n hld-lab`. Break readiness separately from liveness. A PodDisruptionBudget limits supported voluntary disruptions; it does not prevent a node crash or create spare capacity.

For canary: create a second Deployment with a distinct version label, route a small stable cohort through a gateway, compare metrics, then increase or revert. For blue-green: prepare the green deployment fully, switch Service selection only after readiness/schema compatibility, retain blue for rollback. Database changes follow expand/backfill/switch/contract, not destructive schema replacement.

## Cleanup

```bash
kubectl config current-context
kubectl delete namespace hld-lab
kind delete cluster --name hld-lab
```

Deleting the namespace/cluster destroys its synthetic PVC-backed data. Export evidence and practice recovery **before** cleanup.



<a id="playbook-sharding"></a>


# Operational playbook: sharding and safe resharding

The reference has both routing algorithms and a **real PostgreSQL multi-database configuration**. Routing tests are not a substitute for executing the database migration tests.

## 1. Compare every placement strategy

```bash
node experiments/run.mjs sharding
node --test --test-name-pattern='shard|ring|rendezvous' test/algorithms.test.mjs
```

Use10,000 tenant IDs, then a skewed population where one tenant generates90% of traffic. Compare modulo, hash ring with virtual nodes, rendezvous hashing, ordered ranges, directory lookups and home-region routing. For each, save key distribution, request distribution, keys moved on add/remove, membership version and routing cost. A balanced key count does not prove balanced CPU or write volume.

**Horizontal sharding** splits rows across independently operated databases. **Vertical partitioning** separates columns/business domains. **Native table partitioning** keeps partitions under one database engine. Implement and measure these as different strategies rather than treating the names interchangeably.

## 2. Start independent shards and the routing directory

```bash
docker compose -f infra/sharding/compose.yaml up -d --build --wait
docker compose -f infra/sharding/compose.yaml exec router node scripts/seed-shards.mjs
BASE_URL=http://127.0.0.1:8089 node scripts/smoke.mjs
BASE_URL=http://127.0.0.1:8089 node scripts/race.mjs
docker compose -f infra/sharding/compose.yaml exec catalog psql -U lab -d eventhub -c 'TABLE tenant_routes;'
```

Initially `org-a ` belongs to A and` org-b`to B. The router resolves the verified tenant through the directory. It then checks the shard's tenant fence **inside each transaction** using the expected routing epoch. It never guesses a replacement shard when the catalog is unavailable. Catalog failure is an explicit availability cost of this simple implementation.

Code to inspect: `src/stores/sharded.mjs `, ` src/stores/pg.mjs `, ` infra/sharding/catalog.sql `, ` scripts/migrate-tenant.mjs`.

## 3. Resumable migration with a bounded write outage

```text
ACTIVE(A, epoch1)
 -> directory MOVING
 -> fence A and drain previously admitted transactions
 -> consistent snapshot A
 -> copy tenant rows to C with epoch2 disabled
 -> compare counts and SHA-256 row checksums
 -> commit target copy
 -> enable epoch2 on C
 -> atomically switch directory to ACTIVE(C, epoch2)
 -> retain old A copy, fenced
```

No worker in this sharding profile writes around the router. Before extending it with publishers, consumers or administrative writers, stop/drain them or make them honor the same tenant fence. An application-only fence cannot protect against a privileged manual SQL session that ignores the protocol.

```bash
# A deliberate failed checkpoint: the command exits nonzero by design.
docker compose -f infra/sharding/compose.yaml exec \
  -e FAIL_AFTER_STAGE=copied router node scripts/migrate-tenant.mjs org-a c

# During MOVING, org-a is rejected; it is not silently routed to both copies.
curl -i -H 'Authorization: Bearer lab-alice' http://127.0.0.1:8089/v1/events
curl -i -H 'Authorization: Bearer lab-charlie' http://127.0.0.1:8089/v1/events

# Resume the same tenant and target.
docker compose -f infra/sharding/compose.yaml exec router node scripts/migrate-tenant.mjs org-a c
BASE_URL=http://127.0.0.1:8089 node scripts/smoke.mjs
```

Repeat on fresh fixtures with `FAIL_AFTER_STAGE=fenced ` and ` target-enabled`. Verify the catalog state, both epoch rows and all copied table checksums. Retrying a checkpoint may recopy the frozen snapshot; this is intentional. The old copy is not automatically deleted. After successful migration, a rollback is a **new fenced reverse migration including newer writes**, not an unsafe pointer flip to the stale old shard.

```bash
docker compose -f infra/sharding/compose.yaml exec -T shard-c psql -U lab -d eventhub < db/assert_invariants.sql
docker compose -f infra/sharding/compose.yaml exec shard-a psql -U lab -d eventhub -c 'TABLE tenant_fences;'
docker compose -f infra/sharding/compose.yaml exec shard-c psql -U lab -d eventhub -c 'TABLE tenant_fences;'
```

**Pass:** at most one authoritative writer, accepted operations survive, retry identities and outbox rows migrate, nonmoving tenants remain available, and the outage duration is recorded. This is not a zero-downtime migration; the tenant is fenced for the copy duration.

## 4. Online snapshot plus change-data-capture extension

Implement this only after the simpler protocol passes. The source continues serving during bulk copy, but not during final cutover:

1. Establish a logical replication slot/change stream and a consistent snapshot at a known log position using the database's supported snapshot/CDC protocol. A random timestamp and a separately started dump are not equivalent.
2. Copy the snapshot and apply ordered, versioned changes to the hidden destination. Preserve deletes/tombstones, idempotency records and transaction boundaries. Record source identity, log position and last fully applied transaction.
3. Fence new source writes and wait for existing source transactions to finish. Capture the final committed source position; catch the destination up **through that position**, not merely until the stream appears idle.
4. Verify counts, sampled records, schema versions, constraints and identity state. Atomically switch the directory epoch. Only then expose target reads/writes. Keep the source fenced and logs retained through the rollback window.
5. Crash before and after each checkpoint, resume from persistent migration state, and prove no write was both omitted and acknowledged. If catch-up cannot converge, abort before cutover and keep the original writer.

PostgreSQL publications/subscriptions are useful building blocks, not turnkey cross-shard routing, DDL migration, sequence synchronization or conflict resolution. Verify those separately [PG-LOGICAL].

## 5. Range splits, geo shards, hot keys and cross-shard work

For range splits: publish versioned nonoverlapping bounds, retain a redirect/directory until all clients use the new epoch, and move rows using the same fence/catch-up protocol. For geo shards: home-region ownership is chosen from a trusted server-side mapping, not a request header. Simulated regions on one laptop validate routing logic, not regional resilience.

A single hot entity cannot be made independent by hashing it differently. Separate append-only analytics, cache read replicas or admission queues from authoritative inventory. Salting a counter requires a well-defined merge/aggregation; salting a unique seat into independent writers violates its invariant.

For cross-shard search/reporting, scatter only to an allowlisted bounded shard set, propagate deadlines, label partial results and use deterministic merge/pagination. For cross-shard writes use an explicit saga or tested distributed transaction, never two independent commits disguised as one transaction.

## Cleanup

`docker compose -f infra/sharding/compose.yaml down ` preserves data. Add`-v` only for an intentional complete reset after saving evidence. Migration rollback and deleting volumes are not the same operation.



<a id="playbook-replication"></a>


# Operational playbook: replication, visibility and fenced failover

## 1. Start physical primary/standby replication

```bash
sh scripts/replication.sh up
sh scripts/replication.sh inspect
docker compose -f infra/replication/compose.yaml --profile app up -d --build app
BASE_URL=http://127.0.0.1:8088 node scripts/smoke.mjs
```

The supplied initializer creates replication credentials/slots and clones standbys using `pg_basebackup -R `. Both standby nodes must report` pg_is_in_recovery()=true `. Inspect` pg_stat_replication`, sent/write/flush/replay LSNs, slot retention and lag. A replica's health probe does not imply that it has applied the latest commit.

## 2. Asynchronous replication and read-your-writes

```bash
sh scripts/replication.sh async
sh scripts/replication.sh pause
docker compose -f infra/replication/compose.yaml exec primary psql -U lab -d eventhub -c \
 "UPDATE events SET title='new primary title',version=version+1 WHERE tenant_id='org-a' AND id='11111111-1111-4111-8111-111111111111';"
docker compose -f infra/replication/compose.yaml exec standby1 psql -U lab -d eventhub -c \
 "SELECT title,version FROM events WHERE tenant_id='org-a';"
sh scripts/replication.sh resume
```

Compare ordinary replica reads with a request carrying a required `minimumVersion`. The reference falls back to the primary when the standby cannot meet that version. Test bounded waiting, replica unavailability and a version beyond the primary's known state. Session consistency is an API promise, not an automatic property of adding a read replica.

## 3. Synchronous replication: distinguish acknowledgement levels

```bash
sh scripts/replication.sh sync
sh scripts/replication.sh inspect
```

The helper selects `ANY 1 (standby1,standby2)` and `remote_apply`. Compare ` on`(remote WAL flush),` remote_write `and` remote_apply`using isolated configuration changes and the same workload. Record latency, required standby count, visibility and availability when a qualifying standby disappears [PG-REPL].

**Important:** withANY 1, either standby may have supplied the acknowledgement. Do not assume standby1 is always the safe promotion candidate. Compare all available positions and the acknowledged client history. A synchronous policy can still have wrong-candidate, unfenced-old-primary or broader failure-domain risks.

## 4. Manual failover with an explicit fence

Save successful operation IDs outside database volumes. Ensure a candidate is caught up as far as your policy requires. In a healthy planned switchover, stop admitting writes, drain, record final LSN, wait for the candidate to apply it, then fence the primary. In an unexpected outage, account for potentially missing asynchronous writes.

```bash
# Supplied demonstration promotes standby1 ONLY after stopping the old primary.
CONFIRM_LAB_FAILOVER=yes sh scripts/replication.sh promote
```

Repoint the application using an override rather than starting the old primary again:

```yaml
# Save as infra/replication/promoted.override.yaml
services:
  app:
    environment:
      DATABASE_URL: postgres://lab:lab_password@standby1:5432/eventhub
      READ_DATABASE_URL: postgres://lab:lab_password@standby1:5432/eventhub
```

```bash
docker compose -f infra/replication/compose.yaml -f infra/replication/promoted.override.yaml \
  --profile app up -d --no-deps --force-recreate app
BASE_URL=http://127.0.0.1:8088 node scripts/race.mjs
docker compose -f infra/replication/compose.yaml exec -T standby1 psql -U lab -d eventhub < db/assert_invariants.sql
```

`--no-deps` prevents the app's original dependency declaration from restarting the old primary. Leave standby2 stopped until deliberately reconfigured to follow the new writer. Rejoin the old primary only by supported rewind/reclone and recovery configuration, after fencing and verifying its timeline. Never turn it on as an independent writer.

**Pass:** writer identity, lost/ambiguous/retained operations, actual outage duration and invariant results are recorded. Automatic failover requires an independent quorum/lease authority, proven fencing, candidate selection and client routing. The supplied helper is manual, not an HA controller.

## 5. Multi-leader and leaderless replication

For multi-leader profiles/preferences, instantiate two writable replicas, attach origin IDs and version vectors, partition, write different fields concurrently, reconcile by declared field semantics, then compare last-write-wins under artificial clock skew. `src/algorithms/consistency.mjs` supplies executable merge models. A general-purpose two-way PostgreSQL publication is not automatically conflict-safe multi-leader inventory.

For leaderless behavior, run the Cassandra RF3 stack and `db/cassandra.cql`. ExerciseONE versus QUORUM reads/writes and conditional writes separately. Stop one/two nodes, heal, run repair and check old/new values. Explain whether hints, read repair and clocks matter. Quorum intersection alone does not supply every assumption needed for linearizability.

For coordination, run the etcd cluster, write keys, stop one member, then isolate a minority member while its process remains alive. Compare linearizable and serializable reads and majority writes. The included Paxos code is a finite single-decree model, not a replacement for a deployed consensus service.

## 6. Redis HA versus Redis Cluster

Sentinel tracks a primary and elects/promotes a replacement; Cluster additionally partitions hash slots. Run `infra/redis/compose.yaml` and `infra/redis/cluster.yaml` in separate experiments. Query membership before/after failure and update clients using maintained Sentinel/Cluster-aware drivers. Check slot coverage, replica placement and non-atomic cross-slot operations. Neither changes a disposable cache into the only safe authority for booking records.

Cleanup each stack with its ownComposefile. A promotion exercise must be reset/rebuilt deliberately before reusing the original primary-topology commands.



<a id="playbook-messaging"></a>


# Operational playbook: asynchronous work, replay and distributed transactions

## 1. Transactional outbox plus duplicate-safe effect

```bash
docker compose --profile async up -d --build --wait
node scripts/smoke.mjs
curl -H 'Authorization: Bearer lab-alice' http://127.0.0.1:8080/v1/notifications
docker compose exec postgres psql -U lab -d eventhub -c \
 'SELECT count(*) AS pending FROM outbox WHERE published_at IS NULL;'
```

A booking commits inventory, booking, idempotency response and an outbox row in one PostgreSQL transaction. The publisher confirms broker acceptance before marking publication. The consumer commits deduplication and the simulated notification record before acknowledging. These are distinct acknowledgement boundaries [RABBIT-CONFIRM].

## 2. Reproduce both duplication windows

```bash
PUBLISH_PAUSE_AFTER_CONFIRM_MS=5000 docker compose --profile async up -d --force-recreate publisher
# Create a fresh booking in another terminal; observe publisher output.
# Kill only during its configured pause, then restore normal operation.
docker compose kill -s KILL publisher
PUBLISH_PAUSE_AFTER_CONFIRM_MS=0 docker compose --profile async up -d --force-recreate publisher

WORKER_PAUSE_AFTER_COMMIT_MS=5000 docker compose --profile async up -d --force-recreate worker
# Create another fresh booking; kill after the worker transaction commits.
docker compose kill -s KILL worker
WORKER_PAUSE_AFTER_COMMIT_MS=0 docker compose --profile async up -d --force-recreate worker
```

Save event IDs and SQL state at the pause boundary; a randomly timed kill does not prove that exact failure window. Automatic restart may restart the killed process: inspect logs and remove the pause during recovery. Verify one notification row per source event while allowing repeated delivery. External providers still need their own idempotency or reconciliation; a local dedup row cannot atomically commit an unrelated remote action.

## 3. Broker topology, failures and scaling

The default broker is a single node, so a quorum queue there is not a three-node HA demonstration. For that use `infra/messaging/rabbit.yaml`, verify ` rabbitmqctl cluster_status`, create a quorum queue with three actual members, and inspect member placement. Stop one member during publication, then lose majority. Restore before the next experiment. A simple DLX route is not a guarantee of lossless dead-letter transfer: configure/test the documented quorum dead-letter strategy before claiming that property.

Implement bounded retry attempts, transient/permanent classification, exponential full jitter, oldest-message-age metrics, a terminal DLQ and authenticated redrive preserving event identity. For competing consumers, use one queue. For fan-out, bind a separate queue per independent role. Replay, retention, ordering and work distribution are different features.

```bash
docker compose --profile async up -d --scale worker=4
# Compare throughput, oldest event age, duplicate counts and database waits.
docker compose exec -T postgres psql -U lab -d eventhub < db/assert_invariants.sql
```

An ordered queue plus concurrent workers does not guarantee ordered business completion. Persist aggregate versions and handle duplicates/gaps before applying state changes.

## 4. Kafka retained-log lab

```bash
docker compose -f infra/messaging/kafka.yaml up -d
docker compose -f infra/messaging/kafka.yaml exec k1 /opt/kafka/bin/kafka-topics.sh \
  --bootstrap-server k1:9092 --create --topic bookings --partitions 3 \
  --replication-factor 3 --config min.insync.replicas=2
docker compose -f infra/messaging/kafka.yaml exec k1 /opt/kafka/bin/kafka-topics.sh \
  --bootstrap-server k1:9092 --describe --topic bookings
```

Produce stable keys with `acks=all` and an idempotent producer. Give notification and analytics separate consumer groups; scale consumers within a group and observe partition ownership. Persist output and checkpoints transactionally where possible. Kill a consumer after its external database effect but before offset commit, then verify deduplication on replay. Kafka transactions/read-committed consumption can coordinate Kafka records and offsets, not automatically an external email, file store or arbitrary database.

Compare partition count, hot-key ordering, compaction versus time retention, replay from earliest, late events, watermarks, and DLQ repair. Read the actual broker names from the file if modified. Stop with the same `-f` path; keep volumes during durability tests.

## 5. Two-phase commit: concrete blocking experiment

Use disposable PostgreSQL participants, not the live EventHub schema. Set `max_prepared_transactions=10` on the two participants and restart them before testing. Create an accounts table on each and a coordinator-decision table in a third durable store.

On participant A:

```sql
CREATE TABLE IF NOT EXISTS transfer_lab(id integer PRIMARY KEY,balance integer NOT NULL CHECK(balance>=0));
INSERT INTO transfer_lab VALUES(1,100) ON CONFLICT DO NOTHING;
BEGIN;
UPDATE transfer_lab SET balance=balance-10 WHERE id=1;
PREPARE TRANSACTION 'lab-transfer-001-a';
```

On participant B, prepare the matching credit with a different GID. After every participant acknowledges preparation, durably commit the coordinator decision, then issue `COMMIT PREPARED ` to each participant. Crash before and after that durable decision. Inspect ` pg_prepared_xacts ` and test a competing update to observe held locks. Recovery consults the decision record; it must not guess an abort after a committed decision. Use ` ROLLBACK PREPARED` only when the protocol permits it, and resolve every prepared transaction before dropping the databases [PG-2PC].

This is a coordinator implementation assignment; the package does not supply a production distributed transaction manager. It teaches why uncertain prepared transactions can block and why blanket rules such as 'never use2PC' conceal tradeoffs.

## 6. Saga, CQRS and event sourcing implementations

**Saga orchestration:** persist saga ID, step, status, operation keys and compensation status. Commit reservation locally, ask the fake payment provider outside the inventory lock, then confirm or schedule compensation. Retry the same step key; duplicate compensation must be harmless. Late payment success after expiry creates refund work, not a stolen seat.

**Saga choreography:** publish state transitions through the outbox; consumers own local transactions, deduplication and aggregate versions. Detect stuck sagas by age. Trace events end-to-end so choreography does not become invisible control flow.

**CQRS:** authoritative writes remain in PostgreSQL; project into a separate read table, reject old versions, retain tombstones, rebuild snapshot+catch-up and atomically switch readers. Expose projection lag.

**Event sourcing:** the append-only event stream is the authority; enforce expected stream version, append events atomically, rebuild state and verify snapshots against replay. Merely adding an outbox to a mutable table is not event sourcing. Compare these alternatives under the same booking story and record which complexity is actually needed.



<a id="playbook-network"></a>


# Operational playbook: networking, protocols and edge delivery

## 1. HTTPS with a local trusted authority

```bash
docker compose -f infra/network/caddy.yaml up -d --build
mkdir -p runtime/tls
docker compose -f infra/network/caddy.yaml cp \
  caddy:/data/caddy/pki/authorities/local/root.crt runtime/tls/root.crt
curl --cacert runtime/tls/root.crt --resolve eventhub.test:8443:127.0.0.1 \
  https://eventhub.test:8443/health/live
```

Wait for Caddy to issue its local certificate before copying the CA. Verify failure without trust and with a mismatched hostname. Do not use `-k` as the passing condition. Identify where TLS terminates: the gateway-to-API HTTP hop is separate from client TLS. Never commit a generated private key.

For mutual TLS, create a separate local client CA, issue a certificate with clientAuth usage, configure the gateway to require and verify it, and test missing, untrusted and expired client certificates. Mapping that certificate to an authorized service identity is a separate policy.

## 2. HTTP/1.1, HTTP/2 and HTTP/3

```bash
curl -V
curl --http1.1 --cacert runtime/tls/root.crt --resolve eventhub.test:8443:127.0.0.1 https://eventhub.test:8443/health/live
curl --http2 --cacert runtime/tls/root.crt --resolve eventhub.test:8443:127.0.0.1 https://eventhub.test:8443/health/live
# Requires a curl build with HTTP/3 support. Failure means capability is absent, not that QUIC is slower.
curl --http3-only --cacert runtime/tls/root.crt --resolve eventhub.test:8443:127.0.0.1 https://eventhub.test:8443/health/live
```

HTTP/3 needs UDP port 8443 mapped as well as TCP, and a compatible client build. Keep payload, trust, concurrency and simulated network delay fixed. Record the negotiated protocol, concurrent-stream behavior and isolated packet-loss conditions. Do not infer universal protocol superiority from one localhost request.

## 3. DNS and load-balancer configuration

Use `infra/network/dns.yaml` for A, AAAA, CNAME, MX, TXT and NS queries and TTL changes. DNS routing and existing TCP connections are different failover paths.

For NGINX policies, edit a copy of the upstream in `infra/network/nginx.conf `, validate using ` nginx -t ` inside the gateway, then reload. Default is round-robin. Insert ` least_conn;` for least connections, server ` weight ` parameters for weighted routing, or ` ip_hash;` for address affinity, removing conflicting policies. Add the optional ` slow` service to the upstream when testing heterogeneous latency; starting the service alone does not route traffic to it.

Hold request load and total capacity constant. Record per-instance counts, active requests, response time and errors. IP hash may collapse all Docker/NAT clients onto one address; explicitly generate or model distinct clients before calling that a distribution failure. For L4 versusL7, compare a TCP-mode proxy with HTTP path routing, header policies and TLS termination; packet forwarding is not application authorization.

## 4. TCP, UDP, polling, SSE and WebSockets

```bash
node --test --test-name-pattern='TCP|UDP' test/storage.test.mjs
npm install
node src/realtime.mjs
```

The supplied TCP decoder handles segmented/coalesced frames and UTF-8 byte boundaries. The UDP test intentionally drops every fifth datagram in the application. This is controlled loss, not a measured physical-network drop rate.

For SSE:

```bash
curl -N -H 'Authorization: Bearer lab-alice' http://127.0.0.1:3010/events
```

Publish through a Node WebSocket client using fixture headers. Browser EventSource/WebSocket APIs do not accept arbitrary Authorization headers in the same way: a browser implementation needs reviewed cookie/session or short-lived ticket authentication. Do not put reusable bearer secrets in URLs. The starter has bounded process-local history, not durable chat. Add database commit-before-ACK, per-room sequences, resume cursors, cross-instance fan-out and subscription authorization.

Short polling periodically requests a snapshot; long polling holds one request until a change or deadline; SSE streams from server to client; WebSockets support bidirectional frames. Measure reconnects, heartbeat, proxy idle timeout, missed updates and bounded slow-client buffers for each.

## 5. gRPC and GraphQL

```bash
node src/grpc-server.mjs
# Separate terminal after dependency installation:
node src/graphql-server.mjs
```

Use `src/catalog.proto` to generate or load clients. Supply fixture authorization metadata and finite RPC deadlines. Test unary, server streaming, client streaming and bidirectional streaming, including cancellation and backpressure. The localhost gRPC server uses insecure transport only for synthetic fixtures; TLS is a separate extension.

For GraphQL, POST a query to localhost:3011 and compare the same event fields with REST. Instrument an intentional N+1 organizer resolver, then add request-scoped batching and depth/cost limits. A request-byte limit alone does not bound execution cost.

## 6. CDN, object delivery and signed content

Use `infra/network/edge.conf` as a caching proxy configuration after implementing the public immutable asset endpoint. Pull caching fills on a miss; push prepopulates chosen objects; hybrid prewarms a bounded hot set and pulls the rest. Add an origin shield and measure collapsed misses and extra operational cost.

Serve immutable public assets under content-hash URLs with correct cache headers. Exercise TTL, revalidation/ETags, purge, versioned URLs, negative caching, origin failure and stampedes. Never publicly cache private bookings or permission-sensitive content beyond its authorization/revocation contract.

For HLS/DASH, generate a short synthetic video, create multiple renditions and a segment manifest using FFmpeg, and serve the immutable segments through the edge. Measure startup buffer, rendition switching and segment misses. This is a local delivery pipeline, not a worldwide CDN.

Cleanup: stop the Caddy and DNS Compose projects using their original configuration files. Remove only generated test certificates when finished, and retain captures without tokens or private payloads.



<a id="playbook-storage"></a>


# Operational playbook: object APIs, resumable files and sync

## Reference boundary

`src/storage/chunks.mjs` implements validated local object keys, streamed uploads, checksums and chunk reconstruction. Its convenience range method reads the complete local object before slicing; replace it with bounded filesystem range streaming before claiming large-file memory efficiency. The HTTP upload/session APIs and complete file-sync product are learner extensions, not already active routes.

## 1. Implement the metadata state machine

Create `upload_sessions(id,tenant_id,owner_id,expected_size,expected_hash,state,expires_at)`, ` upload_parts(session_id,ordinal,size,hash,object_key)` and ` file_versions(id,file_id,version,size,manifest,state)`. Use server-generated IDs, unique session/ordinal keys and owner authorization. Logical states are ` created -> receiving -> verifying -> available`, with terminal aborted/expired states.

Expose initiate, upload-part, list-parts, complete, abort and authorized-download endpoints. Stream bytes through a bounded counter and checksum into a staging object. Validate length even when Content-Length is absent. Complete validates the exact ordered manifest and publishes one version transactionally; repeated completion returns that same version. Object-upload success before metadata commit creates an orphan eligible for later collection, not an advertised missing file.

## 2. Provider-backed object storage and signed requests

Map the adapter to the official S3 multipart API in a sandbox bucket or a compatible local service. Keep metadata in PostgreSQL. Generate scoped, expiring upload/download requests only after authorization, constrain object identity and size/checksum policy, and verify completed bytes before publication. A multipart ETag is not generally the whole-file SHA-256 [S3-MULTIPART].

Test aborted multipart uploads, missing/repeated/out-of-order parts, conflicting part retries, complete-versus-abort races, quotas, expired tickets and revocation. Never assume knowing a content hash grants access. Compare direct browser-to-object upload with API-proxied streaming under an explicit workload rather than treating either choice as universally mandatory.

## 3. Durable manifest, deduplication and garbage collection

Start with tenant-scoped deduplication. Track immutable chunk identity, checksums and manifests separately from permissions and user quota. Quota may charge logical bytes even when physical bytes deduplicate. Whole-file equality, fixed-size chunk reuse and content-defined chunking have different behavior after insertion near the beginning of a file.

Run a collector only after a grace period and a final reference check coordinated with publication. It must consider retained versions, uploads in progress, snapshots and restored metadata. A stale reference count cannot authorize deletion of a chunk that another transaction just published.

## 4. Commit-safe sync

Use `db/sync_cursor.sql`: tenant sequence allocation and the associated metadata/change insert occur in one transaction protected by the same tenant counter row. This intentionally serializes tenant commits. A separately allocated BIGSERIAL value is not automatically commit order.

Implement a consistent snapshot/cursor handoff, paginated changes, tombstones and an explicit 'cursor too old: full resync required' response. Two local client directories store stable file IDs, base versions and pending operations. On stale-version edits preserve both contents or request a merge; never overwrite newer data silently. Test delete/edit, move/edit and parent-cycle races. General binary file conflicts are different from an OT/CRDT text document.

## 5. Acceptance and cleanup

Required evidence: byte-identical download, interrupted upload resume, safe duplicate completion, no visible partial file, cross-tenant denial, quota race prevention, lost-broadcast catch-up, concurrent-edit preservation, reference-safe collection and restored manifests whose chunks exist. Run `node --test test/storage.test.mjs` for reference behavior, then add separate HTTP/database integration tests for the new routes.

Only point client/collector roots to disposable directories under `runtime/`. Use a dedicated sandbox bucket prefix for provider tests and delete unfinished multipart uploads as well as visible objects after saving evidence.



<a id="playbook-durability"></a>


# Operational playbook: WAL, backups, recovery and storage faults

Run destructive experiments only against the named disposable projects. Preserve the client operation history and evidence outside their volumes. A process crash, deleted volume, disk corruption and regional loss are different failures.

## 1. Dependency-free log and integrity experiments

```bash
node experiments/run.mjs storage
node experiments/run.mjs erasure
node --test test/storage.test.mjs
```

The reference WAL serializes one writer, checksums records, synchronizes before acknowledging, ignores an incomplete final record on recovery, and rejects complete corrupt records. It is not a multi-process database or full LSM engine. Extend it with a bounded memtable, immutable sorted runs, sparse indexes, Bloom filters, compaction and crash-safe manifest replacement; test each publication boundary before deleting old runs.

The Reed-Solomon teaching implementation stores three data plus two parity fragments and verifies reconstruction from any three intact fragments. Corrupt fragments must be detected and treated as unavailable. Recoverability needs enough valid independent fragments; checksums alone cannot restore lost bytes.

## 2. Logical backup and verified restoration

```bash
sh scripts/backup.sh
# Select the exact output filename printed above.
export BACKUP_FILE=backups/eventhub-REPLACE_TIMESTAMP.dump
docker compose exec postgres createdb -U lab restore_check
docker compose exec -T postgres pg_restore -U lab -d restore_check --no-owner < "$BACKUP_FILE"
docker compose exec -T postgres psql -U lab -d restore_check < db/assert_invariants.sql
```

Use a new restore database name for each run. Compare source/target row counts, sampled contents, foreign keys, idempotency responses and outbox states. Keep restored workers disconnected from external providers to avoid replaying effects. Measure backup creation and restore times separately. A successful dump command is not evidence of usable recovery.

For full/incremental/differential exercises, first take a full snapshot of an immutable fixture tree and create two generations of changes. Build manifests recording paths, hashes and deletions. An incremental references the previous generation; a differential references the last full snapshot. Restore each chain into a new directory and deliberately remove one required generation. These file-level exercises illustrate dependency chains; database incremental backups must use the engine's supported consistency and restore procedures rather than arbitrary file copying.

## 3. Physical backup plus point-in-time recovery

Use the isolated PITR stack, never a production database:

```bash
docker compose -f infra/pitr/compose.yaml up -d --wait primary
docker compose -f infra/pitr/compose.yaml --profile tools run --rm backup
export RECOVERY_TARGET=$(docker compose -f infra/pitr/compose.yaml exec -T primary \
  psql -U lab -d eventhub -Atc 'SELECT clock_timestamp();' | tr -d '\r')
printf '%s\n' "$RECOVERY_TARGET" > evidence/pitr-target.txt
sleep 2
docker compose -f infra/pitr/compose.yaml exec primary psql -U lab -d eventhub -c \
  "DELETE FROM recovery_probe WHERE id=1; SELECT pg_switch_wal();"
docker compose -f infra/pitr/compose.yaml exec primary psql -U lab -d eventhub -c \
  'SELECT archived_count,last_archived_wal,last_archived_time,failed_count FROM pg_stat_archiver;'
```

Before restoring, confirm the forced WAL segment has been archived; poll the statistics and archive directory, rather than assuming a fixed sleep is sufficient. The base backup command verifies its manifest. The deletion occurs after the recorded target, which is the desired restore point. Recovery requires a complete chain of WAL from the backup through that point [PG-PITR].

```bash
RECOVERY_TARGET="$RECOVERY_TARGET" docker compose -f infra/pitr/compose.yaml \
  --profile restore up -d recovery
docker compose -f infra/pitr/compose.yaml logs -f recovery
# After recovery completes, in another terminal:
docker compose -f infra/pitr/compose.yaml exec recovery psql -U lab -d eventhub -c \
  'TABLE recovery_probe; SELECT pg_is_in_recovery();'
```

The original must show the row deleted; the recovered instance must show the pre-error row. Verify target-reached messages, not merely that PostgreSQL started. Reusing a nonempty recovery directory is deliberately refused. A second run requires a new recovery volume or a documented disposable reset; never overwrite a live data directory.

## 4. Crash tests and acknowledgement boundaries

Keep an append-only client history of successful booking IDs. Kill PostgreSQL without deleting its volume; restart and query every acknowledged ID. Repeat with explicitly changed synchronous commit settings in a separate disposable project. A kill test does not reproduce power loss, a faulty write cache or disk firmware behavior.

For files, compare buffered writes, file synchronization, atomic rename and parent-directory synchronization. Record what the operating system/storage provider actually promises. For broker messages, compare unconfirmed publication, confirmed publication, effect commit and consumer acknowledgement as separate stages.

## 5. RAID and persistent-memory track

Do not run disk administration against your workstation's real devices. Use a disposable Linux VM with newly created image files and **verified loop-device mappings**, or dedicated test hardware. Never substitute a real block-device path into examples blindly.

Create equal-sized virtual disks, attach only those files as loop devices, and assemble separate arrays forRAID 0, 1, 5, 6 and 10. Format and mount only the newly created array. Write a synthetic checksummed dataset, fail one/two virtual members, measure read/rebuild behavior and compare usable capacity. Record layout-dependent RAID10 failure cases. Tear down in reverse order: unmount the test mount, stop the test array, detach only recorded loop devices, then remove only the test files. A virtual-device model does not benchmark physical disk reliability.

For NVDIMM, battery-backed RAM and persistent memory, enumerate supported hardware and driver modes before allocating a test namespace. Compare durability APIs, crash behavior and fallback paths using platform/vendor documentation. Without suitable hardware, complete the failure-boundary design exercise and mark performance as not measured.

## 6. Replication is not backup

Delete a synthetic row on a primary and watch it disappear from the replica. Recover from an earlier verified backup. Test loss of an encryption key separately from loss of ciphertext. Object versions and immutable retention require explicit permissions and lifecycle policies; a directory named `immutable` is not an enforced retention control.

For disaster recovery, record RPO(recoverable acknowledged history gap)and RTO(time to verified useful service), restore both metadata and bytes, reconcile pending external work, validate permissions/tombstones, and reopen traffic only after invariant checks. Keep region simulations separate from real regional evidence.

## Cleanup

```bash
docker compose -f infra/pitr/compose.yaml --profile tools --profile restore down
```

Retain backup/archive volumes until verification is complete. Deleting them is an intentional data destruction step, not a recovery experiment.



<a id="variants"></a>


# All 70 source problem variants

The names and order below follow chapter 16 of the source repository. Each is a **newly authored implementation assignment**, not a claim that70 complete products are shipped. Reuse the indicated labs, then implement the distinctive behavior and pass the specified failure test. No company's private architecture, production throughput or certification is reproduced.

For every variant submit an API contract, data model, working critical path, one failure/recovery experiment, measured workload results and an ADR. References to a lab do not waive the variant-specific work.

## V01. Load Balancer Design

**Build on:** 01-LB, 02-HORIZONTAL.

**Implementation delta:** Implement membership, round-robin/weighted/least-connections selection, passive failure accounting, connection draining and per-backend metrics.

**Failure and acceptance gate:** Stop the busiest backend during read/write traffic; preserve bounded errors and never blindly replay an unsafe write.

## V02. API Gateway Service

**Build on:** 01-PROXY, 14-PROTOCOLS.

**Implementation delta:** Add versioned route configuration, authentication forwarding, per-tenant quotas, request/body limits, deadlines and bounded aggregation.

**Failure and acceptance gate:** Give one optional upstream a long delay; critical routes must remain usable and untrusted headers must not choose a tenant.

## V03. Rate Limiter (Distributed)

**Build on:** 17-LIMITER, 09-LIMITERS.

**Implementation delta:** Wrap all five limiter algorithms behind one contract, then implement shared atomic Redis state, separate tenant/user budgets and Retry-After.

**Failure and acceptance gate:** Burst across two API replicas, cross a window boundary and fail Redis; measure approximation, boundary bursts and the explicit fail-open/closed policy.

## V04. Web App Firewall (WAF)

**Build on:** 12-WAF, 12-INPUT.

**Implementation delta:** Build a defensive request-policy gateway with rule versioning, body/header bounds, normalized paths, audit decisions and a false-positive fixture corpus.

**Failure and acceptance gate:** Send harmless malformed requests only to the lab; reject the intended cases while normal catalog and booking requests still pass.

## V05. DDOS Protection System

**Build on:** 12-WAF, 09-BULKHEAD.

**Implementation delta:** Model a layered defense with admission limits, bounded queues, cache shielding and separate connection/request budgets; keep generated traffic small and local.

**Failure and acceptance gate:** A bounded local traffic spike must not cause unbounded memory growth. Record that upstream bandwidth scrubbing requires a provider, not a laptop proof.

## V06. Secrets Manager (Vault)

**Build on:** 12-SECRETS, 12-AUDIT.

**Implementation delta:** Create an adapter for leased secrets, narrowly scoped service identities, rotation versions, audit records and revocation handling.

**Failure and acceptance gate:** Rotate a database credential while requests run; old leases stop working under policy and no secret appears in logs or artifacts.

## V07. URL Shortener (TinyURL)

**Build on:** 18-CREATE, 18-REDIRECT.

**Implementation delta:** Implement durable unique aliases, optional custom names, expiry, owner management, cache-backed redirects and asynchronous click records.

**Failure and acceptance gate:** Force allocation collisions, warm then expire a link, replay create requests and disable a destination while cached.

## V08. Pastebin (Text Storage)

**Build on:** 19-STORE, 19-EXPIRE.

**Implementation delta:** Separate text bytes from metadata, enforce size and visibility, publish only verified objects, and implement logical/cache/physical expiry.

**Failure and acceptance gate:** Crash between upload and metadata commit; a staged orphan may remain but an incomplete or expired paste must not become readable.

## V09. Distributed ID (Snowflake)

**Build on:** 17-UTILITIES, 02-HASH.

**Implementation delta:** Expose an ID allocator with persisted time/sequence state and leased unique worker IDs; compare sequence, UUID, random Base62 and Snowflake.

**Failure and acceptance gate:** Force clock rollback, sequence exhaustion and a duplicated worker lease; reject unsafe generation rather than silently issuing duplicates.

## V10. Consistent Hashing

**Build on:** 02-HASH, 02-MIGRATE.

**Implementation delta:** Build a versioned hash-ring membership service with virtual-node weights, routing visualization and a separate data-movement protocol.

**Failure and acceptance gate:** Add/remove nodes and compare key movement with modulo routing; routing changes alone must not be treated as migrated data.

## V11. Content Delivery Network

**Build on:** 01-EDGE, 10-CACHE.

**Implementation delta:** Implement local edge and shield caches, immutable URLs, pull/push/hybrid fills, request coalescing and separate private-cache policies.

**Failure and acceptance gate:** Kill the origin after warming, rotate an asset version, then test a cold miss and revoked private content without leaking responses.

## V12. Multi-region Router

**Build on:** 02-GEO, 04-REGIONS.

**Implementation delta:** Route synthetic regions through a trusted home-region directory, health status and measured or simulated latency; distinguish read and write policy.

**Failure and acceptance gate:** Partition the home writer and retain stale DNS answers; strong writes reject or follow a fenced failover, not a second uncontrolled writer.

## V13. News Feed (Push/Pull)

**Build on:** 17-FEED, 07-CQRS.

**Implementation delta:** Implement pull joins, push inbox materialization and a hybrid for high-follower authors, with stable cursors and unique user/post entries.

**Failure and acceptance gate:** Replay fan-out, unfollow during publication and delete a private post; no duplicate entries or permission leaks may survive.

## V14. Instagram (Media Feed)

**Build on:** 17-FEED, 08-VIDEO.

**Implementation delta:** Add a media-ready state, asynchronous rendition generation, immutable asset URLs and a feed that references only published media versions.

**Failure and acceptance gate:** Delay transcoding and revoke a post while cached; the feed must not publish missing bytes or expose revoked private media.

## V15. Pinterest (Image Feed)

**Build on:** 17-FEED, 17-RECOMMEND.

**Implementation delta:** Model boards, pins, image renditions and paginated board/feed aggregation; rank eligible candidates after permission filtering.

**Failure and acceptance gate:** Move/delete a board during pagination and duplicate a pin job; ordering is deterministic and private boards stay private.

## V16. Twitter (Write Path)

**Build on:** 17-FEED, 07-ORDER.

**Implementation delta:** Partition publication events by author, batch follower fan-out, checkpoint jobs and use a separate path for celebrity-scale authors.

**Failure and acceptance gate:** Interrupt a batch after writing half its inbox rows; replay must complete exactly the missing materialization without duplicating posts.

## V17. Reddit (Ranking)

**Build on:** 17-FEED, 17-PROCESSING.

**Implementation delta:** Store one active vote per synthetic user/post, project scores from versioned events, and implement a declared score/ranking function.

**Failure and acceptance gate:** Deliver vote/unvote events out of order and replay them; authoritative vote uniqueness and rebuilt scores must agree.

## V18. HackerNews (Ranking)

**Build on:** 17-FEED, 15-TRAFFIC.

**Implementation delta:** Add deterministic score decay using an injectable clock, a ranking snapshot version and stable tie-breakers for paginated results.

**Failure and acceptance gate:** Jump the test clock and add new posts mid-page; compare live ranking with snapshot pagination without calling the formula an industry replica.

## V19. Friend Suggestions

**Build on:** 17-RECOMMEND, 05-GRAPH.

**Implementation delta:** Build a synthetic follow graph, two-hop candidate generation, common-neighbor scoring, exclusions and a cold-start fallback.

**Failure and acceptance gate:** Remove access or block a user during projection lag; suggestions must exclude forbidden candidates and avoid duplicate identities.

## V20. WhatsApp (1-on-1)

**Build on:** 17-REALTIME, 07-ORDER.

**Implementation delta:** Implement two-user rooms, stable client message IDs, durable commit-before-ACK, offline history and per-recipient delivery receipts.

**Failure and acceptance gate:** Drop the connection after persistence but before ACK, resend, and reconnect through another server; one logical message remains one record.

## V21. Group Chat System

**Build on:** 17-REALTIME, 07-TOPOLOGY.

**Implementation delta:** Add membership versions, bounded room fan-out, per-room sequence allocation and independently tracked recipient cursors.

**Failure and acceptance gate:** Revoke a member during a partition and replay a missing message batch; room permissions remain authoritative and reconnect fills gaps.

## V22. Presence Platform

**Build on:** 17-REALTIME, 06-DISTRIBUTED.

**Implementation delta:** Implement heartbeat leases, last-seen timestamps, TTL-based online state and bounded subscription fan-out, separate from durable chat history.

**Failure and acceptance gate:** Pause a client, delay heartbeats and restart a presence node; stale status expires and never deletes persisted messages.

## V23. Slack (Enterprise)

**Build on:** 17-REALTIME, 12-AUTHZ.

**Implementation delta:** Add workspace/channel membership, private channels, threaded messages, search projection and retention policy.

**Failure and acceptance gate:** Reuse channel IDs across tenant fixtures and revoke access while search lags; every delivery, history and search path enforces workspace scope.

## V24. Notification Service

**Build on:** 07-OUTBOX, 07-INBOX.

**Implementation delta:** Build preference-aware delivery jobs, stable provider operation IDs, retries, DLQ, deduplication, throttling and reconciliation.

**Failure and acceptance gate:** A fake provider performs an effect then drops its response; retries must reuse identity, and unknown outcomes remain visible.

## V25. Zoom Video Conf.

**Build on:** 01-UDP, 17-REALTIME.

**Implementation delta:** Create two local WebRTC clients with signaling, room admission, connection-state reporting and a bounded media relay extension.

**Failure and acceptance gate:** Disconnect one peer and constrain synthetic bandwidth; observe ICE/TURN and reconnection behavior without claiming conferencing production scale.

## V26. Email Service (Gmail)

**Build on:** 07-OUTBOX, 17-SEARCH.

**Implementation delta:** Use a local mail sink and synthetic addresses; persist mailbox metadata, object-backed attachments, per-user folders and searchable projections.

**Failure and acceptance gate:** Duplicate delivery, reject oversized attachments and pause indexing; mailbox ownership and one accepted logical delivery remain intact.

## V27. YouTube (Storage)

**Build on:** 08-VIDEO, 21-CHUNK.

**Implementation delta:** Build resumable uploads, verified source objects, asynchronous multi-rendition transcoding, manifests and a media-ready publication state.

**Failure and acceptance gate:** Crash a worker after one rendition, retry and compare checksums; partial processing must not advertise a complete unavailable stream.

## V28. Netflix Distribution

**Build on:** 01-EDGE, 08-VIDEO.

**Implementation delta:** Preposition a small synthetic media catalog at two local edges, separate manifests from segments, and compare cache/shield strategies.

**Failure and acceptance gate:** Lose one edge or rendition and measure controlled fallback, buffering and origin load; do not infer global CDN guarantees.

## V29. TikTok (Video Feed)

**Build on:** 17-RECOMMEND, 08-VIDEO.

**Implementation delta:** Combine eligibility-filtered ranking, short-video manifests, bounded next-item prefetch and exposure-event deduplication.

**Failure and acceptance gate:** Change user eligibility or skip many videos quickly; prefetch remains bounded and private or deleted content is not served.

## V30. Spotify (Streaming)

**Build on:** 08-VIDEO, 21-SYNC.

**Implementation delta:** Model tracks, playlists, immutable audio assets, playback checkpoints and offline manifest synchronization with licensing represented by synthetic permissions.

**Failure and acceptance gate:** Edit a playlist offline on two clients and revoke an asset; preserve conflict policy and distinguish metadata access from cached-byte revocation.

## V31. Twitch (Live Stream)

**Build on:** 08-VIDEO, 17-REALTIME.

**Implementation delta:** Produce numbered live segments, a sliding manifest, bounded player buffers and reconnect catch-up to a declared live window.

**Failure and acceptance gate:** Pause segment publication, resume with a gap and disconnect a viewer; playback reports a gap rather than serving inconsistent sequence state.

## V32. Video Transcoding

**Build on:** 07-ORDER, 08-VIDEO.

**Implementation delta:** Implement a DAG of source validation, thumbnailing, rendition tasks and manifest publication with deterministic job IDs.

**Failure and acceptance gate:** Kill a worker midway and redeliver its task; output publication is idempotent, compute is bounded and the manifest lists only verified outputs.

## V33. Uber/Lyft (Matching)

**Build on:** 17-GEO, 20-HOLDS.

**Implementation delta:** Generate fictional driver positions, use nearby candidate lookup, then atomically assign a driver using lease/version checks.

**Failure and acceptance gate:** Two dispatchers choose the same driver and stale positions arrive late; no conflicting active assignment is committed.

## V34. Google Maps (Paths)

**Build on:** 17-GEO, 10-COMPUTE.

**Implementation delta:** Build a small weighted road graph, shortest-path API, versioned traffic weights, route cache and bounded alternate-route search.

**Failure and acceptance gate:** Close a road during a query and compare snapshot versus live graph semantics; the returned route references one defined graph version.

## V35. Yelp/Nearby

**Build on:** 17-GEO, 05-INDEX.

**Implementation delta:** Index synthetic venues spatially, validate coordinate order/radius, apply tenant/visibility filters, and rank by exact distance after candidate selection.

**Failure and acceptance gate:** Swap coordinates, test dateline/boundary cases and request an excessive radius; units and bounds remain explicit.

## V36. Food Delivery Tracker

**Build on:** 17-GEO, 17-REALTIME.

**Implementation delta:** Track synthetic delivery positions with sequence/time metadata, publish authorized updates and separate location observations from order state.

**Failure and acceptance gate:** Deliver old positions after newer ones and disconnect a subscriber; the map does not regress and reconnect obtains a snapshot.

## V37. Transit Tracker

**Build on:** 17-GEO, 01-EDGE.

**Implementation delta:** Combine versioned static map/route data with real-time synthetic vehicle positions and arrival estimates.

**Failure and acceptance gate:** Invalidate a route while a client holds cached tiles; stale geometry and live positions are labeled consistently rather than mixed silently.

## V38. Hotel Booking

**Build on:** 20-HOLDS, 03-ISOLATION.

**Implementation delta:** Represent room/date inventory, atomically reserve every night in a requested interval and integrate a fake payment saga.

**Failure and acceptance gate:** Race overlapping multi-night bookings and one unavailable night; the transaction reserves all requested nights or none.

## V39. AirBnB Reservation

**Build on:** 20-HOLDS, 03-DISTRIBUTED-TX.

**Implementation delta:** Model a unique property calendar with interval conflict detection, expiring holds, cancellation and owner/guest permissions.

**Failure and acceptance gate:** Race partially overlapping stays and late payment callbacks; no two confirmed intervals overlap and expired holds cannot be revived unsafely.

## V40. Web Crawler

**Build on:** 17-CRAWLER, 07-ORDER.

**Implementation delta:** Build an allowlisted local frontier with canonical URLs, robots rules, per-host politeness, checksums and durable job states.

**Failure and acceptance gate:** Create redirect loops, duplicate pages, disallowed destinations and oversized responses; the crawler stays bounded and inside its allowlist.

## V41. Autocomplete

**Build on:** 17-SEARCH, 06-DISTRIBUTED.

**Implementation delta:** Create a prefix index/trie, bounded top suggestions, update versions and cache invalidation, with request-time eligibility filtering.

**Failure and acceptance gate:** Delete a term while its cache is warm and send long prefixes; results remain permitted and resource work is bounded.

## V42. Google Search

**Build on:** 17-CRAWLER, 17-SEARCH.

**Implementation delta:** Combine the local crawler, inverted index, a small PageRank-style graph score, snippets and index-generation cutover.

**Failure and acceptance gate:** Rebuild while documents are deleted/updated; catch-up and tombstones prevent resurrection and results use one declared generation.

## V43. Elasticsearch (Logs)

**Build on:** 17-OBSERVE, 05-PARTITION.

**Implementation delta:** Ingest synthetic structured logs, apply time partitions/index lifecycle, bounded full-text queries and explicit retention.

**Failure and acceptance gate:** Flood high-cardinality fields and pause the sink; buffers stay bounded and loss/backpressure is accounted for.

## V44. E-commerce Search

**Build on:** 17-SEARCH, 07-CQRS.

**Implementation delta:** Build product facets, price/category filters, autocomplete and a versioned asynchronous search projection.

**Failure and acceptance gate:** Update price/stock while indexing is delayed; browsing may lag under contract, but checkout consults authoritative price/inventory.

## V45. Payment Gateway

**Build on:** 17-PAYMENT, 12-MESSAGE-SECURITY.

**Implementation delta:** Integrate a fake provider with stable operation IDs, signed callbacks, durable attempts, reconciliation and separately idempotent refunds.

**Failure and acceptance gate:** Timeout after effect, duplicate callback, out-of-order callback and late success must all reach explicit reconciled or compensation states.

## V46. Digital Wallet

**Build on:** 17-PAYMENT, 03-ISOLATION.

**Implementation delta:** Implement an integer-minor-unit double-entry ledger with balanced postings, transaction identities and atomic balance constraints.

**Failure and acceptance gate:** Race debits, replay a transfer and attempt cross-currency imbalance; no negative disallowed balance or unbalanced posting is committed.

## V47. Crypto Exchange

**Build on:** 17-PAYMENT, 07-ORDER.

**Implementation delta:** Use synthetic balances and a deterministic price/time-priority order book with reserved funds and append-only trade events.

**Failure and acceptance gate:** Replay orders and crash between match and settlement projection; reservations, fills and ledger postings reconcile without real-money operations.

## V48. Fraud Detection

**Build on:** 17-PROCESSING, 17-RECOMMEND.

**Implementation delta:** Build a synthetic transaction stream, deterministic rules/features, time-window aggregation and a reviewed decision audit trail.

**Failure and acceptance gate:** Duplicate or late events must not inflate counts; offline evaluation uses chronological splits and explicitly labeled simulated outcomes.

## V49. Stock Brokerage

**Build on:** 17-PAYMENT, 07-CQRS.

**Implementation delta:** Model synthetic order submission, reservation, execution callbacks, partial fills, cancellation and a ledger-backed position projection.

**Failure and acceptance gate:** Receive a fill after cancellation request and replay execution IDs; quantities and funds reconcile according to an explicit state machine.

## V50. Ad-Click Aggregator

**Build on:** 17-PROCESSING, 07-INBOX.

**Implementation delta:** Ingest stable click IDs, deduplicate by declared window, aggregate event-time buckets and compare exact counts with sketches.

**Failure and acceptance gate:** Replay a partition, inject late clicks and rotate windows; final exact counts match the ground truth and approximate errors are measured.

## V51. Ticketmaster

**Build on:** 20-HOLDS, 20-FLASH.

**Implementation delta:** Implement numbered-seat holds, waiting-room admission, stable booking/payment identities and outbox-driven seat maps.

**Failure and acceptance gate:** Direct concurrent clients to one seat, expire a hold and send late payment success; only the current valid owner can confirm.

## V52. Flash Sale System

**Build on:** 02-HOTKEY, 20-FLASH.

**Implementation delta:** Add bounded admission and tenant quotas before guarded inventory transactions; separate cheap browse cache from authoritative checkout.

**Failure and acceptance gate:** Instantaneous synthetic demand exceeds capacity; admitted work preserves conservation and rejected work fails promptly without retry amplification.

## V53. Flight Reservation

**Build on:** 20-HOLDS, 03-DISTRIBUTED-TX.

**Implementation delta:** Represent legs and seats separately, reserve a multi-leg itinerary atomically where colocated, and use a saga where participants differ.

**Failure and acceptance gate:** Fail one leg or receive late payment success after expiry; compensate completed steps and never silently report a partially reserved itinerary as confirmed.

## V54. Online Quiz (Kahoot)

**Build on:** 17-REALTIME, 17-PROCESSING.

**Implementation delta:** Implement server-timed questions, one accepted answer per participant/question, deterministic scoring and live ranking snapshots.

**Failure and acceptance gate:** Duplicate answers, delayed clients and reconnects do not award repeated points or bypass the server deadline.

## V55. Election System

**Build on:** 12-AUDIT, 03-ISOLATION.

**Implementation delta:** For a synthetic classroom-only system, separate anonymous test ballots from eligibility fixtures, enforce one-use submission tokens and independently recount an append-only record set.

**Failure and acceptance gate:** Replay tokens and corrupt a copied tally; duplicate rejection and discrepancy detection must work. This is not a certified public-election system.

## V56. Distributed Cache

**Build on:** 17-CACHE, 06-DISTRIBUTED.

**Implementation delta:** Expose a cache client with ring membership, eviction policies, versioned entries, singleflight and failure-aware origin fallback.

**Failure and acceptance gate:** Remove a node during hot-key expiry and drop invalidations; authoritative data remains intact and fallback load is bounded.

## V57. Message Queue (Kafka)

**Build on:** 07-KAFKA, 11-WAL.

**Implementation delta:** Build a teaching append-only partition log with offsets and retention, then compare it with the supplied Kafka cluster and consumer groups.

**Failure and acceptance gate:** Crash before/after append or checkpoint and replay; distinguish broker record delivery from downstream effect uniqueness.

## V58. S3 Object Storage

**Build on:** 08-DIRECT, 11-INTEGRITY.

**Implementation delta:** Implement immutable object identity, manifests, checksums, replicated/erasure fragments, repair and metadata publication states.

**Failure and acceptance gate:** Lose or corrupt fragments during reads and repair; return verified bytes only when enough valid fragments exist.

## V59. Key-Value Store (Paxos)

**Build on:** 03-PAXOS, 03-RAFT.

**Implementation delta:** Extend the single-decree model into a documented multi-slot design exercise with durable promises, accepted values, ordered application and snapshots; compare with real etcd behavior.

**Failure and acceptance gate:** Conflicting proposals, proposer replacement and minority partitions cannot create two chosen values for one slot; unimplemented persistence is not assumed.

## V60. HDFS (Distributed FS)

**Build on:** 08-TYPES, 11-INTEGRITY.

**Implementation delta:** Model a metadata authority, large-file blocks, replica placement, heartbeats and checksum-based re-replication across isolated nodes.

**Failure and acceptance gate:** Lose a data node and restart metadata from a checkpoint/log; block maps must not point to falsely healthy or corrupt replicas.

## V61. Logging Pipeline

**Build on:** 17-OBSERVE, 07-ORDER.

**Implementation delta:** Build bounded collection, parsing, buffering, partitioned retention and a searchable sink with sensitive-field redaction.

**Failure and acceptance gate:** Stop the sink and exhaust the buffer; account for dropped or deferred events without blocking critical booking transactions indefinitely.

## V62. Metrics Monitoring

**Build on:** 17-OBSERVE, 13-OBSERVABILITY.

**Implementation delta:** Scrape each replica, retain bounded labels, compute histograms correctly, evaluate alerts and attach runbooks.

**Failure and acceptance gate:** Lose the collector and introduce a queue backlog without API latency change; alerts distinguish missing telemetry from healthy service.

## V63. Task Scheduler

**Build on:** 17-UTILITIES, 09-DISTRIBUTED-LIMIT.

**Implementation delta:** Persist due jobs, atomic claims, lease epochs, renewal, retries and an idempotent guarded effect.

**Failure and acceptance gate:** Pause worker A past lease expiry, let B claim, then resume A; the storage resource rejects the stale fencing token.

## V64. Top K (Heavy Hitters)

**Build on:** 17-PROCESSING, 15-TRAFFIC.

**Implementation delta:** Compare exact frequency counts with Count-Min Sketch plus a bounded candidate structure, merge partition summaries and rotate windows.

**Failure and acceptance gate:** Generate skew, collisions, duplicates and late events; report memory and measured approximation error against exact truth.

## V65. A/B Testing Platform

**Build on:** 13-DEPLOY, 17-RECOMMEND.

**Implementation delta:** Implement stable user/experiment hashing, allocation versions, exposure deduplication, guardrail metrics and a kill switch.

**Failure and acceptance gate:** Change rollout percentage and resend exposure events; assignment and denominators remain reproducible without unreported cohort drift.

## V66. Data Warehouse

**Build on:** 05-PARTITION, 17-PROCESSING.

**Implementation delta:** Build an incremental fact/dimension load, event-time partitioning, schema evolution, deduplication and rebuildable analytical aggregates.

**Failure and acceptance gate:** Reprocess a day, receive a correction and interrupt checkpointing; the warehouse reconciles to declared source records and retention.

## V67. Google Docs (OT/CRDT)

**Build on:** 17-COLLAB, 03-MERGE.

**Implementation delta:** Implement authorized collaborative text using a maintained OT/CRDT library, durable update log, reconnect replay and validated snapshots.

**Failure and acceptance gate:** Concurrent offline edits, duplicate updates and revocation are tested separately; convergence never substitutes for authorization.

## V68. Whiteboard (Miro)

**Build on:** 17-COLLAB, 17-REALTIME.

**Implementation delta:** Give every shape/stroke a stable identity, define insert/update/delete merge rules, and persist compacted operation history.

**Failure and acceptance gate:** Edit/delete a shape offline on two clients, reorder operations and reload; both clients converge under the documented conflict policy.

## V69. Code Judge (LeetCode)

**Build on:** 09-BULKHEAD, 12-NETWORK.

**Implementation delta:** Queue only fixed trusted fixture programs in this lab, isolate execution in a disposable VM/container sandbox, disable network access and cap CPU, memory, time and output.

**Failure and acceptance gate:** A fixture loops or emits excess output; the job is terminated and other work continues. Do not execute arbitrary untrusted code on the host.

## V70. Google Forms

**Build on:** 14-VERSION, 05-MODEL.

**Implementation delta:** Version form schemas, validate typed answers against the submission's schema version, store idempotent responses and build exports.

**Failure and acceptance gate:** Change required fields while old clients submit and replay a response key; accepted data remains interpretable and tenant-owned exports stay isolated.



<a id="overlays"></a>


# Cross-cutting system-pattern overlays

The source repeats archetype-specific discussions across core chapters. The following120 review assignments preserve that cross-cutting structure without pretending every combination needs a new service. Build each pattern's critical path first; then apply the chapter angle, run the concrete failure and record applicable/nonapplicable decisions.

## Chapter 01: trace protocol/hop cost and reconnect behavior

| Source archetype | Build on | Required evidence |
|---|---|---|
| A: CRUD / storage | 17-CRUD | Apply chapter 01 to owner-scoped CRUD, version conflicts and orphan cleanup; trace protocol/hop cost and reconnect behavior. |
| B: Feed / timeline | 17-FEED | Apply chapter 01 to fan-out replay, pagination and revoked-post filtering; trace protocol/hop cost and reconnect behavior. |
| C: Real-time | 17-REALTIME | Apply chapter 01 to commit-before-ACK, reconnect gaps and slow clients; trace protocol/hop cost and reconnect behavior. |
| D: Search | 17-SEARCH | Apply chapter 01 to projection lag, tombstones and rebuild cutover; trace protocol/hop cost and reconnect behavior. |
| E: Geospatial | 17-GEO | Apply chapter 01 to coordinate units, stale positions and atomic assignment; trace protocol/hop cost and reconnect behavior. |
| F: Financial / critical consistency | 17-PAYMENT | Apply chapter 01 to stable operation identity, balanced ledger and ambiguous outcomes; trace protocol/hop cost and reconnect behavior. |
| G: Processing / infrastructure | 17-PROCESSING | Apply chapter 01 to checkpoint replay, bounded memory and partition ownership; trace protocol/hop cost and reconnect behavior. |
| H: Collaboration / utilities | 17-COLLAB | Apply chapter 01 to concurrent updates, access revocation and durable replay; trace protocol/hop cost and reconnect behavior. |

## Chapter 02: load the limiting resource and compare placement/scale

| Source archetype | Build on | Required evidence |
|---|---|---|
| A: CRUD / storage | 17-CRUD | Apply chapter 02 to owner-scoped CRUD, version conflicts and orphan cleanup; load the limiting resource and compare placement/scale. |
| B: Feed / timeline | 17-FEED | Apply chapter 02 to fan-out replay, pagination and revoked-post filtering; load the limiting resource and compare placement/scale. |
| C: Real-time | 17-REALTIME | Apply chapter 02 to commit-before-ACK, reconnect gaps and slow clients; load the limiting resource and compare placement/scale. |
| D: Search | 17-SEARCH | Apply chapter 02 to projection lag, tombstones and rebuild cutover; load the limiting resource and compare placement/scale. |
| E: Geospatial | 17-GEO | Apply chapter 02 to coordinate units, stale positions and atomic assignment; load the limiting resource and compare placement/scale. |
| F: Financial / critical consistency | 17-PAYMENT | Apply chapter 02 to stable operation identity, balanced ledger and ambiguous outcomes; load the limiting resource and compare placement/scale. |
| G: Processing / infrastructure | 17-PROCESSING | Apply chapter 02 to checkpoint replay, bounded memory and partition ownership; load the limiting resource and compare placement/scale. |
| H: Collaboration / utilities | 17-COLLAB | Apply chapter 02 to concurrent updates, access revocation and durable replay; load the limiting resource and compare placement/scale. |

## Chapter 03: record concurrent histories and declare the read/write contract

| Source archetype | Build on | Required evidence |
|---|---|---|
| A: CRUD / storage | 17-CRUD | Apply chapter 03 to owner-scoped CRUD, version conflicts and orphan cleanup; record concurrent histories and declare the read/write contract. |
| B: Feed / timeline | 17-FEED | Apply chapter 03 to fan-out replay, pagination and revoked-post filtering; record concurrent histories and declare the read/write contract. |
| C: Real-time | 17-REALTIME | Apply chapter 03 to commit-before-ACK, reconnect gaps and slow clients; record concurrent histories and declare the read/write contract. |
| D: Search | 17-SEARCH | Apply chapter 03 to projection lag, tombstones and rebuild cutover; record concurrent histories and declare the read/write contract. |
| E: Geospatial | 17-GEO | Apply chapter 03 to coordinate units, stale positions and atomic assignment; record concurrent histories and declare the read/write contract. |
| F: Financial / critical consistency | 17-PAYMENT | Apply chapter 03 to stable operation identity, balanced ledger and ambiguous outcomes; record concurrent histories and declare the read/write contract. |
| G: Processing / infrastructure | 17-PROCESSING | Apply chapter 03 to checkpoint replay, bounded memory and partition ownership; record concurrent histories and declare the read/write contract. |
| H: Collaboration / utilities | 17-COLLAB | Apply chapter 03 to concurrent updates, access revocation and durable replay; record concurrent histories and declare the read/write contract. |

## Chapter 04: measure useful service during one bounded outage

| Source archetype | Build on | Required evidence |
|---|---|---|
| A: CRUD / storage | 17-CRUD | Apply chapter 04 to owner-scoped CRUD, version conflicts and orphan cleanup; measure useful service during one bounded outage. |
| B: Feed / timeline | 17-FEED | Apply chapter 04 to fan-out replay, pagination and revoked-post filtering; measure useful service during one bounded outage. |
| C: Real-time | 17-REALTIME | Apply chapter 04 to commit-before-ACK, reconnect gaps and slow clients; measure useful service during one bounded outage. |
| D: Search | 17-SEARCH | Apply chapter 04 to projection lag, tombstones and rebuild cutover; measure useful service during one bounded outage. |
| E: Geospatial | 17-GEO | Apply chapter 04 to coordinate units, stale positions and atomic assignment; measure useful service during one bounded outage. |
| F: Financial / critical consistency | 17-PAYMENT | Apply chapter 04 to stable operation identity, balanced ledger and ambiguous outcomes; measure useful service during one bounded outage. |
| G: Processing / infrastructure | 17-PROCESSING | Apply chapter 04 to checkpoint replay, bounded memory and partition ownership; measure useful service during one bounded outage. |
| H: Collaboration / utilities | 17-COLLAB | Apply chapter 04 to concurrent updates, access revocation and durable replay; measure useful service during one bounded outage. |

## Chapter 05: inspect constraints, plans and transaction boundaries

| Source archetype | Build on | Required evidence |
|---|---|---|
| A: CRUD / storage | 17-CRUD | Apply chapter 05 to owner-scoped CRUD, version conflicts and orphan cleanup; inspect constraints, plans and transaction boundaries. |
| B: Feed / timeline | 17-FEED | Apply chapter 05 to fan-out replay, pagination and revoked-post filtering; inspect constraints, plans and transaction boundaries. |
| C: Real-time | 17-REALTIME | Apply chapter 05 to commit-before-ACK, reconnect gaps and slow clients; inspect constraints, plans and transaction boundaries. |
| D: Search | 17-SEARCH | Apply chapter 05 to projection lag, tombstones and rebuild cutover; inspect constraints, plans and transaction boundaries. |
| E: Geospatial | 17-GEO | Apply chapter 05 to coordinate units, stale positions and atomic assignment; inspect constraints, plans and transaction boundaries. |
| F: Financial / critical consistency | 17-PAYMENT | Apply chapter 05 to stable operation identity, balanced ledger and ambiguous outcomes; inspect constraints, plans and transaction boundaries. |
| G: Processing / infrastructure | 17-PROCESSING | Apply chapter 05 to checkpoint replay, bounded memory and partition ownership; inspect constraints, plans and transaction boundaries. |
| H: Collaboration / utilities | 17-COLLAB | Apply chapter 05 to concurrent updates, access revocation and durable replay; inspect constraints, plans and transaction boundaries. |

## Chapter 06: test cache key scope, stale fills, eviction and fallback

| Source archetype | Build on | Required evidence |
|---|---|---|
| A: CRUD / storage | 17-CRUD | Apply chapter 06 to owner-scoped CRUD, version conflicts and orphan cleanup; test cache key scope, stale fills, eviction and fallback. |
| B: Feed / timeline | 17-FEED | Apply chapter 06 to fan-out replay, pagination and revoked-post filtering; test cache key scope, stale fills, eviction and fallback. |
| C: Real-time | 17-REALTIME | Apply chapter 06 to commit-before-ACK, reconnect gaps and slow clients; test cache key scope, stale fills, eviction and fallback. |
| D: Search | 17-SEARCH | Apply chapter 06 to projection lag, tombstones and rebuild cutover; test cache key scope, stale fills, eviction and fallback. |
| E: Geospatial | 17-GEO | Apply chapter 06 to coordinate units, stale positions and atomic assignment; test cache key scope, stale fills, eviction and fallback. |
| F: Financial / critical consistency | 17-PAYMENT | Apply chapter 06 to stable operation identity, balanced ledger and ambiguous outcomes; test cache key scope, stale fills, eviction and fallback. |
| G: Processing / infrastructure | 17-PROCESSING | Apply chapter 06 to checkpoint replay, bounded memory and partition ownership; test cache key scope, stale fills, eviction and fallback. |
| H: Collaboration / utilities | 17-COLLAB | Apply chapter 06 to concurrent updates, access revocation and durable replay; test cache key scope, stale fills, eviction and fallback. |

## Chapter 07: inject duplicate, delayed and reordered events

| Source archetype | Build on | Required evidence |
|---|---|---|
| A: CRUD / storage | 17-CRUD | Apply chapter 07 to owner-scoped CRUD, version conflicts and orphan cleanup; inject duplicate, delayed and reordered events. |
| B: Feed / timeline | 17-FEED | Apply chapter 07 to fan-out replay, pagination and revoked-post filtering; inject duplicate, delayed and reordered events. |
| C: Real-time | 17-REALTIME | Apply chapter 07 to commit-before-ACK, reconnect gaps and slow clients; inject duplicate, delayed and reordered events. |
| D: Search | 17-SEARCH | Apply chapter 07 to projection lag, tombstones and rebuild cutover; inject duplicate, delayed and reordered events. |
| E: Geospatial | 17-GEO | Apply chapter 07 to coordinate units, stale positions and atomic assignment; inject duplicate, delayed and reordered events. |
| F: Financial / critical consistency | 17-PAYMENT | Apply chapter 07 to stable operation identity, balanced ledger and ambiguous outcomes; inject duplicate, delayed and reordered events. |
| G: Processing / infrastructure | 17-PROCESSING | Apply chapter 07 to checkpoint replay, bounded memory and partition ownership; inject duplicate, delayed and reordered events. |
| H: Collaboration / utilities | 17-COLLAB | Apply chapter 07 to concurrent updates, access revocation and durable replay; inject duplicate, delayed and reordered events. |

## Chapter 08: identify byte ownership, publication and recovery boundaries

| Source archetype | Build on | Required evidence |
|---|---|---|
| A: CRUD / storage | 17-CRUD | Apply chapter 08 to owner-scoped CRUD, version conflicts and orphan cleanup; identify byte ownership, publication and recovery boundaries. |
| B: Feed / timeline | 17-FEED | Apply chapter 08 to fan-out replay, pagination and revoked-post filtering; identify byte ownership, publication and recovery boundaries. |
| C: Real-time | 17-REALTIME | Apply chapter 08 to commit-before-ACK, reconnect gaps and slow clients; identify byte ownership, publication and recovery boundaries. |
| D: Search | 17-SEARCH | Apply chapter 08 to projection lag, tombstones and rebuild cutover; identify byte ownership, publication and recovery boundaries. |
| E: Geospatial | 17-GEO | Apply chapter 08 to coordinate units, stale positions and atomic assignment; identify byte ownership, publication and recovery boundaries. |
| F: Financial / critical consistency | 17-PAYMENT | Apply chapter 08 to stable operation identity, balanced ledger and ambiguous outcomes; identify byte ownership, publication and recovery boundaries. |
| G: Processing / infrastructure | 17-PROCESSING | Apply chapter 08 to checkpoint replay, bounded memory and partition ownership; identify byte ownership, publication and recovery boundaries. |
| H: Collaboration / utilities | 17-COLLAB | Apply chapter 08 to concurrent updates, access revocation and durable replay; identify byte ownership, publication and recovery boundaries. |

## Chapter 09: enforce deadline, retry, queue and failure-isolation budgets

| Source archetype | Build on | Required evidence |
|---|---|---|
| A: CRUD / storage | 17-CRUD | Apply chapter 09 to owner-scoped CRUD, version conflicts and orphan cleanup; enforce deadline, retry, queue and failure-isolation budgets. |
| B: Feed / timeline | 17-FEED | Apply chapter 09 to fan-out replay, pagination and revoked-post filtering; enforce deadline, retry, queue and failure-isolation budgets. |
| C: Real-time | 17-REALTIME | Apply chapter 09 to commit-before-ACK, reconnect gaps and slow clients; enforce deadline, retry, queue and failure-isolation budgets. |
| D: Search | 17-SEARCH | Apply chapter 09 to projection lag, tombstones and rebuild cutover; enforce deadline, retry, queue and failure-isolation budgets. |
| E: Geospatial | 17-GEO | Apply chapter 09 to coordinate units, stale positions and atomic assignment; enforce deadline, retry, queue and failure-isolation budgets. |
| F: Financial / critical consistency | 17-PAYMENT | Apply chapter 09 to stable operation identity, balanced ledger and ambiguous outcomes; enforce deadline, retry, queue and failure-isolation budgets. |
| G: Processing / infrastructure | 17-PROCESSING | Apply chapter 09 to checkpoint replay, bounded memory and partition ownership; enforce deadline, retry, queue and failure-isolation budgets. |
| H: Collaboration / utilities | 17-COLLAB | Apply chapter 09 to concurrent updates, access revocation and durable replay; enforce deadline, retry, queue and failure-isolation budgets. |

## Chapter 10: measure critical path, tail latency and added backend work

| Source archetype | Build on | Required evidence |
|---|---|---|
| A: CRUD / storage | 17-CRUD | Apply chapter 10 to owner-scoped CRUD, version conflicts and orphan cleanup; measure critical path, tail latency and added backend work. |
| B: Feed / timeline | 17-FEED | Apply chapter 10 to fan-out replay, pagination and revoked-post filtering; measure critical path, tail latency and added backend work. |
| C: Real-time | 17-REALTIME | Apply chapter 10 to commit-before-ACK, reconnect gaps and slow clients; measure critical path, tail latency and added backend work. |
| D: Search | 17-SEARCH | Apply chapter 10 to projection lag, tombstones and rebuild cutover; measure critical path, tail latency and added backend work. |
| E: Geospatial | 17-GEO | Apply chapter 10 to coordinate units, stale positions and atomic assignment; measure critical path, tail latency and added backend work. |
| F: Financial / critical consistency | 17-PAYMENT | Apply chapter 10 to stable operation identity, balanced ledger and ambiguous outcomes; measure critical path, tail latency and added backend work. |
| G: Processing / infrastructure | 17-PROCESSING | Apply chapter 10 to checkpoint replay, bounded memory and partition ownership; measure critical path, tail latency and added backend work. |
| H: Collaboration / utilities | 17-COLLAB | Apply chapter 10 to concurrent updates, access revocation and durable replay; measure critical path, tail latency and added backend work. |

## Chapter 11: reconcile acknowledged state after crash and verified restore

| Source archetype | Build on | Required evidence |
|---|---|---|
| A: CRUD / storage | 17-CRUD | Apply chapter 11 to owner-scoped CRUD, version conflicts and orphan cleanup; reconcile acknowledged state after crash and verified restore. |
| B: Feed / timeline | 17-FEED | Apply chapter 11 to fan-out replay, pagination and revoked-post filtering; reconcile acknowledged state after crash and verified restore. |
| C: Real-time | 17-REALTIME | Apply chapter 11 to commit-before-ACK, reconnect gaps and slow clients; reconcile acknowledged state after crash and verified restore. |
| D: Search | 17-SEARCH | Apply chapter 11 to projection lag, tombstones and rebuild cutover; reconcile acknowledged state after crash and verified restore. |
| E: Geospatial | 17-GEO | Apply chapter 11 to coordinate units, stale positions and atomic assignment; reconcile acknowledged state after crash and verified restore. |
| F: Financial / critical consistency | 17-PAYMENT | Apply chapter 11 to stable operation identity, balanced ledger and ambiguous outcomes; reconcile acknowledged state after crash and verified restore. |
| G: Processing / infrastructure | 17-PROCESSING | Apply chapter 11 to checkpoint replay, bounded memory and partition ownership; reconcile acknowledged state after crash and verified restore. |
| H: Collaboration / utilities | 17-COLLAB | Apply chapter 11 to concurrent updates, access revocation and durable replay; reconcile acknowledged state after crash and verified restore. |

## Chapter 12: test identity, permissions, secret exposure and revocation

| Source archetype | Build on | Required evidence |
|---|---|---|
| A: CRUD / storage | 17-CRUD | Apply chapter 12 to owner-scoped CRUD, version conflicts and orphan cleanup; test identity, permissions, secret exposure and revocation. |
| B: Feed / timeline | 17-FEED | Apply chapter 12 to fan-out replay, pagination and revoked-post filtering; test identity, permissions, secret exposure and revocation. |
| C: Real-time | 17-REALTIME | Apply chapter 12 to commit-before-ACK, reconnect gaps and slow clients; test identity, permissions, secret exposure and revocation. |
| D: Search | 17-SEARCH | Apply chapter 12 to projection lag, tombstones and rebuild cutover; test identity, permissions, secret exposure and revocation. |
| E: Geospatial | 17-GEO | Apply chapter 12 to coordinate units, stale positions and atomic assignment; test identity, permissions, secret exposure and revocation. |
| F: Financial / critical consistency | 17-PAYMENT | Apply chapter 12 to stable operation identity, balanced ledger and ambiguous outcomes; test identity, permissions, secret exposure and revocation. |
| G: Processing / infrastructure | 17-PROCESSING | Apply chapter 12 to checkpoint replay, bounded memory and partition ownership; test identity, permissions, secret exposure and revocation. |
| H: Collaboration / utilities | 17-COLLAB | Apply chapter 12 to concurrent updates, access revocation and durable replay; test identity, permissions, secret exposure and revocation. |

## Chapter 13: test change compatibility, observability and reproducible rollout

| Source archetype | Build on | Required evidence |
|---|---|---|
| A: CRUD / storage | 17-CRUD | Apply chapter 13 to owner-scoped CRUD, version conflicts and orphan cleanup; test change compatibility, observability and reproducible rollout. |
| B: Feed / timeline | 17-FEED | Apply chapter 13 to fan-out replay, pagination and revoked-post filtering; test change compatibility, observability and reproducible rollout. |
| C: Real-time | 17-REALTIME | Apply chapter 13 to commit-before-ACK, reconnect gaps and slow clients; test change compatibility, observability and reproducible rollout. |
| D: Search | 17-SEARCH | Apply chapter 13 to projection lag, tombstones and rebuild cutover; test change compatibility, observability and reproducible rollout. |
| E: Geospatial | 17-GEO | Apply chapter 13 to coordinate units, stale positions and atomic assignment; test change compatibility, observability and reproducible rollout. |
| F: Financial / critical consistency | 17-PAYMENT | Apply chapter 13 to stable operation identity, balanced ledger and ambiguous outcomes; test change compatibility, observability and reproducible rollout. |
| G: Processing / infrastructure | 17-PROCESSING | Apply chapter 13 to checkpoint replay, bounded memory and partition ownership; test change compatibility, observability and reproducible rollout. |
| H: Collaboration / utilities | 17-COLLAB | Apply chapter 13 to concurrent updates, access revocation and durable replay; test change compatibility, observability and reproducible rollout. |

## Chapter 14: validate request/response, pagination and retry contracts

| Source archetype | Build on | Required evidence |
|---|---|---|
| A: CRUD / storage | 17-CRUD | Apply chapter 14 to owner-scoped CRUD, version conflicts and orphan cleanup; validate request/response, pagination and retry contracts. |
| B: Feed / timeline | 17-FEED | Apply chapter 14 to fan-out replay, pagination and revoked-post filtering; validate request/response, pagination and retry contracts. |
| C: Real-time | 17-REALTIME | Apply chapter 14 to commit-before-ACK, reconnect gaps and slow clients; validate request/response, pagination and retry contracts. |
| D: Search | 17-SEARCH | Apply chapter 14 to projection lag, tombstones and rebuild cutover; validate request/response, pagination and retry contracts. |
| E: Geospatial | 17-GEO | Apply chapter 14 to coordinate units, stale positions and atomic assignment; validate request/response, pagination and retry contracts. |
| F: Financial / critical consistency | 17-PAYMENT | Apply chapter 14 to stable operation identity, balanced ledger and ambiguous outcomes; validate request/response, pagination and retry contracts. |
| G: Processing / infrastructure | 17-PROCESSING | Apply chapter 14 to checkpoint replay, bounded memory and partition ownership; validate request/response, pagination and retry contracts. |
| H: Collaboration / utilities | 17-COLLAB | Apply chapter 14 to concurrent updates, access revocation and durable replay; validate request/response, pagination and retry contracts. |

## Chapter 15: estimate and measure traffic, working set, bytes and headroom

| Source archetype | Build on | Required evidence |
|---|---|---|
| A: CRUD / storage | 17-CRUD | Apply chapter 15 to owner-scoped CRUD, version conflicts and orphan cleanup; estimate and measure traffic, working set, bytes and headroom. |
| B: Feed / timeline | 17-FEED | Apply chapter 15 to fan-out replay, pagination and revoked-post filtering; estimate and measure traffic, working set, bytes and headroom. |
| C: Real-time | 17-REALTIME | Apply chapter 15 to commit-before-ACK, reconnect gaps and slow clients; estimate and measure traffic, working set, bytes and headroom. |
| D: Search | 17-SEARCH | Apply chapter 15 to projection lag, tombstones and rebuild cutover; estimate and measure traffic, working set, bytes and headroom. |
| E: Geospatial | 17-GEO | Apply chapter 15 to coordinate units, stale positions and atomic assignment; estimate and measure traffic, working set, bytes and headroom. |
| F: Financial / critical consistency | 17-PAYMENT | Apply chapter 15 to stable operation identity, balanced ledger and ambiguous outcomes; estimate and measure traffic, working set, bytes and headroom. |
| G: Processing / infrastructure | 17-PROCESSING | Apply chapter 15 to checkpoint replay, bounded memory and partition ownership; estimate and measure traffic, working set, bytes and headroom. |
| H: Collaboration / utilities | 17-COLLAB | Apply chapter 15 to concurrent updates, access revocation and durable replay; estimate and measure traffic, working set, bytes and headroom. |



<a id="study"></a>


# Execution plan and progress tracking

Use milestones, not a promise of mastery in a fixed number of days. The source has 8-week and 120-day study structures; the plan below is a newly authored implementation sequence.189 labs plus70 variants are not realistically equivalent to a handful of afternoon tutorials.

| Milestone | Primary work | Exit evidence |
|---|---|---|
|1. A small correct service | Quickstart, networking, schema, basic API, unit tests | Reproducible checkout; request trace; tenant tests; fresh one-seat race |
|2. Data correctness | Isolation, idempotency, consistency, replication | No oversell; replay identity; lag experiment; explicit commit contract |
|3. Capacity and caching | Vertical/horizontal scaling, all cache strategies, query plans | Comparable before/after runs; stale-fill test; measured bottleneck |
|4. Partitioning and elasticity | Shard strategies, migration, HPA/VPA/schedules/forecasting | Fenced migration recovery; membership evidence; bounded scaling controller |
|5. Async and resilience | Outbox/inbox, broker/log, retries, circuit/bulkhead, failure drills | Duplicate-safe effect; bounded queue/retries; runbook and recovery |
|6. Storage and protection | Multipart, durability/PITR, identity, permissions, secrets | Verified restore; checksums; cross-tenant denial; rotation evidence |
|7. Operability | CI, observability, deployment, estimation, architecture reviews | Reproducible pipeline; metrics with bounded labels; rollback |
|8. Integrations | Four capstones and14 pattern projects | End-to-end workflows plus concurrency, failure and recovery tests |
|9. Breadth and specialization |70 variants, archetype overlays, cloud/hardware tracks | Unique deltas implemented; limits and remaining unknowns defended |

## A session template

Choose one lab, write the invariant, run a baseline, implement one change and inject one failure. Spend the final part of the session checking authoritative state and saving evidence. A successful screenshot is not a substitute for a transaction/identity/checksum assertion.

## Four trackers, without inventing progress

**Master dashboard:** total labs; not started; implementation in progress; failure reproduced; assertions passed; reviewed. Maintain separate totals for local models, container integration and cloud/hardware work.

**Question/variant tracker:** variant ID, related lab IDs, critical invariant, implementation branch, failed experiment, final evidence and next review date.

**Pattern tracker:**14 pattern families with a small end-to-end demo, a failure case, a performance measurement and a tradeoff statement each.

**Review log:** reviewer, architecture assumptions, unresolved concerns, accepted changes and a concrete next experiment. Do not mark a whole chapter complete merely because its prerequisite tests pass.

## Interview and design review practice

Follow the source's detailed 6-step framing: clarify requirements, estimate scale, define APIs, sketch architecture, deep-dive the critical component, discuss bottlenecks/tradeoffs. Also rehearse its rapid 5-step summary. Use real evidence from the lab, not memorized claims about generic performance.

For each choice finish: 'We chose X because requirement Y and evidence Z justify it; we accept cost/failure W; we would reconsider when condition V changes.'



<a id="interpretation"></a>


# Source alignment, scope and technical interpretation

## What was reviewed

The linked repository directory, consolidated index, chapter tables of contents and selected detailed sections were retrieved through the GitHub connector. All 21 chapter identities were checked. The architecture master map's70 problem names and the roadmap's14 pattern families were inspected. The two uploaded workbooks were read as the prior baseline, including their declared index-only coverage and implementation limits.

Pinned source revision: `5982a3b5491e4cd1c36902e09fc54c6f0080f7b5 ` in ` IamTheBest1Great/HLD `, directory ` HLD/HLD_Master_Notes`.

This is a chapter- and named-strategy-aligned expansion, **not a certified paragraph-by-paragraph audit of every sentence in the repository**. The coverage manifest makes the actual topic-to-lab assignments explicit. Repeated interview questions and comparison tables are handled by evidence reviews and archetype overlays, not by pretending each one is a separate deployed service.

## What is source-derived versus newly authored

Source-derived: chapter names/order; named strategies and system-pattern families; the 70 named problems; the four case-study categories.

Newly authored: EventHub implementation choices,189 lab assignments, reference code, schemas, infrastructure configurations, workloads, failure cases, validation gates, study sequence and operating playbooks. The code was not downloaded from the source repository. The uploaded workbook's reference to another starter is not evidence that those files were attached; this package creates its own standalone starter.

Outside technical basis: official product documentation listed in `REFERENCES.md`. These references qualify implementation behavior and provide version-matched deployment guidance. They do not certify the configurations in this package as production-ready.

## Deliberate qualifications rather than silent substitutions

| Source framing or common shortcut | How the implementation lab treats it |
|---|---|
| SQL means strong consistency; NoSQL means eventual consistency | Test the specific operation, isolation/consistency level, replica topology and failure behavior. Database family alone is not the guarantee. |
| More instances means linear scaling | Measure shared database, pool, hot-key and load-generator bottlenecks; compare equal total resources too. |
| Read/write quorum intersection implies every strong-consistency property | State assumptions about version selection, concurrent writes, clocks, repair and replica membership; compare with actual consensus. |
| Check Redis, perform a payment, then save the response | Reproduce the crash/race window; implement durable transactional idempotency and provider reconciliation. |
| Never use two-phase commit | Implement a bounded prepared-transaction blocking/recovery exercise and compare its guarantees/cost with sagas. |
| A Redis lease prevents every stale writer | Require the resource applying an effect to reject stale fencing tokens; document failover/expiry limitations. |
| Exactly-once messaging | Name the exact transport/transaction/effect scope; test redelivery and independent external effects. |
| TTL deletes content immediately | Enforce logical expiry at read time and treat physical cleanup as separate eventual work. |
| Replication is a backup | Demonstrate replicated deletion and restore from an independent recovery history. |
| A CDN is always a fixed multiplier faster/cheaper | Measure workload, location, cache hit ratio, bytes and cost assumptions; no universal multiplier is asserted. |
| A number of concurrent users determines bytes per second | Require an explicit transfer duration and request/response sizes before computing bandwidth. |
| A table ID sequence is commit order | Use a serialized transactional cursor or a supported commit-log boundary; test delayed lower-sequence commits. |
| Fixed-size chunking always changes one chunk per edit | Test insertion near the beginning and compare content-defined chunking; exact changes determine reuse. |
| Global content-addressed deduplication is automatically safe | Keep authorization separate from content identity, consider existence leaks, and start with tenant-scoped deduplication. |
| Single-host replica directories prove multi-region durability | Label models and require independent failure domains for stronger empirical claims. |
| Governance features imply GDPR/HIPAA/PCI-DSS/SOC2 compliance | Produce synthetic control evidence and identify required expert review; no legal compliance or certification is claimed. |

## Completion is a learner outcome

A lab command may run a prerequisite or an algorithm model. The complete lab includes the implementation steps, failure injection, acceptance gate, recovery and evidence review. The shipped application does not already expose every advanced feature described in these assignments. See `VALIDATION.md` for the exact executed subset.

Image tags and dependency ranges are exercise selections. Upgrade/pin/retest on the target environment rather than treating them as current security recommendations. Cloud and hardware steps require explicit resources; no paid environment was created here.



<a id="coverage"></a>


# Coverage: source chapters, named strategies and implementation depth

Source revision: `5982a3b5491e4cd1c36902e09fc54c6f0080f7b5`. This manifest covers the 21 chapter identities and the explicitly mapped named topics. It is not a paragraph-by-paragraph completeness certificate. Every lab has a distinct implementation/failure gate; a reference command may cover only its prerequisite.

## Chapter inventory

| Chapter | Source | Labs |
|---|---|---|
| 01. Networking & Protocols | [01_Networking_and_Protocols.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/01_Networking_and_Protocols.md) | 14 |
| 02. Scalability | [02_Scalability.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/02_Scalability.md) | 17 |
| 03. Consistency & CAP Theorem | [03_Consistency_and_CAP_Theorem.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/03_Consistency_and_CAP_Theorem.md) | 10 |
| 04. Availability | [04_Availability.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/04_Availability.md) | 7 |
| 05. Database | [05_Database.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/05_Database.md) | 10 |
| 06. Caching | [06_Caching.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/06_Caching.md) | 10 |
| 07. Messaging Queues & Async | [07_Messaging_Queues_and_Async.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/07_Messaging_Queues_and_Async.md) | 9 |
| 08. Storage Systems | [08_Storage_Systems.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/08_Storage_Systems.md) | 9 |
| 09. Fault Tolerance & Reliability | [09_Fault_Tolerance_and_Reliability.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/09_Fault_Tolerance_and_Reliability.md) | 8 |
| 10. Reducing Latency | [10_Reducing_Latency.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/10_Reducing_Latency.md) | 8 |
| 11. Durability | [11_Durability.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/11_Durability.md) | 10 |
| 12. Security | [12_Security.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/12_Security.md) | 14 |
| 13. Maintainability | [13_Maintainability.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/13_Maintainability.md) | 8 |
| 14. API Design | [14_API_Design.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/ 14_API_Design.md) | 7 |
| 15. Estimation Skills | [15_Estimation_Skills.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/15_Estimation_Skills.md) | 6 |
| 16. HLD Architecture and Frameworks | [16_HLD_Architecture_and_Frameworks.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/16_HLD_Architecture_and_Frameworks.md) | 6 |
| 17. Roadmaps and Study Plans | [17_Roadmaps_and_Study_Plans.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/17_Roadmaps_and_Study_Plans.md) | 15 |
| 18. Case Study: URL Shortener | [18_Case_Study_URL_Shortener.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/18_Case_Study_URL_Shortener.md) | 4 |
| 19. Case Study: Pastebin | [19_Case_Study_Pastebin.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/19_Case_Study_Pastebin.md) | 4 |
| 20. Case Study: BookMyShow | [20_Case_Study_Book_My_Show.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/20_Case_Study_Book_My_Show.md) | 6 |
| 21. Case Study: Google Drive / Dropbox | [21_Case_Study_Google_Drive_Dropbox.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/ 21_Case_Study_Google_Drive_Dropbox.md) | 7 |

## Named strategy-to-lab map

| Lab | Strategy / topic coverage | Delivery mode |
|---|---|---|
| 01-TRACE: Trace DNS, TCP, TLS and the seven OSI layers | request path; OSI; latency; throughput; network hops | Run reference |
| 01-DNS: Authoritative DNS, record types, TTL and failover | DNS recursion; authoritative DNS; A; AAAA; CNAME; MX; TXT; NS; TTL; GeoDNS | Container recipe |
| 01-HTTP: HTTP semantics, statelessness, headers and conditional requests | HTTP methods; status codes; headers; cookies; statelessness; ETag; If-Match | Run reference |
| 01-TLS: HTTPS, certificate validation and mTLS | TLS; HTTPS; certificates; TLS termination; mTLS; TLS session reuse | Container recipe |
| 01-VERSIONS: Compare HTTP/1.1, HTTP/2 and HTTP/3 | HTTP/1.1; HTTP/2; multiplexing; head-of-line blocking; HTTP/3; QUIC | Container recipe |
| 01-TCP: TCP framing, flow control and backpressure | TCP; byte streams; framing; flow control; congestion; keep-alive | Run reference |
| 01-UDP: UDP loss, ordering and application acknowledgments | UDP; datagrams; loss; ordering; deduplication | Run reference |
| 01-LB: Round-robin, weighted, least-connections and IP-hash balancing | round-robin; weighted round-robin; least connections; IP hash; sticky sessions | Run reference + container |
| 01-PROXY: L4 versus L7, reverse/forward proxies and gateway policies | L4; L7; reverse proxy; forward proxy; NGINX; HAProxy; Envoy | Build extension |
| 01-POLL: Short polling, long polling and SSE | short polling; long polling; SSE; reconnect cursors | Build extension |
| 01-WS: WebSockets, heartbeat, room authorization and slow consumers | WebSocket; full-duplex; heartbeats; presence; fan-out | Build extension |
| 01-GRPC: All four gRPC interaction shapes | REST; gRPC; Protocol Buffers; unary; server streaming; client streaming; bidirectional streaming | Build extension |
| 01-GRAPHQL: GraphQL queries, N+1, batching and query cost | GraphQL; REST vs GraphQL; N+1; DataLoader; overfetching | Build extension |
| 01-EDGE: CDN pull/push/hybrid, origin shielding and edge compute | CDN; pull; push; hybrid; origin shield; dynamic caching; edge compute; Geo routing; ESI; cache poisoning | Build extension |
| 02-BASELINE: Find the bottleneck before choosing a strategy | throughput; capacity; load tests; open workloads; hotspots; headroom | Run reference + container |
| 02-VERTICAL: Scale CPU, RAM and process parallelism independently | vertical scaling; scale up; scale down; CPU; memory; multi-core; downtime; cost ceiling | Container recipe |
| 02-HORIZONTAL: Scale out stateless application instances | horizontal scaling; stateless services; stateful services; shared sessions; service discovery | Container recipe |
| 02-HPA: Reactive autoscaling with Kubernetes HPA | reactive autoscaling; CPU utilization; requests; warmup; cooldown; HPA; readiness | Cluster recipe |
| 02-SCHEDULED: Scheduled autoscaling and prewarming | scheduled autoscaling; known events; prewarming; CronJob; scheduled ASG actions | Cluster recipe |
| 02-PREDICTIVE: Predictive scaling, forecast error and control-loop safety | predictive autoscaling; forecasting; capacity forecasts; AWS predictive scaling; forecast-only | Model + cloud recipe |
| 02-ASG: Cloud VM scaling, health replacement and vertical instance changes | AWS Auto Scaling Group; target tracking; instance refresh; multi-AZ; scale-in protection | Cloud recipe |
| 02-VPA: Vertical pod recommendations and stateful scaling limits | VPA; vertical pod autoscaling; stateful workloads; resource requests; control-loop conflicts | Cluster recipe |
| 02-HASH: Modulo hash and consistent/rendezvous hashing | hash sharding; modulo; consistent hashing; virtual nodes; rendezvous hashing | Run reference |
| 02-RANGE: Range-based sharding and range split | range sharding; ordered keys; range split; hot partitions | Build extension |
| 02-DIRECTORY: Directory-based tenant routing | directory sharding; tenant isolation; route lookup; fencing tokens; epochs | Container recipe |
| 02-GEO: Geographic/home-region sharding | geo sharding; data residency; home writer; regional routing | Build extension |
| 02-MIGRATE: Resharding with a resumable fenced tenant migration | resharding; rebalancing; snapshot; checksums; cutover; rollback; CDC extension | Container recipe |
| 02-HOTKEY: Hot tenants, hot keys and load shedding | hotspot mitigation; hot key replication; tenant isolation; admission control; bounded queues | Build extension |
| 02-READS: Read replicas, partitioning, denormalization and pools | read scaling; read replicas; partitioning; denormalization; connection pooling | Container recipe |
| 02-ASYNC: Scale background work independently | asynchronous processing; worker pools; queue depth; backpressure; microservices | Container recipe |
| 02-DECIDE: Scaling strategy decision and anti-pattern review | scaling decision framework; CDN; cache; microservices; cost; tradeoffs; anti-patterns | Design + experiment |
| 03-ISOLATION: Lost updates, write skew and isolation levels | Read Committed; Repeatable Read; Serializable; lost update; write skew; locking; MVCC | Container recipe |
| 03-MODELS: Strong, sequential, session and eventual consistency | linearizability; sequential consistency; eventual consistency; session consistency; tunable consistency | Run reference |
| 03-SESSION: Read-your-writes, monotonic reads/writes and causal dependencies | read-your-writes; monotonic reads; monotonic writes; causal consistency; version vectors | Build extension |
| 03-QUORUM: Leaderless replication and tunable quorums | leaderless replication; Cassandra; N R W; quorums; read repair; hinted handoff | Container recipe |
| 03-LEADERS: Leader-follower, multi-leader and replication modes | leader-follower; multi-leader; sync replication; async replication; logical replication | Build extension |
| 03-RAFT: Consensus, elections and minority behavior with etcd | Raft; etcd; majority; leader election; network partitions; split-brain | Container recipe |
| 03-PAXOS: Paxos promises, accepted values and coordination services | Paxos; prepare; accept; ZooKeeper; coordination; leader leases | Run model |
| 03-MERGE: Conflict resolution, CRDTs and anti-entropy | last-write-wins; vector clocks; G-counter; PN-counter; OR-set; application merge; anti-entropy | Build extension |
| 03-CAP: CAP, PACELC and operation-specific tradeoffs | CAP; PACELC; partition tolerance; latency-consistency; hybrid consistency | Design + experiment |
| 03-DISTRIBUTED-TX: Two-phase commit versus sagas and CQRS boundaries | 2PC; prepared transactions; saga orchestration; choreography; CQRS; event sourcing | Build extension |
| 04-SLO: Availability SLIs, SLOs and error budgets | availability; SLI; SLO; SLA; error budgets; MTBF; MTTR | Build extension |
| 04-REDUNDANCY: Active-passive versus active-active serving | redundancy; active-passive; active-active; SPOF; multi-AZ | Build extension |
| 04-PROBES: Liveness, readiness, startup and graceful drain | health checks; liveness; readiness; startup probes; draining; optional dependencies | Container recipe |
| 04-FAILOVER: Physical replica promotion with writer fencing | failover; primary promotion; replica lag; split-brain prevention; RPO; RTO | Container recipe |
| 04-ROLLOUT: Rolling, blue-green and canary deployments | rolling deployment; blue-green; canary; expand-contract; rollback | Build extension |
| 04-DEGRADE: Graceful degradation, feature flags and fail-safe policy | graceful degradation; fallbacks; feature flags; retry; timeouts; circuit breakers | Build extension |
| 04-REGIONS: Multi-region failover and global health routing | multi-region; global load balancing; GeoDNS; active-active; active-passive; disaster recovery | Regional extension |
| 05-MODEL: Relational schema, constraints and normalized relationships | SQL; relational modeling; primary/foreign keys; ACID; normalization; joins | Container recipe |
| 05-INDEX: B-tree, composite, covering, hash and secondary indexes | B-tree; hash index; composite index; covering index; secondary index; query plans | Container recipe |
| 05-LSM: LSM trees, memtables, SSTables and compaction | LSM tree; memtable; SSTable; WAL; compaction; read amplification; write amplification | Build extension |
| 05-DOCUMENT: Document databases, embedding and replica sets | document store; MongoDB; JSONB; embedding; referencing; schema evolution | Container recipe |
| 05-KEYVALUE: Key-value and wide-column access patterns | key-value; Redis; DynamoDB; wide-column; Cassandra; HBase; partition keys | Build extension |
| 05-GRAPH: Graph traversal and geospatial indexes | graph database; Neo4j; relationships; traversal; geospatial; GiST; PostGIS | Build extension |
| 05-PARTITION: Horizontal, vertical, time and functional partitioning | table partitioning; range; list; hash; vertical partitioning; functional partitioning; partition pruning | Container recipe |
| 05-SHARDS: Shard keys, cross-shard queries and global constraints | database sharding; shard-key choice; global uniqueness; cross-shard joins; scatter-gather | Build extension |
| 05-PERFORMANCE: Pools, N+1, denormalization and materialized views | connection pools; N+1; denormalization; materialized views; query cancellation; lock timeout | Build extension |
| 05-REPLICATE: Physical versus logical replication and schema coordination | physical replication; logical replication; read replicas; failover; schema changes | Build extension |
| 06-LAYERS: Browser, gateway, CDN, application and database caches | browser cache; HTTP cache; CDN; application cache; database buffers; Redis; Memcached | Build extension |
| 06-ASIDE: Cache-aside and read-through | cache-aside; lazy loading; read-through; cache misses; loader ownership | Run reference |
| 06-THROUGH: Write-through cache with a non-atomic second store | write-through; synchronous cache maintenance; dual-write failure | Run reference |
| 06-BEHIND: Write-back/write-behind with explicit loss boundaries | write-back; write-behind; batching; durable queue; acknowledgment boundary | Build extension |
| 06-AROUND: Write-around and read pollution | write-around; write-heavy workload; cache pollution; invalidation | Run reference |
| 06-EVICTION: LRU, LFU, FIFO, random and TTL policies | LRU; LFU; FIFO; random; TTL; noeviction; working set | Run reference + container |
| 06-INVALIDATE: TTL, event-driven and versioned invalidation | TTL invalidation; event invalidation; versioned keys; generation fencing; stale-fill race | Build extension |
| 06-STAMPEDE: Stampede prevention and refresh-ahead | cache stampede; singleflight; lease; refresh-ahead; probabilistic refresh; TTL jitter | Build extension |
| 06-PENETRATION: Negative caching and safe Bloom-filter rebuilds | cache penetration; negative caching; Bloom filter; false positives; filter generation | Run reference + extension |
| 06-DISTRIBUTED: Redis Cluster, Sentinel and multi-level caches | Redis Cluster; hash slots; Sentinel; replication; distributed cache; L1; L2 | Container recipe |
| 07-TOPOLOGY: Work queues, pub/sub fan-out and replayable logs | point-to-point; competing consumers; publish-subscribe; fan-out; replayable logs; RabbitMQ; Kafka | Container recipe |
| 07-OUTBOX: Transactional outbox and ambiguous publish outcomes | transactional outbox; publisher confirms; atomic local transaction; duplicate publication | Container recipe |
| 07-INBOX: Consumer deduplication and effect-before-ACK | idempotent consumer; inbox; at-least-once; at-most-once; exactly-once scope | Container recipe |
| 07-RETRY: Backoff, dead letters, poison messages and redrive | retry queues; exponential backoff; jitter; DLQ; poison messages; redrive | Build extension |
| 07-ORDER: Partition ordering, versions and backpressure | ordering; aggregate versions; partitions; prefetch; consumer groups; backpressure | Build extension |
| 07-KAFKA: Kafka replication, ISR, producer idempotence and transactions | Kafka; KRaft; ISR; acks; retention; offsets; idempotent producer; transactions; read_committed | Container recipe |
| 07-BROKER-HA: RabbitMQ quorum queues and alternative brokers | RabbitMQ quorum; Redis Streams; NATS JetStream; SQS; Pub/Sub; Pulsar; broker selection | Build extension |
| 07-SAGA: Saga orchestration and choreography | saga; orchestration; choreography; compensating actions; state machine | Build extension |
| 07-CQRS: CQRS, event sourcing, snapshots and projection rebuild | CQRS; event sourcing; append-only events; projections; snapshots; replay; schema evolution | Build extension |
| 08-TYPES: Block, file and object storage are different interfaces | block storage; EBS; SAN; Azure Disk; file storage; NFS; SMB; object storage; S3; GCS; Azure Blob | Run reference + storage extension |
| 08-UPLOAD: Streamed uploads and durable publication | streaming upload; size limits; checksums; temporary objects; metadata publication; orphans | Build extension |
| 08-MULTIPART: Multipart uploads, resumability and manifest validation | multipart upload; resumable upload; bounded parallelism; part checksums; S3 multipart | Build extension |
| 08-DIRECT: Direct-to-object-store upload and signed download authorization | presigned URLs; direct upload; signed downloads; ownership; expiry; revocation | Build extension |
| 08-RANGE: Range downloads, resume and cache-safe media delivery | HTTP ranges; Content-Range; 206; 416; resumable downloads; CDN | Build extension |
| 08-DEDUP: Fixed-size versus content-defined chunking and deduplication | chunking; delta sync; fixed chunks; content-defined chunks; deduplication; hashes | Build extension |
| 08-ERASURE: Replication versus Reed-Solomon erasure coding | replication; erasure coding; k+m; checksums; repair; failure domains | Run reference |
| 08-LIFECYCLE: Tiered storage, integrity scans and cleanup | hot storage; warm storage; cold storage; archive; lifecycle; integrity scans; orphan cleanup | Build extension |
| 08-VIDEO: Async transcoding, HLS/DASH and adaptive bitrate delivery | media storage; transcoding; thumbnails; HLS; DASH; adaptive bitrate; CDN distribution | Build extension |
| 09-DEADLINES: Timeouts, propagated deadlines and ambiguous outcomes | connect timeout; read timeout; deadline; cancellation; unknown outcome | Build extension |
| 09-RETRIES: Exponential backoff, full jitter and retry budgets | retry; exponential backoff; jitter; retry budget; idempotency; overload amplification | Run reference |
| 09-CIRCUIT: Circuit breaker closed/open/half-open transitions | circuit breaker; failure threshold; half-open probe; recovery; stale completions | Run reference |
| 09-BULKHEAD: Bulkheads, priority admission and load shedding | bulkhead; semaphore; bounded queue; priority; adaptive load shedding; fallback | Build extension |
| 09-LIMITERS: All five rate-limiting algorithms | token bucket; leaky bucket; fixed window; sliding window log; sliding window counter | Run reference |
| 09-DISTRIBUTED-LIMIT: Atomic distributed limiters and lease fencing | Redis Lua; distributed rate limit; server clock; atomicity; fail-open; fail-closed; leases | Container recipe |
| 09-HEDGING: Hedged reads, watchdogs and redundant paths | hedged requests; timeouts; heartbeats; watchdog; redundancy; failover | Build extension |
| 09-CHAOS: Failure injection, recovery matrices and incident runbooks | chaos engineering; fault injection; checkpoint recovery; observability; incident response | Build extension |
| 10-CRITICAL: Critical-path latency and percentile-safe measurement | network latency; disk latency; processing latency; queueing; lock wait; p50; p95; p99 | Build extension |
| 10-PARALLEL: Parallel fan-out, batching and asynchronous work | parallel calls; fan-out/fan-in; async I/O; batching; background jobs; N+1 | Build extension |
| 10-CACHE: Cache placement, query optimization and shard locality | browser cache; CDN; app cache; database buffers; indexes; read replicas; sharding; co-location | Build extension |
| 10-PREFETCH: Prefetching, predictive loading and precomputed feeds | browser prefetch; predictive loading; feed precomputation; recommendation prefetch; wasted work | Build extension |
| 10-PAYLOAD: Gzip/Brotli, media compression and binary serialization | gzip; Brotli; payload size; compression CPU; media compression; protobuf | Build extension |
| 10-CONNECTIONS: Connection pools, keep-alive and TLS session reuse | connection pooling; persistent connections; TCP keep-alive; TLS session resumption; HTTP/2; HTTP/3 | Build extension |
| 10-COMPUTE: Algorithm, batching, SIMD and storage-hardware experiments | CPU optimization; algorithm complexity; batching; SIMD; SSD; HDD; in-memory DB; WAL tuning; hot data | Hardware-aware extension |
| 10-TAIL: Tail-latency hedging, admission and service budgets | tail latency; hedging; timeouts; jitter; adaptive load shedding; latency budget; tradeoffs | Build extension |
| 11-WAL: Append-only WAL, fsync, replay and partial-tail recovery | WAL; append-only logs; fsync; crash recovery; replay; checkpoint | Run reference |
| 11-ACK: Database and broker acknowledgment boundaries | ACID durability; synchronous_commit; remote_write; remote_apply; publisher confirms; commit logs | Build extension |
| 11-BACKUP: Full, incremental, differential and snapshot backups | full backup; incremental backup; differential backup; snapshots; restore verification | Build extension |
| 11-PITR: Point-in-time recovery with physical backup and archived WAL | PITR; base backup; WAL archive; recovery target; RPO; RTO | Container recipe |
| 11-REPLICATION: Replication is not backup; consensus is not independent storage | replication; consensus; Raft; Paxos; multi-region; deletion replication; logical corruption | Build extension |
| 11-RAID: RAID 0, 1, 5, 6 and 10 with explicit device safety | RAID 0; RAID 1; RAID 5; RAID 6; RAID 10; rebuild; storage redundancy | Hardware/VM recipe |
| 11-NVM: NVDIMM, battery-backed RAM and persistent-memory boundaries | non-volatile memory; NVDIMM; battery-backed RAM; persistent memory; flush ordering | Hardware-dependent recipe |
| 11-INTEGRITY: Checksums, scrubbing, erasure recovery and repair sources | end-to-end checksums; silent corruption; erasure coding; scrubbing; repair | Build extension |
| 11-IMMUTABLE: Immutable object versions, retention and key recovery | object versioning; immutable backups; snapshot retention; ransomware resilience; encryption keys | Build extension |
| 11-DR: Disaster recovery planning, drills and failback | regional failure; RPO; RTO; DR; recovery runbook; data residency; failback | Build extension |
| 12-THREAT: CIA, defense in depth, least privilege and failure policy | CIA triad; defense in depth; least privilege; zero trust; secure defaults; separation | Design + defensive test |
| 12-IDENTITY: Password login, OAuth 2.0, OIDC and SSO | password authentication; OAuth 2.0; OIDC; SSO; identity provider | Build extension |
| 12-MFA: MFA, passkeys and biometric-backed authentication | MFA; WebAuthn; passkeys; biometrics; account recovery | Identity/browser extension |
| 12-SESSIONS: Access tokens, refresh rotation and secure cookies | JWT; access token; refresh token; session expiration; HttpOnly; Secure; SameSite; CSRF | Build extension |
| 12-AUTHZ: RBAC, ABAC, ownership and row-level security | RBAC; ABAC; fine-grained permissions; object authorization; RLS; tenant isolation | Build extension |
| 12-CRYPTO: Symmetric/asymmetric encryption, hashes and signatures | AES-GCM; asymmetric keys; hashing; digital signatures; password hashing; field encryption; envelope encryption | Run reference + key-management extension |
| 12-PROTECTION: Disk/database/object encryption, tokenization and masking | encryption at rest; disk encryption; database encryption; object encryption; PII; masking; tokenization | Build extension |
| 12-INPUT: Injection prevention, bounded input and output encoding | SQL injection; XSS; command injection; SSRF; input validation; output escaping; resource limits | Build extension |
| 12-HEADERS: Security headers, TLS and certificate rotation | CSP; HSTS; X-Frame-Options; nosniff; Referrer-Policy; TLS; mTLS; certificate rotation | Build extension |
| 12-NETWORK: Private networks, DMZ, service accounts, IAM and service mesh | private subnets; DMZ; segmentation; security groups; IAM; temporary credentials; service accounts; service mesh | Build extension |
| 12-WAF: Firewalls, WAF, quotas and defensive traffic filtering | firewall; WAF; traffic filtering; DDoS protection; CDN scrubbing; rate limiting; load shedding | Build extension |
| 12-SECRETS: Secrets management and rotation without public leakage | secrets vault; API keys; secret rotation; environment variables; key recovery | Build extension |
| 12-AUDIT: Audit logs, detection, incident response and governance evidence | audit logging; immutable logs; SIEM; anomaly detection; incident response; GDPR; HIPAA; PCI-DSS; SOC 2 | Governance + implementation extension |
| 12-MESSAGE-SECURITY: Signed webhooks, replay protection and secure queues | message authentication; encryption; broker ACLs; webhook signatures; replay protection | Build extension |
| 13-BOUNDARIES: Modular monolith, DDD boundaries and service extraction | modularity; cohesion; loose coupling; DDD; bounded contexts; microservices; separation of concerns | Build extension |
| 13-PATTERNS: Factory, strategy, observer, repository and dependency injection | factory; strategy; observer; repository; dependency injection; SOLID; refactoring | Build extension |
| 13-TESTS: Unit, property, integration, contract and end-to-end tests | unit tests; integration tests; E2E; contract tests; property tests; regression tests | Run reference + container |
| 13-CI: CI, dependency locking and reproducible builds | CI; CD; dependency management; lockfiles; static analysis; artifact signing; supply chain | Build extension |
| 13-OBSERVABILITY: Logs, metrics, tracing and meaningful alerts | structured logs; metrics; distributed tracing; OpenTelemetry; Prometheus; alerting; cardinality | Build extension |
| 13-DEPLOY: Deployment strategies, feature flags and safe rollback | rolling; blue-green; canary; feature flags; A/B testing; kill switch; rollback | Build extension |
| 13-IAC: Infrastructure as code, immutable images and configuration | IaC; Terraform; CloudFormation; containers; Kubernetes; immutable infrastructure; config; secrets | Build extension |
| 13-DOCS: ADRs, API documentation, ownership and technical debt | ADRs; runbooks; API docs; code ownership; version control; technical debt; onboarding | Documentation + implementation exercise |
| 14-REST: Resource modeling, method semantics and stable error contracts | REST; resources; HTTP methods; status codes; idempotency; error schema; API gateway | Run reference |
| 14-PAGINATION: Offset, page, cursor and keyset pagination | offset pagination; page pagination; cursor pagination; keyset pagination; stable sort; snapshot | Build extension |
| 14-IDEMPOTENCY: Transactional request deduplication and conditional updates | idempotency keys; request fingerprints; scope; retention; ETag; If-Match; optimistic concurrency | Run reference + container |
| 14-VERSION: URI, query, header and content-negotiated versioning | URI versioning; query versioning; header versioning; media type versioning; deprecation; compatibility | Build extension |
| 14-JOBS: Asynchronous APIs, webhooks and callback security | 202 Accepted; job status; polling; webhooks; retry; callback signatures; replay | Build extension |
| 14-PROTOCOLS: REST, GraphQL, gRPC and gateway composition | REST; GraphQL; gRPC; protocol buffers; gateway aggregation; BFF; binary serialization | Build extension |
| 14-CACHE-SECURITY: API cache controls, compression, auth and quotas | ETag; Cache-Control; conditional GET; gzip; Brotli; API keys; OAuth; JWT; rate limits | Build extension |
| 15-UNITS: Units, powers, assumptions and measured reference latencies | DAU; MAU; QPS; TPS; bytes; bits; decimal; binary; latency reference numbers | Run model |
| 15-TRAFFIC: Traffic, peak factors, fan-out and retry amplification | traffic estimation; read/write ratio; peak factor; fan-out; retry amplification; concurrency | Run model + workload |
| 15-STORAGE: Database, media, replica, index and backup capacity | storage estimation; retention; indexes; metadata; WAL; replication; backup; compression; dedup; erasure | Build extension |
| 15-NETWORK: Bandwidth, media bitrate, CDN hits and internal traffic | bandwidth estimation; ingress; egress; CDN hit ratio; replication traffic; media bitrate | Build extension |
| 15-CACHE: Cache sizing, working set and hot-key concentration | working set; cache capacity; hit ratio; hot keys; Pareto; object overhead | Run reference |
| 15-CAPACITY: Servers, headroom, N+1 and sensitivity analysis | server count; CPU; memory; disk; network; headroom; N+1; growth; sensitivity; cost | Build extension |
| 16-REQUIREMENTS: Requirements, non-functional goals and the design frameworks | four design questions; six-step framework; five-step rapid framework; requirements; NFRs | Design + implementation review |
| 16-LAYERS: Seven-layer architecture and synchronous/asynchronous boundaries | client edge; entry/load balancing; application; cache; persistence; async; analytics | Design + container |
| 16-ARCHETYPES: Read-heavy, write-heavy, realtime, financial, media, search and geo archetypes | seven archetypes; decision trees; polyglot persistence; architecture selection | Design + reference experiments |
| 16-MIGRATION: Design for 100x growth without unsafe big-bang migration | 100x scaling; service extraction; schema migration; resharding; multi-region; compatibility | Build extension |
| 16-MAP70: Implement the 70-problem master map as concrete variants | 70-problem master map; traffic control; global infrastructure; feeds; communication; media; geo; search; finance; concurrency; storage; big data; productivity | Project variant assignments |
| 16-REVIEW: Architecture reviews, decision matrices and source qualifications | tradeoffs; anti-patterns; decision matrix; staff-level design; CLEAR; review | Review exercise |
| 17-CRUD: Pattern 1: CRUD and storage platform | CRUD; URL shortener; Pastebin; metadata; permissions; durable storage | Build extension |
| 17-FEED: Pattern 2: Feed/timeline push, pull and hybrid fan-out | news feed; fan-out on write; fan-out on read; hybrid; celebrity; ranking; pagination | Build extension |
| 17-REALTIME: Pattern 3: Chat, presence and notifications | chat; WebSocket; presence; offline delivery; read receipts; notification fan-out | Build extension |
| 17-SEARCH: Pattern 4: Search, autocomplete and indexing | inverted index; autocomplete; full-text; CDC; ranking; ACL filtering | Build extension |
| 17-LIMITER: Pattern 5: Distributed rate-limiting service | distributed limiter; five algorithms; tenant budgets; API gateway; fairness | Build extension |
| 17-OBSERVE: Pattern 6: Logging, metrics and monitoring pipeline | logs; metrics; monitoring; time series; sampling; cardinality; alerts | Build extension |
| 17-CACHE: Pattern 7: Distributed cache service | distributed cache; hash ring; eviction; replication; invalidation; hot keys | Build extension |
| 17-RECOMMEND: Pattern 8: Recommendation pipeline and online serving | recommendations; candidate generation; ranking; features; batch; stream; fallback | Build extension |
| 17-PAYMENT: Pattern 9: Payment, wallet and financial ledger | payment gateway; double-entry ledger; wallet; idempotency; reconciliation; saga | Build extension |
| 17-PROCESSING: Pattern 10: Large-scale batch and stream processing | batch; streaming; ETL; MapReduce; watermarks; checkpoints; Top K; late events | Build extension |
| 17-GEO: Pattern 11: Nearby search, matching and location updates | geospatial; GeoHash; spatial index; nearby search; matching; routing; location TTL | Build extension |
| 17-COLLAB: Pattern 12: OT/CRDT collaboration and conflict handling | collaboration; OT; CRDT; offline edits; convergence; snapshots; tombstones | Build extension |
| 17-UTILITIES: Pattern 13: IDs, schedulers, leases and infrastructure utilities | Snowflake; ID allocation; distributed scheduler; leader election; leases; fencing | Build extension |
| 17-CRAWLER: Pattern 14: Bounded crawler and indexing frontier | crawler; URL normalization; deduplication; frontier; per-host politeness; robots; SSRF | Build extension |
| 17-STUDY: Roadmaps, study plans, review trackers and cross-pattern mastery | 8-week plan; 120-day plan; top-20; top-30; four-sheet tracker; nine pillars; NFR glossary; CLEAR | Study workflow |
| 18-CREATE: URL shortener: key generation and durable create path | URL shortener; random keys; Base62; hash collision; sequential IDs; Snowflake; key-generation service; custom alias | Build extension |
| 18-REDIRECT: Redirect path, expiry, caching and hot keys | 301; 302; redirect; TTL; cache-aside; CDN; hot link; Bloom filter | Build extension |
| 18-ANALYTICS: Asynchronous click analytics, abuse controls and privacy | click analytics; async events; rate limiting; abuse prevention; privacy; retention | Build extension |
| 18-SCALE: Global shortener, sharding, replication and recovery | read-heavy scaling; geo routing; sharding; replication; global alias uniqueness; capacity | Build extension |
| 19-STORE: Pastebin: bytes, metadata and publication state machine | Pastebin; large text; object storage; metadata; KGS; checksums; atomic publication | Build extension |
| 19-EXPIRE: Three-layer expiry and cache-safe deletion | logical expiry; cache TTL; physical TTL cleanup; deletion; object lifecycle | Build extension |
| 19-VISIBILITY: Public, unlisted and private pastes | public; unlisted; private; authorization; XSS; syntax highlighting; search | Build extension |
| 19-RECOVER: Pastebin growth, abuse limits and disaster recovery | read/write scaling; CDN; rate limits; multi-region; backup; consistency; retention | Build extension |
| 20-CATALOG: BookMyShow: catalog, search, shows and numbered-seat schema | movie/event catalog; shows; venues; geosearch; seat inventory; read model | Build extension |
| 20-HOLDS: Atomic multi-seat holds and expiration fencing | AVAILABLE; HELD; BOOKED; row locks; hold expiry; fencing; atomic multi-seat booking | Build extension |
| 20-PAYMENT: Mock payments, webhooks and late-success compensation | payment idempotency; unknown outcome; signed callbacks; refund; saga; reconciliation | Build extension |
| 20-FLASH: Waiting room, flash-sale admission and realtime seat maps | flash sale; waiting room; fairness; admission control; WebSocket; backpressure; hot key | Build extension |
| 20-CANCEL: Cancellations, refund accounting and ticket validation | cancellation; refund idempotency; ticket identity; validation; reconciliation; audit | Build extension |
| 20-FAILOVER: Home-show sharding, regional failover and final sale proof | show sharding; home writer; replication; fenced failover; backups; RPO; RTO | Build extension |
| 21-MODEL: Drive/Dropbox: stable file identity, versions, folders and ACLs | file metadata; folders; stable IDs; versions; permissions; quotas; metadata vs bytes | Build extension |
| 21-CHUNK: Resumable chunk upload, manifests and durable commit | chunk upload; resume; manifest; checksum; multipart; dedup; atomic version publication | Build extension |
| 21-SYNC: Commit-safe change cursors, tombstones and offline catch-up | delta sync; change feed; commit order; cursor; tombstones; offline client; snapshot | Build extension |
| 21-CONFLICT: Offline conflict copies, OT/CRDT and simultaneous moves | optimistic version; offline edits; conflict copy; OT; CRDT; merge; rename conflict | Build extension |
| 21-SHARE: Sharing, revocation, search and signed download scope | sharing; ACLs; capability tokens; revocation; private search; signed URLs | Build extension |
| 21-GC: Reference-safe garbage collection and version retention | garbage collection; reference counts; mark-sweep; grace period; tombstones; retention | Build extension |
| 21-GLOBAL: Metadata/byte replication, integrity and global recovery | metadata replication; object replication; multi-region; checksums; erasure; RPO; RTO | Build extension |

## Repeated architecture and pattern sections

All 14 roadmap pattern families have chapter 17 assignments. The 70-problem master map has 70 distinct variant deltas and failure gates in `docs/VARIANTS_70.md`. The 8 source archetypes receive 120 cross-chapter review overlays in `docs/PATTERN_OVERLAYS.md`. Interview questions, comparisons, decision trees and anti-patterns culminate in the lab evidence, ADR and review gates. No framework heading is presented as a deployed feature.



<a id="references"></a>


# Source and implementation references

Curriculum names are source-derived. Code, assignments and playbooks are new implementations. Official documentation supports the stated product semantics; a reference link does not mean the supplied deployment was runtime-verified. Follow the version matching your actual installation.

## [SOURCE] Repository curriculum at pinned revision

https://github.com/IamTheBest1Great/HLD/tree/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes

## [PG-ISO] PostgreSQL17 transaction isolation

https://www.postgresql.org/docs/17/transaction-iso.html

## [PG-LOCK] PostgreSQL17 explicit locking

https://www.postgresql.org/docs/17/explicit-locking.html

## [PG-REPL] PostgreSQL17 standby/streaming replication

https://www.postgresql.org/docs/17/warm-standby.html

## [PG-LOGICAL] PostgreSQL17 logical replication

https://www.postgresql.org/docs/17/logical-replication.html

## [PG-PARTITION] PostgreSQL17 partitioning

https://www.postgresql.org/docs/17/ddl-partitioning.html

## [PG-QUERY] PostgreSQL17 EXPLAIN

https://www.postgresql.org/docs/17/using-explain.html

## [PG-PITR] PostgreSQL17 continuous archiving and recovery

https://www.postgresql.org/docs/17/continuous-archiving.html

## [PG-2PC] PostgreSQL17 PREPARE TRANSACTION

https://www.postgresql.org/docs/17/sql-prepare-transaction.html

## [PG-RLS] PostgreSQL17 row security

https://www.postgresql.org/docs/17/ddl-rowsecurity.html

## [PG-SEARCH] PostgreSQL17 text search

https://www.postgresql.org/docs/17/textsearch.html

## [REDIS-LOCK] Redis distributed locks and assumptions

https://redis.io/docs/latest/develop/clients/patterns/distributed-locks/

## [REDIS-EVICT] Redis eviction policies

https://redis.io/docs/latest/develop/reference/eviction/

## [REDIS-LUA] Redis scripting semantics

https://redis.io/docs/latest/develop/programmability/eval-intro/

## [REDIS-HA] Redis Sentinel

https://redis.io/docs/latest/operate/oss_and_stack/management/sentinel/

## [REDIS-CLUSTER] Redis Cluster specification

https://redis.io/docs/latest/operate/oss_and_stack/reference/cluster-spec/

## [RABBIT-CONFIRM] RabbitMQ acknowledgements and publisher confirms

https://www.rabbitmq.com/docs/confirms

## [RABBIT-QUORUM] RabbitMQ quorum queues

https://www.rabbitmq.com/docs/quorum-queues

## [KAFKA] Apache Kafka4 Docker getting started

https://kafka.apache.org/40/getting-started/docker/

## [ETCD] etcdv3.6 failure behavior

https://etcd.io/docs/v3.6/op-guide/failures/

## [CASSANDRA] Apache Cassandra Dynamo architecture

https://cassandra.apache.org/doc/latest/cassandra/architecture/dynamo.html

## [MONGO] MongoDB self-managed replica sets

https://www.mongodb.com/docs/manual/tutorial/deploy-replica-set/

## [HTTP] RFC9110 HTTP semantics

https://www.rfc-editor.org/rfc/rfc9110.html

## [GRPC] gRPC Node basics

https://grpc.io/docs/languages/node/basics/

## [GRAPHQL] GraphQL DataLoader and N+1

https://www.graphql-js.org/docs/n1-dataloader/

## [NGINX] NGINX upstream configuration

https://nginx.org/en/docs/http/ngx_http_upstream_module.html

## [COMPOSE] Docker Compose startup order

https://docs.docker.com/compose/how-tos/startup-order/

## [K8S-HPA] Kubernetes horizontal pod autoscaling

https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/

## [AWS-PREDICT] EC2 Auto Scaling predictive scaling

https://docs.aws.amazon.com/autoscaling/ec2/userguide/predictive-scaling-policy-overview.html

## [AWS-CFN] CloudFormation predictive scaling configuration

https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/aws-properties-autoscaling-scalingpolicy-predictivescalingconfiguration.html

## [AWS-RETRY] AWS exponential backoff and jitter

https://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/

## [S3-MULTIPART] S3 multipart upload overview

https://docs.aws.amazon.com/AmazonS3/latest/userguide/mpuoverview.html

## [DDB-TTL] DynamoDB TTL deletion behavior

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html

## [KEYCLOAK] Keycloak Docker quickstart

https://www.keycloak.org/getting-started/getting-started-docker

## [OWASP] OWASP API object authorization

https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/

## [OTEL] OpenTelemetry Node.js instrumentation

https://opentelemetry.io/docs/languages/js/getting-started/nodejs/

## [POSTGIS] PostGIS distance predicates

https://postgis.net/docs/ST_DWithin.html

## [YJS] Yjs collaborative editor example

https://docs.yjs.dev/getting-started/a-collaborative-editor

## [ROBOTS] RFC9309 Robots Exclusion Protocol

https://www.rfc-editor.org/rfc/rfc9309.html
