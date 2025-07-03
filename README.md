[ApiController]
[Route("api/address")]
public class AddressSearchController : ControllerBase
{
    [HttpPost("search")]
    public IActionResult Search([FromBody] AddressSearchRequest request)
    {
        // Step 1: Check FullAddress
        if (string.IsNullOrWhiteSpace(request.FullAddress))
        {
            return BadRequest("Error: FullAddress is required.");
        }

        // Step 2: Check all required-optional combinations
        if (string.IsNullOrWhiteSpace(request.Zip))
        {
            // Zip is empty, check StateFIPS + CountyFIPS
            if (string.IsNullOrWhiteSpace(request.StateFIPS))
            {
                return BadRequest("Error: StateFIPS is required when using (StateFIPS + CountyFIPS) or (StateFIPS + City).");
            }

            if (string.IsNullOrWhiteSpace(request.CountyFIPS) && string.IsNullOrWhiteSpace(request.City))
            {
                return BadRequest("Error: Either CountyFIPS or City is required along with StateFIPS.");
            }
        }

        // Step 3: Passed all checks
        return Ok(new
        {
            Message = "Search successful",
            FullAddress = request.FullAddress,
            StateFIPS = request.StateFIPS,
            CountyFIPS = request.CountyFIPS,
            City = request.City,
            Zip = request.Zip
        });
    }
}
