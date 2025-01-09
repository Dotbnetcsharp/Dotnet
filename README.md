
if (parentsearchid == null)
{
    if (apnSearchRequest?.Qualifiers != null && Countyinfo.CountyFromDate != null)
    {
        if (Countyinfo.CountyFromDate > apnSearchRequest.Qualifiers.DateFrom)
        {
            DateFrom = apnSearchRequest.Qualifiers.DateFrom;
        }
        else if (Countyinfo.CountyToDate < apnSearchRequest.Qualifiers.DateFrom)
        {
            DateFrom = Countyinfo.CountyToDate;
        }
        else if (apnSearchRequest?.Qualifiers != null)
        {
            DateFrom = apnSearchRequest.Qualifiers.DateFrom;
        }
        else
        {
            DateFrom = null;
        }
    }
    else
    {
        DateFrom = null;
    }
}
else
{
    if (apnSearchRequest?.Qualifiers != null)
    {
        DateFrom = apnSearchRequest?.Qualifiers?.DateFrom;
    }
    else
    {
        DateFrom = null;
    }
}





if (parentsearchid == null)
{
    if (apnSearchRequest?.Qualifiers != null && Countyinfo.CountyToDate != null)
    {
        if (Countyinfo.CountyToDate > apnSearchRequest?.Qualifiers?.DateThru)
        {
            DateThru = apnSearchRequest?.Qualifiers?.DateThru;
        }
        else
        {
            DateThru = Countyinfo.CountyToDate;
        }
    }
    else if (apnSearchRequest?.Qualifiers != null)
    {
        DateThru = apnSearchRequest?.Qualifiers?.DateThru;
    }
    else
    {
        DateThru = null;
    }
}
else
{
    if (apnSearchRequest?.Qualifiers != null)
    {
        DateThru = apnSearchRequest?.Qualifiers?.DateThru;
    }
    else
    {
        DateThru = null;
    }
}














int batchSize = 1500; // Set the batch size to 1500
var batches = result.Select((x, index) => new { x, index })
                    .GroupBy(item => item.index / batchSize)
                    .Select(group => group.Select(item => item.x).ToList());

foreach (var batch in batches)
{
    await cosmosDBService.GetDocumentContainer().BulkInsert(batch);
}


int batchSize = 1500; // Set the batch size
var batches = result.Chunk(batchSize); // Use LINQ's Chunk method to create batches

foreach (var batch in batches)
{
    await cosmosDBService.GetDocumentContainer().BulkInsert(batch.ToList());
}
