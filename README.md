NTP Report Monitoring with Real-Time Status
Currently, there is no centralized or live dashboard for monitoring the progress of NTP report generation requests. Each request goes through various processing stages: submission, Service Bus queuing, function app processing, and saving results to Cosmos DB.
🔍 Challenge: Users are unable to view request statuses (Scheduled, In-Progress, Completed, or Failed) in real-time.
