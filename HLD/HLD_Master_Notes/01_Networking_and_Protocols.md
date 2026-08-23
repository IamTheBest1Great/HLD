# Networking & Protocols — Consolidated HLD Notes

## Table of Contents

- [1. Networking Fundamentals](#1-networking-fundamentals)
  - [1.1 Why Networking Matters in HLD](#11-why-networking-matters-in-hld)
    - [1.1.1 Client-Server Communication](#111-client-server-communication)
    - [1.1.2 Latency & Throughput](#112-latency--throughput)
    - [1.1.3 Reliability](#113-reliability)
    - [1.1.4 Scalability](#114-scalability)
    - [1.1.5 Security](#115-security)
  - [1.2 The Journey of a User Request](#12-the-journey-of-a-user-request)
    - [1.2.1 DNS Resolution](#121-dns-resolution)
    - [1.2.2 TCP/TLS Connection](#122-tcptls-connection)
    - [1.2.3 Load Balancer Routing](#123-load-balancer-routing)
    - [1.2.4 Reverse Proxy Processing](#124-reverse-proxy-processing)
    - [1.2.5 Application Server Handling](#125-application-server-handling)
    - [1.2.6 Database / Cache Access](#126-database--cache-access)
    - [1.2.7 Response Flow](#127-response-flow)
- [2. OSI Model & Networking Layers](#2-osi-model--networking-layers)
  - [2.1 Overview of the OSI Model](#21-overview-of-the-osi-model)
    - [2.1.1 Physical Layer](#211-physical-layer)
    - [2.1.2 Data Link Layer](#212-data-link-layer)
    - [2.1.3 Network Layer](#213-network-layer)
    - [2.1.4 Transport Layer](#214-transport-layer)
    - [2.1.5 Session Layer](#215-session-layer)
    - [2.1.6 Presentation Layer](#216-presentation-layer)
    - [2.1.7 Application Layer](#217-application-layer)
  - [2.2 OSI Layers in High-Level Design](#22-osi-layers-in-high-level-design)
    - [2.2.1 Layer 3 Routing Decisions](#221-layer-3-routing-decisions)
    - [2.2.2 Layer 4 Transport Choices](#222-layer-4-transport-choices)
    - [2.2.3 Layer 7 Application Protocols](#223-layer-7-application-protocols)
    - [2.2.4 Security Across Layers](#224-security-across-layers)
  - [2.3 Practical HLD Examples](#23-practical-hld-examples)
    - [2.3.1 Scalable Web Application](#231-scalable-web-application)
    - [2.3.2 Video Streaming Systems](#232-video-streaming-systems)
    - [2.3.3 Real-Time Messaging Systems](#233-real-time-messaging-systems)
- [3. HTTP & HTTPS](#3-http--https)
  - [3.1 HTTP Fundamentals](#31-http-fundamentals)
    - [3.1.1 Request-Response Model](#311-request-response-model)
    - [3.1.2 Statelessness](#312-statelessness)
    - [3.1.3 Headers & Body](#313-headers--body)
    - [3.1.4 Cookies & Sessions](#314-cookies--sessions)
  - [3.2 HTTP Methods](#32-http-methods)
    - [3.2.1 GET](#321-get)
    - [3.2.2 POST](#322-post)
    - [3.2.3 PUT](#323-put)
    - [3.2.4 PATCH](#324-patch)
    - [3.2.5 DELETE](#325-delete)
  - [3.3 HTTP Status Codes](#33-http-status-codes)
    - [3.3.1 2xx Success](#331-2xx-success)
    - [3.3.2 3xx Redirection](#332-3xx-redirection)
    - [3.3.3 4xx Client Errors](#333-4xx-client-errors)
    - [3.3.4 5xx Server Errors](#334-5xx-server-errors)
  - [3.4 HTTPS & TLS](#34-https--tls)
    - [3.4.1 What HTTPS Is](#341-what-https-is)
    - [3.4.2 TLS Handshake](#342-tls-handshake)
    - [3.4.3 Encryption & Certificates](#343-encryption--certificates)
    - [3.4.4 Why HTTPS Matters](#344-why-https-matters)
  - [3.5 HTTP Versions](#35-http-versions)
    - [3.5.1 HTTP/1.1](#351-http11)
    - [3.5.2 HTTP/2](#352-http2)
    - [3.5.3 HTTP/3](#353-http3)
    - [3.5.4 Head-of-Line Blocking](#354-head-of-line-blocking)
    - [3.5.5 Multiplexing](#355-multiplexing)
    - [3.5.6 QUIC over UDP](#356-quic-over-udp)
- [4. TCP vs UDP](#4-tcp-vs-udp)
  - [4.1 TCP Fundamentals](#41-tcp-fundamentals)
    - [4.1.1 Connection-Oriented Communication](#411-connection-oriented-communication)
    - [4.1.2 Reliability Guarantees](#412-reliability-guarantees)
    - [4.1.3 Flow Control](#413-flow-control)
    - [4.1.4 Congestion Control](#414-congestion-control)
    - [4.1.5 Ordered Delivery](#415-ordered-delivery)
  - [4.2 UDP Fundamentals](#42-udp-fundamentals)
    - [4.2.1 Connectionless Communication](#421-connectionless-communication)
    - [4.2.2 Low Latency](#422-low-latency)
    - [4.2.3 No Delivery Guarantees](#423-no-delivery-guarantees)
    - [4.2.4 Lightweight Communication](#424-lightweight-communication)
  - [4.3 TCP vs UDP Tradeoffs](#43-tcp-vs-udp-tradeoffs)
    - [4.3.1 Reliability vs Speed](#431-reliability-vs-speed)
    - [4.3.2 Latency Considerations](#432-latency-considerations)
    - [4.3.3 Throughput Considerations](#433-throughput-considerations)
    - [4.3.4 Real-World Use Cases](#434-real-world-use-cases)
  - [4.4 Choosing Between TCP & UDP](#44-choosing-between-tcp--udp)
    - [4.4.1 Web Applications](#441-web-applications)
    - [4.4.2 Video Streaming](#442-video-streaming)
    - [4.4.3 Gaming Systems](#443-gaming-systems)
    - [4.4.4 DNS Queries](#444-dns-queries)
    - [4.4.5 VoIP Systems](#445-voip-systems)
- [5. DNS (Domain Name System)](#5-dns-domain-name-system)
  - [5.1 DNS Fundamentals](#51-dns-fundamentals)
    - [5.1.1 Domain Name Resolution](#511-domain-name-resolution)
    - [5.1.2 Recursive Queries](#512-recursive-queries)
    - [5.1.3 Iterative Queries](#513-iterative-queries)
    - [5.1.4 DNS Hierarchy](#514-dns-hierarchy)
  - [5.2 DNS Resolution Flow](#52-dns-resolution-flow)
    - [5.2.1 Browser Cache](#521-browser-cache)
    - [5.2.2 OS Cache](#522-os-cache)
    - [5.2.3 Recursive Resolver](#523-recursive-resolver)
    - [5.2.4 Root Name Servers](#524-root-name-servers)
    - [5.2.5 TLD Servers](#525-tld-servers)
    - [5.2.6 Authoritative Servers](#526-authoritative-servers)
  - [5.3 DNS Record Types](#53-dns-record-types)
    - [5.3.1 A Record](#531-a-record)
    - [5.3.2 AAAA Record](#532-aaaa-record)
    - [5.3.3 CNAME](#533-cname)
    - [5.3.4 MX Record](#534-mx-record)
    - [5.3.5 TXT Record](#535-txt-record)
    - [5.3.6 NS Record](#536-ns-record)
  - [5.4 Advanced DNS Concepts](#54-advanced-dns-concepts)
    - [5.4.1 GeoDNS](#541-geodns)
    - [5.4.2 DNS Load Balancing](#542-dns-load-balancing)
    - [5.4.3 TTL Implications](#543-ttl-implications)
    - [5.4.4 DNS Failover](#544-dns-failover)
- [6. Load Balancing & Proxies](#6-load-balancing--proxies)
  - [6.1 Load Balancing Fundamentals](#61-load-balancing-fundamentals)
    - [6.1.1 Why Load Balancers Matter](#611-why-load-balancers-matter)
    - [6.1.2 Traffic Distribution](#612-traffic-distribution)
    - [6.1.3 High Availability](#613-high-availability)
    - [6.1.4 Scalability](#614-scalability)
  - [6.2 Load Balancing Algorithms](#62-load-balancing-algorithms)
    - [6.2.1 Round Robin](#621-round-robin)
    - [6.2.2 Least Connections](#622-least-connections)
    - [6.2.3 IP Hash](#623-ip-hash)
    - [6.2.4 Weighted Routing](#624-weighted-routing)
  - [6.3 Layer 4 vs Layer 7 Load Balancers](#63-layer-4-vs-layer-7-load-balancers)
    - [6.3.1 Layer 4 Load Balancer](#631-layer-4-load-balancer)
    - [6.3.2 Layer 7 Load Balancer](#632-layer-7-load-balancer)
    - [6.3.3 Performance Tradeoffs](#633-performance-tradeoffs)
    - [6.3.4 Feature Tradeoffs](#634-feature-tradeoffs)
  - [6.4 Reverse Proxy](#64-reverse-proxy)
    - [6.4.1 Request Routing](#641-request-routing)
    - [6.4.2 SSL Termination](#642-ssl-termination)
    - [6.4.3 Authentication](#643-authentication)
    - [6.4.4 Caching](#644-caching)
    - [6.4.5 Compression](#645-compression)
  - [6.5 Reverse Proxy vs Forward Proxy](#65-reverse-proxy-vs-forward-proxy)
    - [6.5.1 Reverse Proxy Use Cases](#651-reverse-proxy-use-cases)
    - [6.5.2 Forward Proxy Use Cases](#652-forward-proxy-use-cases)
    - [6.5.3 Security Differences](#653-security-differences)
  - [6.6 Popular Proxy Technologies](#66-popular-proxy-technologies)
    - [6.6.1 Nginx](#661-nginx)
    - [6.6.2 HAProxy](#662-haproxy)
    - [6.6.3 Envoy](#663-envoy)
    - [6.6.4 Caddy](#664-caddy)
- [7. Real-Time Communication](#7-real-time-communication)
  - [7.1 WebSockets](#71-websockets)
    - [7.1.1 HTTP Upgrade Handshake](#711-http-upgrade-handshake)
    - [7.1.2 Persistent Connections](#712-persistent-connections)
    - [7.1.3 Bidirectional Communication](#713-bidirectional-communication)
    - [7.1.4 Connection Management](#714-connection-management)
    - [7.1.5 Scaling WebSockets](#715-scaling-websockets)
  - [7.2 WebSocket Use Cases](#72-websocket-use-cases)
    - [7.2.1 Chat Applications](#721-chat-applications)
    - [7.2.2 Multiplayer Games](#722-multiplayer-games)
    - [7.2.3 Live Notifications](#723-live-notifications)
    - [7.2.4 Real-Time Dashboards](#724-real-time-dashboards)
  - [7.3 Polling Techniques](#73-polling-techniques)
    - [7.3.1 Short Polling](#731-short-polling)
    - [7.3.2 Long Polling](#732-long-polling)
    - [7.3.3 Server-Sent Events (SSE)](#733-server-sent-events-sse)
    - [7.3.4 Comparison with WebSockets](#734-comparison-with-websockets)
  - [7.4 Choosing Real-Time Communication Methods](#74-choosing-real-time-communication-methods)
    - [7.4.1 WebSockets vs SSE](#741-websockets-vs-sse)
    - [7.4.2 Polling vs Push](#742-polling-vs-push)
    - [7.4.3 Tradeoffs](#743-tradeoffs)
- [8. gRPC, REST & GraphQL](#8-grpc-rest--graphql)
  - [8.1 REST APIs](#81-rest-apis)
    - [8.1.1 Resource-Based Design](#811-resource-based-design)
    - [8.1.2 Stateless Communication](#812-stateless-communication)
    - [8.1.3 JSON Payloads](#813-json-payloads)
    - [8.1.4 REST Constraints](#814-rest-constraints)
  - [8.2 GraphQL](#82-graphql)
    - [8.2.1 Query-Based Fetching](#821-query-based-fetching)
    - [8.2.2 Single Endpoint Model](#822-single-endpoint-model)
    - [8.2.3 Flexible Responses](#823-flexible-responses)
    - [8.2.4 Overfetching vs Underfetching](#824-overfetching-vs-underfetching)
  - [8.3 REST vs GraphQL](#83-rest-vs-graphql)
    - [8.3.1 Performance Tradeoffs](#831-performance-tradeoffs)
    - [8.3.2 Caching Differences](#832-caching-differences)
    - [8.3.3 Complexity Tradeoffs](#833-complexity-tradeoffs)
    - [8.3.4 Use Cases](#834-use-cases)
  - [8.4 gRPC Fundamentals](#84-grpc-fundamentals)
    - [8.4.1 Remote Procedure Calls](#841-remote-procedure-calls)
    - [8.4.2 Protocol Buffers](#842-protocol-buffers)
    - [8.4.3 HTTP/2 Usage](#843-http2-usage)
    - [8.4.4 Binary Serialization](#844-binary-serialization)
  - [8.5 gRPC Communication Patterns](#85-grpc-communication-patterns)
    - [8.5.1 Unary RPC](#851-unary-rpc)
    - [8.5.2 Server Streaming](#852-server-streaming-rpc)
    - [8.5.3 Client Streaming](#853-client-streaming-rpc)
    - [8.5.4 Bidirectional Streaming](#854-bidirectional-streaming-rpc)
  - [8.6 REST vs gRPC](#86-rest-vs-grpc)
    - [8.6.1 Human Readability](#861-human-readability)
    - [8.6.2 Performance](#862-performance)
    - [8.6.3 Browser Support](#863-browser-support)
    - [8.6.4 Internal vs External APIs](#864-internal-vs-external-apis)
  - [8.7 When to Use gRPC](#87-when-to-use-grpc)
    - [8.7.1 Microservices Communication](#871-microservices-communication)
    - [8.7.2 Real-Time Streaming](#872-real-time-streaming)
    - [8.7.3 High-Performance Systems](#873-high-performance-systems)
- [9. CDN & Global Networking](#9-cdn--global-networking)
  - [9.1 CDN Fundamentals](#91-cdn-fundamentals)
    - [9.1.1 Edge Servers](#911-edge-servers)
    - [9.1.2 Origin Servers](#912-origin-servers)
    - [9.1.3 Global Content Distribution](#913-global-content-distribution)
  - [9.2 How CDNs Work](#92-how-cdns-work)
    - [9.2.1 Edge Caching](#921-edge-caching)
    - [9.2.2 Cache Miss Handling](#922-cache-miss-handling)
    - [9.2.3 Request Routing](#923-request-routing)
  - [9.3 CDN Benefits](#93-cdn-benefits)
    - [9.3.1 Reduced Latency](#931-reduced-latency)
    - [9.3.2 Origin Offloading](#932-origin-offloading)
    - [9.3.3 DDoS Protection](#933-ddos-protection)
    - [9.3.4 Improved Availability](#934-improved-availability)
  - [9.4 Advanced CDN Concepts](#94-advanced-cdn-concepts)
    - [9.4.1 Dynamic Content Caching](#941-dynamic-content-caching)
    - [9.4.2 Edge Computing](#942-edge-computing)
    - [9.4.3 Geo Routing](#943-geo-routing)
- [10. Networking Patterns by System Type](#10-networking-patterns-by-system-type)
  - [10.1 Summary: Networking Techniques by Pattern](#101-summary-networking-techniques-by-pattern)
  - [10.2 Pattern A – CRUD & Storage Systems (Networking Focus)](#102-pattern-a---crud--storage-systems-networking-focus)
  - [10.3 Pattern B – Feed / Timeline Systems (Networking Focus)](#103-pattern-b---feed--timeline-systems-networking-focus)
  - [10.4 Pattern C – Real-Time Systems (Networking Focus)](#104-pattern-c---real-time-systems-networking-focus)
  - [10.5 Pattern D – Search Systems (Networking Focus)](#105-pattern-d---search-systems-networking-focus)
  - [10.6 Pattern E – Geospatial Systems (Networking Focus)](#106-pattern-e---geospatial-systems-networking-focus)
  - [10.7 Pattern F – Financial Systems (Networking Focus)](#107-pattern-f---financial-systems-networking-focus)
  - [10.8 Pattern G – Large-Scale Infrastructure (Networking Focus)](#108-pattern-g---large-scale-infrastructure-networking-focus)
  - [10.9 Pattern H – Collaborative Systems (Networking Focus)](#109-pattern-h---collaborative-systems-networking-focus)
- [11. Interview-Oriented HLD Notes](#11-interview-oriented-hld-notes)
  - [11.1 Key Tradeoffs](#111-key-tradeoffs)
    - [11.1.1 TCP vs UDP](#1111-tcp-vs-udp)
    - [11.1.2 REST vs gRPC](#1112-rest-vs-grpc)
    - [11.1.3 WebSockets vs SSE](#1113-websockets-vs-sse)
    - [11.1.4 HTTP/2 vs HTTP/3](#1114-http2-vs-http3)
    - [11.1.5 L4 vs L7 Load Balancers](#1115-l4-vs-l7-load-balancers)
  - [11.2 Common Interview Questions](#112-common-interview-questions)
    - [11.2.1 Why HTTPS?](#1121-why-https)
    - [11.2.2 How Does DNS Work?](#1122-how-does-dns-work)
    - [11.2.3 When to Use WebSockets?](#1123-when-to-use-websockets)
    - [11.2.4 REST vs GraphQL?](#1124-rest-vs-graphql)
    - [11.2.5 How Does HTTP/2 Improve Performance?](#1125-how-does-http2-improve-performance)
  - [11.3 Quick Revision Material](#113-quick-revision-material)
    - [11.3.1 Protocol Comparison Tables](#1131-protocol-comparison-tables)
    - [11.3.2 OSI Cheat Sheet](#1132-osi-cheat-sheet)
    - [11.3.3 Networking Decision Matrix](#1133-networking-decision-matrix)
    - [11.3.4 Key Interview Sentences](#1134-key-interview-sentences)
    - [11.3.5 Self-Test Questions](#1135-self-test-questions)

---

## 1. Networking Fundamentals

### 1.1 Why Networking Matters in HLD

Every system you design communicates over a network. Choosing the right protocols, understanding latency and throughput tradeoffs, and building resilient communication paths is fundamental to high‑level design. Poor networking decisions can cripple an otherwise well‑designed system.

#### 1.1.1 Client-Server Communication
The most basic pattern: a client (browser, mobile app) sends requests to a server over the network. The protocol choice (HTTP, WebSocket, gRPC) determines how efficiently that communication happens. The network is the bridge between all components.

#### 1.1.2 Latency & Throughput
Latency is the time for a packet to travel from source to destination; throughput is the amount of data that can be transferred per second. Protocol design, physical distance, and congestion control directly affect both.

#### 1.1.3 Reliability
Networks are inherently unreliable—packets get lost, delayed, or corrupted. Protocols like TCP add reliability at the cost of speed. In HLD, you decide which parts of the system need guaranteed delivery and which can tolerate loss.

#### 1.1.4 Scalability
The network layer must scale with the system. Load balancers, CDNs, and protocol optimizations (like HTTP/2 multiplexing) are essential to handle growing traffic without degrading performance.

#### 1.1.5 Security
All communication must be protected from eavesdropping, tampering, and impersonation. HTTPS (TLS), network segmentation, and strong authentication are non‑negotiable in modern HLD.

### 1.2 The Journey of a User Request

Understanding what happens when a user types a URL into a browser reveals every networking concept needed for HLD.

#### 1.2.1 DNS Resolution
The browser asks "what is the IP address of `example.com`?" This triggers a recursive DNS lookup through caches, resolvers, root servers, TLD servers, and authoritative nameservers. The result is an IP address, possibly chosen by GeoDNS to be the closest data center.

#### 1.2.2 TCP/TLS Connection
The browser opens a TCP connection to the resolved IP on port 443 (HTTPS). After the three‑way handshake, a TLS handshake negotiates encryption, authenticates the server via its certificate, and establishes a secure session.

#### 1.2.3 Load Balancer Routing
The TCP/TLS connection terminates at a load balancer (or reverse proxy). This component inspects the request and routes it to a healthy backend server based on the load balancing algorithm.

#### 1.2.4 Reverse Proxy Processing
If the load balancer is an L7 reverse proxy, it can perform SSL termination, caching, compression, and authentication before forwarding the (now plain) HTTP request to the application server.

#### 1.2.5 Application Server Handling
The request reaches a stateless application server. The server processes the business logic, possibly calling other services (microservices) via REST, gRPC, or message queues.

#### 1.2.6 Database / Cache Access
The application server queries Redis (cache) or the database. If the data is in cache, the response is fast; otherwise, a database query is executed.

#### 1.2.7 Response Flow
The response travels back through the application server, reverse proxy (which may compress or cache it), through the secure TLS tunnel, over TCP, and back to the browser. Caching layers along the way ensure that subsequent requests for the same resource are served much faster.

---

## 2. OSI Model & Networking Layers

### 2.1 Overview of the OSI Model

The OSI (Open Systems Interconnection) model is a conceptual framework that divides network communication into seven abstraction layers. It helps architects reason about where certain functionalities belong, how components interact, and what tradeoffs exist at each layer.

| Layer | Name | Function | Example Protocols/Technologies |
|-------|------|----------|--------------------------------|
| 7 | Application | User-facing applications, data formatting, and services | HTTP, HTTPS, WebSockets, DNS, gRPC |
| 6 | Presentation | Data translation, encryption, compression | SSL/TLS, JPEG, ASCII, gzip |
| 5 | Session | Session management, checkpoints, recovery | NetBIOS, RPC, SIP |
| 4 | Transport | End-to-end communication, reliability, flow control | TCP, UDP, SCTP |
| 3 | Network | Logical addressing, routing, packet forwarding | IP, ICMP, OSPF, BGP |
| 2 | Data Link | Physical addressing (MAC), error detection, framing | Ethernet, Wi-Fi, PPP, ARP |
| 1 | Physical | Transmission of raw bits over physical medium | Cables, fiber optics, hubs, repeaters |

In HLD, we focus on layers 3–7 because they deal with software‑defined components, but decisions at lower layers (e.g., data center locations) can also impact scalability.

#### 2.1.1 Physical Layer
Transmits raw bit streams over physical media. In HLD, choosing data center locations and redundant fiber paths directly affects latency and fault tolerance.

#### 2.1.2 Data Link Layer
Provides node‑to‑node transfer with error detection and MAC addressing. VLANs isolate traffic within a data center; switch topology design prevents loops and bottlenecks.

#### 2.1.3 Network Layer
Handles logical addressing (IP) and routing between networks. Subnet design, anycast routing, and BGP peering are critical for global systems.

#### 2.1.4 Transport Layer
Manages end‑to‑end connections. The choice between TCP (reliable, ordered) and UDP (fast, loss‑tolerant) is one of the most fundamental decisions in HLD.

#### 2.1.5 Session Layer
Manages sessions, checkpoints, and recovery. In load balancers, session persistence (sticky sessions) ensures a user’s requests always go to the same server.

#### 2.1.6 Presentation Layer
Handles data translation, encryption, and compression. SSL/TLS termination, gzip compression, and data serialization (JSON vs Protobuf) happen here.

#### 2.1.7 Application Layer
Provides network services to end‑user applications. HTTP, WebSockets, DNS, and gRPC all operate at this layer. This is where API design, caching strategies, and application‑level routing are defined.

### 2.2 OSI Layers in High-Level Design

#### 2.2.1 Layer 3 Routing Decisions
Anycast routing at layer 3 allows DNS requests to be answered by the nearest server, reducing latency. IP subnet design affects how services communicate within a VPC.

#### 2.2.2 Layer 4 Transport Choices
TCP for reliability (file uploads, database connections); UDP for speed (video streaming, gaming, DNS). QUIC (HTTP/3) combines the speed of UDP with reliability mechanisms at the transport layer.

#### 2.2.3 Layer 7 Application Protocols
HTTP/1.1, HTTP/2 (multiplexing), HTTP/3 (QUIC), WebSockets (persistent bidirectional), and gRPC (binary RPC) each have distinct performance characteristics and are chosen based on the communication pattern.

#### 2.2.4 Security Across Layers
Security is not a single layer—it spans all:
- L3/4: Network firewalls, IP whitelisting, DDoS protection
- L5/6: TLS encryption, mTLS for service‑to‑service
- L7: API authentication, WAF, rate limiting

### 2.3 Practical HLD Examples

#### 2.3.1 Scalable Web Application
- L1–2: Data centers with redundant fiber, VLAN segmentation for web/app/DB tiers.
- L3: Anycast for DNS, VPC subnets for isolation.
- L4: L4 load balancers for raw TCP throughput.
- L5: Sticky sessions if needed (stateless design preferred).
- L6: SSL offloading at the load balancer, gzip compression.
- L7: API gateway routes requests to microservices; CDN caches static content; Redis caches hot data.

#### 2.3.2 Video Streaming Systems
- L1–2: Global edge points of presence (PoPs) close to users.
- L3: IP multicast (if available) for efficient distribution.
- L4: UDP for real‑time delivery (WebRTC), TCP for buffered content (HLS/DASH).
- L6: Video codecs (H.264, AV1), DTLS/SRTP encryption.
- L7: Manifest files (HLS), adaptive bitrate logic.

#### 2.3.3 Real-Time Messaging Systems
- L4: WebSocket over TCP for reliable ordered delivery; UDP for voice/video.
- L7: WebSocket handshake via HTTP upgrade; message routing via connection manager.
- L6: End‑to‑end encryption (Signal protocol) for privacy.
- L5: Session management for reconnection and state recovery.

---

## 3. HTTP & HTTPS

### 3.1 HTTP Fundamentals

HTTP (Hypertext Transfer Protocol) is a stateless, application‑layer request‑response protocol that runs over TCP. Every interaction consists of a client sending a request and the server returning a response.

#### 3.1.1 Request-Response Model
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
```

#### 3.1.2 Statelessness
Each request is independent; the server does not retain client context between requests. Sessions are maintained via cookies, tokens, or URL parameters passed with each request.

#### 3.1.3 Headers & Body
Headers carry metadata: `Content-Type`, `Authorization`, `Cache-Control`. The body contains the actual data (JSON, HTML, binary). Header compression (HPACK in HTTP/2) significantly reduces overhead.

#### 3.1.4 Cookies & Sessions
HTTP cookies store small pieces of data on the client side, sent with every request. They enable session persistence in an otherwise stateless protocol. In HLD, cookies are often replaced by stateless JWT tokens for scalability.

### 3.2 HTTP Methods

#### 3.2.1 GET
Retrieve a resource. Safe (no side effects) and idempotent. Caching proxies and CDNs cache GET responses.

#### 3.2.2 POST
Create a new resource or perform an action. Not idempotent; repeated POSTs may create multiple resources. Use idempotency keys for safe retries.

#### 3.2.3 PUT
Replace an entire resource. Idempotent; calling it multiple times produces the same result.

#### 3.2.4 PATCH
Partially update a resource. Not inherently idempotent, but can be made so with careful design (e.g., using a version field).

#### 3.2.5 DELETE
Remove a resource. Idempotent; after the first call, the resource is gone, and subsequent calls return the same result (404 or 204).

### 3.3 HTTP Status Codes

#### 3.3.1 2xx Success
- 200 OK: Request succeeded.
- 201 Created: Resource created (POST).
- 204 No Content: Success with no response body (DELETE).

#### 3.3.2 3xx Redirection
- 301 Moved Permanently: Permanent redirect (cached by browsers).
- 302 Found: Temporary redirect (not cached).
- 304 Not Modified: Cached version still valid (used with ETags).

#### 3.3.3 4xx Client Errors
- 400 Bad Request: Malformed request.
- 401 Unauthorized: Authentication required.
- 403 Forbidden: Authenticated but no permission.
- 404 Not Found: Resource doesn’t exist.
- 429 Too Many Requests: Rate limit exceeded.

#### 3.3.4 5xx Server Errors
- 500 Internal Server Error: Generic server failure.
- 502 Bad Gateway: Upstream server returned invalid response.
- 503 Service Unavailable: Overloaded or under maintenance.
- 504 Gateway Timeout: Upstream server timed out.

### 3.4 HTTPS & TLS

#### 3.4.1 What HTTPS Is
HTTPS = HTTP over TLS (Transport Layer Security). It encrypts data in transit, protects integrity, and authenticates the server via digital certificates. Runs over TCP port 443.

#### 3.4.2 TLS Handshake
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
The TLS handshake adds ~1–2 round trips. Connection reuse (HTTP keep‑alive, HTTP/2) mitigates this overhead.

#### 3.4.3 Encryption & Certificates
The server presents a certificate issued by a trusted Certificate Authority (CA). The client verifies the certificate’s chain of trust. Once established, a symmetric session key encrypts all subsequent traffic.

#### 3.4.4 Why HTTPS Matters
- Encrypts sensitive data (passwords, payments).
- Prevents man‑in‑the‑middle attacks.
- Required for HTTP/2 and HTTP/3.
- Builds user trust (padlock icon).
- Improves SEO (search engines rank HTTPS sites higher).

### 3.5 HTTP Versions

#### 3.5.1 HTTP/1.1
The classic protocol. Each request/response uses a separate TCP connection (or sequential over keep‑alive). Suffers from head‑of‑line blocking—a slow request blocks subsequent requests on the same connection.

#### 3.5.2 HTTP/2
Major revision: binary framing, multiplexing (multiple streams over one connection), header compression (HPACK), and server push. Solves head‑of‑line blocking at the application layer, but TCP‑level head‑of‑line blocking remains.

#### 3.5.3 HTTP/3
Replaces TCP with QUIC (UDP‑based). Eliminates TCP head‑of‑line blocking entirely. Built‑in encryption (TLS 1.3), faster connection establishment (0‑RTT). Ideal for mobile and high‑latency networks.

#### 3.5.4 Head-of-Line Blocking
HTTP/1.1: Only one request in‑flight per connection. A slow request blocks all others.
HTTP/2: Multiple streams interleaved, but a lost TCP packet blocks all streams (TCP retransmission).
HTTP/3 (QUIC): Each stream is independent; packet loss affects only that stream.

#### 3.5.5 Multiplexing
HTTP/2 and HTTP/3 allow multiple concurrent requests over a single connection, eliminating the need for multiple TCP connections (domain sharding). This dramatically improves page load performance.

#### 3.5.6 QUIC over UDP
QUIC (Quick UDP Internet Connections) is the transport for HTTP/3. It provides reliable, ordered delivery with built‑in TLS, without the head‑of‑line blocking of TCP. It can switch network paths seamlessly, making it ideal for mobile devices.

---

## 4. TCP vs UDP

### 4.1 TCP Fundamentals

TCP (Transmission Control Protocol) provides a reliable, connection‑oriented byte stream between two endpoints.

#### 4.1.1 Connection-Oriented Communication
Before data transfer, a three‑way handshake (SYN, SYN‑ACK, ACK) establishes a connection. This state is maintained on both ends until the connection is closed (four‑way FIN handshake).

#### 4.1.2 Reliability Guarantees
Every byte sent is acknowledged. Lost or corrupted packets are retransmitted. The receiver reassembles segments in the correct order. This ensures that what is sent is exactly what is received.

#### 4.1.3 Flow Control
TCP’s sliding window protocol prevents a fast sender from overwhelming a slow receiver. The receiver advertises a window size, and the sender limits in‑flight data to that amount.

#### 4.1.4 Congestion Control
TCP algorithms (Reno, Cubic, BBR) detect network congestion and reduce the sending rate to avoid collapse. This makes TCP "fair" to other connections sharing the network.

#### 4.1.5 Ordered Delivery
Segments are numbered and reassembled in order. If a segment is missing, all subsequent segments are buffered until the missing one is retransmitted.

### 4.2 UDP Fundamentals

UDP (User Datagram Protocol) is a minimal, connectionless transport protocol.

#### 4.2.1 Connectionless Communication
No handshake. The sender just fires off datagrams. No connection state is maintained.

#### 4.2.2 Low Latency
Without handshake, acknowledgments, or retransmission, UDP has minimal overhead. This makes it much faster for time‑sensitive applications.

#### 4.2.3 No Delivery Guarantees
Datagrams may be lost, duplicated, or arrive out of order. The application must handle (or ignore) these issues.

#### 4.2.4 Lightweight Communication
The UDP header is only 8 bytes (vs TCP’s 20–60 bytes). No connection state means a server can handle many more UDP "sessions" than TCP connections.

### 4.3 TCP vs UDP Tradeoffs

| Feature | TCP | UDP |
|---------|-----|-----|
| Reliability | Guaranteed delivery, retransmission | Best effort, no guarantees |
| Ordering | Strictly ordered | No ordering guarantee |
| Latency | Higher (handshake, ACKs) | Lower (no handshake) |
| Throughput | Limited by congestion control | Can saturate the link |
| Overhead | Larger header, connection state | Minimal header, no state |
| Use Cases | Web, email, file transfer, databases | Streaming, gaming, DNS, VoIP |

#### 4.3.1 Reliability vs Speed
If data integrity is paramount (financial transactions, file uploads), TCP is necessary. If low latency and real‑time responsiveness matter more (live video, voice), UDP is the right choice.

#### 4.3.2 Latency Considerations
TCP’s handshake and retransmission add latency. UDP avoids these, making it ideal for applications where an old packet is useless (e.g., a missed video frame).

#### 4.3.3 Throughput Considerations
TCP’s congestion control can artificially limit throughput on high‑bandwidth, high‑loss links. UDP can achieve higher throughput but risks network instability.

#### 4.3.4 Real-World Use Cases
- YouTube streaming: TCP (buffered, HLS/DASH).
- Zoom/Teams: UDP for real‑time media (WebRTC).
- DNS: Mostly UDP for single‑packet queries; TCP for large responses or zone transfers.
- Online games: UDP for fast‑paced state updates; TCP for reliable in‑game chat.

### 4.4 Choosing Between TCP & UDP

#### 4.4.1 Web Applications
Always TCP (HTTP/HTTPS). Reliability is non‑negotiable.

#### 4.4.2 Video Streaming
Buffered streaming (Netflix, YouTube) uses TCP (HTTP) because the client can pre‑buffer. Live streaming (Twitch, live sports) also uses TCP‑based HLS/DASH. Real‑time conferencing (Zoom, WebRTC) uses UDP for media.

#### 4.4.3 Gaming Systems
Fast‑paced multiplayer games use UDP for state updates (position, actions). TCP may be used for reliable chat or login.

#### 4.4.4 DNS Queries
UDP for standard queries (small, fast). TCP is used for zone transfers and when responses exceed 512 bytes (EDNS0).

#### 4.4.5 VoIP Systems
Voice and video calls use UDP (via WebRTC). Occasional packet loss is imperceptible; retransmitting old audio would be worse than a brief silence.

---

## 5. DNS (Domain Name System)

### 5.1 DNS Fundamentals

DNS translates human‑readable domain names into IP addresses. It’s the phonebook of the internet.

#### 5.1.1 Domain Name Resolution
`www.example.com` → DNS query → `93.184.216.34`. This happens before any TCP connection is established.

#### 5.1.2 Recursive Queries
The client’s DNS resolver does all the work, querying the hierarchy on behalf of the client and returning the final answer.

#### 5.1.3 Iterative Queries
The resolver receives referrals (e.g., "ask the .com TLD server") and queries each server in turn. The root, TLD, and authoritative servers do not recurse.

#### 5.1.4 DNS Hierarchy
Root (`.`) → TLD (`.com`) → Authoritative (`example.com`). The root is served by 13 root server clusters (A–M).

### 5.2 DNS Resolution Flow

#### 5.2.1 Browser Cache
The browser first checks its own DNS cache (short‑lived).

#### 5.2.2 OS Cache
If not in the browser cache, the OS (stub resolver) checks its cache (often populated by previous lookups).

#### 5.2.3 Recursive Resolver
The query goes to a recursive resolver (ISP’s DNS server or a public resolver like `8.8.8.8`). This resolver caches results aggressively.

#### 5.2.4 Root Name Servers
If the resolver has no cached entry, it queries a root server. The root responds with the TLD servers for the domain extension.

#### 5.2.5 TLD Servers
The resolver queries the appropriate TLD server (e.g., `.com`), which returns the authoritative nameservers for the domain.

#### 5.2.6 Authoritative Servers
The resolver queries the authoritative nameserver, which holds the actual DNS records. This server returns the IP address (or other record).

### 5.3 DNS Record Types

#### 5.3.1 A Record
Maps a domain name to an IPv4 address.

#### 5.3.2 AAAA Record
Maps a domain name to an IPv6 address.

#### 5.3.3 CNAME
Canonical Name — aliases one domain to another. (e.g., `www` → `example.com`). Cannot coexist with other records at the same name.

#### 5.3.4 MX Record
Mail Exchange — routes email to the correct mail server. Includes a priority value.

#### 5.3.5 TXT Record
Arbitrary text. Used for domain verification, SPF (email authentication), DKIM, and DMARC.

#### 5.3.6 NS Record
Nameserver — delegates a domain (or subdomain) to a specific set of authoritative nameservers.

### 5.4 Advanced DNS Concepts

#### 5.4.1 GeoDNS
DNS returns different IP addresses based on the geographic location of the querier. This routes users to the nearest data center, reducing latency. Used by CDNs and global applications.

#### 5.4.2 DNS Load Balancing
Multiple A records for the same hostname; the DNS server rotates the order (round‑robin) or returns the closest IP. This provides simple, low‑level load distribution.

#### 5.4.3 TTL Implications
Time‑To‑Live controls how long resolvers cache a DNS record.
- Low TTL (60s): Quick failover, but more DNS queries.
- High TTL (86400s): Fewer queries, but changes propagate slowly.
For failover scenarios, TTL must be set low in advance.

#### 5.4.4 DNS Failover
When a primary server fails, DNS health checks detect the failure and stop returning that IP address. Combined with low TTL, traffic can be redirected to a backup site within minutes.

---

## 6. Load Balancing & Proxies

### 6.1 Load Balancing Fundamentals

#### 6.1.1 Why Load Balancers Matter
A load balancer distributes incoming traffic across multiple backend servers, preventing any single server from being overwhelmed. It provides high availability, scalability, and fault tolerance.

#### 6.1.2 Traffic Distribution
Requests are spread according to an algorithm (round robin, least connections, etc.), ensuring that no server handles disproportionate load.

#### 6.1.3 High Availability
If a backend server fails health checks, the load balancer stops sending traffic to it. Surviving servers handle the load seamlessly.

#### 6.1.4 Scalability
Adding more servers increases capacity. The load balancer automatically includes new instances as they become healthy.

### 6.2 Load Balancing Algorithms

#### 6.2.1 Round Robin
Requests are distributed sequentially: Server 1, Server 2, Server 3, Server 1… Simple and effective for homogeneous servers.

#### 6.2.2 Least Connections
The request goes to the server with the fewest active connections. Ideal for long‑lived connections (WebSockets, streaming).

#### 6.2.3 IP Hash
The client’s IP address is hashed; the result determines which server always receives requests from that IP. Provides sticky sessions without cookies.

#### 6.2.4 Weighted Routing
Servers are assigned weights; more powerful servers receive a proportionally larger share of traffic. Used in heterogeneous environments.

### 6.3 Layer 4 vs Layer 7 Load Balancers

#### 6.3.1 Layer 4 Load Balancer
Operates at the transport layer (TCP/UDP). Routes based on IP addresses and ports. Does not inspect application data. Extremely fast, protocol‑agnostic. Example: AWS Network Load Balancer.

#### 6.3.2 Layer 7 Load Balancer
Operates at the application layer. Can inspect HTTP headers, URLs, cookies, and request bodies to make intelligent routing decisions. Can terminate SSL, cache content, and perform authentication. Example: AWS Application Load Balancer, NGINX, Envoy.

#### 6.3.3 Performance Tradeoffs
L4 is faster because it does not inspect packets; it simply forwards connections. L7 adds latency due to packet inspection, but provides much greater flexibility.

#### 6.3.4 Feature Tradeoffs
L4: Simple, high throughput, works with any TCP/UDP protocol. L7: Content‑based routing, SSL termination, caching, rate limiting, header manipulation.

### 6.4 Reverse Proxy

#### 6.4.1 Request Routing
A reverse proxy sits in front of servers and routes requests to the appropriate backend based on URL path, headers, or other criteria. It is the foundation of API gateways.

#### 6.4.2 SSL Termination
The reverse proxy handles TLS decryption, forwarding plain HTTP to internal servers. This offloads CPU‑intensive encryption from application servers and simplifies certificate management.

#### 6.4.3 Authentication
The proxy can validate API keys, JWT tokens, or OAuth tokens before forwarding the request. Unauthenticated requests are rejected at the edge.

#### 6.4.4 Caching
Static content and even dynamic responses can be cached at the proxy level, reducing load on backend servers and improving response times.

#### 6.4.5 Compression
The proxy can compress responses (gzip, brotli) before sending them to clients, saving bandwidth.

### 6.5 Reverse Proxy vs Forward Proxy

#### 6.5.1 Reverse Proxy Use Cases
- Load balancing
- SSL termination
- API gateway
- DDoS protection
- Caching

#### 6.5.2 Forward Proxy Use Cases
- Corporate internet access control
- Bypassing geo‑restrictions
- Anonymizing client IP addresses

#### 6.5.3 Security Differences
A reverse proxy protects backend servers from direct exposure to the internet. A forward proxy protects clients by hiding their IPs from destination servers.

### 6.6 Popular Proxy Technologies

#### 6.6.1 Nginx
High‑performance HTTP and reverse proxy server. Widely used for load balancing, SSL termination, and serving static files. Also supports WebSocket and gRPC proxying.

```
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}

upstream ws_backend {
    server 127.0.0.1:8080;
    keepalive 64;
}

server {
    listen 443 ssl http2;
    server_name ws.example.com;

    location /ws/ {
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        proxy_read_timeout 3600s;
        proxy_send_timeout 3600s;
        proxy_buffering off;
        proxy_pass http://ws_backend;
    }
}
```

#### 6.6.2 HAProxy
High‑performance TCP/HTTP load balancer. Known for extreme reliability and low resource usage. Supports both L4 and L7 modes.

#### 6.6.3 Envoy
Modern, cloud‑native edge and service proxy. Used in service meshes (Istio). Supports advanced features like dynamic configuration, gRPC, and fine‑grained observability.

#### 6.6.4 Caddy
Automatic HTTPS (manages certificates via Let’s Encrypt). Simple configuration, supports HTTP/2, HTTP/3, WebSocket, and gRPC proxying.

```
example.com {
    reverse_proxy 127.0.0.1:8000 {
        transport http {
            read_timeout 2h
            write_timeout 2h
        }
    }
}
```

---

## 7. Real-Time Communication

### 7.1 WebSockets

WebSocket provides full‑duplex communication over a single TCP connection, starting as an HTTP upgrade.

#### 7.1.1 HTTP Upgrade Handshake
```
Client sends: GET /chat HTTP/1.1
              Upgrade: websocket
              Connection: Upgrade
Server responds: HTTP/1.1 101 Switching Protocols
                 Upgrade: websocket
                 Connection: Upgrade
```
After the upgrade, the protocol switches from HTTP to WebSocket, and the connection remains open for bidirectional data exchange.

#### 7.1.2 Persistent Connections
Once established, the TCP connection stays open indefinitely. Both client and server can send messages (frames) at any time without re‑establishing the connection.

#### 7.1.3 Bidirectional Communication
Unlike HTTP, where the client must initiate, WebSocket allows the server to push data to the client instantly. This is essential for chat, live scores, and real‑time notifications.

#### 7.1.4 Connection Management
Servers must track open connections. A connection manager (often backed by Redis) maps user IDs to the server handling their connection. On server failure, clients reconnect and the mapping is updated.

#### 7.1.5 Scaling WebSockets
A single server can handle ~65,000 concurrent WebSocket connections (TCP port limit). To scale beyond, deploy multiple WebSocket servers behind an L4 load balancer. Use Redis Pub/Sub or a message broker to relay messages between servers.

### 7.2 WebSocket Use Cases

#### 7.2.1 Chat Applications
WhatsApp, Slack, Discord. Messages are pushed to recipients instantly.

#### 7.2.2 Multiplayer Games
Real‑time state synchronization between players.

#### 7.2.3 Live Notifications
Social media alerts, stock price changes, breaking news.

#### 7.2.4 Real-Time Dashboards
Live metrics, server monitoring, financial trading screens.

### 7.3 Polling Techniques

#### 7.3.1 Short Polling
Client repeatedly requests updates at a fixed interval (e.g., every 5 seconds). Simple but wasteful—most polls return empty.

#### 7.3.2 Long Polling
Client makes a request; server holds the connection open until new data is available or a timeout occurs. Upon response, the client immediately opens a new long‑poll request. Reduces empty responses but still has connection overhead.

#### 7.3.3 Server-Sent Events (SSE)
A one‑way HTTP stream from server to client. The client opens a connection and the server pushes events continuously. Simpler than WebSockets, with built‑in auto‑reconnect. Only supports server‑to‑client.

```
GET /events HTTP/1.1
────────────────────────────
Server keeps connection open and sends:
  data: {"type": "tweet", "id": 123}
  data: {"type": "like", "post_id": 456}
```

#### 7.3.4 Comparison with WebSockets
- WebSocket: bidirectional, full‑duplex, more complex.
- SSE: server→client only, simpler, works over standard HTTP, auto‑reconnect.
- Long Polling: near‑real‑time, works everywhere, more overhead.

### 7.4 Choosing Real-Time Communication Methods

#### 7.4.1 WebSockets vs SSE
Use WebSockets when you need bidirectional communication (chat, multiplayer games). Use SSE when only server‑to‑client push is needed (live feeds, notifications) and simplicity is valued.

#### 7.4.2 Polling vs Push
Polling is simpler but can waste resources and add latency. Push (WebSocket, SSE) is more efficient for frequent updates. For infrequent updates (e.g., once per hour), polling is acceptable.

#### 7.4.3 Tradeoffs
WebSockets require stateful servers and connection management. SSE works over HTTP/2 with multiplexing. Long polling is a fallback when WebSockets/SSE are blocked by firewalls.

---

## 8. gRPC, REST & GraphQL

### 8.1 REST APIs

#### 8.1.1 Resource-Based Design
REST APIs model data as resources identified by URIs. HTTP methods express actions on these resources.

```
GET    /users/123        → Get user 123
POST   /users            → Create user
PUT    /users/123        → Replace user 123
PATCH  /users/123        → Update user 123
DELETE /users/123        → Delete user 123
```

#### 8.1.2 Stateless Communication
Each request contains all information needed to process it. Servers don’t store client context. This makes REST APIs scalable.

#### 8.1.3 JSON Payloads
Most REST APIs use JSON as the data format. It’s human‑readable, widely supported, and easy to debug.

#### 8.1.4 REST Constraints
The REST architectural style imposes six constraints: client‑server, stateless, cacheable, uniform interface, layered system, and (optionally) code‑on‑demand.

### 8.2 GraphQL

#### 8.2.1 Query-Based Fetching
The client specifies exactly which fields it needs, and the server returns only those fields. This eliminates over‑fetching and under‑fetching.

```graphql
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
```

#### 8.2.2 Single Endpoint Model
All queries are sent to a single `/graphql` endpoint (typically via POST). The query itself determines what data is returned.

#### 8.2.3 Flexible Responses
The client can request nested resources in a single query, reducing the number of round trips. The server resolves the query and returns exactly the requested shape.

#### 8.2.4 Overfetching vs Underfetching
REST endpoints often return more data than needed (overfetching) or require multiple requests to assemble a view (underfetching). GraphQL solves both by letting clients request precisely what they need.

### 8.3 REST vs GraphQL

#### 8.3.1 Performance Tradeoffs
REST can leverage HTTP caching (GET requests are cacheable). GraphQL typically uses POST, making caching more complex. However, GraphQL reduces round trips for complex views.

#### 8.3.2 Caching Differences
REST’s resource‑oriented nature aligns well with HTTP caching. GraphQL requires more sophisticated caching strategies (persisted queries, normalized caches).

#### 8.3.3 Complexity Tradeoffs
REST is simpler to implement and understand. GraphQL adds complexity on the server (resolver logic, query cost analysis, rate limiting) but can simplify client development.

#### 8.3.4 Use Cases
- REST: Public APIs, simple CRUD, microservices that expose clear resources.
- GraphQL: Mobile apps with varying data needs, complex UIs, aggregating data from multiple microservices.

### 8.4 gRPC Fundamentals

#### 8.4.1 Remote Procedure Calls
gRPC allows a client to call a method on a server as if it were a local function. The framework handles serialization, transport, and authentication.

#### 8.4.2 Protocol Buffers
gRPC uses Protocol Buffers (protobuf) as its interface definition language and binary serialization format. The `.proto` file defines the service and messages, generating strongly‑typed client/server stubs.

#### 8.4.3 HTTP/2 Usage
gRPC runs over HTTP/2, leveraging its multiplexing, binary framing, and server push capabilities.

#### 8.4.4 Binary Serialization
Protobuf encodes data into a compact binary format, which is much faster to serialize and significantly smaller than JSON. This reduces latency and bandwidth.

### 8.5 gRPC Communication Patterns

#### 8.5.1 Unary RPC
A single request followed by a single response. Equivalent to a traditional REST call.

#### 8.5.2 Server Streaming RPC
Client sends one request; server streams back multiple responses. Example: subscribing to a real‑time data feed.

#### 8.5.3 Client Streaming RPC
Client sends a stream of data; server responds with a single acknowledgment. Example: uploading a large file in chunks.

#### 8.5.4 Bidirectional Streaming RPC
Both client and server send independent streams of messages simultaneously. Example: real‑time chat, interactive game sessions.

### 8.6 REST vs gRPC

#### 8.6.1 Human Readability
REST (JSON) is human‑readable and easy to debug with tools like curl. gRPC (binary Protobuf) requires tools like grpcurl or BloomRPC.

#### 8.6.2 Performance
gRPC’s binary format is 5–10x smaller and faster to serialize than JSON. HTTP/2 multiplexing gives it a significant performance advantage over HTTP/1.1 REST.

#### 8.6.3 Browser Support
REST is natively supported by browsers. gRPC cannot be called directly from a browser; it requires a proxy (e.g., grpc‑web).

#### 8.6.4 Internal vs External APIs
REST is preferred for public APIs due to broad compatibility and tooling. gRPC excels for internal service‑to‑service communication where performance is critical.

### 8.7 When to Use gRPC

#### 8.7.1 Microservices Communication
gRPC’s high performance, strong typing, and built‑in streaming make it ideal for internal microservice communication, especially at high QPS.

#### 8.7.2 Real-Time Streaming
gRPC’s bidirectional streaming enables real‑time, low‑latency communication between services (e.g., telemetry, live data feeds).

#### 8.7.3 High-Performance Systems
In latency‑sensitive environments (finance, gaming), gRPC’s binary serialization and HTTP/2 transport provide a significant edge over text‑based protocols.

---

## 9. CDN & Global Networking

### 9.1 CDN Fundamentals

A Content Delivery Network (CDN) is a geographically distributed group of servers that work together to deliver content (HTML, images, video, APIs) to users quickly and reliably.

#### 9.1.1 Edge Servers
CDNs deploy thousands of Points of Presence (PoPs) worldwide. Content is cached at these edge locations, physically close to end users.

#### 9.1.2 Origin Servers
The original source of content (e.g., your S3 bucket, web application server). Edge servers fetch content from the origin on a cache miss.

#### 9.1.3 Global Content Distribution
By serving content from nearby edges, CDNs reduce the distance data must travel, cutting latency from hundreds of milliseconds to single digits.

### 9.2 How CDNs Work

#### 9.2.1 Edge Caching
Static assets (CSS, JS, images) and even dynamic but cacheable content are stored at edge servers. Subsequent requests are served directly from the edge.

#### 9.2.2 Cache Miss Handling
On a cache miss, the edge server fetches the content from the origin, caches it locally, and returns it to the user. The next request for that content is a cache hit.

#### 9.2.3 Request Routing
CDNs use DNS (GeoDNS or anycast) to route users to the nearest edge server. The user’s DNS query returns the IP of the optimal PoP.

### 9.3 CDN Benefits

#### 9.3.1 Reduced Latency
Content travels a shorter distance, dramatically improving page load times and API responsiveness for global users.

#### 9.3.2 Origin Offloading
Edge caches absorb the vast majority of requests, reducing traffic to your origin servers by 80–99%, which lowers infrastructure costs and improves scalability.

#### 9.3.3 DDoS Protection
CDNs can absorb volumetric DDoS attacks at the edge, filtering malicious traffic before it reaches your origin.

#### 9.3.4 Improved Availability
If an edge server fails, the CDN routes users to the next nearest healthy PoP. This built‑in redundancy ensures high availability.

### 9.4 Advanced CDN Concepts

#### 9.4.1 Dynamic Content Caching
CDNs can cache semi‑dynamic content using short TTLs, Edge Side Includes (ESI), or by caching API responses that are shared across users (e.g., top headlines).

#### 9.4.2 Edge Computing
Platforms like Cloudflare Workers and AWS Lambda@Edge allow you to run custom code at the edge. This enables A/B testing, JWT validation, geolocation‑based routing, and more, without hitting the origin.

#### 9.4.3 Geo Routing
CDNs can serve different content based on the user’s geographic location, enabling compliance with data residency laws and localization.

---

## 10. Networking Patterns by System Type

### 10.1 Summary: Networking Techniques by Pattern

| Pattern | System Type | Key Networking Techniques | Deep Dive / Rationale |
|---------|-------------|------------------------|------------------------|
| **A: CRUD & Storage Systems** | URL Shortener, Pastebin, Online Bookstore, Ticket Booking, Dropbox / Drive | REST APIs; HTTP Load Balancing; CDN Usage | Simple, resource‑oriented REST APIs are the standard for CRUD applications. Use proper HTTP methods, status codes, and pagination. L7 load balancers distribute HTTP requests, perform SSL termination, and provide caching… |
| **B: Feed / Timeline Systems** | Twitter / X, Instagram Feed, Facebook News Feed, LinkedIn Feed | WebSocket Notifications; CDN for Media; API Gateway | Real‑time updates (new posts, likes, comments) are pushed to clients via WebSockets or SSE. Images and videos are stored in object storage and delivered globally via CDN. Routes client requests to appropriate… |
| **C: Real-Time Systems** | WhatsApp / Messenger, Slack / Discord, Live Comments (YouTube Live), Multiplayer Game Backend | WebSockets; UDP Streaming; Persistent Connections | Persistent, bidirectional connections for chat, live comments, and real‑time dashboards. For voice and video, UDP is used (via WebRTC) to achieve low latency at the cost of occasional packet loss. Connection management… |
| **D: Search Systems** | Google Search, YouTube Search, Amazon Product Search, Search Autocomplete | gRPC Internal Calls; CDN for Static Assets; Query Routing | Search indexers communicate internally via gRPC for high performance and strong typing. Search UI and static resources are delivered via CDN. API gateways route search queries to the appropriate index shards or |
| **E: Geospatial Systems** | Uber / Lyft, Google Maps, Food Delivery, Nearby Friends | GeoDNS; Low-Latency Routing; Real-Time Updates | Routes users to the nearest data center based on geographic location, minimizing latency for map tiles and location queries. L4 and L7 load balancers ensure requests are handled by the closest available server. Driver… |
| **F: Financial Systems** | Payment Gateway, Wallet System, UPI-like System, Distributed Job Scheduler | HTTPS Everywhere; TLS Security; Reliable TCP Communication | All communication is encrypted with TLS. No plain HTTP allowed. Mutual TLS (mTLS) is used for service‑to‑service authentication, and strong cipher suites are enforced. Payment and transaction data require TCP’s… |
| **G: Large-Scale Infrastructure** | Analytics / Clickstream, Distributed Logging (ELK), Metrics Monitoring, Kafka-like System | Service Mesh; Internal gRPC; High-Performance Routing | Istio/Linkerd manage service‑to‑service communication, providing mTLS, load balancing, and observability transparently. High‑performance internal APIs use gRPC for efficiency and streaming capabilities. Envoy and NGINX… |
| **H: Collaborative Systems** | Google Docs, Figma / Whiteboard, Distributed Locking Service, Web Crawler | WebSocket Collaboration; Real-Time Sync; Persistent Bidirectional Communication | Google Docs and Figma rely on WebSockets for real‑time, bidirectional collaboration. CRDT operations are broadcast via persistent connections, enabling seamless multi‑user editing. WebSocket gateways manage thousands of… |

---

### 10.2 Pattern A – CRUD & Storage Systems (Networking Focus)

| System | Key Networking Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **URL Shortener** | REST APIs | Simple, resource‑oriented REST APIs are the standard for CRUD applications. | Use proper HTTP methods, status codes, and pagination. |
| **Pastebin** | HTTP Load Balancing | L7 load balancers distribute HTTP requests, perform SSL termination, and provide caching and compression. | L7 load balancers distribute HTTP requests, perform SSL termination, and provide caching and compression. |
| **Online Bookstore** | CDN Usage | Static assets (product images, pastes) are served via CDN for low latency and reduced origin load. | Static assets (product images, pastes) are served via CDN for low latency and reduced origin load. |

---

### 10.3 Pattern B – Feed / Timeline Systems (Networking Focus)

| System | Key Networking Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Twitter / X** | WebSocket Notifications | Real‑time updates (new posts, likes, comments) are pushed to clients via WebSockets or SSE. | Real‑time updates (new posts, likes, comments) are pushed to clients via WebSockets or SSE. |
| **Instagram Feed** | CDN for Media | Images and videos are stored in object storage and delivered globally via CDN. | Images and videos are stored in object storage and delivered globally via CDN. |
| **Facebook News Feed** | API Gateway | Routes client requests to appropriate microservices, handles authentication, and enforces rate limits. | Routes client requests to appropriate microservices, handles authentication, and enforces rate limits. |

---

### 10.4 Pattern C – Real-Time Systems (Networking Focus)

| System | Key Networking Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **WhatsApp / Messenger** | WebSockets | Persistent, bidirectional connections for chat, live comments, and real‑time dashboards. | Persistent, bidirectional connections for chat, live comments, and real‑time dashboards. |
| **Slack / Discord** | UDP Streaming | For voice and video, UDP is used (via WebRTC) to achieve low latency at the cost of occasional packet loss. | For voice and video, UDP is used (via WebRTC) to achieve low latency at the cost of occasional packet loss. |
| **Live Comments (YouTube Live)** | Persistent Connections | Connection management and scaling are critical; use connection managers with Redis and L4 load balancers. | Connection management and scaling are critical; use connection managers with Redis and L4 load balancers. |

---

### 10.5 Pattern D – Search Systems (Networking Focus)

| System | Key Networking Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Google Search** | gRPC Internal Calls | Search indexers communicate internally via gRPC for high performance and strong typing. | Search indexers communicate internally via gRPC for high performance and strong typing. |
| **YouTube Search** | CDN for Static Assets | Search UI and static resources are delivered via CDN. | Search UI and static resources are delivered via CDN. |
| **Amazon Product Search** | Query Routing | API gateways route search queries to the appropriate index shards or clusters. | API gateways route search queries to the appropriate index shards or clusters. |

---

### 10.6 Pattern E – Geospatial Systems (Networking Focus)

| System | Key Networking Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Uber / Lyft** | GeoDNS | Routes users to the nearest data center based on geographic location, minimizing latency for map tiles and location queries. | Routes users to the nearest data center based on geographic location, minimizing latency for map tiles and location queries. |
| **Google Maps** | Low-Latency Routing | L4 and L7 load balancers ensure requests are handled by the closest available server. | L4 and L7 load balancers ensure requests are handled by the closest available server. |
| **Food Delivery** | Real-Time Updates | Driver locations and ride matching events are pushed to clients via WebSockets. | Driver locations and ride matching events are pushed to clients via WebSockets. |

---

### 10.7 Pattern F – Financial Systems (Networking Focus)

| System | Key Networking Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Payment Gateway** | HTTPS Everywhere | All communication is encrypted with TLS. | No plain HTTP allowed. |
| **Wallet System** | TLS Security | Mutual TLS (mTLS) is used for service‑to‑service authentication, and strong cipher suites are enforced. | Mutual TLS (mTLS) is used for service‑to‑service authentication, and strong cipher suites are enforced. |
| **UPI-like System** | Reliable TCP Communication | Payment and transaction data require TCP’s guaranteed, ordered delivery. | No data loss is acceptable. |

---

### 10.8 Pattern G – Large-Scale Infrastructure (Networking Focus)

| System | Key Networking Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Analytics / Clickstream** | Service Mesh | Istio/Linkerd manage service‑to‑service communication, providing mTLS, load balancing, and observability transparently. | Istio/Linkerd manage service‑to‑service communication, providing mTLS, load balancing, and observability transparently. |
| **Distributed Logging (ELK)** | Internal gRPC | High‑performance internal APIs use gRPC for efficiency and streaming capabilities. | High‑performance internal APIs use gRPC for efficiency and streaming capabilities. |
| **Metrics Monitoring** | High-Performance Routing | Envoy and NGINX are used for edge routing and load balancing at massive scale. | Envoy and NGINX are used for edge routing and load balancing at massive scale. |

---

### 10.9 Pattern H – Collaborative Systems (Networking Focus)

| System | Key Networking Techniques | Why (When to Use) | Deep Dive / Implementation |
|--------|------------------------|--------------------|-----------------------------|
| **Google Docs** | WebSocket Collaboration | Google Docs and Figma rely on WebSockets for real‑time, bidirectional collaboration. | Google Docs and Figma rely on WebSockets for real‑time, bidirectional collaboration. |
| **Figma / Whiteboard** | Real-Time Sync | CRDT operations are broadcast via persistent connections, enabling seamless multi‑user editing. | CRDT operations are broadcast via persistent connections, enabling seamless multi‑user editing. |
| **Distributed Locking Service** | Persistent Bidirectional Communication | WebSocket gateways manage thousands of concurrent connections, with state stored in Redis for failover. | --- |

---

---

## 11. Interview-Oriented HLD Notes

### 11.1 Key Tradeoffs

#### 11.1.1 TCP vs UDP
Reliability vs speed. TCP for data integrity; UDP for real‑time applications. Know when to choose each.

#### 11.1.2 REST vs gRPC
Human‑readable JSON vs high‑performance binary. REST for public APIs, gRPC for internal microservices.

#### 11.1.3 WebSockets vs SSE
Bidirectional vs server‑to‑client only. WebSockets for chat, SSE for one‑way feeds.

#### 11.1.4 HTTP/2 vs HTTP/3
HTTP/2 fixes application‑layer head‑of‑line blocking. HTTP/3 (QUIC) fixes transport‑layer head‑of‑line blocking and improves mobile performance.

#### 11.1.5 L4 vs L7 Load Balancers
L4: faster, protocol‑agnostic. L7: application‑aware, supports content‑based routing, SSL termination, and caching.

### 11.2 Common Interview Questions

#### 11.2.1 Why HTTPS?
HTTPS encrypts data, protects integrity, and authenticates the server. It’s mandatory for any system handling user data, payments, or personal information.

#### 11.2.2 How Does DNS Work?
DNS translates domain names to IP addresses through a hierarchical system of root, TLD, and authoritative servers. Recursive resolvers cache records based on TTL.

#### 11.2.3 When to Use WebSockets?
When you need bidirectional, real‑time communication with low latency (chat, live scores, collaborative editing).

#### 11.2.4 REST vs GraphQL?
REST: simpler, uses HTTP caching, good for public APIs. GraphQL: flexible queries, reduces over‑/under‑fetching, best for mobile apps and complex data views.

#### 11.2.5 How Does HTTP/2 Improve Performance?
Multiplexing eliminates multiple connections, header compression reduces overhead, and server push can proactively send resources. All over a single TCP connection.

### 11.3 Quick Revision Material

#### 11.3.1 Protocol Comparison Tables
| Protocol | Transport | Use Case | Key Advantage |
|----------|-----------|----------|---------------|
| HTTP/1.1 | TCP | Web APIs | Simplicity |
| HTTP/2 | TCP | Modern web, APIs | Multiplexing |
| HTTP/3 | QUIC | Mobile, high‑latency | No TCP HOL blocking |
| WebSocket | TCP | Real‑time bidirectional | Persistent, full‑duplex |
| gRPC | HTTP/2 | Internal microservices | Binary, streaming |
| DNS | UDP/TCP | Name resolution | Hierarchical caching |
| TCP | IP | Reliable data transfer | Guaranteed delivery |
| UDP | IP | Streaming, gaming | Low latency |

#### 11.3.2 OSI Cheat Sheet
| Layer | Key HLD Components | Technologies |
|-------|--------------------|--------------|
| 7 | API gateways, CDN | HTTP, WebSocket, gRPC, DNS |
| 6 | Encryption, compression | TLS, gzip, Protobuf |
| 5 | Sessions, RPC | WebSocket sessions, SIP |
| 4 | Load balancers, protocol choice | TCP, UDP, QUIC, L4 LB |
| 3 | Routing, anycast | IP, BGP, anycast |
| 2 | VLANs, switches | Ethernet, Wi-Fi |
| 1 | Data centers, cables | Fiber, physical redundancy |

#### 11.3.3 Networking Decision Matrix
| Scenario | Recommended Protocol/Technology |
|----------|--------------------------------|
| Public REST API | HTTP/2 + JSON |
| Internal microservice | gRPC + Protobuf |
| Real‑time chat | WebSocket |
| Live video streaming | UDP (WebRTC) |
| File upload | TCP (HTTP multipart) |
| Global static assets | CDN + HTTPS |
| DNS resolution | UDP, with TCP fallback |
| API gateway | L7 Reverse Proxy (NGINX, Envoy) |

#### 11.3.4 Key Interview Sentences
> *“For real‑time bidirectional communication like chat, I’d use WebSockets over a WebSocket gateway. The gateway maintains a connection map in Redis so any server can route messages to the right user.”*

> *“For internal microservice communication, I’d use gRPC — it’s binary, faster than JSON, and supports streaming. For the public API, REST is better because browsers and third parties can call it directly.”*

> *“The client connects over HTTPS with HTTP/2 multiplexing, so multiple API calls share one connection. For the live feed, instead of polling, we use Server‑Sent Events since it’s server‑to‑client only and simpler than WebSockets.”*

> *“A CDN is non‑negotiable for a global user base. It caches static assets at edge locations, reducing latency from hundreds of milliseconds to single digits and absorbing DDoS attacks.”*

#### 11.3.5 Self-Test Questions
1. A user opens Twitter and loads their feed, then sees a new tweet appear without refreshing. What protocols are involved from DNS lookup to the live tweet appearing?
2. Why does Zoom use UDP instead of TCP for video?
3. You’re designing a stock trading dashboard that shows live price updates. Would you use WebSockets, SSE, or Long Polling? Why?
4. Your microservices are calling each other 10,000 times per second. REST or gRPC? Why?
5. What is the difference between a reverse proxy and a load balancer? (Hint: a load balancer IS a reverse proxy)
6. How does HTTP/2 solve the head‑of‑line blocking problem, and why does HTTP/3 go further?
7. Describe the DNS resolution process from the moment a user types a URL until the browser establishes a TCP connection.

*End of consolidated notes.*
