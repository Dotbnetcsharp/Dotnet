ReportGenerator.Api/
│
├── Controllers/
│   └── ReportController.cs
│
├── Models/
│   ├── ReportRequest.cs
│   ├── ReportStatus.cs
│   └── ReportStore.cs
│
├── Services/
│   ├── ReportQueueSender.cs
│   ├── ReportQueueProcessor.cs
│   └── ReportHub.cs
│
├── appsettings.json
├── Program.cs
├── Startup.cs (optional if not using minimal hosting model)
└── ReportGenerator.Api.csproj

{
  "AzureServiceBus": {
    "ConnectionString": "<Your Connection String>",
    "TopicBegin": "publication-begin",
    "TopicStatus": "publication-status",
    "SubscriptionStatus": "subscription-status"
  },
  "AllowedHosts": "*"
}

namespace ReportGenerator.Api.Models;

public class ReportRequest
{
    public Guid RequestId { get; set; } = Guid.NewGuid();
    public DateTime FromDate { get; set; }
    public DateTime ToDate { get; set; }
    public string Status { get; set; } = "Pending";
}

namespace ReportGenerator.Api.Models;

public static class ReportStore
{
    public static List<ReportRequest> Reports = new();
}

using Azure.Messaging.ServiceBus;
using ReportGenerator.Api.Models;

namespace ReportGenerator.Api.Services;

public class ReportQueueSender
{
    private readonly IConfiguration _config;
    private readonly ServiceBusClient _client;
    private readonly string _topic;

    public ReportQueueSender(IConfiguration config)
    {
        _config = config;
        _client = new ServiceBusClient(_config["AzureServiceBus:ConnectionString"]);
        _topic = _config["AzureServiceBus:TopicBegin"];
    }

    public async Task SendReportRequestAsync(ReportRequest request)
    {
        var sender = _client.CreateSender(_topic);
        var message = new ServiceBusMessage(System.Text.Json.JsonSerializer.Serialize(request))
        {
            MessageId = request.RequestId.ToString()
        };

        await sender.SendMessageAsync(message);
    }
}
