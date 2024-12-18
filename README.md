
// Original URL
string azureAddressApiGetURL = "https://dbsmp1dtlmsi1.search.windows.net/indexes/idxpropertyownerdev1/docs?api-version=2020-06-30&$top=50&searchMode=all&search=(FullTextSearchAddress: (10 PINE TREE RD #*) OR FullTextSearchAddress keyword: (/10 PINE TREE RD #.*/)) AND County Fips: \"37125\"&querytype=full&$count=true";

// Step 1: Handle the "FullTextSearchAddress" part
int fullTextSearchIndex = azureAddressApiGetURL.IndexOf("FullTextSearchAddress:");
if (fullTextSearchIndex != -1)
{
    int fullTextSearchEndIndex = azureAddressApiGetURL.IndexOf(")", fullTextSearchIndex);
    string fullTextSearchPart = azureAddressApiGetURL.Substring(fullTextSearchIndex, fullTextSearchEndIndex - fullTextSearchIndex + 1);

    // Replace # with "%23" in this part
    string modifiedFullTextSearchPart = fullTextSearchPart.Replace("#", "\"%23\"");

    // Replace the original part in the URL
    azureAddressApiGetURL = azureAddressApiGetURL.Replace(fullTextSearchPart, modifiedFullTextSearchPart);
}

// Step 2: Handle the "FullTextSearchAddress keyword" part
int keywordIndex = azureAddressApiGetURL.IndexOf("FullTextSearchAddress keyword:");
if (keywordIndex != -1)
{
    int keywordEndIndex = azureAddressApiGetURL.IndexOf(")", keywordIndex);
    string keywordPart = azureAddressApiGetURL.Substring(keywordIndex, keywordEndIndex - keywordIndex + 1);

    // Replace # with "/%23" in this part
    string modifiedKeywordPart = keywordPart.Replace("#", "\"/%23\"");

    // Replace the original part in the URL
    azureAddressApiGetURL = azureAddressApiGetURL.Replace(keywordPart, modifiedKeywordPart);
}

// Final Output
Console.WriteLine(azureAddressApiGetURL);



// Original URL
string azureAddressApiGetURL = "https://dbsmp1dtlmsi1.search.windows.net/indexes/idxpropertyownerdev1/docs?api-version=2020-06-30&$top=50&searchMode=all&search=(FullTextSearchAddress: (10 PINE TREE RD #*) OR FullTextSearchAddress keyword: (/10 PINE TREE RD #.*/)) AND County Fips: \"37125\"&querytype=full&$count=true";

// Step 1: Identify and handle FullTextSearchAddress portion
int searchStartIndex = azureAddressApiGetURL.IndexOf("FullTextSearchAddress:");
if (searchStartIndex != -1)
{
    // Extract the FullTextSearchAddress part dynamically
    int searchEndIndex = azureAddressApiGetURL.IndexOf(")", searchStartIndex);
    string fullTextSearchPart = azureAddressApiGetURL.Substring(searchStartIndex, searchEndIndex - searchStartIndex + 1);

    // Replace # with "%23" in this part
    string modifiedSearchPart = fullTextSearchPart.Replace("#", "\"%23\"");

    // Replace the original part in the URL with the modified part
    azureAddressApiGetURL = azureAddressApiGetURL.Replace(fullTextSearchPart, modifiedSearchPart);
}

// Step 2: Replace all remaining # symbols with %23 globally, except for the already processed portion
// This works because the FullTextSearchAddress part is already encoded correctly
azureAddressApiGetURL = azureAddressApiGetURL.Replace("#", "%23");

// Output the final URL
Console.WriteLine(azureAddressApiGetURL);
