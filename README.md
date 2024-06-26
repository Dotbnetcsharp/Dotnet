using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<JsonValidationMiddleware> _logger;

        public JsonValidationMiddleware(RequestDelegate next, ILogger<JsonValidationMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                // Log the received JSON body for debugging
                _logger.LogInformation("Received JSON: " + body);

                try
                {
                    var json = JObject.Parse(body);

                    // Check for duplicate properties recursively
                    var errorMessage = CheckForDuplicateProperties(json);
                    if (errorMessage != null)
                    {
                        context.Response.ContentType = "application/json"; // Set content type
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync($"{{ \"error\": \"{errorMessage}\" }}");
                        return;
                    }
                }
                catch (JsonReaderException ex)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    _logger.LogError("JSON Parsing Exception: " + ex.Message);
                    return;
                }
            }

            await _next(context);
        }

        private string CheckForDuplicateProperties(JToken jsonToken, HashSet<string> parentPropertyNames = null)
        {
            var propertyNames = parentPropertyNames ?? new HashSet<string>();
            
            if (jsonToken is JObject jsonObject)
            {
                foreach (var property in jsonObject.Properties())
                {
                    var upperCaseName = property.Name.ToUpper();
                    if (!propertyNames.Add(upperCaseName))
                    {
                        return $"Duplicate property '{property.Name}' is not allowed.";
                    }
                    
                    var errorMessage = CheckForDuplicateProperties(property.Value, new HashSet<string>(propertyNames));
                    if (errorMessage != null)
                    {
                        return errorMessage;
                    }
                }
            }
            else if (jsonToken is JArray jsonArray)
            {
                foreach (var item in jsonArray)
                {
                    var errorMessage = CheckForDuplicateProperties(item, new HashSet<string>(propertyNames));
                    if (errorMessage != null)
                    {
                        return errorMessage;
                    }
                }
            }
            
            return null;
        }
    }
}









...........
using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<JsonValidationMiddleware> _logger;

        public JsonValidationMiddleware(RequestDelegate next, ILogger<JsonValidationMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                // Log the received JSON body for debugging
                _logger.LogInformation("Received JSON: " + body);

                try
                {
                    var json = JObject.Parse(body);

                    // Normalize property names to uppercase for case-insensitive comparison
                    var normalizedPropertyNames = new HashSet<string>();
                    foreach (var property in json.Properties())
                    {
                        var upperCaseName = property.Name.ToUpper();
                        if (!normalizedPropertyNames.Add(upperCaseName))
                        {
                            context.Response.ContentType = "application/json"; // Set content type
                            context.Response.StatusCode = StatusCodes.Status400BadRequest;
                            await context.Response.WriteAsync($"{{ \"error\": \"Duplicate property '{property.Name}' is not allowed.\" }}");
                            return;
                        }
                    }
                }
                catch (JsonReaderException ex)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    _logger.LogError("JSON Parsing Exception: " + ex.Message);
                    return;
                }
            }

            await _next(context);
        }
    }
}



using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<JsonValidationMiddleware> _logger;

        public JsonValidationMiddleware(RequestDelegate next, ILogger<JsonValidationMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                // Log the received JSON body for debugging
                _logger.LogInformation("Received JSON: " + body);

                try
                {
                    var json = JObject.Parse(body);

                    // Normalize property names to uppercase for case-insensitive comparison
                    var normalizedPropertyNames = new HashSet<string>();
                    foreach (var property in json.Properties())
                    {
                        var upperCaseName = property.Name.ToUpper();
                        if (!normalizedPropertyNames.Add(upperCaseName))
                        {
                            context.Response.StatusCode = StatusCodes.Status400BadRequest;
                            await context.Response.WriteAsync($"Duplicate property '{property.Name}' is not allowed.");
                            return;
                        }
                    }
                }
                catch (JsonReaderException ex)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    _logger.LogError("JSON Parsing Exception: " + ex.Message);
                    return;
                }
            }

            await _next(context);
        }
    }
}








using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;
using System.Collections.Generic;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<JsonValidationMiddleware> _logger;

        public JsonValidationMiddleware(RequestDelegate next, ILogger<JsonValidationMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                // Log the received JSON body for debugging
                _logger.LogInformation("Received JSON: " + body);

                try
                {
                    var json = JObject.Parse(body);

                    // Check for duplicate properties using a loop
                    var propertyNames = new HashSet<string>();
                    foreach (var property in json.Properties())
                    {
                        if (!propertyNames.Add(property.Name))
                        {
                            context.Response.StatusCode = StatusCodes.Status400BadRequest;
                            await context.Response.WriteAsync("Duplicate properties are not allowed.");
                            return;
                        }
                    }
                }
                catch (JsonReaderException ex)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    _logger.LogError("JSON Parsing Exception: " + ex.Message);
                    return;
                }
            }

            await _next(context);
        }
    }
}










using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;

        public JsonValidationMiddleware(RequestDelegate next)
        {
            _next = next;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                try
                {
                    var json = JObject.Parse(body);

                    // Log the JSON received for debugging
                    Console.WriteLine("Received JSON: " + json.ToString());

                    if (json.Properties().GroupBy(p => p.Name).Any(g => g.Count() > 1))
                    {
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync("Duplicate properties are not allowed.");
                        return;
                    }
                }
                catch (JsonReaderException ex)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    Console.WriteLine("JSON Parsing Exception: " + ex.Message);
                    return;
                }
            }

            await _next(context);
        }
    }
}






using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;

        public JsonValidationMiddleware(RequestDelegate next)
        {
            _next = next;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                try
                {
                    var json = JObject.Parse(body);

                    if (json.Properties().GroupBy(p => p.Name).Any(g => g.Count() > 1))
                    {
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync("Duplicate properties are not allowed.");
                        return;
                    }
                }
                catch (JsonReaderException)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    return;
                }
            }

            await _next(context);
        }
    }
}








using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;

        public JsonValidationMiddleware(RequestDelegate next)
        {
            _next = next;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                string body;
                using (var reader = new StreamReader(context.Request.Body, leaveOpen: true))
                {
                    body = await reader.ReadToEndAsync();
                    context.Request.Body.Position = 0;
                }

                try
                {
                    var json = JObject.Parse(body);

                    if (json.Properties().GroupBy(p => p.Name).Any(g => g.Count() > 1))
                    {
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync("Duplicate properties are not allowed.");
                        return;
                    }
                }
                catch (JsonReaderException)
                {
                    context.Response.StatusCode = StatusCodes.Status400BadRequest;
                    await context.Response.WriteAsync("Invalid JSON format.");
                    return;
                }
            }

            await _next(context);
        }
    }
}






To ensure that your API rejects JSON requests with duplicate properties and returns an appropriate error message, you can create a custom middleware to inspect and validate the JSON request body before it reaches the controller. This middleware will reject requests with duplicate properties and return a suitable error response.

### Step-by-Step Guide to Implement Custom Middleware

1. **Create a Middleware Class:**

   - In your main web project, create a new folder named `Middleware`.
   - Inside the `Middleware` folder, create a new class file named `JsonValidationMiddleware.cs`.

2. **Implement the Middleware:**

   - Copy the following code into the `JsonValidationMiddleware.cs` file:

```csharp
using Microsoft.AspNetCore.Http;
using Newtonsoft.Json.Linq;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

namespace YourNamespace.Middleware
{
    public class JsonValidationMiddleware
    {
        private readonly RequestDelegate _next;

        public JsonValidationMiddleware(RequestDelegate next)
        {
            _next = next;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            // Only validate JSON requests
            if (context.Request.ContentType != null && context.Request.ContentType.Contains("application/json"))
            {
                context.Request.EnableBuffering();

                using (var reader = new StreamReader(context.Request.Body))
                {
                    var body = await reader.ReadToEndAsync();
                    var json = JObject.Parse(body);

                    if (json.Properties().GroupBy(p => p.Name).Any(g => g.Count() > 1))
                    {
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync("Duplicate properties are not allowed.");
                        return;
                    }

                    // Reset the request body stream position so the next middleware can read it
                    context.Request.Body.Position = 0;
                }
            }

            // Call the next middleware in the pipeline
            await _next(context);
        }
    }
}
```

3. **Register the Middleware:**

   - Open `Startup.cs` and register the custom middleware in the `Configure` method:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseRouting();

    // Add the custom JSON validation middleware
    app.UseMiddleware<YourNamespace.Middleware.JsonValidationMiddleware>();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Other middleware registrations
}
```

4. **Test Your API:**

   - Test your API with various JSON inputs to ensure that it properly rejects JSON with duplicate properties and returns a 400 Bad Request response with the message "Duplicate properties are not allowed."

### Summary

- Create a custom middleware (`JsonValidationMiddleware`) to inspect the JSON request body for duplicate properties.
- Register the custom middleware in the `Configure` method of `Startup.cs`.
- Test your API to ensure that it correctly rejects invalid JSON requests and returns an appropriate error response.

This approach ensures that your API validates JSON requests at the middleware level, providing a clear error message when duplicate properties are detected.





,....................................




To enforce a rule in your ASP.NET Core API that rejects JSON input containing duplicate properties, you can use a custom model binder or a custom action filter. Here's a step-by-step guide to achieving this:

### Step 1: Create a Custom Action Filter

Create a custom action filter that checks for duplicate properties in the JSON input.

1. **Create the Filter:**

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Filters;
using Newtonsoft.Json.Linq;
using System.IO;
using System.Linq;

public class ValidateJsonFilter : ActionFilterAttribute
{
    public override void OnActionExecuting(ActionExecutingContext context)
    {
        var request = context.HttpContext.Request;
        request.EnableBuffering();

        using (var reader = new StreamReader(request.Body))
        {
            var body = reader.ReadToEnd();
            var json = JObject.Parse(body);

            if (json.Properties().GroupBy(p => p.Name).Any(g => g.Count() > 1))
            {
                context.Result = new BadRequestObjectResult("Duplicate properties are not allowed.");
                return;
            }
        }

        request.Body.Position = 0;
        base.OnActionExecuting(context);
    }
}
```

### Step 2: Apply the Filter Globally or per Controller/Action

1. **Globally:**

In `Startup.cs`, apply the filter globally:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers(options =>
    {
        options.Filters.Add<ValidateJsonFilter>();
    });
}
```

2. **Per Controller/Action:**

Alternatively, apply the filter to specific controllers or actions:

```csharp
[ApiController]
[Route("[controller]")]
public class YourController : ControllerBase
{
    [HttpPost]
    [ValidateJsonFilter]
    public IActionResult YourAction([FromBody] YourModel model)
    {
        // Your action logic here
        return Ok();
    }
}
```

### Step 3: Test Your API

Now, when you send a JSON request with duplicate properties to your API, it will return a 400 Bad Request with the message "Duplicate properties are not allowed."

### Additional Tips

1. **Use `JsonSerializerSettings` to enforce strict JSON parsing:** This can catch some issues at the deserialization level, but it might not catch duplicate properties.

2. **Unit Tests:** Write unit tests to ensure your filter works correctly.

Here's an example of how to test the filter:

```csharp
using Microsoft.AspNetCore.Mvc.Testing;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Xunit;

public class YourApiTests : IClassFixture<WebApplicationFactory<Startup>>
{
    private readonly HttpClient _client;

    public YourApiTests(WebApplicationFactory<Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task Post_WithDuplicateProperties_ShouldReturnBadRequest()
    {
        var json = @"{
            ""UserId"": ""skunarrajak@firstam.com"",
            ""Branch"": ""ANE"",
            ""IsMergeResults"": true,
            ""Property"": [{""ΑΡΝ"": ""10-00092-993-620-0000""}],
            ""property"": [{""ΑΡΝ"": ""20-0-000-001.153""}]
        }";

        var content = new StringContent(json, Encoding.UTF8, "application/json");

        var response = await _client.PostAsync("/your-endpoint", content);

        Assert.Equal(System.Net.HttpStatusCode.BadRequest, response.StatusCode);
    }
}
```

This approach ensures that your API will reject any JSON input containing duplicate properties.
