# API Design — Consolidated HLD Notes

## Table of Contents

- [1. API Design Fundamentals](#1-api-design-fundamentals)
  - [1.1 What Is API Design?](#11-what-is-api-design)
    - [1.1.1 Communication Between Systems](#111-communication-between-systems)
    - [1.1.2 Public vs Internal APIs](#112-public-vs-internal-apis)
    - [1.1.3 Contract-Driven Development](#113-contract-driven-development)
    - [1.1.4 Scalability & Maintainability Concerns](#114-scalability--maintainability-concerns)
  - [1.2 API Design Goals](#12-api-design-goals)
    - [1.2.1 Simplicity](#121-simplicity)
    - [1.2.2 Consistency](#122-consistency)
    - [1.2.3 Scalability](#123-scalability)
    - [1.2.4 Backward Compatibility](#124-backward-compatibility)
    - [1.2.5 Security](#125-security)
    - [1.2.6 Developer Experience](#126-developer-experience)
- [2. RESTful Principles](#2-restful-principles)
  - [2.1 What Is REST?](#21-what-is-rest)
    - [2.1.1 Representational State Transfer](#211-representational-state-transfer)
    - [2.1.2 Stateless Communication](#212-stateless-communication)
    - [2.1.3 Resource-Oriented Design](#213-resource-oriented-design)
  - [2.2 Core REST Constraints](#22-core-rest-constraints)
    - [2.2.1 Client-Server Architecture](#221-client-server-architecture)
    - [2.2.2 Statelessness](#222-statelessness)
    - [2.2.3 Cacheability](#223-cacheability)
    - [2.2.4 Uniform Interface](#224-uniform-interface)
    - [2.2.5 Layered System](#225-layered-system)
    - [2.2.6 Code on Demand](#226-code-on-demand)
  - [2.3 Resource-Oriented Design](#23-resource-oriented-design)
    - [2.3.1 Resources & URIs](#231-resources--uris)
    - [2.3.2 Nouns Instead of Verbs](#232-nouns-instead-of-verbs)
    - [2.3.3 Nested Resources](#233-nested-resources)
    - [2.3.4 Resource Relationships](#234-resource-relationships)
  - [2.4 HTTP Methods](#24-http-methods)
    - [2.4.1 GET](#241-get)
    - [2.4.2 POST](#242-post)
    - [2.4.3 PUT](#243-put)
    - [2.4.4 PATCH](#244-patch)
    - [2.4.5 DELETE](#245-delete)
    - [2.4.6 OPTIONS](#246-options)
  - [2.5 Idempotency in HTTP Methods](#25-idempotency-in-http-methods)
    - [2.5.1 Safe Methods](#251-safe-methods)
    - [2.5.2 Idempotent Methods](#252-idempotent-methods)
    - [2.5.3 Non-Idempotent Methods](#253-non-idempotent-methods)
    - [2.5.4 Retry Implications](#254-retry-implications)
  - [2.6 HTTP Status Codes](#26-http-status-codes)
    - [2.6.1 2xx Success Codes](#261-2xx-success-codes)
    - [2.6.2 3xx Redirection Codes](#262-3xx-redirection-codes)
    - [2.6.3 4xx Client Errors](#263-4xx-client-errors)
    - [2.6.4 5xx Server Errors](#264-5xx-server-errors)
  - [2.7 Example RESTful API Design](#27-example-restful-api-design)
    - [2.7.1 User APIs](#271-user-apis)
    - [2.7.2 Order APIs](#272-order-apis)
    - [2.7.3 CRUD Endpoint Patterns](#273-crud-endpoint-patterns)
- [3. Pagination](#3-pagination)
  - [3.1 Why Pagination Matters](#31-why-pagination-matters)
    - [3.1.1 Large Dataset Handling](#311-large-dataset-handling)
    - [3.1.2 Performance Optimization](#312-performance-optimization)
    - [3.1.3 Bandwidth Reduction](#313-bandwidth-reduction)
    - [3.1.4 Better User Experience](#314-better-user-experience)
  - [3.2 Offset-Based Pagination](#32-offset-based-pagination)
    - [3.2.1 Limit & Offset](#321-limit--offset)
    - [3.2.2 Advantages](#322-advantages)
    - [3.2.3 Performance Problems](#323-performance-problems)
    - [3.2.4 Duplicate / Missing Data Issues](#324-duplicate--missing-data-issues)
  - [3.3 Cursor-Based Pagination](#33-cursor-based-pagination)
    - [3.3.1 Cursor Tokens](#331-cursor-tokens)
    - [3.3.2 Stable Pagination](#332-stable-pagination)
    - [3.3.3 Infinite Scroll Use Cases](#333-infinite-scroll-use-cases)
    - [3.3.4 Tradeoffs](#334-tradeoffs)
  - [3.4 Keyset Pagination (Seek Method)](#34-keyset-pagination-seek-method)
    - [3.4.1 Indexed Lookups](#341-indexed-lookups)
    - [3.4.2 High Performance](#342-high-performance)
    - [3.4.3 Real-Time Feeds](#343-real-time-feeds)
  - [3.5 Page-Based Pagination](#35-page-based-pagination)
    - [3.5.1 Traditional Page Numbers](#351-traditional-page-numbers)
    - [3.5.2 UI Simplicity](#352-ui-simplicity)
    - [3.5.3 Tradeoffs](#353-tradeoffs)
  - [3.6 Pagination Best Practices](#36-pagination-best-practices)
    - [3.6.1 Default Limits](#361-default-limits)
    - [3.6.2 Maximum Limits](#362-maximum-limits)
    - [3.6.3 Sorting Requirements](#363-sorting-requirements)
    - [3.6.4 Stable Ordering](#364-stable-ordering)
  - [3.7 Pagination Examples](#37-pagination-examples)
    - [3.7.1 Offset Pagination Example](#371-offset-pagination-example)
    - [3.7.2 Cursor Pagination Example](#372-cursor-pagination-example)
    - [3.7.3 Keyset Pagination Example](#373-keyset-pagination-example)
- [4. Idempotency Keys](#4-idempotency-keys)
  - [4.1 Why Idempotency Matters](#41-why-idempotency-matters)
    - [4.1.1 Network Retries](#411-network-retries)
    - [4.1.2 Duplicate Requests](#412-duplicate-requests)
    - [4.1.3 Payment Safety](#413-payment-safety)
    - [4.1.4 Distributed System Reliability](#414-distributed-system-reliability)
  - [4.2 Idempotent HTTP Methods](#42-idempotent-http-methods)
    - [4.2.1 GET](#421-get)
    - [4.2.2 PUT](#422-put)
    - [4.2.3 DELETE](#423-delete)
    - [4.2.4 POST Challenges](#424-post-challenges)
  - [4.3 How Idempotency Keys Work](#43-how-idempotency-keys-work)
    - [4.3.1 Client-Generated Keys](#431-client-generated-keys)
    - [4.3.2 Request Deduplication](#432-request-deduplication)
    - [4.3.3 Response Reuse](#433-response-reuse)
    - [4.3.4 Request Fingerprinting](#434-request-fingerprinting)
  - [4.4 Idempotency Implementation](#44-idempotency-implementation)
    - [4.4.1 Redis Storage](#441-redis-storage)
    - [4.4.2 TTL Expiration](#442-ttl-expiration)
    - [4.4.3 Database Constraints](#443-database-constraints)
    - [4.4.4 Response Caching](#444-response-caching)
  - [4.5 Real-World Idempotency](#45-real-world-idempotency)
    - [4.5.1 Stripe Payment APIs](#451-stripe-payment-apis)
    - [4.5.2 Order Creation APIs](#452-order-creation-apis)
    - [4.5.3 Booking Systems](#453-booking-systems)
  - [4.6 Important Considerations](#46-important-considerations)
    - [4.6.1 Key Expiration](#461-key-expiration)
    - [4.6.2 Request Validation](#462-request-validation)
    - [4.6.3 Replay Attack Prevention](#463-replay-attack-prevention)
    - [4.6.4 Scalability Tradeoffs](#464-scalability-tradeoffs)
- [5. API Versioning](#5-api-versioning)
  - [5.1 Why Version APIs?](#51-why-version-apis)
    - [5.1.1 Backward Compatibility](#511-backward-compatibility)
    - [5.1.2 Safe API Evolution](#512-safe-api-evolution)
    - [5.1.3 Client Compatibility](#513-client-compatibility)
    - [5.1.4 Incremental Upgrades](#514-incremental-upgrades)
  - [5.2 URI Path Versioning](#52-uri-path-versioning)
    - [5.2.1 /v1/users](#521-v1users)
    - [5.2.2 Advantages](#522-advantages)
    - [5.2.3 Drawbacks](#523-drawbacks)
  - [5.3 Query Parameter Versioning](#53-query-parameter-versioning)
    - [5.3.1 ?version=v1](#531-versionv1)
    - [5.3.2 Advantages](#532-advantages)
    - [5.3.3 Drawbacks](#533-drawbacks)
  - [5.4 Header-Based Versioning](#54-header-based-versioning)
    - [5.4.1 Custom Headers](#541-custom-headers)
    - [5.4.2 Advantages](#542-advantages)
    - [5.4.3 Drawbacks](#543-drawbacks)
  - [5.5 Content Negotiation](#55-content-negotiation)
    - [5.5.1 Accept Headers](#551-accept-headers)
    - [5.5.2 Media Types](#552-media-types)
    - [5.5.3 Advantages](#553-advantages)
    - [5.5.4 Drawbacks](#554-drawbacks)
  - [5.6 API Deprecation](#56-api-deprecation)
    - [5.6.1 Deprecation Headers](#561-deprecation-headers)
    - [5.6.2 Sunset Policies](#562-sunset-policies)
    - [5.6.3 Migration Guides](#563-migration-guides)
    - [5.6.4 Gradual Rollouts](#564-gradual-rollouts)
  - [5.7 API Versioning Best Practices](#57-api-versioning-best-practices)
    - [5.7.1 Minimize Breaking Changes](#571-minimize-breaking-changes)
    - [5.7.2 Semantic Versioning](#572-semantic-versioning)
    - [5.7.3 Compatibility Windows](#573-compatibility-windows)
- [6. API Security](#6-api-security)
  - [6.1 Authentication](#61-authentication)
    - [6.1.1 API Keys](#611-api-keys)
    - [6.1.2 OAuth 2.0](#612-oauth-20)
    - [6.1.3 JWT Authentication](#613-jwt-authentication)
    - [6.1.4 Session-Based Authentication](#614-session-based-authentication)
  - [6.2 Authorization](#62-authorization)
    - [6.2.1 RBAC](#621-rbac)
    - [6.2.2 ABAC](#622-abac)
    - [6.2.3 Resource-Level Permissions](#623-resource-level-permissions)
  - [6.3 Transport Security](#63-transport-security)
    - [6.3.1 HTTPS Everywhere](#631-https-everywhere)
    - [6.3.2 TLS Enforcement](#632-tls-enforcement)
    - [6.3.3 Certificate Validation](#633-certificate-validation)
  - [6.4 API Abuse Prevention](#64-api-abuse-prevention)
    - [6.4.1 Rate Limiting](#641-rate-limiting)
    - [6.4.2 Throttling](#642-throttling)
    - [6.4.3 WAF Protection](#643-waf-protection)
    - [6.4.4 Input Validation](#644-input-validation)
- [7. API Performance & Scalability](#7-api-performance--scalability)
  - [7.1 Caching](#71-caching)
    - [7.1.1 HTTP Caching](#711-http-caching)
    - [7.1.2 ETags](#712-etags)
    - [7.1.3 Cache-Control Headers](#713-cache-control-headers)
    - [7.1.4 CDN Integration](#714-cdn-integration)
  - [7.2 Compression](#72-compression)
    - [7.2.1 Gzip](#721-gzip)
    - [7.2.2 Brotli](#722-brotli)
    - [7.2.3 Payload Reduction](#723-payload-reduction)
  - [7.3 Asynchronous APIs](#73-asynchronous-apis)
    - [7.3.1 Background Processing](#731-background-processing)
    - [7.3.2 Webhooks](#732-webhooks)
    - [7.3.3 Polling vs Push Notifications](#733-polling-vs-push-notifications)
  - [7.4 Rate Limiting & Quotas](#74-rate-limiting--quotas)
    - [7.4.1 Token Bucket](#741-token-bucket)
    - [7.4.2 Sliding Window](#742-sliding-window)
    - [7.4.3 User-Level Limits](#743-user-level-limits)
    - [7.4.4 Tenant-Level Limits](#744-tenant-level-limits)
- [8. API Design Patterns](#8-api-design-patterns)
  - [8.1 REST vs GraphQL](#81-rest-vs-graphql)
    - [8.1.1 REST Advantages](#811-rest-advantages)
    - [8.1.2 GraphQL Advantages](#812-graphql-advantages)
    - [8.1.3 Tradeoffs](#813-tradeoffs)
    - [8.1.4 Use Cases](#814-use-cases)
  - [8.2 REST vs gRPC](#82-rest-vs-grpc)
    - [8.2.1 Human Readability](#821-human-readability)
    - [8.2.2 Binary Protocols](#822-binary-protocols)
    - [8.2.3 Streaming Support](#823-streaming-support)
    - [8.2.4 Internal Microservices](#824-internal-microservices)
  - [8.3 Webhooks](#83-webhooks)
    - [8.3.1 Event Notifications](#831-event-notifications)
    - [8.3.2 Retry Strategies](#832-retry-strategies)
    - [8.3.3 Signature Verification](#833-signature-verification)
  - [8.4 API Gateway Pattern](#84-api-gateway-pattern)
    - [8.4.1 Centralized Routing](#841-centralized-routing)
    - [8.4.2 Authentication](#842-authentication)
    - [8.4.3 Rate Limiting](#843-rate-limiting)
    - [8.4.4 Request Aggregation](#844-request-aggregation)
- [9. API Design by System Type](#9-api-design-by-system-type)
  - [9.1 Summary: API Design Techniques by Pattern](#91-summary-api-design-techniques-by-pattern)
  - [9.2 Pattern A – CRUD & Storage Systems](#92-pattern-a---crud--storage-systems)
  - [9.3 Pattern B – Feed / Timeline Systems](#93-pattern-b---feed--timeline-systems)
  - [9.4 Pattern C – Real-Time Systems](#94-pattern-c---real-time-systems)
  - [9.5 Pattern D – Search Systems](#95-pattern-d---search-systems)
  - [9.6 Pattern E – Geospatial & Location-Based Systems](#96-pattern-e---geospatial--location-based-systems)
  - [9.7 Pattern F – Financial & Critical Consistency Systems](#97-pattern-f---financial--critical-consistency-systems)
  - [9.8 Pattern G – Large-Scale Processing & Infrastructure](#98-pattern-g---large-scale-processing--infrastructure)
  - [9.9 Pattern H – Collaborative & Infrastructure Utilities](#99-pattern-h---collaborative--infrastructure-utilities)
- [10. Common API Anti-Patterns](#10-common-api-anti-patterns)
  - [10.1 Verb-Based URLs](#101-verb-based-urls)
  - [10.2 Breaking API Changes](#102-breaking-api-changes)
  - [10.3 Missing Pagination](#103-missing-pagination)
  - [10.4 Returning Excessive Data](#104-returning-excessive-data)
  - [10.5 Inconsistent Naming](#105-inconsistent-naming)
  - [10.6 No Versioning Strategy](#106-no-versioning-strategy)
  - [10.7 Ignoring Idempotency](#107-ignoring-idempotency)
  - [10.8 Overfetching & Underfetching](#108-overfetching--underfetching)
  - [10.9 Weak Error Handling](#109-weak-error-handling)
  - [10.10 Missing Rate Limits](#1010-missing-rate-limits)
- [11. Interview-Oriented HLD Notes](#11-interview-oriented-hld-notes)
  - [11.1 Key Tradeoffs](#111-key-tradeoffs)
    - [11.1.1 REST vs GraphQL](#1111-rest-vs-graphql)
    - [11.1.2 Offset vs Cursor Pagination](#1112-offset-vs-cursor-pagination)
    - [11.1.3 Sync vs Async APIs](#1113-sync-vs-async-apis)
    - [11.1.4 Simplicity vs Flexibility](#1114-simplicity-vs-flexibility)
  - [11.2 Common Interview Questions](#112-common-interview-questions)
    - [11.2.1 How Would You Design REST APIs?](#1121-how-would-you-design-rest-apis)
    - [11.2.2 Offset vs Cursor Pagination?](#1122-offset-vs-cursor-pagination)
    - [11.2.3 Why Use Idempotency Keys?](#1123-why-use-idempotency-keys)
    - [11.2.4 How to Version APIs?](#1124-how-to-version-apis)
    - [11.2.5 REST vs gRPC?](#1125-rest-vs-grpc)
  - [11.3 Quick Revision Material](#113-quick-revision-material)
    - [11.3.1 HTTP Methods Cheat Sheet](#1131-http-methods-cheat-sheet)
    - [11.3.2 Pagination Comparison Table](#1132-pagination-comparison-table)
    - [11.3.3 API Versioning Comparison](#1133-api-versioning-comparison)
    - [11.3.4 Key Interview Sentences](#1134-key-interview-sentences)
    - [11.3.5 Self-Test Questions](#1135-self-test-questions)

---

## 1. API Design Fundamentals

### 1.1 What Is API Design?

API Design is the process of defining the interface through which software systems communicate. A well‑designed API is intuitive, consistent, scalable, and secure. It acts as a contract between the provider and consumers, enabling independent evolution on both sides.

#### 1.1.1 Communication Between Systems
APIs define how different software components interact — whether across a network (REST, gRPC, GraphQL) or within the same process (library interfaces). The design determines how requests are structured, how responses are formatted, and how errors are communicated.

#### 1.1.2 Public vs Internal APIs
- **Public APIs:** Exposed to external developers; require strong backward compatibility, versioning, rate limiting, comprehensive documentation, and security hardening.
- **Internal APIs:** Used within an organization; faster iteration cycles, fewer compatibility constraints, but still benefit from consistent design and clear contracts.

#### 1.1.3 Contract-Driven Development
The API specification serves as a contract between frontend and backend teams. Tools like OpenAPI/Swagger, Protocol Buffers (.proto files), and GraphQL schemas define the contract first. Development proceeds in parallel against the agreed contract, reducing integration surprises.

#### 1.1.4 Scalability & Maintainability Concerns
API design decisions directly impact system scalability and maintainability:
- Statelessness enables horizontal scaling.
- Proper pagination prevents database overload.
- Caching headers reduce load on backend services.
- Consistent naming and versioning simplify long‑term maintenance.

### 1.2 API Design Goals

#### 1.2.1 Simplicity
Keep the API surface minimal. Each endpoint should do one thing well. Avoid unnecessary complexity in request/response structures. Use sensible defaults.

#### 1.2.2 Consistency
Use the same naming conventions, error formats, authentication patterns, and response structures across all endpoints. Predictability reduces cognitive load for developers.

#### 1.2.3 Scalability
Design for growth from the start: stateless servers, caching support, pagination, async processing for long‑running operations, and rate limiting to protect backend resources.

#### 1.2.4 Backward Compatibility
Once an API is published, existing clients must not break. Add new fields as optional, avoid changing field semantics, and use versioning when breaking changes are unavoidable.

#### 1.2.5 Security
Authentication, authorization, input validation, TLS encryption, and rate limiting must be built into the API design — not bolted on later.

#### 1.2.6 Developer Experience
Clear documentation, consistent error messages, sandbox/test environments, SDKs, and predictable behavior make developers productive and reduce support burden.

---

## 2. RESTful Principles

### 2.1 What Is REST?

REST (Representational State Transfer) is an architectural style for designing networked applications, introduced by Roy Fielding. It has become the de facto standard for web APIs.

#### 2.1.1 Representational State Transfer
Resources are identified by URIs. Clients interact with resources by exchanging **representations** (JSON, XML, HTML) of their state. The server does not maintain client session state between requests.

#### 2.1.2 Stateless Communication
Each client request must contain all information necessary to process it. The server never stores context between requests. This enables horizontal scaling — any server instance can handle any request.

#### 2.1.3 Resource-Oriented Design
Everything is modeled as a **resource** — a noun, not a verb. Resources are identified by URIs, and HTTP methods describe the action to perform on them.

### 2.2 Core REST Constraints

#### 2.2.1 Client-Server Architecture
Separation of concerns: the client handles the user interface and user experience; the server manages data storage and business logic. They evolve independently.

#### 2.2.2 Statelessness
The server does not store client context between requests. Session state is held entirely by the client. Each request is self‑contained, making the system easier to scale horizontally.

#### 2.2.3 Cacheability
Responses must explicitly indicate whether they are cacheable. HTTP headers like `Cache‑Control`, `ETag`, and `Last‑Modified` enable clients and intermediaries to cache responses, reducing latency and server load.

#### 2.2.4 Uniform Interface
A consistent way to interact with all resources. Four sub‑constraints:
- **Resource identification:** URIs identify resources.
- **Resource manipulation through representations:** Clients send and receive representations (JSON) of resource state.
- **Self‑descriptive messages:** Each message contains all information needed to process it.
- **HATEOAS:** Hypermedia as the Engine of Application State — clients navigate via links in responses. (Often partially implemented.)

#### 2.2.5 Layered System
The architecture can include intermediate layers (load balancers, caches, proxies, API gateways) without the client or server knowing. Each layer only interacts with its immediate neighbor.

#### 2.2.6 Code on Demand
Optional: servers can extend client functionality by transferring executable code (e.g., JavaScript). Rarely used in modern API design; APIs focus on data, not code.

### 2.3 Resource-Oriented Design

#### 2.3.1 Resources & URIs
Resources are nouns. URIs identify resources:
```
/users          → collection of users
/users/123      → a specific user
/users/123/orders → orders belonging to user 123
/orders/456     → a specific order
```

#### 2.3.2 Nouns Instead of Verbs
Use HTTP methods to express actions, not URLs:
```
GOOD:  POST /users          (create user)
       GET  /users/123      (get user)
       DELETE /users/123    (delete user)

BAD:   POST /createUser
       GET  /getUser?id=123
       POST /deleteUser
```

#### 2.3.3 Nested Resources
Express relationships through URI nesting, but keep it shallow (1-2 levels max):
```
/users/123/orders       ← good (user's orders)
/users/123/orders/456   ← good (specific order of a user)

/users/123/orders/456/items/789  ← too deep, use /items/789 instead
```

#### 2.3.4 Resource Relationships
For many‑to‑many or complex relationships, use query parameters or separate endpoints:
```
GET /users/123/orders?status=shipped
GET /orders?user_id=123&sort=created_at
```

### 2.4 HTTP Methods

#### 2.4.1 GET
Retrieve a representation of a resource. Must be safe (no side effects) and idempotent.

#### 2.4.2 POST
Create a new resource or perform an operation. Not idempotent by default — multiple identical POST requests may create multiple resources.

#### 2.4.3 PUT
Replace an entire resource (or create if it doesn't exist — upsert). Idempotent: repeated requests produce the same result.

#### 2.4.4 PATCH
Partially update a resource. Not inherently idempotent, but can be made so with careful design (e.g., using JSON Patch with a specific set of operations).

#### 2.4.5 DELETE
Remove a resource. Idempotent: subsequent requests return the same result (resource gone).

#### 2.4.6 OPTIONS
Describes the communication options for the target resource. Used for CORS preflight requests and API discovery.

### 2.5 Idempotency in HTTP Methods

#### 2.5.1 Safe Methods
Safe methods do not modify resources: GET, HEAD, OPTIONS. They can be called any number of times without side effects.

#### 2.5.2 Idempotent Methods
These produce the same result regardless of how many times they are called: GET, HEAD, PUT, DELETE, OPTIONS, TRACE.

#### 2.5.3 Non-Idempotent Methods
POST and PATCH are not inherently idempotent. Multiple calls may produce different results (e.g., creating multiple resources or applying the same patch twice).

#### 2.5.4 Retry Implications
- Idempotent requests can be safely retried on network failure without special handling.
- Non‑idempotent requests require **idempotency keys** (see Section 4) or other deduplication mechanisms for safe retries.

### 2.6 HTTP Status Codes

#### 2.6.1 2xx Success Codes
- **200 OK:** Request succeeded.
- **201 Created:** Resource created (use with POST).
- **202 Accepted:** Request accepted for processing, but not yet completed (async).
- **204 No Content:** Request succeeded, no body in response (common for DELETE).

#### 2.6.2 3xx Redirection Codes
- **301 Moved Permanently:** URI permanently changed.
- **304 Not Modified:** Cached version is still valid (used with ETag/If‑None‑Match).

#### 2.6.3 4xx Client Errors
- **400 Bad Request:** Malformed request.
- **401 Unauthorized:** Authentication required.
- **403 Forbidden:** Authenticated but not permitted.
- **404 Not Found:** Resource does not exist.
- **409 Conflict:** Request conflicts with current state (e.g., duplicate).
- **422 Unprocessable Entity:** Valid syntax but semantic error.
- **429 Too Many Requests:** Rate limit exceeded.

#### 2.6.4 5xx Server Errors
- **500 Internal Server Error:** Generic server failure.
- **502 Bad Gateway:** Upstream server returned invalid response.
- **503 Service Unavailable:** Server temporarily unable to handle request (overload, maintenance).

### 2.7 Example RESTful API Design

#### 2.7.1 User APIs
```
GET    /users              → List users (with pagination, filtering)
POST   /users              → Create a new user
GET    /users/{id}         → Get user details
PUT    /users/{id}         → Replace entire user
PATCH  /users/{id}         → Update user (partial)
DELETE /users/{id}         → Delete user
```

#### 2.7.2 Order APIs
```
GET    /users/{id}/orders  → List orders for a user
POST   /orders             → Create a new order
GET    /orders/{id}        → Get order details
PATCH  /orders/{id}        → Update order status
DELETE /orders/{id}        → Cancel order
```

#### 2.7.3 CRUD Endpoint Patterns
| Method | Collection | Single Resource |
|--------|-----------|-----------------|
| GET | List items (paginated) | Get item by ID |
| POST | Create new item | — |
| PUT | Replace collection (rare) | Replace/upsert item |
| PATCH | — | Partial update item |
| DELETE | Delete all (rare) | Delete item |

---

## 3. Pagination

### 3.1 Why Pagination Matters

#### 3.1.1 Large Dataset Handling
APIs often need to return datasets that could contain millions of records. Returning everything in a single response is impossible — it would exhaust memory, bandwidth, and time.

#### 3.1.2 Performance Optimization
Pagination reduces the amount of data scanned by the database and transferred over the network, keeping response times fast and predictable.

#### 3.1.3 Bandwidth Reduction
Clients only download the data they need, when they need it. This is especially important for mobile clients on limited data plans.

#### 3.1.4 Better User Experience
Incremental loading (infinite scroll, "Load More" buttons) feels faster and more responsive than waiting for a massive response.

### 3.2 Offset-Based Pagination

#### 3.2.1 Limit & Offset
Client specifies how many items to skip (`offset`) and how many to return (`limit`).

Example:
```
GET /items?offset=20&limit=10
```

#### 3.2.2 Advantages
- Simple to understand and implement.
- Easy to calculate page numbers and total pages.
- Works with any sort order.

#### 3.2.3 Performance Problems
For large offsets, the database still scans all skipped rows. `OFFSET 1000000 LIMIT 10` scans and discards 1,000,000 rows.

#### 3.2.4 Duplicate / Missing Data Issues
If items are inserted or deleted between page fetches, items can shift between pages, causing duplicates or missed items.

### 3.3 Cursor-Based Pagination

#### 3.3.1 Cursor Tokens
The server returns an opaque cursor pointing to the next item. The client passes this cursor to get the next page.

Example:
```
GET /items?cursor=abc123&limit=10
Response: { "data": [...], "next_cursor": "xyz789" }
```

#### 3.3.2 Stable Pagination
Because the cursor points to a specific item (e.g., by ID or timestamp), the result set is stable even when new items are added. No duplicate or missing items.

#### 3.3.3 Infinite Scroll Use Cases
Ideal for social media feeds, activity logs, and any UI with "Load More" or infinite scroll where users rarely jump to arbitrary pages.

#### 3.3.4 Tradeoffs
- Cannot jump to an arbitrary page number.
- Cursor logic is more complex to implement on the server.
- Cursors must be encrypted or signed to prevent client manipulation.

### 3.4 Keyset Pagination (Seek Method)

#### 3.4.1 Indexed Lookups
Uses a unique, sortable column (e.g., `id`, `created_at`) to filter.

Example:
```
GET /items?after_id=100&limit=10
→ WHERE id > 100 ORDER BY id LIMIT 10
```

#### 3.4.2 High Performance
The database uses an index on the seek column, making queries extremely fast even for large offsets.

#### 3.4.3 Real-Time Feeds
Excellent for real‑time sorted feeds where data is always appended. The client remembers the last seen ID and requests new items.

### 3.5 Page-Based Pagination

#### 3.5.1 Traditional Page Numbers
Client requests a specific page number and page size.

Example:
```
GET /items?page=3&size=10
```

#### 3.5.2 UI Simplicity
Familiar to users and developers. Easy to display "Page 3 of 15" navigation.

#### 3.5.3 Tradeoffs
Underneath, it's typically implemented as offset‑based pagination, inheriting its performance problems and instability under data changes.

### 3.6 Pagination Best Practices

#### 3.6.1 Default Limits
Set sensible defaults (e.g., `limit=20`) when the client doesn't specify.

#### 3.6.2 Maximum Limits
Cap the maximum page size (e.g., `limit <= 100`) to prevent abuse and protect the database.

#### 3.6.3 Sorting Requirements
Cursor and keyset pagination require a stable, sortable column. Offset pagination can use any sort order but may be inconsistent.

#### 3.6.4 Stable Ordering
Always include a tiebreaker (e.g., `id`) in the sort order to guarantee deterministic ordering, especially when timestamps can have duplicates.

### 3.7 Pagination Examples

#### 3.7.1 Offset Pagination Example
```
Request:  GET /api/products?offset=0&limit=20
Response: {
  "items": [...20 products...],
  "total": 105,
  "offset": 0,
  "limit": 20,
  "next": "/api/products?offset=20&limit=20",
  "prev": null
}
```

#### 3.7.2 Cursor Pagination Example
```
Request:  GET /api/products?cursor=eyJpZCI6MTAwfQ==&limit=20
Response: {
  "items": [...20 products...],
  "next_cursor": "eyJpZCI6MTIwfQ=="
}
```
The cursor is a base64‑encoded value (e.g., `{"id":100}`). The server decodes it and queries `WHERE id > 100 ORDER BY id LIMIT 20`.

#### 3.7.3 Keyset Pagination Example
```
Request:  GET /api/tweets?after_id=987654&limit=20
Response: {
  "items": [...20 tweets...],
  "next_after_id": 987634
}
```

---

## 4. Idempotency Keys

### 4.1 Why Idempotency Matters

#### 4.1.1 Network Retries
Network failures, timeouts, and server restarts can cause clients to retry requests without knowing whether the original succeeded. Idempotency prevents side effects from retries.

#### 4.1.2 Duplicate Requests
Without idempotency, a retried POST could create duplicate resources, send multiple emails, or process a payment twice.

#### 4.1.3 Payment Safety
In payment systems, double‑charging is catastrophic. Idempotency keys are mandatory for any API that initiates a charge.

#### 4.1.4 Distributed System Reliability
Idempotency is a fundamental building block for at‑least‑once delivery semantics, enabling reliable distributed systems.

### 4.2 Idempotent HTTP Methods

#### 4.2.1 GET
Safe and idempotent. Retrieves resource without side effects.

#### 4.2.2 PUT
Idempotent. Replaces a resource at a known URI. Repeated PUTs with the same body produce the same resource state.

#### 4.2.3 DELETE
Idempotent. The resource is deleted after the first call; subsequent calls return the same result (404 or 204).

#### 4.2.4 POST Challenges
POST is not inherently idempotent. Creating resources via POST requires an idempotency key to safely retry.

### 4.3 How Idempotency Keys Work

#### 4.3.1 Client-Generated Keys
The client generates a unique key (UUID v4) for each logical operation before sending the request.

```
POST /v1/charges
Idempotency-Key: 123e4567-e89b-12d3-a456-426614174000
```

#### 4.3.2 Request Deduplication
The server checks whether the idempotency key has been seen before. If yes, it returns the stored response without re‑executing the operation.

#### 4.3.3 Response Reuse
The server stores the complete response (status code, headers, body) associated with the key. Subsequent requests with the same key get the identical response.

#### 4.3.4 Request Fingerprinting
If a request with an existing key has a different body, the server should reject it (422) because idempotency guarantees apply only to identical requests.

### 4.4 Idempotency Implementation

#### 4.4.1 Redis Storage
Redis is ideal for idempotency key storage — it's fast, supports TTL, and can store serialized responses.

```python
def create_order(request):
    key = request.headers.get('Idempotency-Key')
    if not key:
        return error("Idempotency-Key required", 400)

    cached = cache.get(key)
    if cached:
        return cached['response'], cached['status']

    order = create_order_in_db(request.body)
    response = {"order_id": order.id, "status": "created"}
    cache.set(key, {'response': response, 'status': 201}, ex=86400)
    return response, 201
```

#### 4.4.2 TTL Expiration
Keys should expire after a reasonable period (e.g., 24 hours) to prevent unbounded storage growth while covering realistic retry windows.

#### 4.4.3 Database Constraints
Use database unique constraints on business keys (e.g., `order_id`, `payment_id`). If a duplicate insert occurs, catch the error and return the existing resource.

#### 4.4.4 Response Caching
Store the full HTTP response, not just a flag. This ensures idempotent retries return exactly the same response body and headers.

### 4.5 Real-World Idempotency

#### 4.5.1 Stripe Payment APIs
Stripe requires an idempotency key for all POST requests that create or modify resources. The key is scoped to the API key, and results are cached for 24 hours.

```
POST /v1/charges
Idempotency-Key: abc123...
```

#### 4.5.2 Order Creation APIs
E‑commerce platforms use idempotency keys to prevent duplicate orders when users click "Place Order" multiple times or when mobile clients retry on poor connections.

#### 4.5.3 Booking Systems
Ticket and hotel bookings use idempotency keys to ensure that a single booking attempt doesn't reserve multiple seats or rooms.

### 4.6 Important Considerations

#### 4.6.1 Key Expiration
Idempotency keys must expire. Keys from months ago should not consume storage. Choose a TTL that balances retry safety and storage cost.

#### 4.6.2 Request Validation
Always validate the request body even when the idempotency key exists. If the new body differs from the original, return an error — don't silently return the old response for a different request.

#### 4.6.3 Replay Attack Prevention
Idempotency keys alone don't prevent replay attacks. Combine with authentication, short‑lived tokens, and request timestamps. Idempotency keys should be scoped to the authenticated user/API key.

#### 4.6.4 Scalability Tradeoffs
Storing idempotency responses adds storage and lookup latency. Use a fast cache (Redis) with TTL. For very high‑throughput systems, consider probabilistic deduplication (Bloom filters) as a first pass.

---

## 5. API Versioning

### 5.1 Why Version APIs?

#### 5.1.1 Backward Compatibility
Once clients depend on your API, you cannot break them. Versioning allows you to introduce breaking changes without disrupting existing consumers.

#### 5.1.2 Safe API Evolution
Add new endpoints, optional fields, and new response formats in a compatible way within the same version. Only increment the version for breaking changes.

#### 5.1.3 Client Compatibility
Different clients update at different paces. Versioning lets mobile apps, web apps, and third‑party integrations migrate on their own schedules.

#### 5.1.4 Incremental Upgrades
Teams can deploy new API versions alongside old ones, monitor adoption, and deprecate old versions gracefully.

### 5.2 URI Path Versioning

#### 5.2.1 /v1/users
The most explicit and common approach. The version is embedded in the URL path.

```
GET /api/v1/users/123
GET /api/v2/users/123
```

#### 5.2.2 Advantages
- Very visible and easy to understand.
- Simple to route in API gateways and load balancers.
- Easy to debug — the version is in the URL and logs.
- No custom headers needed.

#### 5.2.3 Drawbacks
- URI changes require client updates.
- Can lead to code duplication on the server.
- "v1" becomes part of the resource identity (purely for routing).

### 5.3 Query Parameter Versioning

#### 5.3.1 ?version=v1
The version is passed as a query parameter.

```
GET /users/123?version=1
GET /users/123?version=2
```

#### 5.3.2 Advantages
- URL path remains stable.
- Simple to implement on server side.

#### 5.3.3 Drawbacks
- Easy for clients to forget.
- Caching systems may not include the query parameter in the cache key by default.
- Less explicit — harder to discover.

### 5.4 Header-Based Versioning

#### 5.4.1 Custom Headers
A custom HTTP header specifies the version.

```
GET /users/123
Accept-Version: 1
```

#### 5.4.2 Advantages
- URLs remain clean and stable.
- Good separation of concerns — versioning is a protocol concern, not a resource concern.

#### 5.4.3 Drawbacks
- Requires clients to set headers — easy to forget.
- Not visible in URLs, making debugging harder.
- Harder to route in API gateways without header inspection.

### 5.5 Content Negotiation

#### 5.5.1 Accept Headers
Uses standard HTTP `Accept` header with custom media types.

```
GET /users/123
Accept: application/vnd.myapi.v1+json
```

#### 5.5.2 Media Types
Each version has a distinct media type. The server selects the response format based on what the client accepts.

#### 5.5.3 Advantages
- Leverages HTTP semantics correctly.
- Version is tied to the content representation.
- URLs never change.

#### 5.5.4 Drawbacks
- Complex to implement and test.
- Developers often find it unintuitive.
- Difficult to route at the infrastructure level.

### 5.6 API Deprecation

#### 5.6.1 Deprecation Headers
Communicate deprecation via response headers:
```
Deprecation: true
Sunset: Sat, 31 Dec 2025 23:59:59 GMT
```

#### 5.6.2 Sunset Policies
Define a clear policy for how long old versions are supported (e.g., 6 months after a new version is released). Communicate this to developers.

#### 5.6.3 Migration Guides
Provide detailed documentation explaining what changed, why, and how to migrate. Include code examples in popular languages.

#### 5.6.4 Gradual Rollouts
Gradually ramp down traffic to the old version using feature flags or traffic splitting. Monitor for remaining clients before shutting off.

### 5.7 API Versioning Best Practices

#### 5.7.1 Minimize Breaking Changes
Add new optional fields rather than removing or renaming existing ones. Introduce new endpoints rather than changing existing ones. Only bump the version when absolutely necessary.

#### 5.7.2 Semantic Versioning
Consider semver for internal APIs: MAJOR for breaking changes, MINOR for backward‑compatible additions, PATCH for fixes. For public APIs, a single version number (v1, v2) is simpler.

#### 5.7.3 Compatibility Windows
Support at least two versions simultaneously. Give clients clear deadlines for migration. Monitor usage of old versions before sunsetting.

---

## 6. API Security

### 6.1 Authentication

#### 6.1.1 API Keys
Simple, static tokens sent in headers or query parameters. Good for server‑to‑server communication and simple use cases. Must be transmitted over HTTPS and rotated regularly. Weak for user‑facing authentication.

#### 6.1.2 OAuth 2.0
An authorization framework that allows third‑party applications to access resources on behalf of a user without sharing credentials. Uses short‑lived access tokens and optional refresh tokens. The industry standard for public APIs.

**Flow:** Authorization Code Grant, Client Credentials Grant, Implicit (deprecated), PKCE for mobile/SPA.

#### 6.1.3 JWT Authentication
JSON Web Tokens are self‑contained, signed tokens carrying claims about the user. Stateless — the server verifies the signature without a database lookup. Payload is base64‑encoded, not encrypted; never store secrets in JWT claims.

#### 6.1.4 Session-Based Authentication
Server creates a session after login, stores a session ID in a cookie. Subsequent requests include the cookie. Requires server‑side session storage. Simpler for server‑rendered applications.

### 6.2 Authorization

#### 6.2.1 RBAC
Role‑Based Access Control: assign roles to users, permissions to roles. Example: `admin` can create/delete users; `viewer` can only read.

#### 6.2.2 ABAC
Attribute‑Based Access Control: policies evaluated based on user attributes, resource attributes, and environment. More flexible than RBAC. Example: "Allow if `user.department == resource.owner` and `time.now() between 9‑5`."

#### 6.2.3 Resource-Level Permissions
Grant permissions on individual resources, not just API endpoints. A user might be able to read their own orders but not another user's orders. Enforce in the application layer.

### 6.3 Transport Security

#### 6.3.1 HTTPS Everywhere
All API traffic must use TLS 1.2 or higher. HTTP should be rejected or redirected to HTTPS. This prevents eavesdropping and man‑in‑the‑middle attacks.

#### 6.3.2 TLS Enforcement
Use HTTP Strict Transport Security (HSTS) headers to instruct browsers to always use HTTPS. Reject requests with invalid or expired certificates.

#### 6.3.3 Certificate Validation
Validate certificates on both ends for mTLS. Use a trusted Certificate Authority. Automate certificate renewal (Let's Encrypt, AWS ACM).

### 6.4 API Abuse Prevention

#### 6.4.1 Rate Limiting
Limit the number of requests per client per time window. Use algorithms like token bucket or sliding window. Return HTTP 429 with `Retry-After` header when limit is exceeded.

#### 6.4.2 Throttling
Gradually slow down a client that exceeds limits rather than abruptly blocking. Allows legitimate burst traffic while preventing sustained abuse.

#### 6.4.3 WAF Protection
A Web Application Firewall inspects HTTP traffic and blocks common attacks: SQL injection, XSS, path traversal, and bot traffic. Cloud‑based WAFs (AWS WAF, Cloudflare) are easy to deploy.

#### 6.4.4 Input Validation
Validate all input on the server: type, length, format, range, and allowed characters. Use allowlists, not blocklists. Reject invalid input with clear error messages.

---

## 7. API Performance & Scalability

### 7.1 Caching

#### 7.1.1 HTTP Caching
Leverage HTTP caching semantics to reduce server load and response latency. Use `Cache‑Control` headers to control cache behavior:
- `Cache-Control: public, max-age=3600` → cacheable by intermediaries for 1 hour.
- `Cache-Control: private, max-age=60` → cacheable by browser only for 1 minute.
- `Cache-Control: no-store` → never cache.

#### 7.1.2 ETags
Entity Tags allow conditional requests. The server returns an ETag; the client sends `If‑None‑Match` on subsequent requests. If the resource hasn't changed, the server returns 304 Not Modified with no body.

```
Response: ETag: "abc123"
Request:  If-None-Match: "abc123"
Response: 304 Not Modified
```

#### 7.1.3 Cache-Control Headers
Key directives:
- `max-age`: Maximum time (seconds) the response is fresh.
- `s-maxage`: Overrides `max-age` for shared caches (CDNs).
- `stale-while-revalidate`: Serve stale content while asynchronously refreshing.
- `stale-if-error`: Serve stale content if the origin is unreachable.

#### 7.1.4 CDN Integration
Place a CDN (CloudFront, Cloudflare, Fastly) in front of your API to cache responses at the edge. Especially effective for read‑heavy, cacheable endpoints with limited personalization.

### 7.2 Compression

#### 7.2.1 Gzip
Reduce response body size by 60‑80% for text‑based content (JSON, HTML, CSS). Widely supported, low CPU overhead. Enable on the server or at the API gateway.

#### 7.2.2 Brotli
Provides better compression ratios than gzip (15‑20% smaller) at the cost of higher CPU. Best for static assets that can be compressed once. Supported by all modern browsers and CDNs.

#### 7.2.3 Payload Reduction
Beyond compression: return only necessary fields, use pagination, and consider sparse fieldsets (GraphQL) or field selection query parameters (`?fields=id,name,email`).

### 7.3 Asynchronous APIs

#### 7.3.1 Background Processing
For long‑running operations, accept the request immediately and process asynchronously:
1. Client sends request → server returns `202 Accepted` with a status URL.
2. Client polls `GET /jobs/{id}` until completion.
3. Alternatively, the server sends a webhook when done.

#### 7.3.2 Webhooks
Instead of polling, the client registers a callback URL. When processing completes, the server sends an HTTP POST to the callback URL. This reduces network overhead and latency for the client.

#### 7.3.3 Polling vs Push Notifications
- **Polling:** Simple, works everywhere, but wastes bandwidth and adds latency.
- **Webhooks:** Efficient, real‑time, but requires the client to have a public endpoint.
- **WebSockets/SSE:** For truly real‑time updates, use persistent connections.

### 7.4 Rate Limiting & Quotas

#### 7.4.1 Token Bucket
Tokens are added at a constant rate up to a maximum bucket size. Each request consumes a token. If bucket is empty → 429 Too Many Requests. Allows bursts up to bucket capacity.

#### 7.4.2 Sliding Window
A more accurate alternative to fixed window counters. Tracks requests in a rolling time window using Redis sorted sets or approximate counters.

#### 7.4.3 User-Level Limits
Apply limits per user, API key, or OAuth token. Return rate limit status in response headers:
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 973
X-RateLimit-Reset: 1614556800
```

#### 7.4.4 Tenant-Level Limits
In multi‑tenant systems, apply aggregate limits per tenant to prevent one tenant from consuming all resources. Useful for SaaS platforms.

---

## 8. API Design Patterns

### 8.1 REST vs GraphQL

#### 8.1.1 REST Advantages
- Simple, well‑understood, leverages HTTP fully.
- Built‑in caching via HTTP semantics.
- Stateless, scalable.
- Rich ecosystem of tools and libraries.
- Easy to test (curl, Postman).

#### 8.1.2 GraphQL Advantages
- Clients request exactly the data they need (no over‑fetching or under‑fetching).
- Single endpoint for all operations.
- Strongly typed schema.
- Built‑in introspection for documentation.
- Nested resources in a single query.

#### 8.1.3 Tradeoffs
- GraphQL shifts complexity from the client to the server; queries can be expensive.
- REST has better HTTP caching; GraphQL typically uses POST, making caching harder.
- GraphQL rate limiting is more complex (query cost analysis).
- REST is simpler for basic CRUD; GraphQL excels for complex, nested data views.

#### 8.1.4 Use Cases
- **REST:** Public APIs, simple CRUD, microservices, mobile backends.
- **GraphQL:** Rich frontend applications, mobile apps with varying data needs, aggregating multiple data sources.

### 8.2 REST vs gRPC

#### 8.2.1 Human Readability
REST uses JSON, which is human‑readable and easy to debug. gRPC uses Protocol Buffers (binary), which is not human‑readable without tools.

#### 8.2.2 Binary Protocols
gRPC's Protocol Buffers are more compact and faster to serialize/deserialize than JSON. Better for high‑throughput, low‑latency communication.

#### 8.2.3 Streaming Support
gRPC natively supports bidirectional streaming, server‑side streaming, and client‑side streaming. REST typically relies on polling, WebSockets, or SSE for streaming.

#### 8.2.4 Internal Microservices
gRPC is excellent for internal service‑to‑service communication within a data center. Strong typing, code generation, and high performance. REST is better for public APIs and browser‑facing endpoints.

### 8.3 Webhooks

#### 8.3.1 Event Notifications
Instead of polling, the server pushes events to registered callback URLs when state changes. Example: Stripe sends a webhook when a payment succeeds.

#### 8.3.2 Retry Strategies
If the webhook delivery fails, retry with exponential backoff. After N failed attempts, disable the webhook and notify the user. Implement idempotency on the receiver side.

#### 8.3.3 Signature Verification
Sign webhook payloads with a shared secret (HMAC). The receiver verifies the signature to ensure the request genuinely came from the sender. Stripe's `Stripe-Signature` header is an example.

### 8.4 API Gateway Pattern

#### 8.4.1 Centralized Routing
An API Gateway is a single entry point for all API traffic. It routes requests to the appropriate backend services. Examples: AWS API Gateway, Kong, Apigee, Envoy.

#### 8.4.2 Authentication
The gateway can centralize authentication, validating tokens (JWT, OAuth) before forwarding requests to backend services. This removes authentication logic from individual services.

#### 8.4.3 Rate Limiting
Apply rate limiting at the gateway level, protecting all backend services uniformly. Can be combined with per‑service or per‑user limits.

#### 8.4.4 Request Aggregation
The gateway can combine multiple backend calls into a single client response, reducing the number of round trips and simplifying client logic.

---

## 9. API Design by System Type

### 9.1 Summary: API Design Techniques by Pattern

| Pattern | System Type | Primary API Design Techniques | Typical Bottleneck & Solution |
|---------|-------------|-----------------------------|-------------------------------|
| **A: CRUD & Storage Systems** | URL shortener, Pastebin, Bookstore, Ticket booking, Dropbox | REST, resource-oriented URIs, offset/page pagination for admin, idempotency keys for writes | CRUD-heavy → standard REST is simplest and most widely understood |
| **B: Feed / Timeline Systems** | Twitter, Instagram, Facebook, LinkedIn | Cursor-based pagination, sparse fieldsets, REST + WebSocket for updates | Infinite-scroll feeds → cursor pagination avoids offset drift as new items arrive |
| **C: Real-Time Systems** | WhatsApp, Slack, Live Comments, Game backend | WebSocket/gRPC streaming, token-authenticated connection upgrade | Bidirectional low-latency need → REST alone can't deliver push updates |
| **D: Search Systems** | Google Search, YouTube Search, Amazon Search, Autocomplete | Dedicated search endpoint with facets, separate lightweight autocomplete endpoint | Search vs autocomplete have very different latency budgets → separate endpoints, separate caching |
| **E: Geospatial & Location-Based Systems** | Uber, Google Maps, Food Delivery, Nearby Friends | REST for booking, streaming/WebSocket for live location, geo query parameters | Mixed request types → REST for transactional actions, streaming for continuous updates |
| **F: Financial & Critical Consistency Systems** | Payment gateway, Wallet, UPI, Job scheduler | Mandatory idempotency keys, strong validation, audit trail endpoints | State-changing financial calls → idempotency key is non-negotiable to prevent duplicate charges |
| **G: Large-Scale Processing & Infrastructure** | Analytics, Clickstream, Logging, Metrics, Kafka-like | Batch/bulk ingestion APIs, internal gRPC contracts with schema versioning | High-volume internal ingestion → gRPC + batching beats per-record REST calls |
| **H: Collaborative & Infrastructure Utilities** | Google Docs, Figma, Distributed locks, Snowflake, Web Crawler | WebSocket for op streaming, gRPC for internal coordination service calls | Live multi-client sync → stream ops over WebSocket rather than polling REST |

---

### 9.2 Pattern A – CRUD & Storage Systems

| Question | API Design Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|--------------------|-----------------------------|
| **URL Shortener** | Minimal REST API: POST /urls to create, GET /{code} to redirect | The entire feature surface is two operations, so a small, resource-oriented REST API is the simplest fit. | POST /urls returns the short code; GET /{code} issues a 301 redirect; no pagination needed given the API's simplicity. |
| **Pastebin** | REST with resource URIs (POST /pastes, GET /pastes/{id}), Idempotency-Key optional | Paste creation and retrieval map naturally onto standard REST resource semantics. | GET /pastes/{id} returns paste content and metadata; DELETE supports early removal; optional TTL field in the create payload. |
| **Online Bookstore** | REST with resource URIs, offset/page pagination for catalog browsing, filtering/sorting query params | Catalog browsing is a classic paginated-list use case well served by standard REST conventions. | GET /books?category=fiction&sort=price&page=2 supports filtering and pagination; POST /orders handles checkout with idempotency keys. |
| **Ticket Booking** | REST with mandatory Idempotency-Key header on booking endpoints | A booking retry due to a network blip must not create a duplicate reservation, making idempotency essential here. | POST /bookings requires an Idempotency-Key; duplicate requests with the same key return the original booking's response. |
| **Dropbox / Google Drive** | REST for metadata operations, separate resumable upload API for file content | File content transfer has very different requirements (large payloads, resumability) than simple metadata CRUD. | PATCH /files/{id} updates metadata; a separate chunked/resumable upload endpoint handles the actual file bytes. |

---

### 9.3 Pattern B – Feed / Timeline Systems

| Question | API Design Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|--------------------|-----------------------------|
| **Twitter / X** | Cursor-based pagination for timelines, sparse fieldsets, REST + WebSocket/push for live updates | Offset pagination breaks when new tweets are inserted above the current page; a cursor anchored to a tweet ID doesn't. | GET /timeline?cursor=<tweet_id>&limit=20 returns items after that cursor; a WebSocket pushes newly arrived tweets separately. |
| **Instagram** | Cursor pagination for feed/comments, sparse fields for feed responses | Feed responses should return only what's rendered (thumbnail, caption preview) to keep payloads small at high request volume. | GET /feed?cursor=...&fields=id,image_url,caption returns a minimal payload; full post detail fetched on demand. |
| **Facebook News Feed** | GraphQL for flexible per-client field selection, cursor pagination underneath | Different client surfaces (mobile, web, ads) need different subsets of feed data, which GraphQL handles more gracefully than fixed REST responses. | A GraphQL query specifies exactly the fields/edges needed; the resolver internally uses cursor-based pagination over the underlying store. |
| **LinkedIn Feed** | REST with cursor pagination, sparse fieldsets for feed list views | A pull-based feed still benefits from cursor pagination to keep 'load more' behavior stable as new posts arrive. | GET /feed?cursor=...&fields=id,author,summary keeps responses lean; full post fetched via a separate detail endpoint. |

---

### 9.4 Pattern C – Real-Time Systems

| Question | API Design Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|--------------------|-----------------------------|
| **WhatsApp / Messenger** | Persistent WebSocket API authenticated via a token passed during the upgrade handshake | Chat requires the server to push messages the instant they arrive, which only a persistent connection supports. | Client sends an auth token during the WebSocket upgrade; the connection then carries bidirectional message frames. |
| **Slack / Discord** | WebSocket event API for live events, REST for historical/administrative operations | Splitting real-time events from CRUD-style operations keeps each API surface simple and purpose-built. | WebSocket streams typing/message/presence events; REST endpoints handle channel creation, history fetch, and settings. |
| **Live Comments (YouTube Live)** | Server-Sent Events (SSE) or WebSocket for the comment stream, REST for posting a comment | Posting is a simple one-shot action well suited to REST; receiving the live stream needs a persistent push channel. | POST /comments submits a new comment; a separate SSE/WebSocket endpoint streams the live aggregated comment feed. |
| **Multiplayer Game Backend** | Low-level binary protocol over UDP/WebSocket for game state, REST for matchmaking/lobby | In-game state updates need minimal-overhead binary framing; lobby/matchmaking is infrequent and fits REST fine. | Matchmaking uses REST (POST /matches); once matched, clients switch to a binary UDP/WebSocket protocol for gameplay. |

---

### 9.5 Pattern D – Search Systems

| Question | API Design Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|--------------------|-----------------------------|
| **Google Search** | Dedicated GET /search?q=... endpoint returning ranked results with rich metadata | Search has unique needs (facets, ranking signals, pagination) that deserve a purpose-built endpoint rather than generic CRUD. | GET /search?q=keyword&page=1 returns results plus facets/suggestions; response schema is stable but internally backed by a complex pipeline. |
| **YouTube Search** | Search endpoint similar to Google Search, plus separate endpoints for video metadata and playback | Search results, metadata, and playback have different caching and latency profiles that benefit from separate endpoints. | GET /search returns video IDs and snippets; a separate GET /videos/{id} fetches full metadata and playback URLs. |
| **Amazon Product Search** | Search endpoint with facet/filter parameters, separate low-latency inventory-check endpoint | Search relevance and live inventory have very different freshness requirements, warranting separate API paths. | GET /search?q=...&category=... returns product listings; GET /inventory/{id} is a lightweight, frequently-refreshed check. |
| **Search Autocomplete** | A separate, minimal GET /search/suggest?q=pref endpoint distinct from full search | Autocomplete has an aggressive sub-50ms latency budget that a general-purpose search endpoint isn't optimized for. | GET /suggest?q=pre returns a small ranked list of completions; response is aggressively cached and kept payload-minimal. |

---

### 9.6 Pattern E – Geospatial & Location-Based Systems

| Question | API Design Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|--------------------|-----------------------------|
| **Uber / Lyft** | REST for ride request/booking, streaming (WebSocket/gRPC) for live driver location | Requesting a ride is a discrete transactional action; tracking it afterward needs continuous updates. | POST /rides creates a ride request; once matched, a WebSocket/gRPC stream pushes driver location updates to the rider's app. |
| **Google Maps / Nearby Places** | REST for place search/directions, tile requests as simple GETs with cache-friendly URLs | Both operations are naturally request/response and benefit from standard HTTP caching semantics. | GET /places?lat=..&lng=..&radius=.. and GET /tile/{z}/{x}/{y} are both cacheable, idempotent REST-style GETs. |
| **Food Delivery (DoorDash)** | REST for order placement/menu browsing, streaming for live driver tracking | Same rationale as Uber — transactional actions fit REST, continuous tracking needs a push channel. | POST /orders places an order; a WebSocket stream tracks driver location and order status changes in real time. |
| **Nearby Friends** | REST polling or a lightweight WebSocket for periodic location updates | The lower urgency of this feature means a simpler API (even polling) is an acceptable and simpler tradeoff. | GET /friends/nearby?radius=.. can be polled periodically, or a WebSocket can push updates on a relaxed interval. |

---

### 9.7 Pattern F – Financial & Critical Consistency Systems

| Question | API Design Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|--------------------|-----------------------------|
| **Payment Gateway (Stripe)** | Mandatory Idempotency-Key header, strict field validation, webhook API for async events | Duplicate charge attempts from network retries are unacceptable, and merchants need reliable async notification of later state changes. | POST /charges requires Idempotency-Key; duplicate requests return the original response; webhooks notify of refunds/disputes later. |
| **Wallet System** | Idempotency keys on all balance-changing endpoints, strong request validation | Any retried debit/credit call must be guaranteed not to double-apply. | POST /transactions requires an Idempotency-Key; the server returns the original result for a repeated key rather than reprocessing. |
| **UPI-like System** | Idempotency keys per transaction leg, mandatory audit-trail retrieval endpoints | Multi-party financial protocols need both duplicate-prevention and a queryable record of exactly what happened. | Each transaction carries a unique reference ID used as the idempotency key across all participating banks' APIs. |
| **Distributed Job Scheduler** | REST/gRPC API for job CRUD with strong validation on schedule expressions | Malformed schedules (e.g., invalid cron expressions) should be rejected at the API boundary, not discovered at run time. | POST /jobs validates the cron expression and payload schema before accepting the job; GET /jobs/{id}/runs exposes execution history. |

---

### 9.8 Pattern G – Large-Scale Processing & Infrastructure

| Question | API Design Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|--------------------|-----------------------------|
| **Google Analytics** | Lightweight ingestion endpoint (beacon/pixel) for clients, batch query API for reporting | Client-side event submission must be as cheap as possible; reporting queries can afford a richer, slower API. | POST /collect accepts a minimal event payload; GET /reports?metrics=..&dimensions=.. serves aggregated query results. |
| **Clickstream Processing** | Batch/bulk ingestion API for producers, internal gRPC contracts between pipeline stages | Sending events one-by-one over HTTP would be far less efficient than batching at ingestion-scale volumes. | Producers batch events client-side before a single POST /events/batch call; internal stages communicate via gRPC with protobuf schemas. |
| **Distributed Logging (ELK)** | Bulk ingestion API (Elasticsearch's _bulk endpoint pattern), REST query API for search | Indexing logs one at a time would be prohibitively slow; bulk APIs amortize the overhead across many documents. | Log shippers POST batched documents to a bulk endpoint; Kibana/clients query via Elasticsearch's REST search API. |
| **Metrics Monitoring (Prometheus)** | Pull-based /metrics endpoint (not a typical request/response API), PromQL query API for reads | Prometheus deliberately inverts the usual API model: the server pulls rather than clients pushing. | Each service exposes GET /metrics in a simple text format; Prometheus's query API (PromQL over HTTP) serves dashboard/alert queries. |
| **Design Kafka-like System** | Binary produce/fetch API rather than a REST/JSON contract | A JSON/REST API would add serialization overhead incompatible with the system's throughput goals. | Custom binary protocol defines Produce and Fetch request/response frames; an optional REST proxy can wrap it for compatibility. |

---

### 9.9 Pattern H – Collaborative & Infrastructure Utilities

| Question | API Design Techniques | Why (When to use) | Deep Dive / Implementation |
|----------|---------------------|--------------------|-----------------------------|
| **Google Docs** | WebSocket API streaming operational-transform ops, REST for document metadata/sharing | Live editing needs a continuous op stream; document-level actions (rename, share) are naturally REST-shaped. | WebSocket carries a stream of {op, position, content} messages; REST handles PATCH /documents/{id} for title/sharing changes. |
| **Figma / Collaborative Whiteboard** | Same WebSocket-op-stream pattern as Google Docs, tuned for higher message frequency | Canvas interactions generate more frequent, smaller messages than typical text editing. | WebSocket carries frequent small delta messages (move, resize); REST manages file-level metadata and permissions. |
| **Distributed Locking Service** | Simple synchronous gRPC/REST API: acquire, release, renew; streaming watch API for lock-release notification | Lock semantics need an immediate synchronous response, plus an efficient way to be notified when a contended lock frees up. | gRPC methods Lock()/Unlock()/Renew() return immediately; a Watch() streaming RPC notifies waiters without polling. |
| **Unique ID Generator (Snowflake)** | Minimal internal API: a single GenerateID() call, with a separate one-time WorkerAssignment() call at startup | The steady-state API surface should be as small and fast as possible since it's called on virtually every write path elsewhere. | GenerateID() is a local library call with no network round trip; WorkerAssignment() talks to etcd once, at process startup. |
| **Web Crawler** | Internal REST/gRPC API for frontier management (enqueue/dequeue URLs), no external-facing API | The crawler's 'clients' are its own worker processes, so the API is optimized for internal throughput, not external usability. | Workers call an internal EnqueueURLs()/DequeueURLs() API backed by the distributed queue; no public API is exposed for this system. |

---

---

## 10. Common API Anti-Patterns

### 10.1 Verb-Based URLs
Using verbs instead of HTTP methods + nouns:
```
BAD:  POST /createUser
      GET  /getUser
      POST /deleteUser
GOOD: POST /users
      GET  /users/{id}
      DELETE /users/{id}
```

### 10.2 Breaking API Changes
Changing field names, removing fields, or changing response formats without versioning. Break existing clients silently. Use versioning and deprecation windows.

### 10.3 Missing Pagination
Returning all results in a single response for large datasets. Causes timeouts, memory exhaustion, and poor user experience. Always paginate collection endpoints.

### 10.4 Returning Excessive Data
Returning entire objects when the client only needs a few fields. Wastes bandwidth and slows responses. Support sparse fieldsets or use GraphQL.

### 10.5 Inconsistent Naming
Mixing `snake_case`, `camelCase`, and `PascalCase` across endpoints. Confuses developers. Pick one convention and enforce it consistently.

### 10.6 No Versioning Strategy
Making breaking changes without versioning, forcing all clients to update simultaneously. Plan versioning from day one.

### 10.7 Ignoring Idempotency
Allowing duplicate POST requests to create duplicate resources. Critical for payment, order, and booking APIs. Use idempotency keys.

### 10.8 Overfetching & Underfetching
- **Overfetching:** Returning more data than the client needs (REST endpoints returning full objects).
- **Underfetching:** Requiring multiple round trips to assemble a view (classic REST N+1 problem).
- Solution: GraphQL, or REST endpoints designed around specific use cases (BFF pattern).

### 10.9 Weak Error Handling
Returning HTTP 200 with an error message in the body, or returning stack traces to clients. Use proper HTTP status codes, structured error responses, and hide internal details.

### 10.10 Missing Rate Limits
Exposing APIs without throttling, enabling abuse, DDoS, and resource exhaustion. Always implement rate limiting.

---

## 11. Interview-Oriented HLD Notes

### 11.1 Key Tradeoffs

#### 11.1.1 REST vs GraphQL
REST: simplicity, HTTP caching, mature tooling. GraphQL: flexible queries, reduced over‑fetching, single endpoint. Choose REST for public, cacheable APIs; GraphQL for complex frontend data needs.

#### 11.1.2 Offset vs Cursor Pagination
Offset: simple, supports page numbers, unstable under data changes. Cursor: stable, performant, requires a sortable field. Choose offset for admin UIs; cursor for consumer feeds.

#### 11.1.3 Sync vs Async APIs
Sync: simple, immediate response, blocks on long operations. Async: responsive, handles long‑running work, requires polling or webhooks. Use async for operations taking >1 second.

#### 11.1.4 Simplicity vs Flexibility
Simple APIs are easier to learn, maintain, and scale. Flexible APIs (GraphQL, rich query parameters) serve diverse client needs but add complexity. Start simple; add flexibility as validated by real usage.

### 11.2 Common Interview Questions

#### 11.2.1 How Would You Design REST APIs?
Answer with: resource‑oriented URIs, proper HTTP methods, consistent status codes, statelessness, pagination, versioning, authentication, and clear error handling.

#### 11.2.2 Offset vs Cursor Pagination?
Offset: `GET /items?offset=20&limit=10`. Simple but unstable under inserts/deletes, slow for large offsets. Cursor: `GET /items?cursor=abc&limit=10`. Stable, performant, but can't jump to arbitrary pages.

#### 11.2.3 Why Use Idempotency Keys?
Network retries can cause duplicate operations (double charges, duplicate orders). Idempotency keys let the server deduplicate requests and safely return the cached response for retries.

#### 11.2.4 How to Version APIs?
Options: URI path (`/v1/users`), query parameter (`?version=1`), custom header (`Accept-Version: 1`), content negotiation (`Accept: application/vnd.api.v1+json`). URI path is most common and explicit.

#### 11.2.5 REST vs gRPC?
REST: JSON, human‑readable, browser‑friendly, simple. gRPC: Protocol Buffers, binary, high performance, streaming. Use REST for public APIs; gRPC for internal service‑to‑service communication.

### 11.3 Quick Revision Material

#### 11.3.1 HTTP Methods Cheat Sheet

| Method | Purpose | Idempotent? | Safe? |
|--------|---------|-------------|-------|
| GET | Retrieve resource | Yes | Yes |
| POST | Create resource | No | No |
| PUT | Replace resource | Yes | No |
| PATCH | Partial update | No (can be) | No |
| DELETE | Remove resource | Yes | No |
| HEAD | Get headers only | Yes | Yes |
| OPTIONS | Get supported methods | Yes | Yes |

#### 11.3.2 Pagination Comparison Table

| Strategy | How It Works | Pros | Cons |
|----------|--------------|------|------|
| Offset | `offset=20&limit=10` | Simple, page numbers | Unstable, slow for large offsets |
| Cursor | `cursor=abc123` | Stable, performant | No page jumping, complex |
| Keyset | `after_id=100&limit=10` | Fast (indexed) | Tied to sort column |
| Page | `page=3&size=10` | Familiar UI | Offset underneath, unstable |

#### 11.3.3 API Versioning Comparison

| Strategy | Example | Pros | Cons |
|----------|---------|------|------|
| URI Path | `/v1/users` | Explicit, easy to route | URI changes, code duplication |
| Query Param | `?version=1` | URI stable, simple | Easy to forget, cache issues |
| Custom Header | `Accept-Version: 1` | Clean URLs | Hidden, requires header |
| Content Negotiation | `Accept: vnd.api.v1+json` | HTTP semantic | Complex, less intuitive |

#### 11.3.4 Key Interview Sentences

> *"I'd design a RESTful API with resource‑oriented URIs, using nouns for resources and HTTP methods for actions. Every collection endpoint supports cursor‑based pagination with a maximum page size. All state‑changing operations use idempotency keys for safe retries. I'd version the API via the URI path — `/api/v1/` — and maintain backward compatibility within a version."*

> *"For the payment API, every POST request requires an idempotency key. The server stores the response in Redis with a 24‑hour TTL. On retry, the original response is returned without re‑executing the charge — preventing double payments."*

> *"Cursor‑based pagination is the right choice for the timeline feed. It's stable under data insertion and performs well on large datasets because it uses an indexed `WHERE id > cursor` query instead of a slow offset scan."*

#### 11.3.5 Self-Test Questions
1. What are the six constraints of REST? Give an example of each.
2. When would you use cursor‑based pagination instead of offset‑based? What are the tradeoffs?
3. Why is POST not idempotent, and how do idempotency keys solve this?
4. Compare URI path versioning and content negotiation for API versioning. When would you choose each?
5. Design the API endpoints for a social media feed (create post, list feed, like, comment). Include methods, URIs, status codes, and pagination strategy.
6. What is the difference between authentication and authorization in an API context? Give examples of each.
7. How would you implement rate limiting on a public API? What response headers would you include?

*End of consolidated notes.*
