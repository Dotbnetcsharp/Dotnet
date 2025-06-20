var item = await externalDb.SearchItems
    .Where(x => x.Id == correlationGuid)
    .FirstOrDefaultAsync();
