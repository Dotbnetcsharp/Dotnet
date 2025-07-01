
using Microsoft.EntityFrameworkCore;
using MyServiceBusApp.Model;

public class TrackedStatusRepository : ITrackedStatusRepository
{
    private readonly AppDbContext _context;

    public TrackedStatusRepository(AppDbContext context)
    {
        _context = context; // 👈 This is where AppDbContext comes in
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
