if (Guid.TryParse(correlationId, out Guid correlationGuid))
{
    var statusRecord = await externalDb.SearchItems
        .FirstOrDefaultAsync(x => x.Id == correlationGuid);

    if (statusRecord != null)
    {
        status = statusRecord.Status.ToString();
    }
}
else
{
    Console.WriteLine("Invalid CorrelationId format.");
}
