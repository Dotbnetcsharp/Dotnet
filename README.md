Task<List<SearchItemDTO>> GetItemsByIdsAsync(IEnumerable<Guid> ids);


public async Task<List<SearchItemDTO>> GetItemsByIdsAsync(IEnumerable<Guid> ids)
{
    return await _dbSet
        .Where(x => ids.Contains(x.Id))
        .Select(x => new SearchItemDTO
        {
            Id = x.Id,
            Status = x.Status,
            // Add anything else you want like Name, Branch etc.
        })
        .ToListAsync();
}

public async Task<List<SearchItemDTO>> GetItemsByIdsAsync(IEnumerable<Guid> ids)
{
    return await _dbSet
        .Where(x => ids.Contains(x.Id))
        .Select(x => new SearchItemDTO
        {
            Id = x.Id,
            Status = x.Status,
            // Add anything else you want like Name, Branch etc.
        })
        .ToListAsync();
}
