
Map<String, List<String>> headers = httpWrapper.getHeaders();
for (Map.Entry<String, List<String>> entry : headers.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}


if (!(SearchRequest.DocumentNumber?.Any() ?? false) &&  // Check if DocumentNumber is not provided
    !(SearchRequest.CMTDocumentNumber?.Any() ?? false) && // Check if CMTDocumentNumber is not provided
    (!(SearchRequest.Book?.Any() ?? false) || !(SearchRequest.Page?.Any() ?? false))) // Ensure both Book and Page are provided together
{
    throw new BadRequestException("EC-03", "Document Search", "No document information provided. Please provide either Document Number, CMT Document Number, or both Book and Page to perform the search.");
}
using (var connection = new SqlConnection(_connectionStrings.NeoDatabase))
{
    var parameters = new DynamicParameters();
    parameters.Add("@FIPS", documentModel.CountryFips);
    parameters.Add("@instrument_number", documentModel.CMTDocumentNumber);
    parameters.Add("@Book", string.IsNullOrWhiteSpace(documentModel.Book) ? null : documentModel.Book);
    parameters.Add("@Page", string.IsNullOrWhiteSpace(documentModel.Page) ? null : documentModel.Page);
    parameters.Add("@cmtid", string.IsNullOrWhiteSpace(documentModel.DocCMTID) ? null : documentModel.DocCMTID);
    parameters.Add("@IRFlag", IrFlag);
    parameters.Add("@msg", dbType: DbType.String, direction: ParameterDirection.Output, size: 1000); // Output Parameter

    await connection.ExecuteAsync("NEO.SP_usp_GetDocumentByInstWithRef", parameters, commandType: CommandType.StoredProcedure);

    // Retrieve the output parameter
    string txtMessage = parameters.Get<string>("@msg");

    return new DBDocumentModel
    {
        Message = new DBMessage { Message = txtMessage }
    };
}










using (var connection = new SqlConnection(_connectionStrings.NeoDatabase))
{
    var parameters = new DynamicParameters();
    parameters.Add("FIPS", documentModel.CountyFips);
    parameters.Add("instrument_number", string.IsNullOrWhiteSpace(documentModel.CMTDocumentNumber) ? null : documentModel.DocumentNumber);
    parameters.Add("Document", documentModel.CMTDocumentNumber);
    parameters.Add("Book", string.IsNullOrWhiteSpace(documentModel.Book) ? null : documentModel.Book);
    parameters.Add("Page", string.IsNullOrWhiteSpace(documentModel.Page) ? null : documentModel.Page);
    parameters.Add("IRFLAG", iRflag);
    parameters.Add("CMTID", string.IsNullOrWhiteSpace(documentModel.DocCMTID) ? null : documentModel.DocCMTID);

    // Adding OUTPUT parameter
    parameters.Add("msg", dbType: DbType.String, size: 100, direction: ParameterDirection.Output);

    // Execute stored procedure and retrieve the message as a result
    var result = await connection.QueryAsync(
        StoredProceduresName.NEO_SP_usp_GetDocumentByInstWithRref,
        parameters,
        commandTimeout: 30,
        commandType: CommandType.StoredProcedure
    );

    // Retrieve the output message from the parameters
    string outputMessage = parameters.Get<string>("msg");

    // Create an anonymous object with a custom column name
    var resultWithColumnName = new { TxtMessage = outputMessage };

    Console.WriteLine($"Output Message: {resultWithColumnName.TxtMessage}");
}









......yhhj....

using (var connection = new SqlConnection(_connectionStrings.NeoDatabase))
{
    var parameters = new DynamicParameters();
    parameters.Add("FIPS", documentModel.CountyFips);
    parameters.Add("InstrumentNumber", string.IsNullOrWhiteSpace(documentModel.CMTDocumentNumber) ? null : documentModel.DocumentNumber);
    parameters.Add("Book", string.IsNullOrWhiteSpace(documentModel.Book) ? null : documentModel.Book);
    parameters.Add("Page", string.IsNullOrWhiteSpace(documentModel.Page) ? null : documentModel.Page);
    parameters.Add("CMTID", string.IsNullOrWhiteSpace(documentModel.DocCMTID) ? null : documentModel.DocCMTID);
    parameters.Add("IRFLAG", iRflag);

    var query = await connection.QueryAsync<dynamic>(
        StoredProceduresName.NEO_SP_usp_GetDocumentByInstWithRref,
        parameters,
        commandTimeout: 30,
        commandType: CommandType.StoredProcedure
    );

    // Convert to a list
    var resultList = query.ToList();

    if (resultList.Count == 0 || resultList.First().ContainsKey("TxtMessage"))
    {
        Console.WriteLine("Output Message: Invalid Input");
    }
    else
    {
        // Process your data
        Console.WriteLine("Data Found:");
        foreach (var row in resultList)
        {
            Console.WriteLine(row);
        }
    }
}
....




public async Task<DBDocumentModel> GetDocumentByToVisitBkRef(string searchId, IBatchSearchDto documentModel)
{
    var stopwatch = new Stopwatch();
    try
    {
        string icFlag = "";
        if (documentModel.InstrumentSearchType == IRTypeEnum.InstrumentOnly)
            icFlag = "I";
        else if (documentModel.InstrumentSearchType == IRTypeEnum.ReferenceOnly)
            icFlag = "R";
        else if (documentModel.InstrumentSearchType == IRTypeEnum.InstrumentWithReference)
            icFlag = "IR";

        using (var connection = new SqlConnection(connectionStrings.NeoDatabase))
        {
            var query = await connection.QueryMultipleAsync(
                storedProcedureName.NEO_SP_usp_GetDocumentByToVisitBkRef,
                new
                {
                    FIPS = documentModel.CountyFips,
                    instrument_number = string.IsNullOrWhiteSpace(documentModel.CMTDInstrumentNumber) 
                        ? documentModel.DocumentNumber 
                        : documentModel.CMTDInstrumentNumber,
                    book = string.IsNullOrWhiteSpace(documentModel.Book) ? null : documentModel.Book,
                    deed_page = string.IsNullOrWhiteSpace(documentModel.DeedPage) ? null : documentModel.DeedPage,
                    docGUID = string.IsNullOrWhiteSpace(documentModel.DocGUID) ? null : documentModel.DocGUID,
                    msg = "" // Pass an empty string or a relevant message instead of a DataTable
                },
                commandType: CommandType.StoredProcedure
            );

            var result = query.ReadAsync<DBDocumentModel>();
            var documents = query.ReadAsync<DocumentsModel>();

            return new DBDocumentModel
            {
                Message = result.Result.FirstOrDefault()?.Message ?? "",
                Documents = documents.Result.ToList(),
                Count = documents.Result.Count()
            };
        }
    }
    catch (Exception ex)
    {
        return new DBDocumentModel { Message = ex.Message };
    }
}





public async Task<DBDocumentModel> GetDocumentByToVisitBkRef(string searchId, IBatchSearchDto documentModel)
{
    var stopwatch = new Stopwatch();
    try
    {
        string icFlag = "";
        if (documentModel.InstrumentSearchType == IRTypeEnum.InstrumentOnly)
            icFlag = "I";
        else if (documentModel.InstrumentSearchType == IRTypeEnum.ReferenceOnly)
            icFlag = "R";
        else if (documentModel.InstrumentSearchType == IRTypeEnum.InstrumentWithReference)
            icFlag = "IR";

        using (var connection = new SqlConnection(connectionStrings.NeoDatabase))
        {
            var table = new DataTable();
            table.Columns.Add(new DataColumn("msg", typeof(string))); // Ensure this matches SQL table type

            // Add a sample value or modify as needed
            table.Rows.Add("Your message here");

            var query = await connection.QueryMultipleAsync(
                storedProcedureName.NEO_SP_usp_GetDocumentByToVisitBkRef,
                new
                {
                    FIPS = documentModel.CountyFips,
                    instrument_number = string.IsNullOrWhiteSpace(documentModel.CMTDInstrumentNumber) 
                        ? documentModel.DocumentNumber 
                        : documentModel.CMTDInstrumentNumber,
                    book = string.IsNullOrWhiteSpace(documentModel.Book) ? null : documentModel.Book,
                    deed_page = string.IsNullOrWhiteSpace(documentModel.DeedPage) ? null : documentModel.DeedPage,
                    docGUID = string.IsNullOrWhiteSpace(documentModel.DocGUID) ? null : documentModel.DocGUID,
                    msg = table // Pass DataTable as a structured parameter
                },
                commandType: CommandType.StoredProcedure
            );

            var result = query.ReadAsync<DBDocumentModel>();
            var documents = query.ReadAsync<DocumentsModel>();

            return new DBDocumentModel
            {
                Message = result.Result.FirstOrDefault()?.Message ?? "",
                Documents = documents.Result.ToList(),
                Count = documents.Result.Count()
            };
        }
    }
    catch (Exception ex)
    {
        return new DBDocumentModel { Message = ex.Message };
    }
}


/////

using (var connection = new SqlConnection(connectionStrings.NeoDatabase))
{
    var tblMsg = new DataTable();
    tblMsg.Columns.Add(new DataColumn("msg", typeof(string)));

    var query = await connection.QueryMultipleAsync(
        StoredProceduresName.NEO.SP_usp_GetDocumentByInstOrRef,
        new
        {
            FIPS = documentModel.CountyFips,
            instrument_number = !string.IsNullOrWhiteSpace(documentModel.CMTDocumentNumber)
                ? documentModel.CMTDocumentNumber
                : !string.IsNullOrWhiteSpace(documentModel.DocumentNumber)
                    ? documentModel.DocumentNumber
                    : null,
            Book = !string.IsNullOrWhiteSpace(documentModel.Book) ? documentModel.Book : null,
            Page = !string.IsNullOrWhiteSpace(documentModel.Page) ? documentModel.Page : null,
            cmtid = !string.IsNullOrWhiteSpace(documentModel.DocCMTID) ? documentModel.DocCMTID : null,
            IRFlag = irFlag,
            msg = tblMsg.AsTableValuedParameter("[dbo].[udt_msg]") // Adjust as per your database structure
        },
        commandTimeout: 30,
        commandType: CommandType.StoredProcedure
    );

    var docs = await query.ReadAsync<DBDocumentModel>();
    var refs = await query.ReadAsync<DBReferenceModel>();
    var count = await query.ReadFirstOrDefaultAsync<DBDocumentCount>();

    return new DBDocumentsModel
    {
        Documents = docs.ToList(),
        References = refs.ToList(),
        UniqueDocumentCount = count?.DocumentCount
    };
}





////

*Dev note* 
Cosmos RU scale value updated. We are now able to save 3000 records in Cosmos. Russle needs to check typeahead is returning only 3000 records.



List<OwnerNameEntity> allOwners = new List<OwnerNameEntity>();

// Initialize the FeedIterator
var feedIterator = documentContainer.GetItemLinqQueryable<OwnerNameEntity>()
    .Where(p => 
        p.PartitionKey == itemResponse.DocumentSearchId.ToString() || 
        (itemResponse.Matchcorelationid != null && p.PartitionKey == itemResponse.Matchcorelationid.ToString()))
    .ToFeedIterator();

// Iterate through all pages
while (feedIterator.HasMoreResults)
{
    var response = await feedIterator.ReadNextAsync();
    allOwners.AddRange(response); // Add current page of results to the list
}

// Optionally process allOwners here



List<APNMainEntity> allApns = new List<APNMainEntity>();

// Initialize the FeedIterator
var feedIterator = documentContainer.GetItemLinqQueryable<APNMainEntity>()
    .Where(p => 
        p.PartitionKey == itemResponse.DocumentSearchId.ToString() ||
        (itemResponse.Matchcorelationid != null && p.PartitionKey == itemResponse.Matchcorelationid.ToString()))
    .ToFeedIterator();

// Iterate through all pages
while (feedIterator.HasMoreResults)
{
    var response = await feedIterator.ReadNextAsync();
    allApns.AddRange(response); // Add current page of results to the list
}

// Optionally log the end time after fetching all data
apiCosmosLog.EndTime = DateTime.UtcNow;







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
