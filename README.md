public interface ITrackedStatusRepository
{
    Task<List<TrackedStatus>> GetAllAsync();
    Task<TrackedStatus?> GetDetailedByCorrelationIdAsync(string correlationId);
}

using Microsoft.EntityFrameworkCore;
using MyServiceBusApp.Model;

public class TrackedStatusRepository : ITrackedStatusRepository
{
    private readonly AppDbContext _context;

    public TrackedStatusRepository(AppDbContext context)
    {
        _context = context;
    }

    public async Task<List<TrackedStatus>> GetAllAsync()
    {
        return await _context.TrackedStatuses
            .Include(x => x.APNSearch) // Optional if you only need SearchedAPN in table view
            .OrderByDescending(x => x.LastUpdated)
            .ToListAsync();
    }

    public async Task<TrackedStatus?> GetDetailedByCorrelationIdAsync(string correlationId)
    {
        return await _context.TrackedStatuses
            .Include(x => x.APNSearch)
            .Include(x => x.Address)
            .Include(x => x.Owner)
            .FirstOrDefaultAsync(x => x.CorrelationId == correlationId);
    }
}

