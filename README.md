using System.Text.Json;
using System.Text.RegularExpressions;
using Microsoft.AspNetCore.Http;

// Read request body
context.Request.EnableBuffering();
using var reader = new StreamReader(context.Request.Body);
var body = await reader.ReadToEndAsync();
context.Request.Body.Position = 0;

// Extract orderNumber from JSON
var match = Regex.Match(body, @"""orderNumber""\s*:\s*""([^""\\]*(?:\\.[^""\\]*)*)""", RegexOptions.IgnoreCase);
if (match.Success)
{
    string orderNumber = match.Groups[1].Value;

    // Check for invalid backslashes (any odd number)
    if (Regex.IsMatch(orderNumber, @"\\(?:\\{2})*\\"))
    {
        context.Response.StatusCode = 400;
        await context.Response.WriteAsync(JsonSerializer.Serialize(new
        {
            error = "Invalid order number containing wrong backslash."
        }));
        return; // Stop further processing
    }
}

// ✅ If valid, do nothing
