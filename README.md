public class StatusWatcherService : BackgroundService
{
    private readonly IServiceProvider _serviceProvider;
    private readonly IHubContext<MessageHub> _hubContext;
    private readonly TimeSpan _pollingInterval = TimeSpan.FromSeconds(5);

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
            var repo = scope.ServiceProvider.GetRequiredService<ISearchItemRepository>();
            var localDb = scope.ServiceProvider.GetRequiredService<AppDbContext>();

            var trackedItems = await localDb.TrackedStatuses.ToListAsync(stoppingToken);
            var correlationIds = trackedItems
                .Select(x => Guid.TryParse(x.CorrelationId, out var id) ? id : Guid.Empty)
                .Where(id => id != Guid.Empty)
                .ToList();

            var latestStatuses = await repo.GetItemsByIdsAsync(correlationIds);

            foreach (var item in latestStatuses)
            {
                var local = trackedItems.FirstOrDefault(x => x.CorrelationId == item.Id.ToString());
                if (local != null && local.Status != item.Status)
                {
                    local.Status = item.Status;
                    local.LastUpdated = DateTime.UtcNow;

                    localDb.TrackedStatuses.Update(local);
                    await localDb.SaveChangesAsync();

                    await _hubContext.Clients.All.SendAsync("ReceiveMessage", new
                    {
                        CorrelationId = local.CorrelationId,
                        Text = local.Message,
                        Status = local.Status
                    });
                }
            }

            await Task.Delay(_pollingInterval, stoppingToken);
        }
    }
}
