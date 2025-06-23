public async Task<List<SearchItemDTO>> GetRecentlyUpdatedSearchItems(TimeSpan withinLast)
{
    var cutoff = DateTime.UtcNow - withinLast;

    return await _dbSet
        .Where(x => x.LastModifiedDate >= cutoff)
        .Select(x => new SearchItemDTO
        {
            Id = x.Id,
            Status = x.Status,
            LastModifiedDate = x.LastModifiedDate
            // Add more properties if needed
        })
        .ToListAsync();
}
