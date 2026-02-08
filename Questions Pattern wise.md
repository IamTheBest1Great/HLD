We've categorized our 70 problems into the **4 Pillars of HLD**. This structure is your "cheat sheet" for interviews—when an interviewer asks a question, your first step is to identify which pillar it belongs to so you know which architectural "tools" to pull out of your bag. 🧰

Below is the complete, master table of all 70 problems, deeply categorized by their sub-category, primary challenge, and their corresponding HLD pillar.

### 🏛️ The 70-Problem HLD Master Map

| Sub-Category | Pillar | Primary Challenge | Problem | Diff | Freq |
| --- | --- | --- | --- | --- | --- |
| **1. Traffic Control** 🧱 | **Storage/HA** | Load Balancing | 1. Load Balancer Design | Easy | ⭐ |
|  | **Storage/HA** | Request Routing | 2. API Gateway Service | Easy | ⭐ |
|  | **Write-Heavy** | Request Throttling | 3. Rate Limiter (Distributed) | Med | ⭐ |
|  | **Storage/HA** | Security Filtering | 4. Web App Firewall (WAF) | Med |  |
|  | **Storage/HA** | Traffic Scrubbing | 5. DDOS Protection System | Hard |  |
|  | **Storage/HA** | Secret Management | 6. Secrets Manager (Vault) | Med |  |
| **2. Global Infra** 🔗 | **Storage/HA** | Key Mapping | 7. URL Shortener (TinyURL) | Easy | ⭐ |
|  | **Storage/HA** | Blob Storage | 8. Pastebin (Text Storage) | Easy | ⭐ |
|  | **Storage/HA** | Unique Sequence | 9. Distributed ID (Snowflake) | Med | ⭐ |
|  | **Storage/HA** | Data Partitioning | 10. Consistent Hashing | Med | ⭐ |
|  | **Storage/HA** | Edge Caching | 11. Content Delivery Network | Med |  |
|  | **Storage/HA** | Geo-Routing | 12. Multi-region Router | Hard |  |
| **3. Content Discovery** 📱 | **Read-Heavy** | Feed Generation | 13. News Feed (Push/Pull) | Hard | ⭐ |
|  | **Read-Heavy** | Media Delivery | 14. Instagram (Media Feed) | Med | ⭐ |
|  | **Read-Heavy** | Grid Aggregation | 15. Pinterest (Image Feed) | Med |  |
|  | **Write-Heavy** | Massive Fan-out | 16. Twitter (Write Path) | Hard | ⭐ |
|  | **Read-Heavy** | Vote Aggregation | 17. Reddit (Ranking) | Med |  |
|  | **Read-Heavy** | Time-Decay Math | 18. HackerNews (Ranking) | Med |  |
|  | **Read-Heavy** | Graph Traversal | 19. Friend Suggestions | Med |  |
| **4. Communication** 💬 | **Write-Heavy** | Socket Mgmt | 20. WhatsApp (1-on-1) | Med | ⭐ |
|  | **Write-Heavy** | Msg Ordering | 21. Group Chat System | Hard | ⭐ |
|  | **Read-Heavy** | Heartbeat Mgmt | 22. Presence Platform | Med |  |
|  | **Write-Heavy** | Pub/Sub | 23. Slack (Enterprise) | Hard |  |
|  | **Write-Heavy** | Fan-out | 24. Notification Service | Med | ⭐ |
|  | **Read-Heavy** | Low Latency Video | 25. Zoom Video Conf. | Hard |  |
|  | **Storage/HA** | Mail Retrieval | 26. Email Service (Gmail) | Hard |  |
| **5. Media/Streaming** 🎥 | **Storage/HA** | Large File Upload | 27. YouTube (Storage) | Hard | ⭐ |
|  | **Read-Heavy** | Global Replication | 28. Netflix Distribution | Hard |  |
|  | **Read-Heavy** | Vertical Feed | 29. TikTok (Video Feed) | Hard |  |
|  | **Read-Heavy** | Metadata Sync | 30. Spotify (Streaming) | Med |  |
|  | **Read-Heavy** | Live Buffer Mgmt | 31. Twitch (Live Stream) | Hard |  |
|  | **Write-Heavy** | Compute Pipeline | 32. Video Transcoding | Med |  |
| **6. Geospatial** 🚗 | **Read-Heavy** | Proximity Search | 33. Uber/Lyft (Matching) | Hard | ⭐ |
|  | **Read-Heavy** | Graph Routing | 34. Google Maps (Paths) | Hard | ⭐ |
|  | **Read-Heavy** | Spatial Indexing | 35. Yelp/Nearby | Med | ⭐ |
|  | **Write-Heavy** | Real-time Tracking | 36. Food Delivery Tracker | Med |  |
|  | **Read-Heavy** | Static Map Tiles | 37. Transit Tracker | Med |  |
|  | **ACID/Trans** | Inventory Locking | 38. Hotel Booking | Med |  |
|  | **ACID/Trans** | Double-Booking | 39. AirBnB Reservation | Hard |  |
| **7. Search/Data** 🔍 | **Write-Heavy** | BFS at Scale | 40. Web Crawler | Med | ⭐ |
|  | **Read-Heavy** | Trie Traversal | 41. Autocomplete | Med | ⭐ |
|  | **Read-Heavy** | PageRank/Indexing | 42. Google Search | Hard |  |
|  | **Read-Heavy** | Inverted Index | 43. Elasticsearch (Logs) | Hard |  |
|  | **Read-Heavy** | Dynamic Filters | 44. E-commerce Search | Med |  |
| **8. Fintech** 💸 | **ACID/Trans** | External Sync | 45. Payment Gateway | Hard | ⭐ |
|  | **ACID/Trans** | Double Entry | 46. Digital Wallet | Hard | ⭐ |
|  | **ACID/Trans** | High-Freq Trading | 47. Crypto Exchange | Hard |  |
|  | **Write-Heavy** | Pattern Match | 48. Fraud Detection | Hard |  |
|  | **ACID/Trans** | Order Matching | 49. Stock Brokerage | Hard |  |
|  | **Write-Heavy** | Stream Aggreg. | 50. Ad-Click Aggregator | Hard | ⭐ |
| **9. Concurrency** 🎟️ | **ACID/Trans** | High Contention | 51. Ticketmaster | Hard | ⭐ |
|  | **ACID/Trans** | Inventory Rush | 52. Flash Sale System | Hard | ⭐ |
|  | **ACID/Trans** | Seat Reservation | 53. Flight Reservation | Hard |  |
|  | **Read-Heavy** | Real-time Score | 54. Online Quiz (Kahoot) | Med |  |
|  | **ACID/Trans** | Vote Integrity | 55. Election System | Hard |  |
| **10. Infra/Storage** ⚙️ | **Read-Heavy** | LRU Eviction | 56. Distributed Cache | Med |  |
|  | **Write-Heavy** | Append-only Log | 57. Message Queue (Kafka) | Hard | ⭐ |
|  | **Storage/HA** | Erasure Coding | 58. S3 Object Storage | Hard |  |
|  | **Storage/HA** | Consensus (Paxos) | 59. Key-Value Store | Hard |  |
|  | **Storage/HA** | File Chunking | 60. HDFS (Distributed FS) | Hard |  |
|  | **Write-Heavy** | Time-series Ingest | 61. Logging Pipeline | Med |  |
|  | **Write-Heavy** | Data Collection | 62. Metrics Monitoring | Hard |  |
|  | **Write-Heavy** | Delayed Execution | 63. Task Scheduler | Med | ⭐ |
| **11. Big Data** 📈 | **Write-Heavy** | Counting at Scale | 64. Top K (Heavy Hitters) | Med | ⭐ |
|  | **Read-Heavy** | Bucket Traffic | 65. A/B Testing Platform | Med |  |
|  | **Read-Heavy** | OLAP Queries | 66. Data Warehouse | Hard |  |
| **12. Productivity** ✍️ | **ACID/Trans** | Conflict Resolut. | 67. Google Docs (OT/CRDT) | Hard | ⭐ |
|  | **Write-Heavy** | Event Replay | 68. Whiteboard (Miro) | Hard |  |
|  | **Write-Heavy** | Sandboxed Exec | 69. Code Judge (LeetCode) | Med |  |
|  | **Storage/HA** | Form Schema Mgmt | 70. Google Forms | Easy |  |

---

### 🚀 Let's Pick Your Starting Point

To master HLD, we shouldn't just read the answers. We need to build them together. Based on your goals for top product companies, I recommend starting with one of these three high-impact "anchor" problems. They each teach a fundamental skill that applies to dozens of others.

1. **TinyURL (Pillar 4):** Learn how to scale a database from 1 to 1 billion entries. (Foundational) 🔗
2. **Twitter/News Feed (Pillar 2):** Learn how to handle "Celebrity users" and massive data distribution. (The "Fan-out" King) 📣
3. **Rate Limiter (Pillar 1/10):** Learn how to protect your system from crashing when too many people use it at once. (Logic & Concurrency) 🛡️

**Which of these three should we tackle first?**
