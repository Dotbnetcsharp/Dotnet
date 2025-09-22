using System.Text.Json;
using Microsoft.AspNetCore.Http;

// Read raw request body
context.Request.EnableBuffering();
using var reader = new StreamReader(context.Request.Body);
var body = await reader.ReadToEndAsync();
context.Request.Body.Position = 0;

// Try to get orderNumber value manually
int start = body.IndexOf("\"orderNumber\"") ;
if (start >= 0)
{
    int colon = body.IndexOf(":", start);
    int quoteStart = body.IndexOf("\"", colon) + 1;
    int quoteEnd = body.IndexOf("\"", quoteStart);
    if (quoteStart > 0 && quoteEnd > quoteStart)
    {
        string orderNumber = body.Substring(quoteStart, quoteEnd - quoteStart);

        // Check for any odd number of consecutive backslashes
        int count = 0;
        foreach (char c in orderNumber)
        {
            if (c == '\\') count++;
            else { if (count % 2 == 1) goto Invalid; count = 0; }
        }
        if (count % 2 == 1) goto Invalid;

        return; // ✅ Valid, do nothing

    Invalid:
        context.Response.StatusCode = 400;
        await context.Response.WriteAsync(JsonSerializer.Serialize(new
        {
            error = "Invalid order number containing wrong backslash."
        }));
        return;
    }
}
