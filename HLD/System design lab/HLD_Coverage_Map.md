# Coverage: source chapters, named strategies and implementation depth

Source revision: `5982a3b5491e4cd1c36902e09fc54c6f0080f7b5`. This manifest covers the21 chapter identities and the explicitly mapped named topics. It is not a paragraph-by-paragraph completeness certificate. Every lab has a distinct implementation/failure gate; a reference command may cover only its prerequisite.

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
| 14. API Design | [14_API_Design.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/14_API_Design.md) | 7 |
| 15. Estimation Skills | [15_Estimation_Skills.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/15_Estimation_Skills.md) | 6 |
| 16. HLD Architecture and Frameworks | [16_HLD_Architecture_and_Frameworks.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/16_HLD_Architecture_and_Frameworks.md) | 6 |
| 17. Roadmaps and Study Plans | [17_Roadmaps_and_Study_Plans.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/17_Roadmaps_and_Study_Plans.md) | 15 |
| 18. Case Study: URL Shortener | [18_Case_Study_URL_Shortener.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/18_Case_Study_URL_Shortener.md) | 4 |
| 19. Case Study: Pastebin | [19_Case_Study_Pastebin.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/19_Case_Study_Pastebin.md) | 4 |
| 20. Case Study: BookMyShow | [20_Case_Study_Book_My_Show.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/20_Case_Study_Book_My_Show.md) | 6 |
| 21. Case Study: Google Drive / Dropbox | [21_Case_Study_Google_Drive_Dropbox.md](https://github.com/IamTheBest1Great/HLD/blob/5982a3b5491e4cd1c36902e09fc54c6f0080f7b5/HLD/HLD_Master_Notes/21_Case_Study_Google_Drive_Dropbox.md) | 7 |

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

All14 roadmap pattern families have chapter17 assignments. The70-problem master map has70 distinct variant deltas and failure gates in `docs/VARIANTS_70.md`. The8 source archetypes receive120 cross-chapter review overlays in `docs/PATTERN_OVERLAYS.md`. Interview questions, comparisons, decision trees and anti-patterns culminate in the lab evidence, ADR and review gates. No framework heading is presented as a deployed feature.
