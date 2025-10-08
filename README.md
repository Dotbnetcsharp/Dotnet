public class GeographicValidationResult
{
    public bool IsAllowed { get; set; }
    public string CountyCode { get; set; } // returned from matched JSON
}

public GeographicValidationResult ValidateGeographicAccess(
    CompanyDetailsResponse companyDetails,
    string requestStateCode,      // optional
    string requestCountyFips)     // optional (e.g., "02300")
{
    var result = new GeographicValidationResult { IsAllowed = false, CountyCode = null };

    if (string.IsNullOrWhiteSpace(requestStateCode) && string.IsNullOrWhiteSpace(requestCountyFips))
        return result;

    string normalizedCountyFips = requestCountyFips?.PadLeft(5, '0');
    string stateFipsPart = normalizedCountyFips?.Substring(0, 2);
    string countyFipsPart = normalizedCountyFips?.Substring(2, 3);

    // ✅ CASE 1: Both stateCode & countyFips present
    if (!string.IsNullOrWhiteSpace(requestStateCode) && !string.IsNullOrWhiteSpace(normalizedCountyFips))
    {
        var matchedCounty = companyDetails.GeographicAccess
            .Where(g =>
                string.Equals(g.StateCode, requestStateCode, StringComparison.OrdinalIgnoreCase) &&
                string.Equals(g.StateFips, stateFipsPart, StringComparison.OrdinalIgnoreCase))
            .SelectMany(g => g.Counties
                .Where(c => string.Equals(c.CountyFips, countyFipsPart, StringComparison.OrdinalIgnoreCase))
                .Select(c => new { c.CountyCode }))
            .FirstOrDefault();

        if (matchedCounty != null)
            return new GeographicValidationResult { IsAllowed = true, CountyCode = matchedCounty.CountyCode };
    }

    // ✅ CASE 2: Only countyFips present
    if (string.IsNullOrWhiteSpace(requestStateCode) && !string.IsNullOrWhiteSpace(normalizedCountyFips))
    {
        var matchedCounty = companyDetails.GeographicAccess
            .Where(g => string.Equals(g.StateFips, stateFipsPart, StringComparison.OrdinalIgnoreCase))
            .SelectMany(g => g.Counties
                .Where(c => string.Equals(c.CountyFips, countyFipsPart, StringComparison.OrdinalIgnoreCase))
                .Select(c => new { c.CountyCode }))
            .FirstOrDefault();

        if (matchedCounty != null)
            return new GeographicValidationResult { IsAllowed = true, CountyCode = matchedCounty.CountyCode };
    }

    // ✅ CASE 3: Only stateCode present
    if (!string.IsNullOrWhiteSpace(requestStateCode) && string.IsNullOrWhiteSpace(requestCountyFips))
    {
        bool stateExists = companyDetails.GeographicAccess
            .Any(g => string.Equals(g.StateCode, requestStateCode, StringComparison.OrdinalIgnoreCase));

        if (stateExists)
            return new GeographicValidationResult { IsAllowed = true };
    }

    return result;
}
