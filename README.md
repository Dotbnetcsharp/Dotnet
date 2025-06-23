using Microsoft.AspNetCore.SignalR;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.EntityFrameworkCore;
using MyServiceBusApp.Hubs;
using MyServiceBusApp.Model;
using DTSQuickHit.Repositories.Interfaces;

public class StatusWatcherService : BackgroundService
{
    private readonly IServiceProvider _serviceProvider;
    private readonly IHubContext<MessageHub> _hubContext;
    private readonly Dictionary<string, string> _lastKnownStatus = new();

    public StatusWatcherService(IServiceProvider serviceProvider, IHubContext<MessageHub> hubContext)
    {
        _serviceProvider = serviceProvider;
        _hubContext = hubContext;
    }

    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        while (!stoppingToken.IsCancellationRequested)
        {
            using var scope = _serviceProvider.CreateScope();
            var searchItemRepo = scope.ServiceProvider.GetRequiredService<ISearchItemRepository>();
            var localDb = scope.ServiceProvider.GetRequiredService<AppDbContext>();

            var externalRecords = await searchItemRepo.GetAllSearchItems();

            foreach (var record in externalRecords)
            {
                string correlationId = record.Id.ToString();
                string status = record.Status?.ToString() ?? "Unknown";

                if (!_lastKnownStatus.TryGetValue(correlationId, out var lastStatus) || lastStatus != status)
                {
                    _lastKnownStatus[correlationId] = status;

                    var tracked = await localDb.TrackedStatuses.FirstOrDefaultAsync(x => x.CorrelationId == correlationId);
                    if (tracked == null)
                    {
                        localDb.TrackedStatuses.Add(new TrackedStatus
                        {
                            CorrelationId = correlationId,
                            Status = status,
                            LastUpdated = DateTime.UtcNow
                        });
                    }
                    else
                    {
                        tracked.Status = status;
                        tracked.LastUpdated = DateTime.UtcNow;
                        localDb.TrackedStatuses.Update(tracked);
                    }

                    await localDb.SaveChangesAsync();

                    await _hubContext.Clients.All.SendAsync("ReceiveStatusUpdate", new
                    {
                        CorrelationId = correlationId,
                        Status = status
                    });
                }
            }

            await Task.Delay(2000, stoppingToken);
        }
    }
}
