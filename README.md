using System.Text.Json;
using System.Text.RegularExpressions;
using Microsoft.AspNetCore.Http;

// Read request body as raw string
context.Request.EnableBuffering();
using var reader = new StreamReader(context.Request.Body);
var body = await reader.ReadToEndAsync();
context.Request.Body.Position = 0;

// Regex to match "orderNumber":"..." containing an odd number of backslashes
if (Regex.IsMatch(body, @"""orderNumber""\s*:\s*""[^""]*\\(?:\\\\)*\\[^""]*""", RegexOptions.IgnoreCase))
{
    context.Response.StatusCode = 400;
    await context.Response.WriteAsync(JsonSerializer.Serialize(new
    {
        error = "Invalid order number containing wrong backslash."
    }));
    return; // Stop further processing
}

// ✅ If valid or missing, do nothing
