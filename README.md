
Current Challenges:

Users submitting multiple report requests at once causes queue delays.

Azure Function processes messages one by one → increased wait time.

Users see “In Progress” with no clear visibility on real-time status.

Sometimes users resend the same request due to lack of feedback → duplicates.


POC Title: "NTP Report Tracker: Real-Time Queue-Based Processing System"

Objective: Build a real-time, scalable solution to track report request statuses using Azure Service Bus, .NET Core APIs, and SignalR with a user-friendly React UI.


---

✅ Slide 2: Business Impact & Improvements

Before:

No visibility on request state (Success/Failure/In Progress).

No dashboard to monitor total or failed jobs.

Repeated API hits due to status uncertainty.

Delay in report availability.


After:

Real-time status tracking on dashboard (In Progress, Completed, Failed).

React-based UI for user-side transparency.

Summary of total messages processed from Service Bus.

Reduced duplicate requests, improved resource utilization.



---

✅ Slide 3: Solution Architecture & Tech Stack

Architecture Highlights:

React UI → ASP.NET Core 8 API → Azure Service Bus → Azure Function → Cosmos DB / SQL DB

SignalR used for real-time status updates

Background worker service processes request data and stores status


Tech Stack:

Frontend: React + Bootstrap

Backend: ASP.NET Core 8.0 Web API

Queueing: Azure Service Bus

Processing: Azure Function + Worker Service

Storage: Cosmos DB / SQL Server

Real-Time: SignalR



---

✅ Slide 4: Demo & Screenshots

Include:

📸 Dashboard UI (status cards, filter by type, progress viewer)

📸 Request details with real-time updates

📸 Message count from Service Bus

🎥 Short demo walkthrough (if applicable)



---
