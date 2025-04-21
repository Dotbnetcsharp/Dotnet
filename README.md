
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>

if (fieldCounter % 2 == 0)
{
    currentRow = new Panel { CssClass = "row mb-3" };
    phNewentryFields.Controls.Add(currentRow);
}

Panel colPanel = new Panel { CssClass = "col-md-6 mb-2" };

Panel formGroup = new Panel { CssClass = "form-group" };
formGroup.Controls.Add(lbl);
formGroup.Controls.Add(ddl); // or txtBox

colPanel.Controls.Add(formGroup);
currentRow.Controls.Add(colPanel);




<style>
    .custom-label {
        font-size: 14px;
        color: #333;
    }
    .custom-input {
        border-radius: 6px;
        padding: 8px;
    }
</style>
Label lbl = new Label
{
    Text = fieldname + ":",
    CssClass = "form-label fw-bold mb-1 custom-label"
};

DropDownList ddl = new DropDownList
{
    ID = "ddl_" + fieldname,
    CssClass = "form-select custom-input w-100"
};



..
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
