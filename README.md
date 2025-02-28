azureAddressApiGetURL = addressAzrStng.QueryType;
azureAddressApiGetURL += addressAzrStng.Count;

if (Count > 3000)
{
    int skip = 0;
    int maxBatchSize = 3000;
    int totalCount = (int)Count; // Ensure Count is an int

    while (skip < totalCount)
    {
        // Determine remaining records and update $top dynamically
        int currentBatchSize = Math.Min(maxBatchSize, totalCount - skip);

        // Update $top in the existing URL (preserve the structure)
        string finalUrl = Regex.Replace(azureAddressApiGetURL, @"\$top=\d+", $"$top={currentBatchSize}");

        // Add $skip only if it's not the first request
        if (skip > 0)
        {
            finalUrl += $"&$skip={skip}";
        }

        apiUrls.Add(finalUrl);
        skip += currentBatchSize; // Move to next batch
    }
}
else
{
    // For count <= 3000, just update $top in the original URL
    string finalUrl = Regex.Replace(azureAddressApiGetURL, @"\$top=\d+", $"$top={(int)Count}");
    apiUrls.Add(finalUrl);
}

return apiUrls;








azureAddressApiGetURL += addressAzrStng.QueryType;
azureAddressApiGetURL += addressAzrStng.Count;

if (Count > 3000)
{
    int skip = 0;  
    int maxBatchSize = 3000;
    int totalCount = (int)Count; // Ensure Count is an int

    while (skip < totalCount)
    {
        int currentBatchSize = Math.Min(maxBatchSize, totalCount - skip);

        string finalUrl = $"{azureAddressApiGetURL}&$top={currentBatchSize}";

        if (skip > 0)
        {
            finalUrl += $"&$skip={skip}";
        }

        apiUrls.Add(finalUrl);
        skip += currentBatchSize;
    }
}
else
{
    apiUrls.Add($"{azureAddressApiGetURL}&$top={(int)Count}");
}

return apiUrls;






azureAddressApiGetURL += addressAzrStng.QueryType;
azureAddressApiGetURL += addressAzrStng.Count;

if (Count > 3000)
{
    int skip = 0;  
    int maxBatchSize = 3000;

    while (skip < Count)
    {
        int currentBatchSize = Math.Min(maxBatchSize, Count - skip);

        // Construct URL dynamically without modifying existing URL
        string finalUrl = $"{azureAddressApiGetURL}&$top={currentBatchSize}";

        // Add $skip only if it's not the first batch
        if (skip > 0)
        {
            finalUrl += $"&$skip={skip}";
        }

        apiUrls.Add(finalUrl);
        skip += currentBatchSize; // Move to next batch
    }
}
else
{
    // If count <= 3000, no need to paginate
    apiUrls.Add($"{azureAddressApiGetURL}&$top={Count}");
}

return apiUrls;


...




azureAddressApiGetURL += addressAzrStng.QueryType;
azureAddressApiGetURL += addressAzrStng.Count;

if (Count > 3000)
{
    int skip = 0;  // Ensure it's 'int'
    int maxBatchSize = 3000;

    while (skip < Count)
    {
        string finalUrl = azureAddressApiGetURL;

        // Determine the remaining records and update $top dynamically
        int currentBatchSize = Math.Min(maxBatchSize, Count - skip);
        finalUrl = Regex.Replace(finalUrl, @"&\$top=\d+", $"&$top={currentBatchSize}");

        // Add $skip only if it's not the first batch
        if (skip > 0)
        {
            finalUrl += $"&$skip={skip}";
        }

        apiUrls.Add(finalUrl);
        skip += currentBatchSize; // Move to next batch
    }
}
else
{
    // If count <= 3000, no need to paginate
    apiUrls.Add(azureAddressApiGetURL);
}

return apiUrls;

....





azureAddressApiGetURL += addressAzrStng.QueryType;
azureAddressApiGetURL += addressAzrStng.Count;

if (Count > 3000)
{
    int skip = 0;
    int maxBatchSize = 3000;

    while (skip < Count)
    {
        string finalUrl = azureAddressApiGetURL;

        // Determine the remaining records and update $top
        int currentBatchSize = Math.Min(maxBatchSize, Count - skip);
        finalUrl = Regex.Replace(finalUrl, @"&\$top=\d+", $"&$top={currentBatchSize}");

        // Add $skip only if it's not the first batch
        if (skip > 0)
        {
            finalUrl += $"&$skip={skip}";
        }

        apiUrls.Add(finalUrl);
        skip += currentBatchSize; // Move to next batch
    }
}
else
{
    // If count <= 3000, no need to paginate
    apiUrls.Add(azureAddressApiGetURL);
}

return apiUrls;

....



List<string> urlList = new List<string>();

// Construct the base URL (without skip)
string baseUrl = $"{azureAddressSearchIndex.BaseURL}{azureAddressSearchIndex.DocsURL}" +
                 $"{azureAddressSearchIndex.ApiVersion}" +
                 $"{azureAddressSearchIndex.SearchMode}" +
                 $"{azureAddressSearchIndex.QueryType}" +
                 $"{azureAddressSearchIndex.Count}";

// Pagination logic
if (totalCount > 3000)
{
    int skip = 0;
    int maxBatchSize = 3000;

    while (skip < totalCount)
    {
        string finalUrl = baseUrl; // Start with the base URL

        // Add skip only after the first request
        if (skip > 0)
        {
            finalUrl += $"&$skip={skip}";
        }

        // Add the generated URL to the list
        urlList.Add(finalUrl);

        // Increment skip for the next request
        skip += maxBatchSize;
    }
}
else
{
    // If total count is ≤ 3000, return just the base URL
    urlList.Add(baseUrl);
}

// Return the list of URLs
return urlList;



.....






private List<string> GetTypeAheadAddressApiURLs(
    string address, AzureSearchFilter filterBy, string stateCode, string countyFips, 
    int totalCount, bool isAmbiguousSearch = false, string StreetNumber = null, 
    string StreetName = null, string StreetDir = null, string StreetType = null, 
    string StreetPostDir = null, string Unit = null, string Zip = null, 
    string City = null, string Latitude = null, string Longitude = null)
{
    int maxBatchSize = 3000; // API limit per request
    List<string> apiUrls = new List<string>();

    for (int skip = 0; skip < totalCount; skip += maxBatchSize)
    {
        string TopResults = $"&$top={Math.Min(maxBatchSize, totalCount - skip)}";
        string SkipResults = $"&$skip={skip}";

        string azureAddressApiGetURL = $"{azureAddressSearchIndex.BaseURL}{azureAddressSearchIndex.DocsURL}" +
                                        $"{azureAddressSearchIndex.ApiVersion}{TopResults}" +
                                        $"{azureAddressSearchIndex.SearchMode}";

        if (!string.IsNullOrEmpty(address))
        {
            azureAddressApiGetURL += azureAddressSearchIndex.SearchKeyAddress
                .Replace("#ADDRESS#", address)
                .Replace("#ADDRESS_KEYWORD#", ReplaceUnsafeCharactersKeyWord(address));
        }

        // Add pagination skip parameter
        azureAddressApiGetURL += SkipResults;  
        apiUrls.Add(azureAddressApiGetURL);
    }

    return apiUrls;
}

public async Task<List<Address>> GetAllTypeAheadAddressesAsync(
    string address, AzureSearchFilter filterBy, string stateCode, string countyFips, 
    int totalCount, bool isAmbiguousSearch = false, string StreetNumber = null, 
    string StreetName = null, string StreetDir = null, string StreetType = null, 
    string StreetPostDir = null, string Unit = null, string Zip = null, 
    string City = null, string Latitude = null, string Longitude = null)
{
    List<Address> allResults = new List<Address>();

    // Step 1: Generate all paginated URLs
    List<string> apiUrls = GetTypeAheadAddressApiURLs(address, filterBy, stateCode, countyFips, 
                                                      totalCount, isAmbiguousSearch, StreetNumber, 
                                                      StreetName, StreetDir, StreetType, StreetPostDir, 
                                                      Unit, Zip, City, Latitude, Longitude);
    
    // Step 2: Call each API URL asynchronously and collect results
    foreach (var apiUrl in apiUrls)
    {
        var azureApiStringResponse = await AzureApiHelper.CallApi<List<Address>>(
            apiUrl, azureAddressSearchIndex.ApiKey, azureAddressSearchIndex.ApiMethod
        );

        if (azureApiStringResponse != null)
        {
            allResults.AddRange(azureApiStringResponse);
        }
    }

    return allResults;
}
