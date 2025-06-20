private async Task OnMessageReceived(ProcessMessageEventArgs args)
{
    var message = args.Message;
    var body = message.Body.ToString();
    var correlationId = message.CorrelationId;
    string status = "Unknown";

    using var scope = _serviceProvider.CreateScope();
    var externalDb = scope.ServiceProvider.GetRequiredService<ExternalDbContext>();
    var localDb = scope.ServiceProvider.GetRequiredService<AppDbContext>();

    // Check status in external DB
    var statusRecord = await externalDb.MessageStatuses
        .FirstOrDefaultAsync(x => x.CorrelationId == correlationId);

    if (statusRecord != null)
        status = statusRecord.Status;

    // Save to your own DB
    var tracked = await localDb.TrackedStatuses
        .FirstOrDefaultAsync(x => x.CorrelationId == correlationId);

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

    // Send to SignalR clients
    await _hubContext.Clients.All.SendAsync("ReceiveMessage", new
    {
        Text = body,
        CorrelationId = correlationId,
        Status = status
    });

    await args.CompleteMessageAsync(message);
}
