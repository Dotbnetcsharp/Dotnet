bool isAllowed = false;

// Normalize countyFips to exactly 5 digits (pad with zeros if shorter)
string normalizedCountyFips = null;
if (!string.IsNullOrWhiteSpace(requestCountyFips))
{
    normalizedCountyFips = requestCountyFips.PadLeft(5, '0');
}

// ===== CASE 1: Both stateCode & countyFips =====
if (!string.IsNullOrWhiteSpace(requestStateCode) && !string.IsNullOrWhiteSpace(normalizedCountyFips))
{
    string stateFipsPart = normalizedCountyFips.Substring(0, 2);
    string countyFipsPart = normalizedCountyFips.Substring(2, 3);

    isAllowed = companyDetails.GeographicAccess.Any(geo =>
        string.Equals(geo.StateCode, requestStateCode, StringComparison.OrdinalIgnoreCase) &&
        string.Equals(geo.StateFips, stateFipsPart, StringComparison.OrdinalIgnoreCase) &&
        geo.Counties.Any(c => string.Equals(c.CountyFips, countyFipsPart, StringComparison.OrdinalIgnoreCase))
    );
}

// ===== CASE 2: Only countyFips =====
else if (!string.IsNullOrWhiteSpace(normalizedCountyFips))
{
    string stateFipsPart = normalizedCountyFips.Substring(0, 2);
    string countyFipsPart = normalizedCountyFips.Substring(2, 3);

    isAllowed = companyDetails.GeographicAccess.Any(geo =>
        string.Equals(geo.StateFips, stateFipsPart, StringComparison.OrdinalIgnoreCase) &&
        geo.Counties.Any(c => string.Equals(c.CountyFips, countyFipsPart, StringComparison.OrdinalIgnoreCase))
    );
}

// ===== CASE 3: Only stateCode =====
else if (!string.IsNullOrWhiteSpace(requestStateCode))
{
    isAllowed = companyDetails.GeographicAccess.Any(geo =>
        string.Equals(geo.StateCode, requestStateCode, StringComparison.OrdinalIgnoreCase)
    );
}

if (!isAllowed)
{
    throw new BadRequestException($"Invalid combination — not found for stateCode '{requestStateCode}' and countyFips '{requestCountyFips}'.");
}
