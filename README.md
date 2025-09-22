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
if (!match.Success)
{
    context.Response.StatusCode = 400;
    await context.Response.WriteAsync(JsonSerializer.Serialize(new { error = "Missing or invalid orderNumber." }));
    return;
}

string orderNumber = match.Groups[1].Value;

// Validate single/odd backslashes
if (Regex.IsMatch(orderNumber, @"(?<!\\)\\(?!\\)"))
{
    context.Response.StatusCode = 400;
    await context.Response.WriteAsync(JsonSerializer.Serialize(new
    {
        error = $"Order number '{orderNumber}' has invalid backslash. Use double \\\\ instead of single \\."
    }));
    return;
}

// Normalize multiple backslashes
orderNumber = Regex.Replace(orderNumber, @"\\{2,}", @"\");

// Check empty
if (string.IsNullOrWhiteSpace(orderNumber))
{
    context.Response.StatusCode = 400;
    await context.Response.WriteAsync(JsonSerializer.Serialize(new { error = "Order number cannot be empty." }));
    return;
}

// ✅ Safe to use
await context.Response.WriteAsync(JsonSerializer.Serialize(new { success = true, orderNumber }));
