public class GeographicValidationResult
{
    public bool IsAllowed { get; set; }
    public string CountyCode { get; set; } // returned from matched JSON (null if none)
}

public GeographicValidationResult ValidateGeographicAccess(
    CompanyDetailsResponse companyDetails,
    string requestStateCode,      // optional
    string requestCountyFips)     // optional, expected as up to 5 digits (e.g. "02300")
{
    var result = new GeographicValidationResult { IsAllowed = false, CountyCode = null };

    // Nothing to check
    if (string.IsNullOrWhiteSpace(requestStateCode) && string.IsNullOrWhiteSpace(requestCountyFips))
        return result;

    // Normalize countyFips to 5 chars (pad with leading zeros if needed)
    string normalizedCountyFips = null;
    if (!string.IsNullOrWhiteSpace(requestCountyFips))
        normalizedCountyFips = requestCountyFips.PadLeft(5, '0');

    // CASE 1: both stateCode && countyFips provided -> exact pair (and stateFips must match first 2 digits)
    if (!string.IsNullOrWhiteSpace(requestStateCode) && !string.IsNullOrWhiteSpace(normalizedCountyFips))
    {
        string stateFipsPart = normalizedCountyFips.Substring(0, 2);
        string countyFipsPart = normalizedCountyFips.Substring(2, 3);

        foreach (var geo in companyDetails.GeographicAccess)
        {
            if (string.Equals(geo.StateCode, requestStateCode, StringComparison.OrdinalIgnoreCase) &&
                string.Equals(geo.StateFips, stateFipsPart, StringComparison.OrdinalIgnoreCase))
            {
                var matchedCounty = geo.Counties.FirstOrDefault(c =>
                    string.Equals(c.CountyFips, countyFipsPart, StringComparison.OrdinalIgnoreCase));

                if (matchedCounty != null)
                {
                    result.IsAllowed = true;
                    result.CountyCode = matchedCounty.CountyCode;
                    return result;
                }
            }
        }
    }
    // CASE 2: only countyFips provided -> derive stateFips (first 2) and find the county under that stateFips
    else if (!string.IsNullOrWhiteSpace(normalizedCountyFips))
    {
        string stateFipsPart = normalizedCountyFips.Substring(0, 2);
        string countyFipsPart = normalizedCountyFips.Substring(2, 3);

        foreach (var geo in companyDetails.GeographicAccess)
        {
            if (string.Equals(geo.StateFips, stateFipsPart, StringComparison.OrdinalIgnoreCase))
            {
                var matchedCounty = geo.Counties.FirstOrDefault(c =>
                    string.Equals(c.CountyFips, countyFipsPart, StringComparison.OrdinalIgnoreCase));

                if (matchedCounty != null)
                {
                    result.IsAllowed = true;
                    result.CountyCode = matchedCounty.CountyCode;
                    return result;
                }
            }
        }
    }
    // CASE 3: only stateCode provided -> just check state exists
    else if (!string.IsNullOrWhiteSpace(requestStateCode))
    {
        var matchedState = companyDetails.GeographicAccess
            .FirstOrDefault(geo => string.Equals(geo.StateCode, requestStateCode, StringComparison.OrdinalIgnoreCase));

        if (matchedState != null)
            result.IsAllowed = true; // CountyCode stays null
    }

    return result;
}
