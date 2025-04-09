// Assuming resultJson is your JSON string
JSONObject root = new JSONObject(resultJson);

// Now navigate inside
JSONArray transactions = root.getJSONArray("Transactions");

for (int i = 0; i < transactions.length(); i++) {
    JSONObject transaction = transactions.getJSONObject(i);

    if (transaction.has("LineItems") && !transaction.isNull("LineItems")) {
        JSONArray lineItems = transaction.getJSONArray("LineItems");

        for (int j = 0; j < lineItems.length(); j++) {
            JSONObject lineItem = lineItems.getJSONObject(j);

            if (lineItem.has("BillId")) {
                String billId = lineItem.get("BillId").toString();
                System.out.println("Found BillId inside LineItems: " + billId);
            }
        }
    } else {
        System.out.println("LineItems not found in transaction " + i);
    }
}




System.out.println("Item: " + item.toString());

// First check if LineItems array exists
if (item.has("LineItems")) {
    JSONArray lineItems = item.getJSONArray("LineItems");

    // Loop through each item inside LineItems
    for (int i = 0; i < lineItems.length(); i++) {
        JSONObject lineItem = lineItems.getJSONObject(i);

        // Now check if BillId exists inside lineItem
        if (lineItem.has("BillId")) {
            String billId = lineItem.get("BillId").toString();
            System.out.println("Found BillId inside LineItems: " + billId);
        }
    }
} else {
    System.out.println("No LineItems found.");
}

System.out.println("Item: " + item.toString());

if (item.has("BillId")) {
    String billId = item.getString("BillId");
    System.out.println("Found BillId directly: " + billId);
} else {
    Iterator<String> keys = item.keys();
    while (keys.hasNext()) {
        String key = keys.next();
        Object value = item.get(key);
        if (value instanceof JSONObject) {
            JSONObject nestedObject = (JSONObject) value;
            if (nestedObject.has("BillId")) {
                String billId = nestedObject.getString("BillId");
                System.out.println("Found BillId inside nested object: " + billId);
            }
        }
    }
}.



  JSONArray dataItems = resultJson.getJSONArray("DataItem");
for (int i = 0; i < dataItems.length(); i++) {
    JSONObject item = dataItems.getJSONObject(i);
    if (item.has("billid")) {
        String billid = item.getString("billid");
        System.out.println("Bill ID: " + billid);
    } else {
        // Check deeper if billid is inside a nested object
        Iterator<String> keys = item.keys();
        while (keys.hasNext()) {
            String key = keys.next();
            Object value = item.get(key);
            if (value instanceof JSONObject) {
                JSONObject nested = (JSONObject) value;
                if (nested.has("billid")) {
                    String billid = nested.getString("billid");
                    System.out.println("Bill ID (nested): " + billid);
                }
            }
        }
    }
}
  
  
  
  <ItemGroup>
    <FunctionsPreservedDependencies Include="System.Memory.Data.dll" />
  </ItemGroup>

<PackageReference Include="System.Memory" Version="4.5.5">
  <IncludeAssets>all</IncludeAssets>
  <PrivateAssets>all</PrivateAssets>
</PackageReference>

dotnet list package --include-transitive

global.json..{
  "sdk": {
    "version": "8.0.204"
  }
}

{
  "sdk": {
    "version": "8.0.204"
  }
}
dotnet add package System.Memory --version 4.5.5



<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <AzureFunctionsVersion>v4</AzureFunctionsVersion>
    <OutputType>Exe</OutputType>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
    <RootNamespace>DTSQuickHit.Functions</RootNamespace>
    <FunctionsWorkerRuntime>dotnet-isolated</FunctionsWorkerRuntime> <!-- ADD THIS -->
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.Http" Version="3.2.0" />
    <PackageReference Include="Newtonsoft.Json" Version="13.0.3" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.Functions.Worker" Version="1.21.0" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.ServiceBus" Version="5.22.2" />
    <PackageReference Include="Microsoft.Azure.Functions.Worker.Sdk" Version="1.20.0" />
  </ItemGroup>

  <ItemGroup>
    <Content Include="appsettings.Development.json">
      <CopyToOutputDirectory>Always</CopyToOutputDirectory>
      <ExcludeFromSingleFile>true</ExcludeFromSingleFile>
      <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
    </Content>
    <Content Include="appsettings.json">
      <CopyToOutputDirectory>Always</CopyToOutputDirectory>
      <ExcludeFromSingleFile>true</ExcludeFromSingleFile>
      <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
    </Content>
  </ItemGroup>

  <ItemGroup>
    <ProjectReference Include="..\DTSQuickHit.BusinessService\DTSQuickHit.BusinessService.csproj" />
  </ItemGroup>

  <ItemGroup>
    <None Update="host.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
      <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
  </ItemGroup>

</Project>



.











namespace MyApi.Models
{
    public class EchoRequestDto
    {
        public string Name { get; set; }
        public string Message { get; set; }
    }
}
using Microsoft.AspNetCore.Mvc;
using MyApi.Models;

namespace MyApi.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class EchoController : ControllerBase
    {
        [HttpPost]
        public IActionResult EchoJson([FromBody] EchoRequestDto requestData)
        {
            return Ok(requestData);
        }
    }
}



private DataTable ReadExcel(string filePath, string currentUser)
{
    DataTable dt = new DataTable();

    using (var workbook = new XLWorkbook(filePath))
    {
        var worksheet = workbook.Worksheet("Leave Plan"); // Load "Leave Plan" sheet
        var rows = worksheet.RangeUsed().RowsUsed().ToList();

        if (rows.Count < 2)
            return dt; // No data available

        // Add column headers (from first row)
        var headerRow = rows[0].Cells().Select(c => c.Value.ToString()).ToList();
        foreach (var col in headerRow)
            dt.Columns.Add(col);

        // Read data rows and filter by username
        foreach (var row in rows.Skip(1))
        {
            var rowData = row.Cells().Select(c => c.Value.ToString()).ToList();

            if (rowData.Count > 1)
            {
                string employeeName = rowData[1].ToLower().Replace(" ", "");
                if (employeeName.Contains(currentUser))
                {
                    dt.Rows.Add(rowData.ToArray());
                }
            }
        }
    }

    return dt;
}

<Window x:Class="ExcelDataApp.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="450" Width="800">
    <Grid>
        <Button Content="Load My Data" Click="LoadData_Click" Width="120" Height="30" Margin="10"/>
        <DataGrid x:Name="dataGrid" AutoGenerateColumns="True" Margin="10,50,10,10"/>
    </Grid>
</Window>
...
using System;
using System.Data;
using System.Linq;
using System.Windows;
using ClosedXML.Excel;

namespace ExcelDataApp
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private void LoadData_Click(object sender, RoutedEventArgs e)
        {
            string filePath = "C:\\Path\\To\\Your\\File.xlsx"; // Update the correct file path
            string currentUser = Environment.UserName.ToLower(); // Get PC username

            DataTable dt = ReadExcel(filePath, currentUser);

            if (dt.Rows.Count > 0)
            {
                dataGrid.ItemsSource = dt.DefaultView;
            }
            else
            {
                MessageBox.Show("No matching data found!");
            }
        }

        private DataTable ReadExcel(string filePath, string currentUser)
        {
            DataTable dt = new DataTable();

            using (var workbook = new XLWorkbook(filePath))
            {
                var worksheet = workbook.Worksheet(1); // Read first worksheet
                var rows = worksheet.RangeUsed().RowsUsed().ToList();

                if (rows.Count < 2)
                    return dt; // No data available

                // Add column headers (from first row)
                var headerRow = rows[0].Cells().Select(c => c.Value.ToString()).ToList();
                foreach (var col in headerRow)
                    dt.Columns.Add(col);

                // Read data rows and filter by username
                foreach (var row in rows.Skip(1))
                {
                    var rowData = row.Cells().Select(c => c.Value.ToString()).ToList();

                    if (rowData.Count > 1)
                    {
                        string employeeName = rowData[1].ToLower().Replace(" ", "");
                        if (employeeName.Contains(currentUser))
                        {
                            dt.Rows.Add(rowData.ToArray());
                        }
                    }
                }
            }

            return dt;
        }
    }
}



using System;
using System.Data;
using System.IO;
using System.Linq;
using System.Windows;
using ClosedXML.Excel;
using System.DirectoryServices.AccountManagement;

public class ExcelReader
{
    public static DataTable ReadExcel(string filePath)
    {
        DataTable dt = new DataTable();

        // Get full name from Active Directory
        string currentUser;
        using (var context = new PrincipalContext(ContextType.Domain))
        {
            var user = UserPrincipal.Current;
            currentUser = user?.DisplayName ?? Environment.UserName;
        }

        if (File.Exists(filePath))
        {
            using (var workbook = new XLWorkbook(filePath))
            {
                var worksheet = workbook.Worksheet(1); // Read first sheet
                bool firstRow = true;
                int userColumnIndex = -1;

                foreach (var row in worksheet.RowsUsed())
                {
                    var cellValues = row.Cells().Select(cell => cell.Value.ToString()).ToArray();

                    if (firstRow)
                    {
                        // Add column headers
                        foreach (var cell in cellValues)
                            dt.Columns.Add(cell);

                        // Find the index of the "Employee Name" column
                        userColumnIndex = Array.IndexOf(cellValues, "Employee Name");
                        firstRow = false;
                    }
                    else if (userColumnIndex != -1 && userColumnIndex < cellValues.Length)
                    {
                        // Check if the row matches the logged-in user's full name
                        if (cellValues[userColumnIndex].Equals(currentUser, StringComparison.OrdinalIgnoreCase))
                        {
                            dt.Rows.Add(cellValues);
                        }
                    }
                }
            }
        }
        else
        {
            MessageBox.Show("File not found!");
        }

        return dt;
    }
}

Install-Package System.DirectoryServices.AccountManagement


private DataTable ReadExcel(string filePath)
{
    DataTable dt = new DataTable();

    // Get full name from Active Directory
    string currentUser;
    using (var context = new PrincipalContext(ContextType.Domain))
    {
        var user = UserPrincipal.Current;
        currentUser = user?.DisplayName ?? Environment.UserName;
    }

    if (File.Exists(filePath))
    {
        using (var workbook = new XLWorkbook(filePath))
        {
            var worksheet = workbook.Worksheet(1); // Read first sheet
            bool firstRow = true;
            int userColumnIndex = -1;

            foreach (var row in worksheet.RowsUsed())
            {
                var cellValues = row.Cells().Select(cell => cell.Value.ToString()).ToArray();

                if (firstRow)
                {
                    // Add column headers
                    foreach (var cell in cellValues)
                        dt.Columns.Add(cell);

                    // Find the index of the "Employee Name" column
                    userColumnIndex = Array.IndexOf(cellValues, "Employee Name");
                    firstRow = false;
                }
                else if (userColumnIndex != -1 && userColumnIndex < cellValues.Length)
                {
                    // Check if this row matches the logged-in user's full name
                    if (cellValues[userColumnIndex].Equals(currentUser, StringComparison.OrdinalIgnoreCase))
                    {
                        while (dt.Columns.Count < cellValues.Length)
                            dt.Columns.Add("Column" + dt.Columns.Count);

                        dt.Rows.Add(cellValues);
                    }
                }
            }
        }
    }
    else
    {
        MessageBox.Show("File not found!");
    }

    return dt;
}

...





Install-Package ClosedXML
Install-Package DocumentFormat.OpenXml

<Window x:Class="WPFExcelReader.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Excel Reader" Height="450" Width="800">
    <Grid>
        <Button Content="Load My Data" Width="150" Height="30" HorizontalAlignment="Left" VerticalAlignment="Top" Margin="10"
                Click="LoadExcelData"/>
        
        <DataGrid x:Name="ExcelDataGrid" AutoGenerateColumns="True" Margin="10,50,10,10"/>
    </Grid>
</Window>

....

using System;
using System.Data;
using System.IO;
using System.Linq;
using System.Windows;
using ClosedXML.Excel;

namespace WPFExcelReader
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private void LoadExcelData(object sender, RoutedEventArgs e)
        {
            string networkFilePath = @"\\AZUVNAPPDTLM013.datatrace.local\DTSStaging\DeveloperShare\FAI\2025\FAI - Planned Leave.xlsx";
            DataTable dt = ReadExcel(networkFilePath);
            ExcelDataGrid.ItemsSource = dt.DefaultView; // Bind to DataGrid
        }

        private DataTable ReadExcel(string filePath)
        {
            DataTable dt = new DataTable();
            string currentUser = Environment.UserName; // Get logged-in user

            if (File.Exists(filePath))
            {
                using (var workbook = new XLWorkbook(filePath))
                {
                    var worksheet = workbook.Worksheet(1); // Read first sheet
                    bool firstRow = true;
                    int userColumnIndex = -1;

                    foreach (var row in worksheet.RowsUsed())
                    {
                        var cellValues = row.Cells().Select(cell => cell.Value.ToString()).ToArray();

                        if (firstRow)
                        {
                            // Add column headers
                            foreach (var cell in cellValues)
                                dt.Columns.Add(cell);

                            // Find the index of the "Employee Name" column
                            userColumnIndex = Array.IndexOf(cellValues, "Employee Name");
                            firstRow = false;
                        }
                        else if (userColumnIndex != -1 && userColumnIndex < cellValues.Length)
                        {
                            // Check if this row matches the logged-in user
                            if (cellValues[userColumnIndex].Equals(currentUser, StringComparison.OrdinalIgnoreCase))
                            {
                                // Ensure the DataTable has enough columns
                                while (dt.Columns.Count < cellValues.Length)
                                    dt.Columns.Add("Column" + dt.Columns.Count);

                                dt.Rows.Add(cellValues);
                            }
                        }
                    }
                }
            }
            else
            {
                MessageBox.Show("File not found!");
            }

            return dt;
        }
    }
}


<Window x:Class="WPFExcelReader.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Excel Reader" Height="450" Width="800">
    <Grid>
        <Button Content="Load Excel Data" Width="150" Height="30" HorizontalAlignment="Left" VerticalAlignment="Top" Margin="10"
                Click="LoadExcelData"/>
        
        <DataGrid x:Name="ExcelDataGrid" AutoGenerateColumns="True" Margin="10,50,10,10"/>
    </Grid>
</Window>

using System;
using System.Data;
using System.IO;
using System.Linq;
using System.Windows;
using ClosedXML.Excel;

namespace WPFExcelReader
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private void LoadExcelData(object sender, RoutedEventArgs e)
        {
            string networkFilePath = @"\\AZUVNAPPDTLM013.datatrace.local\DTSStaging\DeveloperShare\FAI\2025\FAI - Planned Leave.xlsx";
            DataTable dt = ReadExcel(networkFilePath);
            ExcelDataGrid.ItemsSource = dt.DefaultView; // Bind to DataGrid
        }

        private DataTable ReadExcel(string filePath)
        {
            DataTable dt = new DataTable();

            if (File.Exists(filePath))
            {
                using (var workbook = new XLWorkbook(filePath))
                {
                    var worksheet = workbook.Worksheet(1); // Read first sheet
                    bool firstRow = true;
                    foreach (var row in worksheet.RowsUsed())
                    {
                        if (firstRow)
                        {
                            foreach (var cell in row.Cells())
                                dt.Columns.Add(cell.Value.ToString()); // Add column headers
                            firstRow = false;
                        }
                        else
                        {
                            dt.Rows.Add(row.Cells().Select(cell => cell.Value.ToString()).ToArray());
                        }
                    }
                }
            }
            else
            {
                MessageBox.Show("File not found!");
            }

            return dt;
        }
    }
}




.....
String taxYear = (inputElement != null) ? inputElement.attr("value") : "";



import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;

public class ExtractInputValue {
    public static void main(String[] args) {
        // Sample HTML containing the input field
        String html = "<input name=\"dnn$ctl01$TaxYear\" type=\"text\" id=\"dnn_ctl01_TaxYear\" style=\"display: none;\" value=\"2025\" />";

        // Parse the HTML
        Document doc = Jsoup.parse(html);

        // Select the input field using its ID
        Element inputElement = doc.selectFirst("input[id=dnn_ctl01_TaxYear]");

        if (inputElement != null) {
            String value = inputElement.attr("value");
            System.out.println("Extracted Value: " + value);
        } else {
            System.out.println("Input field not found!");
        }
    }
}


import java.util.*;

public class CookieCleaner {
    public static void main(String[] args) {
        List<String> cookies = httpWrapper.getHeaders("Set-Cookie");

        // Define unwanted attributes
        String[] unwantedAttributes = { "path=/;", "HttpOnly", "SameSite=Lax" };

        // Use LinkedHashSet to remove duplicates while maintaining order
        Set<String> uniqueCookies = new LinkedHashSet<>(cookies);

        // Clean cookies
        List<String> cleanedCookies = new ArrayList<>();
        for (String cookie : uniqueCookies) {
            for (String attr : unwantedAttributes) {
                cookie = cookie.replace(attr, "").trim();
            }
            // Remove "expires" attribute along with its date
            cookie = cookie.replaceAll("expires=[^;]+;?", "").trim();
            cleanedCookies.add(cookie.replaceAll(";\\s*;", ";").replaceAll(";\\s*$", ""));
        }

        // Output cleaned cookies
        System.out.println("Filtered Cookies:");
        cleanedCookies.forEach(System.out::println);
    }
}
..
import java.util.*;

public class CookieCleaner {
    public static void main(String[] args) {
        // Simulate fetching cookies from headers
        List<String> cookies = httpWrapper.getHeaders("Set-Cookie");

        // Define unwanted attributes
        String[] unwantedAttributes = { "path=/;", "HttpOnly", "SameSite=Lax" };

        // Use LinkedHashSet to remove duplicates while maintaining order
        Set<String> uniqueCookies = new LinkedHashSet<>(cookies);

        // Clean cookies by removing unwanted attributes
        List<String> cleanedCookies = new ArrayList<>();
        for (String cookie : uniqueCookies) {
            for (String attr : unwantedAttributes) {
                cookie = cookie.replace(attr, "").trim();
            }
            cleanedCookies.add(cookie.replaceAll(";\\s*;", ";").replaceAll(";\\s*$", ""));
        }

        // Output cleaned cookies
        System.out.println("Filtered Cookies:");
        cleanedCookies.forEach(System.out::println);
    }
}


import java.util.*;

public class Main {
    public static void main(String[] args) {
        // Simulating the response from httpWrapper.getHeaders("Set-Cookie")
        List<String> cookies = httpWrapper.getHeaders("Set-Cookie");

        // Using LinkedHashSet to remove duplicates while maintaining order
        Set<String> uniqueCookies = new LinkedHashSet<>(cookies);

        // Converting back to a list if needed
        List<String> filteredCookies = new ArrayList<>(uniqueCookies);

        // Print the filtered cookies
        System.out.println("Filtered Unique Cookies:");
        for (String cookie : filteredCookies) {
            System.out.println(cookie);
        }
    }
}



using System;
using System.Collections.Generic;
using System.Linq;

class Program
{
    static void Main()
    {
        // Get cookies dynamically
        List<string> cookies = httpWrapper.getHeaders("Set-Cookie");

        // Remove duplicates dynamically
        List<string> uniqueCookies = cookies.Distinct().ToList();

        // Print the unique cookies
        Console.WriteLine("Filtered Unique Cookies:");
        foreach (string cookie in uniqueCookies)
        {
            Console.WriteLine(cookie);
        }
    }
}

import java.util.List;
import java.util.ArrayList;

public class CookieExtractor {
    public static void main(String[] args) {
        // Simulating the cookies retrieved from httpWrapper.getHeaders("Set-Cookie")
        List<String> cookies = new ArrayList<>();
        cookies.add("dnn_IsMobile=False; path=/; HttpOnly");
        cookies.add("language=en-US; path=/; HttpOnly");
        cookies.add("ASPXANONYMOUS=XGO-Je8_EIofYM1Wi7unGf2buLv6g-wDr1ZxEWak1X6anmJSqGyzHr1euQkvnWqr6f2nfg4oPIiHCK8-0Fv3a1w4QFCj2yazEyI2ew4K4Ytbecjn0; expires=Thu, 15-May-2025 15:31:26 GMT; path=/; HttpOnly");
        cookies.add("ASP.NET_SessionId=m1r2jpxosz42sswp10uugvlw; path=/; HttpOnly; SameSite=Lax");

        // Extracting required cookies
        String outputCookie = "";
        for (String cookie : cookies) {
            if (cookie.startsWith("dnn_IsMobile") || cookie.startsWith("language") || 
                cookie.startsWith("ASPXANONYMOUS") || cookie.startsWith("ASP.NET_SessionId")) {
                outputCookie += cookie.split(";")[0] + "; \n"; // Extract key-value pair only
            }
        }

        // Printing the required cookies
        System.out.println("Output Cookies:");
        System.out.println(outputCookie);
    }
}






import java.util.regex.*;

public class Main {
    public static void main(String[] args) {
        String cookie = "dnn IsMobile=False; language=en-US; ASPXANONYMOUS=XG0-Je8_EIofYM1Wi7unGf2bulV6g-wDr1zXEWak1X6anmJSqGyzHr1euQkvnWqr6f2nfg4oPIiHCK8-0Fv3a1w4QFCj2yazEyI2ew4K4Ytbecjm0; ASP.NET_SessionId=m1r2jpxosz42sswp10uugvlw";

        Pattern pattern = Pattern.compile("ASP.NET_SessionId=([^;]+)");
        Matcher matcher = pattern.matcher(cookie);

        if (matcher.find()) {
            System.out.println("ASP.NET SessionId: " + matcher.group(1));
        } else {
            System.out.println("ASP.NET SessionId not found.");
        }
    }
}




Dev Note: The API first checks for DocumentNumber in the request. If DocumentNumber is not provided, then as a second priority, it will check for CmtDocumentNo and fetch records using it.



Dev Note: In the request, the documentno field is incorrect. We need to pass DocumentNumber instead, as it is correctly defined in the DTO.


using System;
using System.Text.RegularExpressions;

public class InstrumentParserService
{
    public string ParseInstrumentInput(string input)
    {
        if (string.IsNullOrWhiteSpace(input)) return "Invalid input";

        // Remove all delimiters
        string cleanedInput = Regex.Replace(input, @"[\/.,:\s\-_\\n]", "");

        // Extract year (first 4 or last 4 digits)
        if (cleanedInput.Length > 4)
        {
            string firstFour = cleanedInput.Substring(0, 4);
            string lastFour = cleanedInput.Substring(cleanedInput.Length - 4);

            if (IsValidYear(firstFour)) return $"{firstFour}.{cleanedInput.Substring(4)}";
            if (IsValidYear(lastFour)) return $"{lastFour}.{cleanedInput.Substring(0, cleanedInput.Length - 4)}";
        }

        return "Invalid format";
    }

    private bool IsValidYear(string yearStr) => int.TryParse(yearStr, out int year) && year >= 2020 && year <= DateTime.Now.Year;
}





Dev Note:

Instrument search type information is not available in type-ahead results. We are only using it to pass as a parameter to the stored procedure (SP).



Task: Standardizing Instrument Number Lookup in the NTP API

Your system needs to support instrument number lookups in multiple formats. Users can enter instrument numbers with or without delimiters, and the system should correctly identify, format, and return standardized values.


---

Supported Formats & How to Handle Them

1. Year-Instrument Number Format (YYYY.InstrumentNumber)

✅ What to do?

Users enter the year first, followed by the instrument number.

The input can have delimiters (/ . \ : - _).


✅ Example Inputs → Expected Outputs:
| Input | Standardized Output | |--------|---------------------| | 2024/123456 | 2024.123456 | | 2023-987654 | 2023.987654 | | 2025.654321 | 2025.654321 |

✅ Implementation Steps:

1. Extract the year (first four digits, must be between 2020 and the current year).


2. Extract the instrument number (remaining digits).


3. Format the output as YYYY.InstrumentNumber.



✅ How to validate?

Enter values with different delimiters and verify that the API correctly formats them.



---

2. Instrument Number - Year Format (Reverse Format)

✅ What to do?

Users enter the instrument number first, followed by the year.

The input can have delimiters (/ . \ : - _).


✅ Example Inputs → Expected Outputs:
| Input | Standardized Output | |--------|---------------------| | 123456-2024 | 2024.123456 | | 987654/2025 | 2025.987654 | | 654321.2023 | 2023.654321 |

✅ Implementation Steps:

1. Extract the year (last four digits, must be between 2020 and the current year).


2. Extract the instrument number (remaining digits).


3. Rearrange it into standard format → YYYY.InstrumentNumber.



✅ How to validate?

Enter values in reverse format and ensure the API correctly identifies the year and instrument number.



---

3. Year-Instrument Number Without Delimiters

✅ What to do?

Users may not use any delimiters, e.g., 2024123456.

The system should detect the first four digits as the year and the rest as the instrument number.


✅ Example Inputs → Expected Outputs:
| Input | Standardized Output | |--------|---------------------| | 2024123456 | 2024.123456 | | 2023987654 | 2023.987654 | | 2025654321 | 2025.654321 |

✅ Implementation Steps:

1. Extract the first four digits as the year (check if it is between 2020 and the current year).


2. Extract the remaining digits as the instrument number.


3. Format as YYYY.InstrumentNumber.



✅ How to validate?

Test inputs without delimiters and check if the API correctly identifies the year and instrument number.



---

4. Instrument Number - Year Without Delimiters (Reverse Format)

✅ What to do?

Users enter the instrument number first, followed by the year, without delimiters.

Example: 1234562024.


✅ Example Inputs → Expected Outputs:
| Input | Recognized Year | Instrument Number | Standardized Output | |--------|---------------|------------------|---------------------| | 1234562024 | 2024 | 123456 | 2024.123456 | | 9876542025 | 2025 | 987654 | 2025.987654 | | 6543212023 | 2023 | 654321 | 2023.654321 |

✅ Implementation Steps:

1. Identify the last four digits as the year (must be between 2020 and the current year).


2. Extract the remaining digits as the instrument number.


3. Rearrange it into standard format → YYYY.InstrumentNumber.



✅ How to validate?

Enter values without delimiters in reverse format and verify that the system correctly identifies the year and instrument number.



---

5. Book-Page Format Lookup

✅ What to do?

Allow users to input book and page numbers separately.

Example:

Book: "ABC123"

Page: "45"


The system should return matching records based on book and page number.


✅ How to validate?

Enter different book and page values and ensure the API correctly finds matching data.



---

Final Steps to Implement in .NET Core API

1. Parse the input string to detect whether it follows:

Year-InstrumentNumber

InstrumentNumber-Year

With or without delimiters



2. Extract and validate the year (2020–current year) and instrument number.


3. Standardize the output format as YYYY.InstrumentNumber.


4. Write unit tests to check all scenarios.


5. Ensure API returns correct standardized values for each input format.




---

Example API Response

Input: 123456-2024

Output:

{
  "input": "123456-2024",
  "standardized": "2024.123456",
  "status": "success"
}


---

This ensures that no matter how users enter the instrument number, your API will always return a consistent, standardized format.

Let me know if you need help with implementation or code samples!


