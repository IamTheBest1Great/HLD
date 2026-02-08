🗺️ The HLD Interview Framework
Requirement Clarification (The "What") 🎯: Define the scope. Is it a global system? Who are the users? What are the core features vs. "nice-to-haves"?

Estimation (The "Scale") 📊: Perform "back-of-the-envelope" math. How many requests per second (RPS)? How much data storage is needed per year? This dictates your database and caching choices.

High-Level Diagram (The "Skeleton") 🦴: Sketch the main components: Clients, Load Balancers, Web Servers, and Databases.

Deep Dive (The "Guts") 🧠: Pick 1-2 critical challenges (e.g., "How do we handle a celebrity with 100M followers?") and propose detailed technical solutions.

Bottlenecks & Trade-offs (The "Reality Check") ⚠️: Identify where the system might fail (Single Point of Failure) and discuss how to improve it.
