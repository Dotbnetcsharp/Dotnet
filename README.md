
var isDuplicate = await _context.Customers
    .Where(c => c.CustomerNumber == customerNumber 
                || c.SourceUID == sourceUID 
                || c.Mobile == mobile)
    .AnyAsync(c => c.Id != selectedRowId);





var isDuplicate = await _context.Customers
    .GroupBy(c => new { c.CustomerNumber, c.SourceUID, c.Mobile })
    .AnyAsync(g => g.Count() > 1);



wo-- Declare and populate the table-valued parameters
DECLARE @instrument_number AS [dbo].[udt_instrnum];
DECLARE @book_page AS [dbo].[udt_bookpage];

-- Example data for instrument numbers
INSERT INTO @instrument_number (instrument_number)
VALUES 
    ('123456789'),
    ('987654321');

-- Example data for books and pages
INSERT INTO @book_page (book, page)
VALUES 
    ('Book1', 'Page1'),
    ('Book2', 'Page2');

-- Now execute the stored procedure
EXEC [dbo].[SP_usp_GetDocumentByInstrument]
    @FIPS = 12345, -- Replace with actual FIPS value
    @instrument_number = @instrument_number,
    @book_page = @book_page;








using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.Linq;
using System.Text.Json;

[ApiController]
[Route("[controller]")]
public class UsersController : ControllerBase
{
    [HttpPost]
    public IActionResult Post([FromBody] JsonElement jsonElement)
    {
        // Check if the JSON has the 'Name' property as an array
        if (jsonElement.TryGetProperty("Name", out JsonElement namesElement) && namesElement.ValueKind == JsonValueKind.Array)
        {
            // Create a list to keep track of unique names
            var uniqueNameList = new List<string>();
            var duplicateNameMessages = new List<string>();

            // Iterate through each name in the array
            foreach (var name in namesElement.EnumerateArray())
            {
                // Convert the name object to a serialized string for comparison
                var nameString = JsonSerializer.Serialize(name);

                if (uniqueNameList.Contains(nameString))
                {
                    // If the name is already in the unique list, add a duplicate message
                    duplicateNameMessages.Add($"Duplicate entry detected: {nameString}");
                }
                else
                {
                    // Add the name to the unique list if not already present
                    uniqueNameList.Add(nameString);
                }
            }

            // Check for duplicates and process unique entries
            if (duplicateNameMessages.Any())
            {
                // Return duplicate messages if any duplicates were found
                return Ok(new { Messages = duplicateNameMessages });
            }
            else
            {
                // Process the unique names here
                // You can implement any processing logic you need for unique entries

                return Ok(new { Message = "User data processed successfully.", UniqueEntries = uniqueNameList });
            }
        }

        // Return an error if the 'Name' property is missing or not an array
        return BadRequest(new { Message = "'Name' property missing or not an array." });
    }
}


.......







[ApiController]
[Route("[controller]")]
public class DataController : ControllerBase
{
    private readonly SharedProperties _sharedProperties;
    private readonly IHttpClientFactory _httpClientFactory;

    public DataController(SharedProperties sharedProperties, IHttpClientFactory httpClientFactory)
    {
        _sharedProperties = sharedProperties;
        _httpClientFactory = httpClientFactory;
    }

    [HttpGet]
    public async Task<IActionResult> Get()
    {
        var apnSearchRequest = new { APN = "someValue" }; // Example request object
        var apnSearchResponse = new APNTypeAheadResponse();

        apnSearchResponse.APNs = await CallTypeAheadAPNSearchAzureApi(apnSearchRequest.APN);

        // Assign values to shared properties
        _sharedProperties.APNs = apnSearchResponse.APNs;

        return Ok(apnSearchResponse);
    }

    private async Task<List<string>> CallTypeAheadAPNSearchAzureApi(string apn)
    {
        var client = _httpClientFactory.CreateClient();
        var response = await client.GetAsync($"https://example.com/api/apnsearch?apn={apn}");
        if (response.IsSuccessStatusCode)
        {
            var data = await response.Content.ReadFromJsonAsync<List<string>>();
            return data;
        }
        return new List<string>();
    }
}






bool allSameTransactionHistoryId = itemResponses.All(item => item.transitionhystoryId == itemResponses.First().transitionhystoryId);





if (data.TitleDocs != null && data.TitleDocs.Count() > 0) 
{
    HashSet<int> existingDocumentIds = titledocs.Select(td => td.DocumentId).ToHashSet();

    // Pre-check for duplicates
    bool hasDuplicates = data.TitleDocs.Any(doc => existingDocumentIds.Contains(doc.DocumentId));

    if (hasDuplicates)
    {
        Console.WriteLine("One or more documents already present in the list. Skipping the loop.");
    }
    else
    {
        foreach (var doc in data.TitleDocs)
        {
            TitleDoc titledoc = new TitleDoc
            {
                DocumentId = doc.DocumentId,
                // Copy other properties if necessary
            };

            titledocs.Add(titledoc);
        }
    }
}







if (data.TitleDocs != null && data.TitleDocs.Count() > 0)
{
    HashSet<int> documentIds = new HashSet<int>();
    List<TitleDoc> titledocs = new List<TitleDoc>();

    // Loop to check for duplicates
    foreach (var doc in data.TitleDocs)
    {
        if (!documentIds.Contains(doc.DocumentId))
        {
            documentIds.Add(doc.DocumentId);
        }
        else
        {
            Console.WriteLine($"Document with ID {doc.DocumentId} is already present and will not be added to the list.");
        }
    }

    // Loop to add documents to titledocs
    foreach (var doc in data.TitleDocs)
    {
        if (documentIds.Contains(doc.DocumentId))
        {
            TitleDoc titledoc = new TitleDoc();
            titledoc = doc;
            titledocs.Add(titledoc);

            // Once added, remove it from the set to avoid duplicate entries
            documentIds.Remove(doc.DocumentId);
        }
    }
}










if (data.TitleDocs != null && data.TitleDocs.Count() > 0) 
{
    HashSet<int> documentIds = new HashSet<int>();
    List<TitleDoc> titledocs = new List<TitleDoc>();

    foreach (var doc in data.TitleDocs)
    {
        if (documentIds.Contains(doc.DocumentId))
        {
            Console.WriteLine($"Document with ID {doc.DocumentId} is already present and will not be added to the list.");
        }
        else
        {
            TitleDoc titledoc = new TitleDoc();
            titledoc = doc;
            titledocs.Add(titledoc);
            documentIds.Add(doc.DocumentId);
        }
    }
}






if (data.TitleDocs != null && data.TitleDocs.Count() > 0) 
{
    HashSet<int> documentIds = new HashSet<int>();
    List<TitleDoc> titledocs = new List<TitleDoc>();

    foreach (var doc in data.TitleDocs)
    {
        if (documentIds.Add(doc.DocumentId)) // Add returns false if the item already exists
        {
            titledocs.Add(doc);
        }
    }
}








DECLARE @dbName NVARCHAR(255);
DECLARE @sql NVARCHAR(MAX);
DECLARE @successLog TABLE (DatabaseName NVARCHAR(255));
DECLARE @errorLog TABLE (DatabaseName NVARCHAR(255), ErrorMsg NVARCHAR(MAX));

DECLARE db_cursor CURSOR FOR
SELECT name
FROM sys.databases;

OPEN db_cursor;
FETCH NEXT FROM db_cursor INTO @dbName;

WHILE @@FETCH_STATUS = 0
BEGIN
    BEGIN TRY
        SET @sql = 'USE [' + @dbName + '];';
        EXEC sp_executesql @sql;
        PRINT 'Successfully connected to ' + @dbName;
        INSERT INTO @successLog (DatabaseName)
        VALUES (@dbName);
    END TRY
    BEGIN CATCH
        PRINT 'Failed to connect to ' + @dbName;
        INSERT INTO @errorLog (DatabaseName, ErrorMsg)
        VALUES (@dbName, ERROR_MESSAGE());
    END CATCH;
    FETCH NEXT FROM db_cursor INTO @dbName;
END;

CLOSE db_cursor;
DEALLOCATE db_cursor;

-- Display the successful connections
PRINT 'Successful Connections:';
SELECT * FROM @successLog;

-- Display the failed connections
PRINT 'Failed Connections:';
SELECT * FROM @errorLog;








DECLARE @dbName NVARCHAR(255);
DECLARE @sql NVARCHAR(MAX);
DECLARE @errorLog TABLE (DatabaseName NVARCHAR(255), ErrorMsg NVARCHAR(MAX));

DECLARE db_cursor CURSOR FOR
SELECT name
FROM sys.databases;

OPEN db_cursor;
FETCH NEXT FROM db_cursor INTO @dbName;

WHILE @@FETCH_STATUS = 0
BEGIN
    BEGIN TRY
        SET @sql = 'USE [' + @dbName + '];';
        EXEC sp_executesql @sql;
        PRINT 'Successfully connected to ' + @dbName;
    END TRY
    BEGIN CATCH
        PRINT 'Failed to connect to ' + @dbName;
        INSERT INTO @errorLog (DatabaseName, ErrorMsg)
        VALUES (@dbName, ERROR_MESSAGE());
    END CATCH;
    FETCH NEXT FROM db_cursor INTO @dbName;
END;

CLOSE db_cursor;
DEALLOCATE db_cursor;

-- Display the error log
SELECT * FROM @errorLog;










using System;
using System.Collections.Generic;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""ΑΡΝ"": ""10-00092-993-620-0001"",
            ""ΑΡΝ"": ""10-00092-993-620-0002"",
            ""Details"": {
                ""ΑΡΝ"": ""10-00092-993-620-0003"",
                ""Nested"": {
                    ""ΑΡΝ"": ""10-00092-993-620-0004""
                }
            }
        }";

        // Parse JSON string
        JsonDocument doc = JsonDocument.Parse(json);
        JsonElement root = doc.RootElement;

        // Check for duplicate keys
        CheckForDuplicateKeys(root);
    }

    static void CheckForDuplicateKeys(JsonElement root)
    {
        Stack<JsonElement> stack = new Stack<JsonElement>();
        stack.Push(root);

        while (stack.Count > 0)
        {
            JsonElement element = stack.Pop();

            if (element.ValueKind == JsonValueKind.Object)
            {
                // Track seen keys in the current object
                HashSet<string> seenKeys = new HashSet<string>();
                List<string> duplicateKeys = new List<string>();

                foreach (JsonProperty property in element.EnumerateObject())
                {
                    if (!seenKeys.Add(property.Name))
                    {
                        duplicateKeys.Add(property.Name);
                    }
                }

                // Print duplicate keys found in the current object
                foreach (var key in duplicateKeys)
                {
                    Console.WriteLine($"Duplicate key found: {key}");
                }

                // Push nested objects onto the stack
                foreach (JsonProperty property in element.EnumerateObject())
                {
                    stack.Push(property.Value);
                }
            }
            else if (element.ValueKind == JsonValueKind.Array)
            {
                foreach (JsonElement item in element.EnumerateArray())
                {
                    stack.Push(item);
                }
            }
        }
    }
}


........




using System;
using System.Collections.Generic;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""ΑΡΝ"": ""10-00092-993-620-0001"",
            ""ΑΡΝ"": ""10-00092-993-620-0002"",
            ""Details"": {
                ""ΑΡΝ"": ""10-00092-993-620-0003"",
                ""Nested"": {
                    ""ΑΡΝ"": ""10-00092-993-620-0004""
                }
            }
        }";

        // Parse JSON string
        JObject parsedJson = JObject.Parse(json);

        // Check for duplicate keys
        CheckForDuplicateKeys(parsedJson);
    }

    static void CheckForDuplicateKeys(JToken token)
    {
        if (token.Type == JTokenType.Object)
        {
            // Track seen keys in the current object
            HashSet<string> seenKeys = new HashSet<string>();
            List<string> duplicateKeys = new List<string>();

            foreach (JProperty property in token.Children<JProperty>())
            {
                if (!seenKeys.Add(property.Name))
                {
                    duplicateKeys.Add(property.Name);
                }
            }

            // Print duplicate keys found in the current object
            foreach (var key in duplicateKeys)
            {
                Console.WriteLine($"Duplicate key found: {key}");
            }

            // Recursively check nested objects
            foreach (JProperty property in token.Children<JProperty>())
            {
                CheckForDuplicateKeys(property.Value);
            }
        }
        else if (token.Type == JTokenType.Array)
        {
            foreach (JToken item in token.Children())
            {
                CheckForDuplicateKeys(item);
            }
        }
    }
}


,.........


using System;
using System.Collections.Generic;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""ΑΡΝ"": ""10-00092-993-620-0001"",
            ""ΑΡΝ"": ""10-00092-993-620-0002"",
            ""Details"": {
                ""ΑΡΝ"": ""10-00092-993-620-0003"",
                ""Nested"": {
                    ""ΑΡΝ"": ""10-00092-993-620-0004""
                }
            }
        }";

        // Parse JSON string
        JsonDocument doc = JsonDocument.Parse(json);
        JsonElement root = doc.RootElement;

        // Check for duplicate keys
        CheckForDuplicateKeys(root);
    }

    static void CheckForDuplicateKeys(JsonElement element)
    {
        if (element.ValueKind == JsonValueKind.Object)
        {
            // Track seen keys in the current object
            HashSet<string> seenKeys = new HashSet<string>();
            List<string> duplicateKeys = new List<string>();

            foreach (JsonProperty property in element.EnumerateObject())
            {
                if (!seenKeys.Add(property.Name))
                {
                    duplicateKeys.Add(property.Name);
                }
            }

            // Print duplicate keys found in the current object
            foreach (var key in duplicateKeys)
            {
                Console.WriteLine($"Duplicate key found: {key}");
            }

            // Recursively check nested objects
            foreach (JsonProperty property in element.EnumerateObject())
            {
                CheckForDuplicateKeys(property.Value);
            }
        }
        else if (element.ValueKind == JsonValueKind.Array)
        {
            foreach (JsonElement item in element.EnumerateArray())
            {
                CheckForDuplicateKeys(item);
            }
        }
    }
}




using System;
using System.Collections.Generic;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""ΑΡΝ"": ""10-00092-993-620-0001"",
            ""ΑΡΝ"": ""10-00092-993-620-0002"",
            ""Details"": {
                ""ΑΡΝ"": ""10-00092-993-620-0003"",
                ""Nested"": {
                    ""ΑΡΝ"": ""10-00092-993-620-0004""
                }
            }
        }";

        // Parse JSON string
        JsonDocument doc = JsonDocument.Parse(json);
        JsonElement root = doc.RootElement;

        // Check for duplicate keys
        CheckForDuplicateKeys(root);
    }

    static void CheckForDuplicateKeys(JsonElement element)
    {
        if (element.ValueKind == JsonValueKind.Object)
        {
            // Track seen keys in the current object
            HashSet<string> seenKeys = new HashSet<string>();
            List<string> duplicateKeys = new List<string>();

            foreach (JsonProperty property in element.EnumerateObject())
            {
                if (!seenKeys.Add(property.Name))
                {
                    duplicateKeys.Add(property.Name);
                }
            }

            // Print duplicate keys found in the current object
            foreach (var key in duplicateKeys)
            {
                Console.WriteLine($"Duplicate key found: {key}");
            }

            // Recursively check nested objects
            foreach (JsonProperty property in element.EnumerateObject())
            {
                CheckForDuplicateKeys(property.Value);
            }
        }
        else if (element.ValueKind == JsonValueKind.Array)
        {
            foreach (JsonElement item in element.EnumerateArray())
            {
                CheckForDuplicateKeys(item);
            }
        }
    }
}





using System;
using System.Collections.Generic;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""ΑΡΝ"": ""10-00092-993-620-0001"",
            ""ΑΡΝ"": ""10-00092-993-620-0002"",
            ""Details"": {
                ""ΑΡΝ"": ""10-00092-993-620-0003""
            }
        }";

        // Parse JSON string and check for duplicate keys
        CheckForDuplicateKeys(json);
    }

    static void CheckForDuplicateKeys(string json)
    {
        HashSet<string> seenKeys = new HashSet<string>();
        Dictionary<string, int> duplicateCounts = new Dictionary<string, int>();

        // Remove whitespaces (optional step for cleaner JSON processing)
        json = json.Replace(" ", "");

        // Initialize variables to track current parsing state
        bool inObject = false;
        int objectDepth = 0;
        int startIndex = 0;

        for (int i = 0; i < json.Length; i++)
        {
            char currentChar = json[i];

            if (currentChar == '{')
            {
                if (!inObject)
                {
                    inObject = true;
                    objectDepth++;
                    startIndex = i + 1;
                }
                else
                {
                    objectDepth++;
                }
            }
            else if (currentChar == '}')
            {
                objectDepth--;

                if (objectDepth == 0)
                {
                    inObject = false;
                    string objectString = json.Substring(startIndex, i - startIndex + 1);

                    // Process the current object string for duplicate keys
                    ProcessObjectForDuplicates(objectString, seenKeys, duplicateCounts);
                }
            }
        }

        // Print duplicate keys found
        foreach (var kvp in duplicateCounts)
        {
            Console.WriteLine($"Duplicate key found: {kvp.Key}");
        }
    }

    static void ProcessObjectForDuplicates(string objectString, HashSet<string> seenKeys, Dictionary<string, int> duplicateCounts)
    {
        // Parse the object string into JSON element
        JsonDocument doc = JsonDocument.Parse(objectString);
        JsonElement root = doc.RootElement;

        // Check for duplicate keys within the object
        foreach (JsonProperty property in root.EnumerateObject())
        {
            string key = property.Name;

            if (!seenKeys.Add(key))
            {
                // Key is a duplicate
                if (duplicateCounts.ContainsKey(key))
                {
                    duplicateCounts[key]++;
                }
                else
                {
                    duplicateCounts[key] = 2; // Start count at 2 for the first duplicate found
                }
            }
        }
    }
}




...........
using System;
using System.Collections.Generic;
using System.Text.RegularExpressions;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""ΑΡΝ"": ""10-00092-993-620-0001"",
            ""ΑΡΝ"": ""10-00092-993-620-0002"",
            ""Details"": {
                ""ΑΡΝ"": ""10-00092-993-620-0003""
            }
        }";

        // Parse JSON string and check for duplicate keys
        CheckForDuplicateKeys(json);
    }

    static void CheckForDuplicateKeys(string json)
    {
        Dictionary<string, int> keyCounts = new Dictionary<string, int>();

        // Regular expression to match JSON object property names
        Regex regex = new Regex("\"([^\"]+)\":");

        // Find all matches of property names in JSON string
        MatchCollection matches = regex.Matches(json);

        foreach (Match match in matches)
        {
            string key = match.Groups[1].Value;

            if (keyCounts.ContainsKey(key))
            {
                keyCounts[key]++;
            }
            else
            {
                keyCounts[key] = 1;
            }
        }

        // Print keys that have more than one occurrence (duplicates)
        foreach (var kvp in keyCounts)
        {
            if (kvp.Value > 1)
            {
                Console.WriteLine($"Duplicate key found: {kvp.Key}");
            }
        }
    }
}



using System;
using System.Collections.Generic;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        [
            {
                ""ΑΡΝ"": ""10-00092-993-620-0001"",
                ""ΑΡΝ"": ""10-00092-993-620-0002""
            },
            {
                ""ΑΡΝ"": ""10-00092-993-620-0003""
            }
        ]";

        // Parse JSON
        JsonDocument doc = JsonDocument.Parse(json);
        JsonElement root = doc.RootElement;

        // Call the method to find and print duplicate property names in the same scope
        FindAndPrintDuplicateProperties(root);
    }

    static void FindAndPrintDuplicateProperties(JsonElement element)
    {
        switch (element.ValueKind)
        {
            case JsonValueKind.Object:
                HashSet<string> seenProperties = new HashSet<string>();
                foreach (JsonProperty property in element.EnumerateObject())
                {
                    string propertyName = property.Name;
                    if (!seenProperties.Add(propertyName)) // If it returns false, it means it's a duplicate
                    {
                        Console.WriteLine($"Duplicate property found: {propertyName}");
                    }
                }
                break;
            case JsonValueKind.Array:
                foreach (JsonElement item in element.EnumerateArray())
                {
                    // Recursively check each item in the array
                    FindAndPrintDuplicateProperties(item);
                }
                break;
            default:
                // For primitive types and unsupported kinds, we don't need to check for duplicates.
                break;
        }
    }
}


using System;
using System.Collections.Generic;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""UserId"": ""skunarrajak@firstam.com"",
            ""Branch"": ""ANE"",
            ""IsMergeResults"": true,
            ""Property"": [
                {
                    ""ΑΡΝ"": ""10-00092-993-620-0000""
                },
                {
                    ""ΑΡΝ"": ""10-00092-993-620-0000""
                }
            ],
            ""Details"": {
                ""APN"": ""123""
            }
        }";

        // Parse JSON
        JsonDocument doc = JsonDocument.Parse(json);
        JsonElement root = doc.RootElement;

        // Call the method to find and print duplicate property names
        FindAndPrintDuplicateProperties(root);
    }

    static void FindAndPrintDuplicateProperties(JsonElement element, HashSet<string> seenProperties = null)
    {
        if (seenProperties == null)
            seenProperties = new HashSet<string>();

        switch (element.ValueKind)
        {
            case JsonValueKind.Object:
                foreach (JsonProperty property in element.EnumerateObject())
                {
                    string propertyName = property.Name;
                    if (!seenProperties.Add(propertyName)) // If it returns false, it means it's a duplicate
                    {
                        Console.WriteLine($"Duplicate property found: {propertyName}");
                    }

                    // Recursively check nested properties
                    FindAndPrintDuplicateProperties(property.Value, seenProperties);
                }
                break;
            case JsonValueKind.Array:
                foreach (JsonElement item in element.EnumerateArray())
                {
                    // Recursively check items in array
                    FindAndPrintDuplicateProperties(item, seenProperties);
                }
                break;
            default:
                // For primitive types and unsupported kinds, we don't need to check for duplicates.
                break;
        }
    }
}
.....
using System;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""UserId"": ""skunarrajak@firstam.com"",
            ""Branch"": ""ANE"",
            ""IsMergeResults"": true,
            ""Property"": [
                {
                    ""ΑΡΝ"": ""10-00092-993-620-0000""
                },
                {
                    ""ΑΡΝ"": ""10-00092-993-620-0000""
                }
            ],
            ""Details"": {
                ""APN"": ""123""
            }
        }";

        // Parse JSON
        JsonDocument doc = JsonDocument.Parse(json);
        JsonElement root = doc.RootElement;

        // Call the method to iterate and print all property names
        PrintJsonPropertyNames(root);
    }

    static void PrintJsonPropertyNames(JsonElement element, string indent = "")
    {
        switch (element.ValueKind)
        {
            case JsonValueKind.Object:
                foreach (JsonProperty property in element.EnumerateObject())
                {
                    Console.WriteLine($"{indent}{property.Name}");
                    PrintJsonPropertyNames(property.Value, indent + "  ");
                }
                break;
            case JsonValueKind.Array:
                foreach (JsonElement item in element.EnumerateArray())
                {
                    PrintJsonPropertyNames(item, indent + "  ");
                }
                break;
            default:
                // For primitive types and unsupported kinds, we don't need to print anything.
                break;
        }
    }
}

......



using System;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""UserId"": ""skunarrajak@firstam.com"",
            ""Branch"": ""ANE"",
            ""IsMergeResults"": true,
            ""Property"": [
                {
                    ""ΑΡΝ"": ""10-00092-993-620-0000""
                },
                {
                    ""ΑΡΝ"": ""10-00092-993-620-0000""
                }
            ],
            ""Details"": {
                ""APN"": ""123""
            }
        }";

        // Parse JSON
        JsonDocument doc = JsonDocument.Parse(json);
        JsonElement root = doc.RootElement;

        // Call the method to iterate and print all properties
        PrintJsonProperties(root);
    }

    static void PrintJsonProperties(JsonElement element, string indent = "")
    {
        switch (element.ValueKind)
        {
            case JsonValueKind.Object:
                foreach (JsonProperty property in element.EnumerateObject())
                {
                    Console.WriteLine($"{indent}{property.Name}:");
                    PrintJsonProperties(property.Value, indent + "  ");
                }
                break;
            case JsonValueKind.Array:
                foreach (JsonElement item in element.EnumerateArray())
                {
                    PrintJsonProperties(item, indent + "  ");
                }
                break;
            case JsonValueKind.String:
                Console.WriteLine($"{indent}{element.GetString()}");
                break;
            case JsonValueKind.Number:
                Console.WriteLine($"{indent}{element.GetDecimal()}");
                break;
            case JsonValueKind.True:
            case JsonValueKind.False:
                Console.WriteLine($"{indent}{element.GetBoolean()}");
                break;
            case JsonValueKind.Null:
                Console.WriteLine($"{indent}null");
                break;
            default:
                Console.WriteLine($"{indent}Unsupported ValueKind: {element.ValueKind}");
                break;
        }
    }
}






using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<JsonValidationMiddleware> _logger;

        public JsonValidationMiddleware(RequestDelegate next, ILogger<JsonValidationMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                // Log the received JSON body for debugging
                _logger.LogInformation("Received JSON: " + body);

                try
                {
                    var json = JObject.Parse(body);

                    // Check for duplicate properties
                    var propertyNames = new HashSet<string>();
                    if (HasDuplicateProperties(json, propertyNames))
                    {
                        context.Response.ContentType = "application/json"; // Set content type
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync("{ \"error\": \"Duplicate properties are not allowed.\" }");
                        return;
                    }
                }
                catch (JsonReaderException ex)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    _logger.LogError("JSON Parsing Exception: " + ex.Message);
                    return;
                }
            }

            await _next(context);
        }

        private bool HasDuplicateProperties(JToken token, HashSet<string> propertyNames)
        {
            if (token is JObject jsonObject)
            {
                foreach (var property in jsonObject.Properties())
                {
                    var upperCaseName = property.Name.ToUpper();
                    if (!propertyNames.Add(upperCaseName))
                    {
                        return true;
                    }

                    // Check nested properties
                    if (property.Value is JObject || property.Value is JArray)
                    {
                        var nestedPropertyNames = new HashSet<string>();
                        if (HasDuplicateProperties(property.Value, nestedPropertyNames))
                        {
                            return true;
                        }
                    }
                }
            }
            else if (token is JArray jsonArray)
            {
                foreach (var item in jsonArray)
                {
                    if (HasDuplicateProperties(item, propertyNames))
                    {
                        return true;
                    }
                }
            }

            return false;
        }
    }
}



.......

using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<JsonValidationMiddleware> _logger;

        public JsonValidationMiddleware(RequestDelegate next, ILogger<JsonValidationMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                // Log the received JSON body for debugging
                _logger.LogInformation("Received JSON: " + body);

                try
                {
                    var json = JObject.Parse(body);

                    // Check for duplicate properties
                    var propertyNames = new HashSet<string>();
                    if (HasDuplicateProperties(json, propertyNames))
                    {
                        context.Response.ContentType = "application/json"; // Set content type
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync("{ \"error\": \"Duplicate properties are not allowed.\" }");
                        return;
                    }
                }
                catch (JsonReaderException ex)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    _logger.LogError("JSON Parsing Exception: " + ex.Message);
                    return;
                }
            }

            await _next(context);
        }

        private bool HasDuplicateProperties(JObject jsonObject, HashSet<string> propertyNames)
        {
            var stack = new Stack<JToken>();
            stack.Push(jsonObject);

            while (stack.Count > 0)
            {
                var current = stack.Pop();

                if (current is JObject obj)
                {
                    foreach (var property in obj.Properties())
                    {
                        var upperCaseName = property.Name.ToUpper();
                        if (!propertyNames.Add(upperCaseName))
                        {
                            return true;
                        }

                        if (property.Value is JObject || property.Value is JArray)
                        {
                            stack.Push(property.Value);
                        }
                    }
                }
                else if (current is JArray array)
                {
                    foreach (var item in array)
                    {
                        stack.Push(item);
                    }
                }
            }

            return false;
        }
    }
}





using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<JsonValidationMiddleware> _logger;

        public JsonValidationMiddleware(RequestDelegate next, ILogger<JsonValidationMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                // Log the received JSON body for debugging
                _logger.LogInformation("Received JSON: " + body);

                try
                {
                    var json = JObject.Parse(body);

                    // Check for duplicate properties
                    var errorMessage = CheckForDuplicateProperties(json);
                    if (errorMessage != null)
                    {
                        context.Response.ContentType = "application/json"; // Set content type
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync($"{{ \"error\": \"{errorMessage}\" }}");
                        return;
                    }
                }
                catch (JsonReaderException ex)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    _logger.LogError("JSON Parsing Exception: " + ex.Message);
                    return;
                }
            }

            await _next(context);
        }

        private string CheckForDuplicateProperties(JToken jsonToken)
        {
            if (jsonToken is JObject jsonObject)
            {
                var propertyNames = new HashSet<string>();
                foreach (var property in jsonObject.Properties())
                {
                    var upperCaseName = property.Name.ToUpper();
                    if (!propertyNames.Add(upperCaseName))
                    {
                        return $"Duplicate property '{property.Name}' is not allowed.";
                    }

                    // Check nested objects
                    var nestedErrorMessage = CheckForDuplicateProperties(property.Value);
                    if (nestedErrorMessage != null)
                    {
                        return nestedErrorMessage;
                    }
                }
            }
            else if (jsonToken is JArray jsonArray)
            {
                foreach (var item in jsonArray)
                {
                    var nestedErrorMessage = CheckForDuplicateProperties(item);
                    if (nestedErrorMessage != null)
                    {
                        return nestedErrorMessage;
                    }
                }
            }

            return null;
        }
    }
}







using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<JsonValidationMiddleware> _logger;

        public JsonValidationMiddleware(RequestDelegate next, ILogger<JsonValidationMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                // Log the received JSON body for debugging
                _logger.LogInformation("Received JSON: " + body);

                try
                {
                    var json = JObject.Parse(body);

                    // Check for duplicate properties
                    if (HasDuplicateProperties(json))
                    {
                        context.Response.ContentType = "application/json"; // Set content type
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync("{ \"error\": \"Duplicate properties are not allowed.\" }");
                        return;
                    }
                }
                catch (JsonReaderException ex)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    _logger.LogError("JSON Parsing Exception: " + ex.Message);
                    return;
                }
            }

            await _next(context);
        }

        private bool HasDuplicateProperties(JToken jsonToken)
        {
            var propertyNames = new HashSet<string>();

            var queue = new Queue<JToken>();
            queue.Enqueue(jsonToken);

            while (queue.Count > 0)
            {
                var current = queue.Dequeue();

                if (current is JObject jsonObject)
                {
                    foreach (var property in jsonObject.Properties())
                    {
                        var upperCaseName = property.Name.ToUpper();
                        if (!propertyNames.Add(upperCaseName))
                        {
                            return true;
                        }

                        queue.Enqueue(property.Value);
                    }
                }
                else if (current is JArray jsonArray)
                {
                    foreach (var item in jsonArray)
                    {
                        queue.Enqueue(item);
                    }
                }
            }

            return false;
        }
    }
}




,..............
using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<JsonValidationMiddleware> _logger;

        public JsonValidationMiddleware(RequestDelegate next, ILogger<JsonValidationMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                // Log the received JSON body for debugging
                _logger.LogInformation("Received JSON: " + body);

                try
                {
                    var json = JObject.Parse(body);

                    // Check for duplicate properties recursively
                    var errorMessage = CheckForDuplicateProperties(json);
                    if (errorMessage != null)
                    {
                        context.Response.ContentType = "application/json"; // Set content type
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync($"{{ \"error\": \"{errorMessage}\" }}");
                        return;
                    }
                }
                catch (JsonReaderException ex)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    _logger.LogError("JSON Parsing Exception: " + ex.Message);
                    return;
                }
            }

            await _next(context);
        }

        private string CheckForDuplicateProperties(JToken jsonToken, HashSet<string> parentPropertyNames = null)
        {
            var propertyNames = parentPropertyNames ?? new HashSet<string>();
            
            if (jsonToken is JObject jsonObject)
            {
                foreach (var property in jsonObject.Properties())
                {
                    var upperCaseName = property.Name.ToUpper();
                    if (!propertyNames.Add(upperCaseName))
                    {
                        return $"Duplicate property '{property.Name}' is not allowed.";
                    }
                    
                    var errorMessage = CheckForDuplicateProperties(property.Value, new HashSet<string>(propertyNames));
                    if (errorMessage != null)
                    {
                        return errorMessage;
                    }
                }
            }
            else if (jsonToken is JArray jsonArray)
            {
                foreach (var item in jsonArray)
                {
                    var errorMessage = CheckForDuplicateProperties(item, new HashSet<string>(propertyNames));
                    if (errorMessage != null)
                    {
                        return errorMessage;
                    }
                }
            }
            
            return null;
        }
    }
}









...........
using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<JsonValidationMiddleware> _logger;

        public JsonValidationMiddleware(RequestDelegate next, ILogger<JsonValidationMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                // Log the received JSON body for debugging
                _logger.LogInformation("Received JSON: " + body);

                try
                {
                    var json = JObject.Parse(body);

                    // Normalize property names to uppercase for case-insensitive comparison
                    var normalizedPropertyNames = new HashSet<string>();
                    foreach (var property in json.Properties())
                    {
                        var upperCaseName = property.Name.ToUpper();
                        if (!normalizedPropertyNames.Add(upperCaseName))
                        {
                            context.Response.ContentType = "application/json"; // Set content type
                            context.Response.StatusCode = StatusCodes.Status400BadRequest;
                            await context.Response.WriteAsync($"{{ \"error\": \"Duplicate property '{property.Name}' is not allowed.\" }}");
                            return;
                        }
                    }
                }
                catch (JsonReaderException ex)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    _logger.LogError("JSON Parsing Exception: " + ex.Message);
                    return;
                }
            }

            await _next(context);
        }
    }
}



using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<JsonValidationMiddleware> _logger;

        public JsonValidationMiddleware(RequestDelegate next, ILogger<JsonValidationMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                // Log the received JSON body for debugging
                _logger.LogInformation("Received JSON: " + body);

                try
                {
                    var json = JObject.Parse(body);

                    // Normalize property names to uppercase for case-insensitive comparison
                    var normalizedPropertyNames = new HashSet<string>();
                    foreach (var property in json.Properties())
                    {
                        var upperCaseName = property.Name.ToUpper();
                        if (!normalizedPropertyNames.Add(upperCaseName))
                        {
                            context.Response.StatusCode = StatusCodes.Status400BadRequest;
                            await context.Response.WriteAsync($"Duplicate property '{property.Name}' is not allowed.");
                            return;
                        }
                    }
                }
                catch (JsonReaderException ex)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    _logger.LogError("JSON Parsing Exception: " + ex.Message);
                    return;
                }
            }

            await _next(context);
        }
    }
}








using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;
using System.Collections.Generic;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<JsonValidationMiddleware> _logger;

        public JsonValidationMiddleware(RequestDelegate next, ILogger<JsonValidationMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                // Log the received JSON body for debugging
                _logger.LogInformation("Received JSON: " + body);

                try
                {
                    var json = JObject.Parse(body);

                    // Check for duplicate properties using a loop
                    var propertyNames = new HashSet<string>();
                    foreach (var property in json.Properties())
                    {
                        if (!propertyNames.Add(property.Name))
                        {
                            context.Response.StatusCode = StatusCodes.Status400BadRequest;
                            await context.Response.WriteAsync("Duplicate properties are not allowed.");
                            return;
                        }
                    }
                }
                catch (JsonReaderException ex)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    _logger.LogError("JSON Parsing Exception: " + ex.Message);
                    return;
                }
            }

            await _next(context);
        }
    }
}










using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;

        public JsonValidationMiddleware(RequestDelegate next)
        {
            _next = next;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                try
                {
                    var json = JObject.Parse(body);

                    // Log the JSON received for debugging
                    Console.WriteLine("Received JSON: " + json.ToString());

                    if (json.Properties().GroupBy(p => p.Name).Any(g => g.Count() > 1))
                    {
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync("Duplicate properties are not allowed.");
                        return;
                    }
                }
                catch (JsonReaderException ex)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    Console.WriteLine("JSON Parsing Exception: " + ex.Message);
                    return;
                }
            }

            await _next(context);
        }
    }
}






using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;

        public JsonValidationMiddleware(RequestDelegate next)
        {
            _next = next;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                try
                {
                    var json = JObject.Parse(body);

                    if (json.Properties().GroupBy(p => p.Name).Any(g => g.Count() > 1))
                    {
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync("Duplicate properties are not allowed.");
                        return;
                    }
                }
                catch (JsonReaderException)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    return;
                }
            }

            await _next(context);
        }
    }
}








using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;

        public JsonValidationMiddleware(RequestDelegate next)
        {
            _next = next;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                try
                {
                    var json = JObject.Parse(body);

                    if (json.Properties().GroupBy(p => p.Name).Any(g => g.Count() > 1))
                    {
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync("Duplicate properties are not allowed.");
                        return;
                    }
                }
                catch (JsonReaderException)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    return;
                }
            }

            await _next(context);
        }
    }
}






To ensure that your API rejects JSON requests with duplicate properties and returns an appropriate error message, you can create a custom middleware to inspect and validate the JSON request body before it reaches the controller. This middleware will reject requests with duplicate properties and return a suitable error response.

### Step-by-Step Guide to Implement Custom Middleware

1. **Create a Middleware Class:**

   - In your main web project, create a new folder named `Middleware`.
   - Inside the `Middleware` folder, create a new class file named `JsonValidationMiddleware.cs`.

2. **Implement the Middleware:**

   - Copy the following code into the `JsonValidationMiddleware.cs` file:

```csharp
using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;

        public JsonValidationMiddleware(RequestDelegate next)
        {
            _next = next;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            // Only validate JSON requests
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                using (var reader = new StreamReader(context.Request.Body))
                {
                    var body = await reader.ReadToEndAsync();
                    var json = JObject.Parse(body);

                    if (json.Properties().GroupBy(p => p.Name).Any(g => g.Count() > 1))
                    {
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync("Duplicate properties are not allowed.");
                        return;
                    }

                    // Reset the request body stream position so the next middleware can read it
                    context.Request.Body.Position = 0;
                }
            }

            // Call the next middleware in the pipeline
            await _next(context);
        }
    }
}
```

3. **Register the Middleware:**

   - Open `Startup.cs` and register the custom middleware in the `Configure` method:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseRouting();

    // Add the custom JSON validation middleware
    app.UseMiddleware<YourNamespace.Middleware.JsonValidationMiddleware>();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Other middleware registrations
}
```

4. **Test Your API:**

   - Test your API with various JSON inputs to ensure that it properly rejects JSON with duplicate properties and returns a 400 Bad Request response with the message "Duplicate properties are not allowed."

### Summary

- Create a custom middleware (`JsonValidationMiddleware`) to inspect the JSON request body for duplicate properties.
- Register the custom middleware in the `Configure` method of `Startup.cs`.
- Test your API to ensure that it correctly rejects invalid JSON requests and returns an appropriate error response.

This approach ensures that your API validates JSON requests at the middleware level, providing a clear error message when duplicate properties are detected.





,....................................




To enforce a rule in your ASP.NET Core API that rejects JSON input containing duplicate properties, you can use a custom model binder or a custom action filter. Here's a step-by-step guide to achieving this:

### Step 1: Create a Custom Action Filter

Create a custom action filter that checks for duplicate properties in the JSON input.

1. **Create the Filter:**

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Filters;
using Newtonsoft.Json.Linq;
using System.IO;
using System.Linq;

public class ValidateJsonFilter : ActionFilterAttribute
{
    public override void OnActionExecuting(ActionExecutingContext context)
    {
        var request = context.HttpContext.Request;
        request.EnableBuffering();

        using (var reader = new StreamReader(request.Body))
        {
            var body = reader.ReadToEnd();
            var json = JObject.Parse(body);

            if (json.Properties().GroupBy(p => p.Name).Any(g => g.Count() > 1))
            {
                context.Result = new BadRequestObjectResult("Duplicate properties are not allowed.");
                return;
            }
        }

        request.Body.Position = 0;
        base.OnActionExecuting(context);
    }
}
```

### Step 2: Apply the Filter Globally or per Controller/Action

1. **Globally:**

In `Startup.cs`, apply the filter globally:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers(options =>
    {
        options.Filters.Add<ValidateJsonFilter>();
    });
}
```

2. **Per Controller/Action:**

Alternatively, apply the filter to specific controllers or actions:

```csharp
[ApiController]
[Route("[controller]")]
public class YourController : ControllerBase
{
    [HttpPost]
    [ValidateJsonFilter]
    public IActionResult YourAction([FromBody] YourModel model)
    {
        // Your action logic here
        return Ok();
    }
}
```

### Step 3: Test Your API

Now, when you send a JSON request with duplicate properties to your API, it will return a 400 Bad Request with the message "Duplicate properties are not allowed."

### Additional Tips

1. **Use `JsonSerializerSettings` to enforce strict JSON parsing:** This can catch some issues at the deserialization level, but it might not catch duplicate properties.

2. **Unit Tests:** Write unit tests to ensure your filter works correctly.

Here's an example of how to test the filter:

```csharp
using Microsoft.AspNetCore.Mvc.Testing;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Xunit;

public class YourApiTests : IClassFixture<WebApplicationFactory<Startup>>
{
    private readonly HttpClient _client;

    public YourApiTests(WebApplicationFactory<Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task Post_WithDuplicateProperties_ShouldReturnBadRequest()
    {
        var json = @"{
            ""UserId"": ""skunarrajak@firstam.com"",
            ""Branch"": ""ANE"",
            ""IsMergeResults"": true,
            ""Property"": [{""ΑΡΝ"": ""10-00092-993-620-0000""}],
            ""property"": [{""ΑΡΝ"": ""20-0-000-001.153""}]
        }";

        var content = new StringContent(json, Encoding.UTF8, "application/json");

        var response = await _client.PostAsync("/your-endpoint", content);

        Assert.Equal(System.Net.HttpStatusCode.BadRequest, response.StatusCode);
    }
}
```

This approach ensures that your API will reject any JSON input containing duplicate properties.
