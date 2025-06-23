using Microsoft.AspNetCore.SignalR;
using Microsoft.EntityFrameworkCore;
using MyServiceBusApp.Hubs;
using MyServiceBusApp.Model;
using DTSQuickHit.Repositories.Contract;

namespace MyServiceBusApp.Services
{
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

                var externalRepo = scope.ServiceProvider.GetRequiredService<ISearchItemRepository>();
                var localDb = scope.ServiceProvider.GetRequiredService<AppDbContext>();

                // Only fetch records updated in the last 10 seconds
                var externalRecords = await externalRepo.GetRecentlyUpdatedSearchItems(TimeSpan.FromSeconds(10));

                foreach (var record in externalRecords)
                {
                    var correlationId = record.Id.ToString();
                    var status = record.Status?.ToString() ?? "Unknown";

                    if (!_lastKnownStatus.TryGetValue(correlationId, out var lastStatus) || lastStatus != status)
                    {
                        _lastKnownStatus[correlationId] = status;

                        // Save or update in internal tracked DB
                        var tracked = await localDb.TrackedStatuses
                            .FirstOrDefaultAsync(x => x.CorrelationId == correlationId);

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

                        // Broadcast to clients
                        await _hubContext.Clients.All.SendAsync("ReceiveStatusUpdate", new
                        {
                            CorrelationId = record.Id,
                            Status = status
                        });
                    }
                }

                await Task.Delay(2000, stoppingToken); // Check every 2 seconds
            }
        }
    }
}
