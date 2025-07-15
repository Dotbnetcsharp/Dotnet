await _nameDataService.GetDocumentsCountByNameIds(
    azureApiResultData
        .Where(x => x.NameId.HasValue)
        .Select(x => x.NameId.Value),
    searchRequest.CountyFips
);
