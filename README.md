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
