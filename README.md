https://github.com/Dotbnetcsharp/Dotnet/setting


The error message "Excessive results found" is displayed because the indexCount in the request is set to 5, which is the maximum limit we support. If the data exceeds this limit, the message is shown. To view the data, increase the indexCount value in the request.
The error message "Excessive results found" is displayed because the indexCount parameter in the request is set to 5, which is the maximum limit currently supported. Previously, the system used a default value of 6000 for fetching data, but we now limit the results dynamically using the indexCount property.

When the data exceeds this limit, the system restricts the output and shows this message to prevent performance issues. To view additional data, the user can increase the indexCount value in the request to fetch more results. For now, the system is designed to support data retrieval only up to the specified maximum limit.

try
{
    apnSearchRequest.APN = apnSearchRequest.APN?.CleanAPNAddress();
    apnSearchResponse.APNS = await CallTypeAheadAPNSearchAzureApi(apnSearchRequest.APN);

    if (apnSearchResponse.APNS == null || !apnSearchResponse.APNS.Any())
    {
        apnSearchResponse.ValidationMessages.Add(new ValidationMessage
        {
            Message = "No APNs found for the given request." // Assuming `ValidationMessage` has a `Message` property
        });
    }

    apnSearchResponse.TotalCount = apnSearchResponse.APNS[0].Count.ToString();
}
catch (Exception ex)
{
    _logger.LogError(ex, $"APNTypeAheadSearch failed, Request for CountyFips: {apnSearchRequest.CountyFips}");
    apnSearchResponse.IsExceptioned = true;
}


using System.Text.RegularExpressions;

public string GetTypeAheadAddressApiURL(string address, AzureSearchFilter filterBy, int count = 15)
{
    // Original TopResultsToReturn value from JSON
    string topResults = addressAzrStng.TopResultsToReturn;

    // Use Regex to replace the numeric value in "&$top=<number>"
    topResults = Regex.Replace(topResults, @"&\$top=\d+", $"&$top={count}");

    // Construct the API URL
    string apiUrl = $"{addressAzrStng.DocsURL}{addressAzrStng.ApiVersion}{topResults}";

    // Replace placeholders in the URL
    apiUrl = apiUrl.Replace("#ADDRESS#", address)
                   .Replace("#STREETNUMBER#", StreetNumber)
                   .Replace("#STREETNAME#", StreetName)
                   .Replace("#STREETTYPE#", StreetType)
                   .Replace("#STREETPOSTDIR#", StreetPostDir)
                   .Replace("#UNIT#", Unit);

    return apiUrl;
}
