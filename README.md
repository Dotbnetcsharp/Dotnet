
List<APNMainEntity> apns = new List<APNMainEntity>();

if (searchRequest.Qualifiers.RecordsPerPage != null && searchRequest.Qualifiers.CurrentPage != null)
{
    var apnQuery = documentContainer
        .GetItemLinqQueryable<APNMainEntity>()
        .Where(p => p.PartitionKey == itemResponse.DocumentsSearchId.ToString() ||
                   (itemResponse.Matchcorelationid != null && p.PartitionKey == itemResponse.Matchcorelationid))
        .Skip((int)searchRequest.Qualifiers.RecordsPerPage * ((int)searchRequest.Qualifiers.CurrentPage - 1))
        .Take((int)searchRequest.Qualifiers.RecordsPerPage);

    var apnsIterator = apnQuery.ToFeedIterator();
    var addressResponse = await apnsIterator.ReadNextAsync();
    apns = addressResponse.ToList();
}
else
{
    var apnQuery = documentContainer
        .GetItemLinqQueryable<APNMainEntity>()
        .Where(p => p.PartitionKey == itemResponse.DocumentsSearchId.ToString() ||
                   (itemResponse.Matchcorelationid != null && p.PartitionKey == itemResponse.Matchcorelationid));

    var apnIterator = apnQuery.ToFeedIterator();
    var addressResponse = await apnIterator.ReadNextAsync();
    apns = addressResponse.ToList();
}


List<AddressMainEntity> addresses = new List<AddressMainEntity>();

if (searchRequest.Qualifiers.RecordsPerPage != null && searchRequest.Qualifiers.CurrentPage != null)
{
    var addressQuery = documentContainer
        .GetItemLinqQueryable<AddressMainEntity>()
        .Where(p => p.PartitionKey == itemResponse.DocumentSearchId.ToString())
        .Skip((int)searchRequest.Qualifiers.RecordsPerPage * ((int)searchRequest.Qualifiers.CurrentPage - 1))
        .Take((int)searchRequest.Qualifiers.RecordsPerPage);

    var addressIterator = addressQuery.ToFeedIterator();
    var addressResponse = await addressIterator.ReadNextAsync();

    addresses = addressResponse.ToList();
}
else
{
    var addressQuery = documentContainer
        .GetItemLinqQueryable<AddressMainEntity>()
        .Where(p => p.PartitionKey == itemResponse.DocumentSearchId.ToString());

    var addressIterator = addressQuery.ToFeedIterator();
    var addressResponse = await addressIterator.ReadNextAsync();

    addresses = addressResponse.ToList();
}






var query = documentContainer
    .GetItemLinqQueryable<AddressMainEntity>()
    .Where(p => p.PartitionKey == itemResponse.DocumentSearchId.ToString())
    .Skip((int)searchRequest.Qualifiers.RecordsPerPage * ((int)searchRequest.Qualifiers.CurrentPage - 1))
    .Take((int)searchRequest.Qualifiers.RecordsPerPage);

var iterator = query.ToFeedIterator();
var response = await iterator.ReadNextAsync();
var addresses = response.ToList();




var query = documentContainer
    .GetItemLinqQueryable<AddressMainEntity>()
    .Where(p => p.PartitionKey == itemResponse.DocumentSearchId.ToString())
    .Skip((int)searchRequest.Qualifiers.RecordsPerPage * ((int)searchRequest.Qualifiers.CurrentPage - 1))
    .Take((int)searchRequest.Qualifiers.RecordsPerPage);
