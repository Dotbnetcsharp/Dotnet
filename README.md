Sure, here is the full code setup to dynamically update the county dropdown based on the selected state using JavaScript.

### 1. Model Classes

First, ensure your model classes are defined correctly:

```csharp
public class StatesAndCounties
{
    public string ReportName { get; set; }
    public List<string> FileNames { get; set; }
    public string SelectedFile { get; set; }
    public List<StateData> States { get; set; }
    public List<CountyData> Counties { get; set; }
}

public class StateData
{
    public string StateName { get; set; }
}

public class CountyData
{
    public string CountyName { get; set; }
}
```

### 2. Controller

Here is the controller code to handle the request and return JSON data:

```csharp
using Microsoft.AspNetCore.Mvc;
using YourNamespace.Models; // Adjust the namespace as needed
using YourNamespace.Services; // Adjust the namespace as needed

public class StateAndCountiesController : Controller
{
    private readonly DataService _service;

    public StateAndCountiesController(DataService service)
    {
        _service = service;
    }

    public IActionResult Index()
    {
        var model = new StatesAndCounties
        {
            States = _service.GetAllState(),
            Counties = new List<CountyData>(), // Start with an empty counties list
            ReportName = "Report Name",
            FileNames = new List<string> { "ATSCV04_PROD_PlantInvestigativeSearchDetailsReport" }
        };
        return View(model);
    }

    [HttpGet]
    public IActionResult GetCounties(string state)
    {
        var counties = _service.GetAllCountyByState(state);
        return Json(counties);
    }
}
```

### 3. View (Index.cshtml)

Here is the Razor view with JavaScript to dynamically update the county dropdown:

```html
@model YourNamespace.Models.StatesAndCounties

<!DOCTYPE html>
<html>
<head>
    <title>Select State and County</title>
</head>
<body>
    <div>
        <label style="margin-right: 15px; margin-top: 10px;">Select State: </label>
        <select id="stateDropdown" name="selectedState" onchange="fetchCounties()">
            <option value="">-- Select State --</option>
            @foreach (var state in Model.States)
            {
                <option value="@state.StateName">@state.StateName</option>
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
        </select>
    </div>

    <div>
        <input type="submit" value="Submit" />
    </div>

    <script type="text/javascript">
        function fetchCounties() {
            var selectedState = document.getElementById("stateDropdown").value;
            var countyDropdown = document.getElementById("countyDropdown");
            countyDropdown.innerHTML = '<option value="">-- Select County --</option>'; // Reset county dropdown

            if (selectedState) {
                fetch('@Url.Action("GetCounties", "StateAndCounties")?state=' + selectedState)
                    .then(response => response.json())
                    .then(data => {
                        data.forEach(county => {
                            var option = document.createElement("option");
                            option.value = county.CountyName;
                            option.text = county.CountyName;
                            countyDropdown.appendChild(option);
                        });
                    })
                    .catch(error => console.error('Error fetching counties:', error));
            }
        }
    </script>
</body>
</html>
```

### 4. Data Service

Ensure your data service is correctly defined to provide the necessary data:

```csharp
using System.Collections.Generic;
using YourNamespace.Models; // Adjust the namespace as needed

public class DataService
{
    public List<StateData> GetAllState()
    {
        // Mock data or retrieve from database
        return new List<StateData>
        {
            new StateData { StateName = "State1" },
            new StateData { StateName = "State2" },
            new StateData { StateName = "State3" }
        };
    }

    public List<CountyData> GetAllCountyByState(string state)
    {
        // Mock data or retrieve from database based on state
        if (state == "State1")
        {
            return new List<CountyData>
            {
                new CountyData { CountyName = "County1" },
                new CountyData { CountyName = "County2" }
            };
        }
        else if (state == "State2")
        {
            return new List<CountyData>
            {
                new CountyData { CountyName = "County3" },
                new CountyData { CountyName = "County4" }
            };
        }
        else
        {
            return new List<CountyData>();
        }
    }
}
```

### Summary

This setup includes model definitions, a controller to handle the data and JSON response, and a view with JavaScript to dynamically update the county dropdown based on the selected state. Make sure to adjust namespaces and file paths as necessary to fit your project structure.
