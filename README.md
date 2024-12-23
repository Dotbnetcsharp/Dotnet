
var installmentDetail = taxInstallment.InstallmentDetail[i];

// Determine if the installmentDetail has a status of 'DELINQUENT'
if (!string.IsNullOrEmpty(installmentDetail.Status) && 
    installmentDetail.Status.Equals(SharedIdentity.DELINQUENT, StringComparison.OrdinalIgnoreCase))
{
    DateTime parsedDueDate;

    // Parse the due date from installmentDetail
    if (DateTime.TryParse(installmentDetail.DueDate, out parsedDueDate))
    {
        // Current date
        var currentDate = DateTime.Now;

        // Check if the current day is greater than or equal to the parsed due date's day
        if (currentDate.Day >= parsedDueDate.Day)
        {
            DateTime newEstDate;

            // Check if the current month is December
            if (currentDate.Month == 12)
            {
                // Increment month to January (wrap around) and year by 1
                newEstDate = new DateTime(currentDate.Year + 1, 1, parsedDueDate.Day);
            }
            else
            {
                // Increment month by 1, keeping the year the same
                newEstDate = new DateTime(currentDate.Year, currentDate.Month + 1, parsedDueDate.Day);
            }

            // Output the new estimated date
            Console.WriteLine($"New Estimated Date: {newEstDate}");
        }
    }
    else
    {
        Console.WriteLine("Failed to parse the due date.");
    }
}











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
