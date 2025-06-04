
Frontend: React (with SignalR Client for real-time updates)

Backend: ASP.NET Core 8.0 API (includes SignalR Hub for pushing live status updates)

Middleware: Azure Service Bus (message queuing)

Processing: Azure Function App, Background Workers (request processing and Cosmos DB saving)

Storage: Cosmos DB (for processed results), SQL Server (for initial tracking)




NTP Report Monitoring with Real-Time Status
Currently, there is no centralized or live dashboard for monitoring the progress of NTP report generation requests. Each request goes through various processing stages: submission, Service Bus queuing, function app processing, and saving results to Cosmos DB.
🔍 Challenge: Users are unable to view request statuses (Scheduled, In-Progress, Completed, or Failed) in real-time.
