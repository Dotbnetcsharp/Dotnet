
using Microsoft.AspNetCore.Mvc;
using ReportGenerator.Api.Models;
using ReportGenerator.Api.Services;

namespace ReportGenerator.Api.Controllers;

[ApiController]
[Route("api/[controller]")]
public class ReportController : ControllerBase
{
    private readonly ReportQueueSender _sender;

    public ReportController(ReportQueueSender sender)
    {
        _sender = sender;
    }

    [HttpPost("request")]
    public async Task<IActionResult> RequestReport([FromBody] ReportRequest request)
    {
        ReportStore.Reports.Add(request);
        await _sender.SendReportRequestAsync(request);
        return Ok(request);
    }

    [HttpGet("all")]
    public IActionResult GetAllReports()
    {
        return Ok(ReportStore.Reports);
    }
}












using Microsoft.AspNetCore.SignalR;

namespace ReportGenerator.Api.Services;

public class ReportHub : Hub { }






using Azure.Messaging.ServiceBus;
using ReportGenerator.Api.Models;
using Microsoft.AspNetCore.SignalR;

namespace ReportGenerator.Api.Services;

public class ReportQueueProcessor : BackgroundService
{
    private readonly IConfiguration _config;
    private readonly IHubContext<ReportHub> _hubContext;
    private readonly ServiceBusProcessor _processor;

    public ReportQueueProcessor(IConfiguration config, IHubContext<ReportHub> hubContext)
    {
        _config = config;
        _hubContext = hubContext;

        var client = new ServiceBusClient(_config["AzureServiceBus:ConnectionString"]);
        _processor = client.CreateProcessor(_config["AzureServiceBus:TopicStatus"], _config["AzureServiceBus:SubscriptionStatus"]);
    }

    protected override Task ExecuteAsync(CancellationToken stoppingToken)
    {
        _processor.ProcessMessageAsync += async args =>
        {
            var body = args.Message.Body.ToString();
            var report = System.Text.Json.JsonSerializer.Deserialize<ReportRequest>(body);
            if (report != null)
            {
                var existing = ReportStore.Reports.FirstOrDefault(x => x.RequestId == report.RequestId);
                if (existing != null)
                {
                    existing.Status = "Completed";
                }

                await _hubContext.Clients.All.SendAsync("ReportUpdated", report);
            }

            await args.CompleteMessageAsync(args.Message);
        };

        _processor.ProcessErrorAsync += args =>
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        };

        return _processor.StartProcessingAsync(stoppingToken);
    }
}
