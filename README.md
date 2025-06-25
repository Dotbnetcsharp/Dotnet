var productCodes = propertyIdSearchRequest.Property.Select(p => p.ProductCode).ToList();

[HttpPost("ScheduleStarterSearch")]
public async Task<IActionResult> ScheduleStarterSearch(PropertyIdMultiSearchRequest propertyIdSearchRequest)
{
    var starterRequest = new StarterMultiPropertySearchRequestDTO
    {
        IsMergeResults = propertyIdSearchRequest.IsMergeResults,
        Property = propertyIdSearchRequest.Property.Select(p => new StarterMultiPropertyRequest
        {
            PropertyId = p.PropertyId,
            // Example: Map ProductCode into OwnerName or any other field
            OwnerName = p.ProductCode,
            BypassIndex = false,
            // Add other default or dummy values if needed
        }).ToList()
    };

    // Now you can pass this to your service
    var result = await _starterSearchService.RunSearchAsync(starterRequest);

    return Ok(result);
}
