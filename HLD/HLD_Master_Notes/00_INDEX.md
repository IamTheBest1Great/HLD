# HLD Master Notes — Consolidated Index

> The complete, union-of-all High-Level Design notes: 15 core concepts, an architecture & frameworks reference, a full interview roadmap, and 4 fully worked CRUD case studies. Every file below has its own detailed, anchor-linked table of contents — click through to a file, then use its TOC to jump straight to any subsection.

---

## How to Use These Notes

1. **Learning a concept for the first time?** Read files 01–15 in order — each builds on ideas from earlier ones.
2. **Revising before an interview?** Jump to the `Real-World System Pattern Playbook` / `Patterns by System Type` section inside each concept file (linked below) — it maps every concept to concrete systems (WhatsApp, Uber, a payment gateway, etc.) in a quick-scan table.
3. **Need the big picture / how it all fits together?** Start with file 16 (Architecture & Frameworks) — it has the 6-step interview framework, decision trees, and 7 system archetypes.
4. **Building a study plan?** File 17 has an 8-week plan, a 120-day day-by-day plan, and the Top-20 must-do questions.
5. **Want to see a concept applied end-to-end?** Files 18–21 are complete worked HLD solutions (URL Shortener, Pastebin, BookMyShow, Drive/Dropbox) that pull together every concept above into one design.

---

## Part I — Core Concepts (Files 01–15)

### 1. [Networking & Protocols](01_Networking_and_Protocols.md)

- [1. Networking Fundamentals](01_Networking_and_Protocols.md#1-networking-fundamentals)
- [2. OSI Model & Networking Layers](01_Networking_and_Protocols.md#2-osi-model--networking-layers)
- [3. HTTP & HTTPS](01_Networking_and_Protocols.md#3-http--https)
- [4. TCP vs UDP](01_Networking_and_Protocols.md#4-tcp-vs-udp)
- [5. DNS (Domain Name System)](01_Networking_and_Protocols.md#5-dns-domain-name-system)
- [6. Load Balancing & Proxies](01_Networking_and_Protocols.md#6-load-balancing--proxies)
- [7. Real-Time Communication](01_Networking_and_Protocols.md#7-real-time-communication)
- [8. gRPC, REST & GraphQL](01_Networking_and_Protocols.md#8-grpc-rest--graphql)
- [9. CDN & Global Networking](01_Networking_and_Protocols.md#9-cdn--global-networking)
- [10. Networking Patterns by System Type](01_Networking_and_Protocols.md#10-networking-patterns-by-system-type)
- [11. Interview-Oriented HLD Notes](01_Networking_and_Protocols.md#11-interview-oriented-hld-notes)

### 2. [Scalability](02_Scalability.md)

- [1. Introduction to Scaling](02_Scalability.md#1-introduction-to-scaling)
- [2. Core Scaling Concepts](02_Scalability.md#2-core-scaling-concepts)
- [3. Core Scalability Techniques](02_Scalability.md#3-core-scalability-techniques)
- [4. CDN (Content Delivery Network)](02_Scalability.md#4-cdn-content-delivery-network)
- [5. Scaling Patterns by System Type](02_Scalability.md#5-scaling-patterns-by-system-type)
- [6. Scalability Decision Frameworks](02_Scalability.md#6-scalability-decision-frameworks)
- [7. Interview-Oriented HLD Notes](02_Scalability.md#7-interview-oriented-hld-notes)

### 3. [Consistency & CAP Theorem](03_Consistency_and_CAP_Theorem.md)

- [1. Distributed Systems Foundations](03_Consistency_and_CAP_Theorem.md#1-distributed-systems-foundations)
- [2. CAP Theorem & PACELC](03_Consistency_and_CAP_Theorem.md#2-cap-theorem--pacelc)
- [3. Consistency Models](03_Consistency_and_CAP_Theorem.md#3-consistency-models)
- [4. Replication & Consensus](03_Consistency_and_CAP_Theorem.md#4-replication--consensus)
- [5. Consistency in System Design](03_Consistency_and_CAP_Theorem.md#5-consistency-in-system-design)
- [6. Interview-Oriented HLD Notes](03_Consistency_and_CAP_Theorem.md#6-interview-oriented-hld-notes)
- [7. Real-World System Pattern Playbook](03_Consistency_and_CAP_Theorem.md#7-real-world-system-pattern-playbook)

### 4. [Availability](04_Availability.md)

- [1. Availability in HLD](04_Availability.md#1-availability-in-hld)
- [2. Availability Techniques & When to Use Them](04_Availability.md#2-availability-techniques--when-to-use-them)
- [3. Availability Patterns by System Type](04_Availability.md#3-availability-patterns-by-system-type)

### 5. [Database](05_Database.md)

- [1. Database Fundamentals](05_Database.md#1-database-fundamentals)
- [2. SQL Databases](05_Database.md#2-sql-databases)
- [3. NoSQL Databases](05_Database.md#3-nosql-databases)
- [4. Replication & High Availability](05_Database.md#4-replication--high-availability)
- [5. Database Partitioning & Sharding](05_Database.md#5-database-partitioning--sharding)
- [6. Database Indexing](05_Database.md#6-database-indexing)
- [7. Database Performance Optimization](05_Database.md#7-database-performance-optimization)
- [8. Scaling Databases](05_Database.md#8-scaling-databases)
- [9. Database Patterns by System Type](05_Database.md#9-database-patterns-by-system-type)
- [10. Interview-Oriented HLD Notes](05_Database.md#10-interview-oriented-hld-notes)

### 6. [Caching](06_Caching.md)

- [1. Caching Fundamentals](06_Caching.md#1-caching-fundamentals)
- [2. Caching Strategies](06_Caching.md#2-caching-strategies)
- [3. Cache Eviction Policies](06_Caching.md#3-cache-eviction-policies)
- [4. Distributed Caching](06_Caching.md#4-distributed-caching)
- [5. Cache Invalidation & Consistency](06_Caching.md#5-cache-invalidation--consistency)
- [6. Common Caching Problems & Solutions](06_Caching.md#6-common-caching-problems--solutions)
- [7. Caching Patterns by System Type](06_Caching.md#7-caching-patterns-by-system-type)
- [8. Caching in Real System Design](06_Caching.md#8-caching-in-real-system-design)
- [9. Interview-Oriented HLD Notes](06_Caching.md#9-interview-oriented-hld-notes)

### 7. [Messaging Queues & Async](07_Messaging_Queues_and_Async.md)

- [1. Messaging & Async Fundamentals](07_Messaging_Queues_and_Async.md#1-messaging--async-fundamentals)
- [2. Messaging Systems](07_Messaging_Queues_and_Async.md#2-messaging-systems)
- [3. Delivery Guarantees & Reliability](07_Messaging_Queues_and_Async.md#3-delivery-guarantees--reliability)
- [4. Idempotency](07_Messaging_Queues_and_Async.md#4-idempotency)
- [5. Asynchronous Processing Patterns](07_Messaging_Queues_and_Async.md#5-asynchronous-processing-patterns)
- [6. Scaling Messaging Systems](07_Messaging_Queues_and_Async.md#6-scaling-messaging-systems)
- [7. Common Messaging Problems](07_Messaging_Queues_and_Async.md#7-common-messaging-problems)
- [8. Messaging Patterns by System Type](07_Messaging_Queues_and_Async.md#8-messaging-patterns-by-system-type)
- [9. Real-World Messaging Architectures](07_Messaging_Queues_and_Async.md#9-real-world-messaging-architectures)
- [10. Interview-Oriented HLD Notes](07_Messaging_Queues_and_Async.md#10-interview-oriented-hld-notes)

### 8. [Storage Systems](08_Storage_Systems.md)

- [1. Storage System Fundamentals](08_Storage_Systems.md#1-storage-system-fundamentals)
- [2. Block Storage](08_Storage_Systems.md#2-block-storage)
- [3. File Storage](08_Storage_Systems.md#3-file-storage)
- [4. Object & Blob Storage](08_Storage_Systems.md#4-object--blob-storage)
- [5. CDN & Media Delivery](08_Storage_Systems.md#5-cdn--media-delivery)
- [6. Large File Handling](08_Storage_Systems.md#6-large-file-handling)
- [7. Distributed Storage Concepts](08_Storage_Systems.md#7-distributed-storage-concepts)
- [8. Storage Decision Framework](08_Storage_Systems.md#8-storage-decision-framework)
- [9. Storage Patterns by System Type](08_Storage_Systems.md#9-storage-patterns-by-system-type)
- [10. Interview-Oriented HLD Notes](08_Storage_Systems.md#10-interview-oriented-hld-notes)

### 9. [Fault Tolerance & Reliability](09_Fault_Tolerance_and_Reliability.md)

- [1. Reliability & Fault Tolerance Foundations](09_Fault_Tolerance_and_Reliability.md#1-reliability--fault-tolerance-foundations)
- [2. Core Reliability Patterns](09_Fault_Tolerance_and_Reliability.md#2-core-reliability-patterns)
- [3. Reliability Engineering Techniques](09_Fault_Tolerance_and_Reliability.md#3-reliability-engineering-techniques)
- [4. Reliability & Fault Tolerance in HLD](09_Fault_Tolerance_and_Reliability.md#4-reliability--fault-tolerance-in-hld)
- [5. Real-World System Pattern Playbook](09_Fault_Tolerance_and_Reliability.md#5-real-world-system-pattern-playbook)

### 10. [Reducing Latency](10_Reducing_Latency.md)

- [1. Latency Fundamentals](10_Reducing_Latency.md#1-latency-fundamentals)
- [2. Core Latency Reduction Techniques](10_Reducing_Latency.md#2-core-latency-reduction-techniques)
- [3. Advanced Latency Optimization](10_Reducing_Latency.md#3-advanced-latency-optimization)
- [4. Latency vs Tradeoffs](10_Reducing_Latency.md#4-latency-vs-tradeoffs)
- [5. Decision Framework](10_Reducing_Latency.md#5-decision-framework)
- [6. Latency Reduction Patterns by System Type](10_Reducing_Latency.md#6-latency-reduction-patterns-by-system-type)
- [7. Common Latency Anti-Patterns](10_Reducing_Latency.md#7-common-latency-anti-patterns)
- [8. Interview-Oriented HLD Notes](10_Reducing_Latency.md#8-interview-oriented-hld-notes)

### 11. [Durability](11_Durability.md)

- [1. Durability Fundamentals](11_Durability.md#1-durability-fundamentals)
- [2. Core Durability Techniques](11_Durability.md#2-core-durability-techniques)
- [3. Backup & Recovery](11_Durability.md#3-backup--recovery)
- [4. Durability in Distributed Systems](11_Durability.md#4-durability-in-distributed-systems)
- [5. Durability Decision Framework](11_Durability.md#5-durability-decision-framework)
- [6. Common Anti-Patterns](11_Durability.md#6-common-anti-patterns)
- [7. Durability Patterns by System Type](11_Durability.md#7-durability-patterns-by-system-type)
- [8. Interview-Oriented HLD Notes](11_Durability.md#8-interview-oriented-hld-notes)

### 12. [Security](12_Security.md)

- [1. Security Fundamentals](12_Security.md#1-security-fundamentals)
- [2. Authentication & Authorization](12_Security.md#2-authentication--authorization)
- [3. Encryption & Data Protection](12_Security.md#3-encryption--data-protection)
- [4. Application Security](12_Security.md#4-application-security)
- [5. Infrastructure & Network Security](12_Security.md#5-infrastructure--network-security)
- [6. Monitoring, Auditing & Incident Response](12_Security.md#6-monitoring-auditing--incident-response)
- [7. Security in Distributed Systems](12_Security.md#7-security-in-distributed-systems)
- [8. Security Decision Framework](12_Security.md#8-security-decision-framework)
- [9. Common Security Anti-Patterns](12_Security.md#9-common-security-anti-patterns)
- [10. Security Patterns by System Type](12_Security.md#10-security-patterns-by-system-type)
- [11. Comparison of the Four Pillars](12_Security.md#11-comparison-of-the-four-pillars)
- [12. Interview-Oriented HLD Notes](12_Security.md#12-interview-oriented-hld-notes)

### 13. [Maintainability](13_Maintainability.md)

- [1. Maintainability Fundamentals](13_Maintainability.md#1-maintainability-fundamentals)
- [2. Architecture for Maintainability](13_Maintainability.md#2-architecture-for-maintainability)
- [3. Code Quality & Engineering Practices](13_Maintainability.md#3-code-quality--engineering-practices)
- [4. Testing Strategies](13_Maintainability.md#4-testing-strategies)
- [5. CI/CD & Deployment Maintainability](13_Maintainability.md#5-cicd--deployment-maintainability)
- [6. Observability & Debuggability](13_Maintainability.md#6-observability--debuggability)
- [7. Infrastructure Maintainability](13_Maintainability.md#7-infrastructure-maintainability)
- [8. Documentation & Knowledge Sharing](13_Maintainability.md#8-documentation--knowledge-sharing)
- [9. Maintainability Decision Framework](13_Maintainability.md#9-maintainability-decision-framework)
- [10. Common Maintainability Anti-Patterns](13_Maintainability.md#10-common-maintainability-anti-patterns)
- [11. Maintainability Patterns by System Type](13_Maintainability.md#11-maintainability-patterns-by-system-type)
- [12. Interview-Oriented HLD Notes](13_Maintainability.md#12-interview-oriented-hld-notes)

### 14. [API Design](14_API_Design.md)

- [1. API Design Fundamentals](14_API_Design.md#1-api-design-fundamentals)
- [2. RESTful Principles](14_API_Design.md#2-restful-principles)
- [3. Pagination](14_API_Design.md#3-pagination)
- [4. Idempotency Keys](14_API_Design.md#4-idempotency-keys)
- [5. API Versioning](14_API_Design.md#5-api-versioning)
- [6. API Security](14_API_Design.md#6-api-security)
- [7. API Performance & Scalability](14_API_Design.md#7-api-performance--scalability)
- [8. API Design Patterns](14_API_Design.md#8-api-design-patterns)
- [9. API Design by System Type](14_API_Design.md#9-api-design-by-system-type)
- [10. Common API Anti-Patterns](14_API_Design.md#10-common-api-anti-patterns)
- [11. Interview-Oriented HLD Notes](14_API_Design.md#11-interview-oriented-hld-notes)

### 15. [Estimation Skills](15_Estimation_Skills.md)

- [1. Estimation Fundamentals](15_Estimation_Skills.md#1-estimation-fundamentals)
- [2. Numbers Every Engineer Should Know](15_Estimation_Skills.md#2-numbers-every-engineer-should-know)
- [3. Traffic Estimation](15_Estimation_Skills.md#3-traffic-estimation)
- [4. Storage Estimation](15_Estimation_Skills.md#4-storage-estimation)
- [5. Bandwidth Estimation](15_Estimation_Skills.md#5-bandwidth-estimation)
- [6. Caching Estimation](15_Estimation_Skills.md#6-caching-estimation)
- [7. Estimation Framework](15_Estimation_Skills.md#7-estimation-framework)
- [8. Worked Examples](15_Estimation_Skills.md#8-worked-examples)
- [9. Common Estimation Patterns](15_Estimation_Skills.md#9-common-estimation-patterns)
- [10. Common Pitfalls](15_Estimation_Skills.md#10-common-pitfalls)
- [11. Interview-Oriented HLD Notes](15_Estimation_Skills.md#11-interview-oriented-hld-notes)
- [12. Quick Numbers by System Type](15_Estimation_Skills.md#12-quick-numbers-by-system-type)

---

## Part II — Architecture, Frameworks & Roadmaps (Files 16–17)

### 16. [HLD Architecture & Frameworks](16_HLD_Architecture_and_Frameworks.md)

- [1. The Mental Model Before You Draw Anything](16_HLD_Architecture_and_Frameworks.md#1-the-mental-model-before-you-draw-anything)
- [2. The 6-Step Interview Framework](16_HLD_Architecture_and_Frameworks.md#2-the-6-step-interview-framework)
- [3. The Universal Architecture Template](16_HLD_Architecture_and_Frameworks.md#3-the-universal-architecture-template)
- [4. Decision Trees for Every Choice Point](16_HLD_Architecture_and_Frameworks.md#4-decision-trees-for-every-choice-point)
- [5. Complete System Archetypes](16_HLD_Architecture_and_Frameworks.md#5-complete-system-archetypes)
- [6. How All Concepts Connect in One View](16_HLD_Architecture_and_Frameworks.md#6-how-all-concepts-connect-in-one-view)
- [7. Anti-Patterns to Avoid](16_HLD_Architecture_and_Frameworks.md#7-anti-patterns-to-avoid)
- [8. Interview Execution Checklist](16_HLD_Architecture_and_Frameworks.md#8-interview-execution-checklist)
- [9. The Mental Walkthrough for Any Problem](16_HLD_Architecture_and_Frameworks.md#9-the-mental-walkthrough-for-any-problem)
- [10. Universal Clarifying-Questions Checklist](16_HLD_Architecture_and_Frameworks.md#10-universal-clarifying-questions-checklist)
- [11. The 5-Step Rapid Interview Framework (Quick Reference)](16_HLD_Architecture_and_Frameworks.md#11-the-5-step-rapid-interview-framework-quick-reference)
- [12. The 70-Problem HLD Master Map](16_HLD_Architecture_and_Frameworks.md#12-the-70-problem-hld-master-map)
- [13. The Generic Seven-Layer Architecture Template (Layer-by-Layer)](16_HLD_Architecture_and_Frameworks.md#13-the-generic-seven-layer-architecture-template-layer-by-layer)
- [14. Worked Reference Numbers: A Feed System at 500M DAU](16_HLD_Architecture_and_Frameworks.md#14-worked-reference-numbers-a-feed-system-at-500m-dau)

### 17. [Roadmaps & Study Plans](17_Roadmaps_and_Study_Plans.md)

- [1. PHASE 0 — Understanding What HLD Interviews Actually Test](17_Roadmaps_and_Study_Plans.md#1-phase-0---understanding-what-hld-interviews-actually-test)
- [2. PHASE 1 — Build Strong Foundational Knowledge (Weeks 1-3)](17_Roadmaps_and_Study_Plans.md#2-phase-1---build-strong-foundational-knowledge-weeks-1-3)
- [3. PHASE 2 — Master the Interview Framework (Your 45-Minute Template)](17_Roadmaps_and_Study_Plans.md#3-phase-2---master-the-interview-framework-your-45-minute-template)
- [4. PHASE 3 — Pattern-Based Practice (The Core of Your Preparation)](17_Roadmaps_and_Study_Plans.md#4-phase-3---pattern-based-practice-the-core-of-your-preparation)
- [5. PATTERN 1 — CRUD / Storage-Based Systems](17_Roadmaps_and_Study_Plans.md#5-pattern-1---crud--storage-based-systems)
- [6. PATTERN 2 — Feed / Timeline Systems](17_Roadmaps_and_Study_Plans.md#6-pattern-2---feed--timeline-systems)
- [7. PATTERN 3 — Real-Time Systems](17_Roadmaps_and_Study_Plans.md#7-pattern-3---real-time-systems)
- [8. PATTERN 4 — Search Systems](17_Roadmaps_and_Study_Plans.md#8-pattern-4---search-systems)
- [9. PATTERN 5 — Rate Limiter / Throttling](17_Roadmaps_and_Study_Plans.md#9-pattern-5---rate-limiter--throttling)
- [10. PATTERN 6 — Logging / Metrics / Monitoring](17_Roadmaps_and_Study_Plans.md#10-pattern-6---logging--metrics--monitoring)
- [11. PATTERN 7 — Distributed Caching Systems](17_Roadmaps_and_Study_Plans.md#11-pattern-7---distributed-caching-systems)
- [12. PATTERN 8 — Recommendation Systems](17_Roadmaps_and_Study_Plans.md#12-pattern-8---recommendation-systems)
- [13. PATTERN 9 — Payment / Financial Systems](17_Roadmaps_and_Study_Plans.md#13-pattern-9---payment--financial-systems)
- [14. PATTERN 10 — Large-Scale Processing](17_Roadmaps_and_Study_Plans.md#14-pattern-10---large-scale-processing)
- [15. PATTERN 11 — Geospatial / Location-Based Systems](17_Roadmaps_and_Study_Plans.md#15-pattern-11---geospatial--location-based-systems)
- [16. PATTERN 12 — Collaborative Systems](17_Roadmaps_and_Study_Plans.md#16-pattern-12---collaborative-systems)
- [17. PATTERN 13 — Infrastructure / System Utilities](17_Roadmaps_and_Study_Plans.md#17-pattern-13---infrastructure--system-utilities)
- [18. PATTERN 14 — Web Crawler / Search Indexing](17_Roadmaps_and_Study_Plans.md#18-pattern-14---web-crawler--search-indexing)
- [19. PHASE 4 — 8-Week Execution Plan](17_Roadmaps_and_Study_Plans.md#19-phase-4---8-week-execution-plan)
- [20. How to Practice (Like Your DP Excel Tracker)](17_Roadmaps_and_Study_Plans.md#20-how-to-practice-like-your-dp-excel-tracker)
- [21. How to Think Like Top Engineers](17_Roadmaps_and_Study_Plans.md#21-how-to-think-like-top-engineers)
- [22. Top 20 Must-Do Questions (Most Frequently Asked)](17_Roadmaps_and_Study_Plans.md#22-top-20-must-do-questions-most-frequently-asked)
- [23. Advanced (Staff-Level) Topics](17_Roadmaps_and_Study_Plans.md#23-advanced-staff-level-topics)
- [24. Complete Resource Guide](17_Roadmaps_and_Study_Plans.md#24-complete-resource-guide)
- [25. Final Advice for You Specifically](17_Roadmaps_and_Study_Plans.md#25-final-advice-for-you-specifically)
- [26. Quick Reference: HLD Interview Checklist](17_Roadmaps_and_Study_Plans.md#26-quick-reference-hld-interview-checklist)
- [27. Your Next Steps](17_Roadmaps_and_Study_Plans.md#27-your-next-steps)
- [28. Master Table: The Nine Pillars of High-Level Design](17_Roadmaps_and_Study_Plans.md#28-master-table-the-nine-pillars-of-high-level-design)
- [29. Non-Functional Requirements Glossary](17_Roadmaps_and_Study_Plans.md#29-non-functional-requirements-glossary)
- [30. Recommended Study Order](17_Roadmaps_and_Study_Plans.md#30-recommended-study-order)
- [31. The 120-Day Day-by-Day Mastery Schedule](17_Roadmaps_and_Study_Plans.md#31-the-120-day-day-by-day-mastery-schedule)
- [32. Ready-to-Use Tracking Template (4-Sheet Spec)](17_Roadmaps_and_Study_Plans.md#32-ready-to-use-tracking-template-4-sheet-spec)
- [33. Alternative Tiered Priority List (Top 30, by Tier)](17_Roadmaps_and_Study_Plans.md#33-alternative-tiered-priority-list-top-30-by-tier)
- [34. Cross-Source Notes: CLEAR Method, LB Algorithms & the Trade-off Sentence](17_Roadmaps_and_Study_Plans.md#34-cross-source-notes-clear-method-lb-algorithms--the-trade-off-sentence)

---

## Part III — Worked Case Studies (Files 18–21)

### 18. [Case Study — URL Shortener](18_Case_Study_URL_Shortener.md)


### 19. [Case Study — Pastebin](19_Case_Study_Pastebin.md)

- [1. Introduction](19_Case_Study_Pastebin.md#1-introduction)
- [2. Requirements](19_Case_Study_Pastebin.md#2-requirements)
- [3. Traffic Estimation & Capacity Planning](19_Case_Study_Pastebin.md#3-traffic-estimation--capacity-planning)
- [4. API Design](19_Case_Study_Pastebin.md#4-api-design)
- [5. Database Design](19_Case_Study_Pastebin.md#5-database-design)
- [6. HLD Architecture — Diagram Walkthrough](19_Case_Study_Pastebin.md#6-hld-architecture---diagram-walkthrough)
- [7. Key Generation Service (KGS) — Deep Dive](19_Case_Study_Pastebin.md#7-key-generation-service-kgs---deep-dive)
- [8. Full High-Level Architecture](19_Case_Study_Pastebin.md#8-full-high-level-architecture)
- [9. Caching Strategy — Multi-Layer](19_Case_Study_Pastebin.md#9-caching-strategy---multi-layer)
- [10. Expiration & Cleanup — Three-Layer Design](19_Case_Study_Pastebin.md#10-expiration--cleanup---three-layer-design)
- [11. Bloom Filter — Optimizing 404 Lookups](19_Case_Study_Pastebin.md#11-bloom-filter---optimizing-404-lookups)
- [12. Multi-Region Deployment](19_Case_Study_Pastebin.md#12-multi-region-deployment)
- [13. Trade-offs & Design Decisions](19_Case_Study_Pastebin.md#13-trade-offs--design-decisions)
- [14. Failure Scenarios & Mitigations](19_Case_Study_Pastebin.md#14-failure-scenarios--mitigations)
- [15. Security Considerations](19_Case_Study_Pastebin.md#15-security-considerations)
- [16. Scaling to 100×](19_Case_Study_Pastebin.md#16-scaling-to-100)
- [17. Key Differences: Video Diagram vs General HLD](19_Case_Study_Pastebin.md#17-key-differences-video-diagram-vs-general-hld)
- [18. Interview Summary](19_Case_Study_Pastebin.md#18-interview-summary)

### 20. [Case Study — BookMyShow](20_Case_Study_Book_My_Show.md)

- [1. Introduction](20_Case_Study_Book_My_Show.md#1-introduction)
- [2. Requirements](20_Case_Study_Book_My_Show.md#2-requirements)
- [3. Traffic Estimation & Capacity Planning](20_Case_Study_Book_My_Show.md#3-traffic-estimation--capacity-planning)
- [4. API Design](20_Case_Study_Book_My_Show.md#4-api-design)
- [5. Database Design](20_Case_Study_Book_My_Show.md#5-database-design)
- [6. HLD Architecture — Diagram Walkthrough](20_Case_Study_Book_My_Show.md#6-hld-architecture---diagram-walkthrough)
- [7. Seat Reservation — The Core Problem](20_Case_Study_Book_My_Show.md#7-seat-reservation---the-core-problem)
- [8. Booking Flow — End to End](20_Case_Study_Book_My_Show.md#8-booking-flow---end-to-end)
- [9. Real-Time Seat Map — WebSocket Design](20_Case_Study_Book_My_Show.md#9-real-time-seat-map---websocket-design)
- [10. Handling the Taylor Swift Problem — Thundering Herd](20_Case_Study_Book_My_Show.md#10-handling-the-taylor-swift-problem---thundering-herd)
- [11. Cancellation & Refund Flow](20_Case_Study_Book_My_Show.md#11-cancellation--refund-flow)
- [12. Search & Discovery](20_Case_Study_Book_My_Show.md#12-search--discovery)
- [13. Multi-Region Deployment](20_Case_Study_Book_My_Show.md#13-multi-region-deployment)
- [14. Trade-offs & Design Decisions](20_Case_Study_Book_My_Show.md#14-trade-offs--design-decisions)
- [15. Failure Scenarios & Mitigations](20_Case_Study_Book_My_Show.md#15-failure-scenarios--mitigations)
- [16. Security Considerations](20_Case_Study_Book_My_Show.md#16-security-considerations)
- [17. Scaling to 100×](20_Case_Study_Book_My_Show.md#17-scaling-to-100)
- [18. Comparison: BookMyShow vs Pastebin vs URL Shortener](20_Case_Study_Book_My_Show.md#18-comparison-bookmyshow-vs-pastebin-vs-url-shortener)
- [19. Interview Summary](20_Case_Study_Book_My_Show.md#19-interview-summary)

### 21. [Case Study — Google Drive / Dropbox](21_Case_Study_Google_Drive_Dropbox.md)

- [1. Introduction](21_Case_Study_Google_Drive_Dropbox.md#1-introduction)
- [2. Requirements](21_Case_Study_Google_Drive_Dropbox.md#2-requirements)
- [3. Traffic Estimation and Capacity Planning](21_Case_Study_Google_Drive_Dropbox.md#3-traffic-estimation-and-capacity-planning)
- [4. API Design](21_Case_Study_Google_Drive_Dropbox.md#4-api-design)
- [5. Database Design](21_Case_Study_Google_Drive_Dropbox.md#5-database-design)
- [6. High-Level Architecture**Component Roles**](21_Case_Study_Google_Drive_Dropbox.md#6-high-level-architecturecomponent-roles)
- [7. Detailed Component Design](21_Case_Study_Google_Drive_Dropbox.md#7-detailed-component-design)
- [8. Multi-Region Deployment](21_Case_Study_Google_Drive_Dropbox.md#8-multi-region-deployment)
- [9. Trade-offs and Discussion](21_Case_Study_Google_Drive_Dropbox.md#9-trade-offs-and-discussion)
- [10. Failure Scenarios and Mitigations](21_Case_Study_Google_Drive_Dropbox.md#10-failure-scenarios-and-mitigations)
- [11. Security Considerations](21_Case_Study_Google_Drive_Dropbox.md#11-security-considerations)
- [12. Conclusion and Interview Summary](21_Case_Study_Google_Drive_Dropbox.md#12-conclusion-and-interview-summary)

---

*This index and all 21 files are the union of every HLD note across your repository and prior uploads, deduplicated, cross-checked for broken links, and enriched with real-world system pattern tables.*
