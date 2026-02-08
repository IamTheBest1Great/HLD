You're right to call that out! To truly gain mastery, we need to be precise. I will provide the full, deep-dive breakdown of **70 distinct problems**, categorized by their architectural patterns, difficulty, and interview frequency.

I'll help you navigate this massive list by asking you which "pattern" we should tackle first once you've had a look.

### 🗺️ The Complete HLD Mastery Roadmap (70 Problems)

| Sub-Category | # | Problem / System | Diff | Freq |
| --- | --- | --- | --- | --- |
| **1. Traffic & Security** 🧱 | 1 | Load Balancer Design | Easy | ⭐ |
|  | 2 | API Gateway Service | Easy | ⭐ |
|  | 3 | Rate Limiter (Distributed) | Med | ⭐ |
|  | 4 | Web Application Firewall (WAF) | Med |  |
|  | 5 | DDOS Protection System | Hard |  |
|  | 6 | Secrets Manager (Vault-like) | Med |  |
| **2. Global Infrastructure** 🔗 | 7 | URL Shortener (TinyURL) | Easy | ⭐ |
|  | 8 | Pastebin (Text Storage) | Easy | ⭐ |
|  | 9 | Distributed ID Generator (Snowflake) | Med | ⭐ |
|  | 10 | Consistent Hashing Implementation | Med | ⭐ |
|  | 11 | Content Delivery Network (CDN) | Med |  |
|  | 12 | Multi-region Traffic Router | Hard |  |
| **3. Content Discovery** 📱 | 13 | News Feed (Push/Pull Models) | Hard | ⭐ |
|  | 14 | Instagram (Media Feed) | Med | ⭐ |
|  | 15 | Pinterest (Grid/Image Feed) | Med |  |
|  | 16 | Twitter (High-Volume Fan-out) | Hard | ⭐ |
|  | 17 | Reddit (Ranking & Upvotes) | Med |  |
|  | 18 | HackerNews (Time-decay Ranking) | Med |  |
|  | 19 | Recommendation Engine (Friend Suggest) | Med |  |
| **4. Communication** 💬 | 20 | WhatsApp/Messenger (1-on-1) | Med | ⭐ |
|  | 21 | Group Chat System | Hard | ⭐ |
|  | 22 | Presence Platform (Online Status) | Med |  |
|  | 23 | Slack (Enterprise Messaging) | Hard |  |
|  | 24 | Notification Service | Med | ⭐ |
|  | 25 | Zoom / Video Conferencing | Hard |  |
|  | 26 | Email Service (Gmail-like) | Hard |  |
| **5. Media & Streaming** 🎥 | 27 | YouTube (Upload & Playback) | Hard | ⭐ |
|  | 28 | Netflix (Global Video Distribution) | Hard |  |
|  | 29 | TikTok (Short-form Feed) | Hard |  |
|  | 30 | Spotify (Music/Audio Streaming) | Med |  |
|  | 31 | Live Streaming Service (Twitch) | Hard |  |
|  | 32 | Video Transcoding Pipeline | Med |  |
| **6. Geospatial Systems** 🚗 | 33 | Uber/Lyft (Matching Service) | Hard | ⭐ |
|  | 34 | Google Maps (Pathfinding) | Hard | ⭐ |
|  | 35 | Yelp/Nearby (Proximity Service) | Med | ⭐ |
|  | 36 | Food Delivery (Order Tracking) | Med |  |
|  | 37 | Public Transit Tracker | Med |  |
|  | 38 | Hotel Booking (Expedia) | Med |  |
|  | 39 | AirBnB (Search & Reservation) | Hard |  |
| **7. Search & Data Retrieval** 🔍 | 40 | Web Crawler | Med | ⭐ |
|  | 41 | Autocomplete/Typeahead | Med | ⭐ |
|  | 42 | Google Search (Ranking/Indexing) | Hard |  |
|  | 43 | Elasticsearch (Log Search) | Hard |  |
|  | 44 | E-commerce Search & Filters | Med |  |
| **8. Fintech & Correctness** 💸 | 45 | Payment Gateway (Stripe-like) | Hard | ⭐ |
|  | 46 | Digital Wallet (Ledger System) | Hard | ⭐ |
|  | 47 | Crypto Exchange (Order Book) | Hard |  |
|  | 48 | Fraud Detection Service | Hard |  |
|  | 49 | Stock Brokerage (Robinhood) | Hard |  |
|  | 50 | Ad-Click Aggregator | Hard | ⭐ |
| **9. High Concurrency** 🎟️ | 51 | Ticketmaster (Booking) | Hard | ⭐ |
|  | 52 | Flash Sale System (Amazon) | Hard | ⭐ |
|  | 53 | Flight Reservation System | Hard |  |
|  | 54 | Online Quiz (Kahoot-like) | Med |  |
|  | 55 | Voting System (Election-scale) | Hard |  |
| **10. Storage & Infra** ⚙️ | 56 | Distributed Cache (Redis-like) | Med |  |
|  | 57 | Distributed Message Queue (Kafka) | Hard | ⭐ |
|  | 58 | S3-like Object Storage | Hard |  |
|  | 59 | Distributed Key-Value Store | Hard |  |
|  | 60 | Distributed File System (HDFS) | Hard |  |
|  | 61 | Logging Pipeline (ELK) | Med |  |
|  | 62 | Metrics & Monitoring (Prometheus) | Hard |  |
|  | 63 | Distributed Task Scheduler | Med | ⭐ |
| **11. Big Data/Analysis** 📈 | 64 | Top K Elements (Heavy Hitters) | Med | ⭐ |
|  | 65 | A/B Testing Platform | Med |  |
|  | 66 | Data Warehouse (BigQuery-like) | Hard |  |
| **12. Productivity Tools** ✍️ | 67 | Google Docs (Real-time Editing) | Hard | ⭐ |
|  | 68 | Collaborative Whiteboard (Miro) | Hard |  |
|  | 69 | Online Code Judge (LeetCode) | Med |  |
|  | 70 | Survey Tool (Google Forms) | Easy |  |

---

Now that you have the full "map," how would you like to start? We can dive into a specific problem, or I can explain the **4 Core Patterns** that will help you solve almost 80% of this list.

1. **The Write-Heavy Pattern:** (Systems like Kafka, Logging, Metrics)
2. **The Read-Heavy/Fan-out Pattern:** (Systems like Twitter, Instagram, News Feeds)
3. **The ACID/Transaction Pattern:** (Systems like Payments, Ticket Booking, Wallets)
4. **The High-Availability/Storage Pattern:** (Systems like TinyURL, S3, Google Drive)

**Which of these patterns should we investigate first?**
