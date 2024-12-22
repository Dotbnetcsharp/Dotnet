[HttpPost("AddressTypeAheadSearch")]
public async Task<IActionResult> AddressTypeAheadSearch(AddressTypeAheadRequest request)
{
    var validationResult = await _addressSearchService.ValidateAddressTypeAheadRequest(request);
    if (!validationResult.IsValidRequest || validationResult.IsExceptioned)
    {
        return BadRequest(validationResult);
    }

    var searchResponse = await _addressSearchService.AddressTypeAheadSearch(request);

    // Convert the response to a memory stream
    var stream = new MemoryStream();
    await System.Text.Json.JsonSerializer.SerializeAsync(stream, searchResponse);
    stream.Position = 0;

    // Return the response as a stream
    return new FileStreamResult(stream, "application/json");
}
