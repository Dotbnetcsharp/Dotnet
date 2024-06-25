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
