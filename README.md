# DotnetCertainly! Another approach for handling the second dropdown could involve using server-side rendering without needing JavaScript. We can submit the form when the state dropdown changes, and then the server will populate the counties dropdown based on the selected state.

### Steps:

1. **Submit the Form on State Change**: When the state dropdown changes, submit the form to the server.
2. **Render the Counties Dropdown Based on Selected State**: The server will handle the logic for fetching and rendering the counties based on the selected state.

### Model:

Ensure your model has `SelectedState` and `SelectedCounty` properties:

```csharp
public class StatesAndCounties
{
    public string ReportName { get; set; }
    public List<string> FileNames { get; set; }
    public string SelectedFile { get; set; }
    public List<StateData> States { get; set; }
    public List<CountyData> Counties { get; set; }
    public string SelectedState { get; set; }
    public string SelectedCounty { get; set; }
}
```

### Controller:

Modify your controller to handle the form submission and populate the counties based on the selected state:

```csharp
public class StateAndCountiesController : Controller
{
    private readonly DataService _service;

    public StateAndCountiesController(DataService service)
    {
        _service = service;
    }

    public IActionResult Index(string selectedState = null, string selectedCounty = null)
    {
        var model = new StatesAndCounties
        {
            States = _service.GetAllState(),
            Counties = selectedState != null ? _service.GetAllCountyByState(selectedState) : new List<CountyData>(),
            SelectedState = selectedState,
            SelectedCounty = selectedCounty,
            ReportName = "Report Name",
            FileNames = new List<string> { "ATSCSV04_PROD_PlantInvestigativeSearchDetailsReport" }
        };

        return View(model);
    }
}
```

### View:

Here’s the updated view that submits the form when the state dropdown changes and renders the counties based on the selected state:

```html
@model YourNamespace.Models.StatesAndCounties

<form method="get" action="@Url.Action("Index", "StateAndCounties")">
    <div>
        <label style="margin-right: 15px; margin-top: 10px;">Select State: </label>
        <select id="stateDropdown" name="selectedState" onchange="this.form.submit();">
            <option value="">-- Select State --</option>
            @foreach (var state in Model.States)
            {
                <option value="@state.StateName" @(state.StateName == Model.SelectedState ? "selected=\"selected\"" : "")>@state.StateName</option>
            }
        </select>
    </div>

    <div>
        <label for="accnum">Account Number:</label>
        <input type="text" id="accnum" name="accnum" />
    </div>

    <div>
        <label style="margin-right: 15px; margin-top: 10px;">Select County: </label>
        <select id="countyDropdown" name="selectedCounty">
            <option value="">-- Select County --</option>
            @foreach (var county in Model.Counties)
            {
                <option value="@county.CountyName" @(county.CountyName == Model.SelectedCounty ? "selected=\"selected\"" : "")>@county.CountyName</option>
            }
        </select>
    </div>

    <div>
        <input type="submit" value="Submit" />
    </div>
</form>
```

### Explanation:

- **Form Submission on State Change**: When the user selects a state, the form is automatically submitted to the server.
- **Server-Side Logic**: The server handles the form submission, retrieves the list of counties for the selected state, and returns the updated model to the view.
- **Razor Syntax**: The view uses Razor syntax to set the `selected` attribute for the state and county options based on the model properties.

### Debugging Tips:

1. **Verify Data Population**: Ensure the `States` and `Counties` properties in the model are correctly populated.
2. **Check Selected Properties**: Confirm that `SelectedState` and `SelectedCounty` are being set correctly in the model and used in the view.
3. **HTML Inspection**: Inspect the rendered HTML to ensure the `selected` attribute is applied correctly to the dropdown options.

This approach ensures the counties dropdown is updated based on the selected state without requiring JavaScript.
