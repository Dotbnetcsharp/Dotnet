bool isAllowed = companyDetails.GeographicAccess.Any(geo =>
    string.Equals(geo.State, requestState, StringComparison.OrdinalIgnoreCase) &&
    geo.Counties.Any(c => string.Equals(c.CountyFips, requestCountyFips, StringComparison.OrdinalIgnoreCase))
);

if (!isAllowed)
{
    throw new BadRequestException($"Invalid state '{requestState}' and countyFips '{requestCountyFips}' combination.");
}
