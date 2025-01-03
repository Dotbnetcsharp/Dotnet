int batchSize = 1500; // Set the batch size to 1500
var batches = result.Select((x, index) => new { x, index })
                    .GroupBy(item => item.index / batchSize)
                    .Select(group => group.Select(item => item.x).ToList());

foreach (var batch in batches)
{
    await cosmosDBService.GetDocumentContainer().BulkInsert(batch);
}


The requirement is to improve the property search feature in the NTP API:

1. When a property is searched, check if it belongs to the current tax roll using a REFID.


2. If it has a REFID, confirm it’s on the current tax roll.


3. If it doesn’t, show this message: "Property not found on current Assessor file."



This helps users know if a property is part of the current tax records.
