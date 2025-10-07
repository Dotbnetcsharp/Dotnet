bool isAllowed = false;

if (!string.IsNullOrWhiteSpace(requestStateCode) && !string.IsNullOrWhiteSpace(requestCountyFips))
{
    // ✅ Case 1: Both stateCode & countyFips → pair check
    isAllowed = companyDetails.GeographicAccess.Any(geo =>
        string.Equals(geo.StateCode, requestStateCode, StringComparison.OrdinalIgnoreCase) &&
        geo.Counties.Any(c => string.Equals(c.CountyFips, requestCountyFips, StringComparison.OrdinalIgnoreCase))
    );
}
else if (!string.IsNullOrWhiteSpace(requestCountyFips))
{
    // ✅ Case 2: Only countyFips → search across all states
    isAllowed = companyDetails.GeographicAccess.Any(geo =>
        geo.Counties.Any(c => string.Equals(c.CountyFips, requestCountyFips, StringComparison.OrdinalIgnoreCase))
    );
}
else if (!string.IsNullOrWhiteSpace(requestStateCode))
{
    // ✅ Case 3: Only stateCode → check if stateCode exists
    isAllowed = companyDetails.GeographicAccess.Any(geo =>
        string.Equals(geo.StateCode, requestStateCode, StringComparison.OrdinalIgnoreCase)
    );
}

if (!isAllowed)
{
    throw new BadRequestException("Invalid stateCode/countyFips combination or not found in geographic access.");
}
