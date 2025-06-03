
Problem Statement: Queue-Based Report Monitoring with Real-Time Status (POC)

In the existing NTP application:

Users submit search requests (e.g., APN, Address, Owner, or Name) through an ASP.NET Core API.

These requests are sent to Azure Service Bus, where a Function App:

Picks them up,

Processes them,

Saves the results to Cosmos DB, and

Updates the status in a local database.



Each request flows through the states:

Scheduled ➜ In Progress ➜ Completed


---

🎯 Goal of the POC

To build a standalone monitoring tool using:

✅ ASP.NET Core 8.0 API

✅ Azure Service Bus SDK

✅ Cosmos DB

✅ SignalR for real-time updates

✅ React for frontend dashboard



---

🧩 What This Monitoring POC Will Do

1. Monitor all submitted search requests in real-time from Service Bus.


2. Show live request status updates (Scheduled, In Progress, Completed).


3. Allow users to filter/search requests by:

Search Type: APN, Address, Owner, Name

Date Range

Keywords (like APN number or Name)



4. For each Completed request:

Show result data pulled from Cosmos DB

Allow user to download report directly from UI.



5. Users can see all submitted requests across statuses in a single UI — like a live dashboard.


6. If a request is stuck or not processed, it will remain as In Progress or Scheduled, making debugging easier.






[HttpGet("status")]
public IActionResult GetReportsByDateRange([FromQuery] DateTime fromDate, [FromQuery] DateTime toDate)
{
    var results = ReportStore.Reports.Values
        .Where(r => r.FromDate >= fromDate && r.ToDate <= toDate)
        .ToList();

    return Ok(results);
}




{
  "fromDate": "2024-05-01T00:00:00",
  "toDate": "2024-05-28T23:59:59"
}
using ReportGenerator.Api.Services;

var builder = WebApplication.CreateBuilder(args);

// Add services
builder.Services.AddControllers();
builder.Services.AddSignalR();
builder.Services.AddSingleton<ReportQueueSender>();
builder.Services.AddHostedService<ReportQueueProcessor>();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();
app.MapHub<ReportHub>("/reportHub");
app.UseSwagger();
app.UseSwaggerUI();

app.Run();
