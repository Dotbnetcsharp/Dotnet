var decodedOrderNumber = orderNumberMatch.Groups[1].Value;

// ✅ Validate only orderNumber
bool hasInvalidBackslash = Regex.IsMatch(decodedOrderNumber, @"(?<!\\)\\(?![\\])");
if (hasInvalidBackslash)
{
    context.Response.ContentType = "application/json";
    context.Response.StatusCode = StatusCodes.Status400BadRequest;
    await context.Response.WriteAsync("{\"error\": \"Order number is invalid due to improper backslash usage. Use \\\\ instead of \\\\ .\"}");
    return;
}

// Normalize if needed
decodedOrderNumber = Regex.Replace(decodedOrderNumber, @"\\+", @"\");
