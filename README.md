

List<AddressMainEntity> allAddresses = new List<AddressMainEntity>();

// Initialize the FeedIterator
var feedIterator = documentContainer.GetItemLinqQueryable<AddressMainEntity>()
    .Where(p => p.PartitionKey == itemResponse.DocumentSearchId.ToString())
    .ToFeedIterator();

// Iterate through all pages
while (feedIterator.HasMoreResults)
{
    var response = await feedIterator.ReadNextAsync();
    allAddresses.AddRange(response); // Add current page of results to the list
}

// Proceed with the mapped results
var addressesMapped = _mapper.Map<ICollection<Address>>(allAddresses);



FeedIterator<Address> feedIterator = documentContainer.GetItemLinqQueryable<Address>()
    .Where(p => p.PartitionKey == itemResponse.DocumentSearchId.ToString()) // Replace 'PartitionKey' with the correct property name
    .ToFeedIterator();


List<Address> allAddresses = new List<Address>();
FeedIterator<Address> feedIterator = documentContainer.GetItemLinqQueryable<Address>()
    .Where(p => p.PartitionKey == itemResponse.DocumentSearchId.ToString())
    .ToFeedIterator();

while (feedIterator.HasMoreResults)
{
    FeedResponse<Address> response = await feedIterator.ReadNextAsync();
    allAddresses.AddRange(response);
}

// Proceed with mapping
var addressesMapped = _mapper.Map<ICollection<Address>>(allAddresses);






using System;
using System.IO;
using Newtonsoft.Json.Linq;

class Program
{
    static void Main(string[] args)
    {
        // File path
        string filePath = @"C:\src\data.json";

        try
        {
            // Read the file content
            string jsonContent = File.ReadAllText(filePath);

            // Parse the JSON content
            JObject jsonObject = JObject.Parse(jsonContent);

            // Get the "APNS" array and count its elements
            JArray apnsArray = (JArray)jsonObject["APNS"];
            int count = apnsArray.Count;

            // Print the count
            Console.WriteLine($"The 'APNS' array contains {count} items.");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Error: {ex.Message}");
        }
    }
}



// Assume this is your original DataTable
DataTable originalTable = GetDataTable(); // Replace with your method to fetch the DataTable

// Create a new DataTable with the desired column names and sequence
DataTable modifiedTable = new DataTable();
modifiedTable.Columns.Add("NewMasterID", typeof(int));          // Renaming 'MasterID' to 'NewMasterID'
modifiedTable.Columns.Add("NewCustomerID", typeof(string));     // Renaming 'CustomerID' to 'NewCustomerID'
modifiedTable.Columns.Add("NewDocDate", typeof(DateTime));      // Renaming 'DocDate' to 'NewDocDate'
modifiedTable.Columns.Add("NewDateReceived", typeof(DateTime)); // Renaming 'DateReceived' to 'NewDateReceived'
modifiedTable.Columns.Add("NewRecordingInfo", typeof(string));  // Renaming 'RecordingInfo' to 'NewRecordingInfo'
modifiedTable.Columns.Add("NewDiscrepancyType", typeof(string));// Renaming 'DiscrepancyType' to 'NewDiscrepancyType'

// Transfer data from the original DataTable to the new one in the desired sequence
foreach (DataRow row in originalTable.Rows)
{
    modifiedTable.Rows.Add(
        row["MasterID"],
        row["CustomerID"],
        row["DocDate"],
        row["DateReceived"],
        row["RecordingInfo"],
        row["DiscrepancyType"]
    );
}

// At this point, `modifiedTable` contains the modified structure and data
// You can now save `modifiedTable` or use it as needed
SaveTableToDatabase(modifiedTable); // Replace with your saving logic









Root Cause:
In API/Search, TitleOfficer = QA is passed, but in API/Schedule, TitleOfficer = "". This mismatch causes the run history to not return properly because it depends on the TitleOfficer value.

Fix:
To get the proper run history, the same TitleOfficer value must be passed in both API/Search and API/Schedule. For example, if TitleOfficer = QA is used in the search, it must also be used in the schedule to ensure consistent results.

Additional Note:
If the DateThru parameter in the request is greater than the CountyToDate in the database, the system will automatically use the CountyToDate from the database to ensure accurate data.






Root Cause:
In API/Search, TitleOfficer = QA is passed, but in API/Schedule, TitleOfficer = "". This mismatch causes the run history to not return properly because it depends on the TitleOfficer value.

Fix:
To get the proper run history, the same TitleOfficer value must be passed in both API/Search and API/Schedule. For example, if TitleOfficer = QA is used in the search, it must also be used in the schedule to ensure consistent results.










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
