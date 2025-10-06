bool isAllowed = false;

if (!string.IsNullOrWhiteSpace(requestState) && !string.IsNullOrWhiteSpace(requestCounty))
{
    // ✅ Case 1: Both state & county given → check pair
    isAllowed = companyDetails.GeographicAccess.Any(geo =>
        string.Equals(geo.State, requestState, StringComparison.OrdinalIgnoreCase) &&
        geo.Counties.Any(c => string.Equals(c, requestCounty, StringComparison.OrdinalIgnoreCase))
    );
}
else if (!string.IsNullOrWhiteSpace(requestState))
{
    // ✅ Case 2: Only state given → check state exists
    isAllowed = companyDetails.GeographicAccess.Any(geo =>
        string.Equals(geo.State, requestState, StringComparison.OrdinalIgnoreCase)
    );
}
else if (!string.IsNullOrWhiteSpace(requestCounty))
{
    // ✅ Case 3: Only county given → check county exists in any state
    isAllowed = companyDetails.GeographicAccess.Any(geo =>
        geo.Counties.Any(c => string.Equals(c, requestCounty, StringComparison.OrdinalIgnoreCase))
    );
}

if (!isAllowed)
{
    throw new BadRequestException("Invalid state/county combination or not allowed for this company.");
}
