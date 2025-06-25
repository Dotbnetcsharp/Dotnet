
using var scope = serviceProvider.CreateScope();

var externalRepo = scope.ServiceProvider.GetRequiredService<ISearchItemRepository>();
var localDb = scope.ServiceProvider.GetRequiredService<AppDbContext>();

if (Guid.TryParse(correlationId, out Guid correlationGuid))
{
    var result = await externalRepo.GetSearchItemWithDetails(correlationGuid);

    if (result != null)
    {
        var status = result.Status?.ToString() ?? "Unknown";

        var tracked = await localDb.TrackedStatuses
            .FirstOrDefaultAsync(x => x.Id == correlationGuid);

        if (tracked == null)
        {
            var trackedStatus = new TrackedStatus
            {
                Id = correlationGuid,
                Status = status,
                ErrorTitle = result.ErrorTitle,
                ErrorMessage = result.ErrorMessage,
                ErrorData = result.ErrorData,
                CountyFips = result.CountyFips,
                StateCode = result.StateCode,
                ParentId = result.ParentId,
                DocumentsSearchId = result.DocumentsSearchId,
                SearchType = result.SearchType,
                DateFrom = result.DateFrom,
                DateThru = result.DateThru,
                Branch = result.Branch,
                User = result.User,
                IsMergeResult = result.IsMergeResult,
                MergeSearchId = result.MergeSearchId,
                ParentSearchId = result.ParentSearchId,
                MatchCorrelationId = result.MatchCorrelationId,
                ClientIp = result.ClientIp,
                LastModifiedDate = result.LastModifiedDate
            };

            // Add trackedStatus to DB
            localDb.TrackedStatuses.Add(trackedStatus);

            // If result contains APNSearch, insert that too
            if (result.APNSearch != null)
            {
                var apnSearch = new APNSearch
                {
                    SearchItemId = correlationGuid, // FK and PK
                    SearchedAPN = result.APNSearch.SearchedAPN,
                    PropertyId = result.APNSearch.PropertyId
                };

                localDb.APNSearches.Add(apnSearch);
            }
        }
        else
        {
            // Minimal update
            tracked.Status = status;
            tracked.LastModifiedDate = result.LastModifiedDate;
        }

        await localDb.SaveChangesAsync();
    }
}
