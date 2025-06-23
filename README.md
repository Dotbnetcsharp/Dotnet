using Azure.Messaging.ServiceBus;
using Microsoft.AspNetCore.SignalR;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.EntityFrameworkCore;
using MyServiceBusApp.Hubs;
using MyServiceBusApp.Model;
using DTSQuickHit.Repositories.Interfaces;
using System.Text.Json;

public class ServiceBusBackgroundService : BackgroundService
{
    private readonly IConfiguration _config;
    private readonly IHubContext<MessageHub> _hubContext;
    private readonly IServiceProvider _serviceProvider;
    private ServiceBusProcessor _processor;

    public ServiceBusBackgroundService(IConfiguration config, IHubContext<MessageHub> hubContext, IServiceProvider serviceProvider)
    {
        _config = config;
        _hubContext = hubContext;
        _serviceProvider = serviceProvider;

        var client = new ServiceBusClient(_config["AzureServiceBus:ConnectionString"]);
        _processor = client.CreateProcessor(
            _config["AzureServiceBus:TopicName"],
            _config["AzureServiceBus:SubscriptionName"],
            new ServiceBusProcessorOptions
            {
                AutoCompleteMessages = false
            });
    }

    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        _processor.ProcessMessageAsync += OnMessageReceived;
        _processor.ProcessErrorAsync += OnError;
        await _processor.StartProcessingAsync(stoppingToken);
    }

    private async Task OnMessageReceived(ProcessMessageEventArgs args)
    {
        var message = args.Message;
        var body = message.Body.ToString();
        var correlationId = message.CorrelationId;
        string status = "Unknown";

        using var scope = _serviceProvider.CreateScope();
        var searchItemRepo = scope.ServiceProvider.GetRequiredService<ISearchItemRepository>();
        var localDb = scope.ServiceProvider.GetRequiredService<AppDbContext>();

        if (Guid.TryParse(correlationId, out Guid guid))
        {
            var result = await searchItemRepo.GetSearchItemWithDetails(guid);
            if (result != null)
            {
                status = result.Status?.ToString() ?? "Unknown";
            }

            var tracked = await localDb.TrackedStatuses.FirstOrDefaultAsync(x => x.CorrelationId == correlationId);

            if (tracked == null)
            {
                localDb.TrackedStatuses.Add(new TrackedStatus
                {
                    CorrelationId = correlationId,
                    Message = body,
                    Status = status,
                    LastUpdated = DateTime.UtcNow
                });
            }
            else
            {
                tracked.Message = body;
                tracked.Status = status;
                tracked.LastUpdated = DateTime.UtcNow;
                localDb.TrackedStatuses.Update(tracked);
            }

            await localDb.SaveChangesAsync();

            await _hubContext.Clients.All.SendAsync("ReceiveMessage", new
            {
                Text = body,
                CorrelationId = correlationId,
                Status = status
            });

            await args.CompleteMessageAsync(message);
        }
    }

    private Task OnError(ProcessErrorEventArgs args)
    {
        Console.WriteLine($"Service Bus Error: {args.Exception.Message}");
        return Task.CompletedTask;
    }

    public override async Task StopAsync(CancellationToken cancellationToken)
    {
        await _processor.StopProcessingAsync();
        await _processor.DisposeAsync();
        await base.StopAsync(cancellationToken);
    }
}
