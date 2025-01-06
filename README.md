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
