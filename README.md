using Microsoft.AspNetCore.SignalR;
using Microsoft.EntityFrameworkCore;
using MyServiceBusApp.Hubs;
using MyServiceBusApp.Models;

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

                var externalDb = scope.ServiceProvider.GetRequiredService<ExternalDbContext>();
                var localDb = scope.ServiceProvider.GetRequiredService<AppDbContext>();

                var externalRecords = await externalDb.MessageStatuses.ToListAsync();

                foreach (var record in externalRecords)
                {
                    if (!_lastKnownStatus.TryGetValue(record.CorrelationId, out var lastStatus) || lastStatus != record.Status)
                    {
                        _lastKnownStatus[record.CorrelationId] = record.Status;

                        // Save/update in your internal tracked DB
                        var tracked = await localDb.TrackedStatuses
                            .FirstOrDefaultAsync(x => x.CorrelationId == record.CorrelationId);

                        if (tracked == null)
                        {
                            localDb.TrackedStatuses.Add(new TrackedStatus
                            {
                                CorrelationId = record.CorrelationId,
                                Status = record.Status,
                                LastUpdated = DateTime.UtcNow
                            });
                        }
                        else
                        {
                            tracked.Status = record.Status;
                            tracked.LastUpdated = DateTime.UtcNow;
                            localDb.TrackedStatuses.Update(tracked);
                        }

                        await localDb.SaveChangesAsync();

                        // Broadcast to clients
                        await _hubContext.Clients.All.SendAsync("ReceiveStatusUpdate", new
                        {
                            CorrelationId = record.CorrelationId,
                            Status = record.Status
                        });
                    }
                }

                await Task.Delay(2000, stoppingToken);
            }
        }
    }
}
