var query = documentContainer
    .GetItemLinqQueryable<AddressMainEntity>()
    .Where(p => p.PartitionKey == itemResponse.DocumentSearchId.ToString())
    .Skip((int)searchRequest.Qualifiers.RecordsPerPage * ((int)searchRequest.Qualifiers.CurrentPage - 1))
    .Take((int)searchRequest.Qualifiers.RecordsPerPage);
