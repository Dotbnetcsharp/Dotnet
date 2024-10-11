// Execute the command and populate DataSet
objDataSet = objDatabase.ExecuteDataSet(objCommand);

// Start building the SQL command with parameters
StringBuilder sqlWithParams = new StringBuilder();

// Append the command text (Stored Procedure Name or SQL Text)
sqlWithParams.AppendLine(objCommand.CommandText);

// Loop through each parameter and append its value
foreach (SqlParameter param in objCommand.Parameters)
{
    if (param.SqlDbType == SqlDbType.Structured) // Handling Table-Valued Parameters (TVP)
    {
        // Handle Table-Valued Parameters
        DataTable table = param.Value as DataTable;
        if (table != null)
        {
            sqlWithParams.AppendLine($"DECLARE @{param.ParameterName} AS {param.TypeName};");
            sqlWithParams.AppendLine($"INSERT INTO @{param.ParameterName} VALUES ");
            foreach (DataRow row in table.Rows)
            {
                sqlWithParams.Append("(");
                for (int i = 0; i < table.Columns.Count; i++)
                {
                    sqlWithParams.Append(row[i].ToString());
                    if (i < table.Columns.Count - 1)
                    {
                        sqlWithParams.Append(", ");
                    }
                }
                sqlWithParams.AppendLine(");");
            }
        }
    }
    else
    {
        // For other types of parameters, append as SQL declare and set statements
        sqlWithParams.AppendLine($"DECLARE @{param.ParameterName} {param.SqlDbType};");
        sqlWithParams.AppendLine($"SET @{param.ParameterName} = '{param.Value}';");
    }
}

// Now, the SQL command and parameters have been built
string finalSqlCommand = sqlWithParams.ToString();

// You can log the SQL command or display it
Console.WriteLine(finalSqlCommand); // Use your logging method here if required






if (Session["Username"] != null)
{
    // Retrieve the path from the session
    string strpath = Session["PopupReportPath"].ToString();

    // Step 1: Replace "https:\" or "http:\" with "https://" or "http://"
    if (strpath.StartsWith("https:\\") || strpath.StartsWith("http:\\"))
    {
        strpath = strpath.Replace("https:\\", "https://").Replace("http:\\", "http://");
    }

    // Step 2: Replace ALL backslashes '\' (single and double) with forward slashes '/'
    strpath = strpath.Replace("\\", "/");

    // Now strpath should have the correct format
    Console.WriteLine(strpath);  // Output or use the valid URL

    // If you need to set the iframe with the valid URL
    LiteralControl literal = new LiteralControl();
    literal.Text = $"<iframe name='PopupReportPopup' id='12' frameborder='no' scrolling='auto' height='800px' width='85%' src='{strpath}'></iframe>";
    div1.Controls.Add(literal);
}
<!--
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Multiple Images with Hyperlinks</title>
    <style>
        img {
            max-width: 100%;
            height: auto;
            margin-top: 20px;
        }
        .image-section {
            margin-bottom: 50px; /* Add spacing between sections */
        }
    </style>
</head>
<body>
    <h2>Image Links</h2>
    <!-- List of hyperlinks -->
    <ul>
        <li><a href="#image1">Image 1</a></li>
        <li><a href="#image2">Image 2</a></li>
        <li><a href="#image3">Image 3</a></li>
    </ul>

    <h2>Images</h2>
    <!-- Image sections with ids -->
    <div id="image1" class="image-section">
        <h3>Image 1</h3>
        <img src="image1.jpg" alt="Image 1">
    </div>

    <div id="image2" class="image-section">
        <h3>Image 2</h3>
        <img src="image2.jpg" alt="Image 2">
    </div>

    <div id="image3" class="image-section">
        <h3>Image 3</h3>
        <img src="image3.jpg" alt="Image 3">
    </div>

</body>
</html>
-->




<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Multiple Images with Hyperlinks</title>
    <style>
        img {
            max-width: 100%;
            height: auto;
            margin-top: 20px;
        }
        .image-section {
            margin-bottom: 50px; /* Add spacing between sections */
        }
    </style>
</head>
<body>
    <h2>Image Links</h2>
    <!-- List of hyperlinks -->
    <ul>
        <li><a href="#image1">Image 1</a></li>
        <li><a href="#image2">Image 2</a></li>
        <li><a href="#image3">Image 3</a></li>
    </ul>

    <h2>Images</h2>
    <!-- Image sections with ids -->
    <div id="image1" class="image-section">
        <h3>Image 1</h3>
        <img src="image1.jpg" alt="Image 1">
    </div>

    <div id="image2" class="image-section">
        <h3>Image 2</h3>
        <img src="image2.jpg" alt="Image 2">
    </div>

    <div id="image3" class="image-section">
        <h3>Image 3</h3>
        <img src="image3.jpg" alt="Image 3">
    </div>

</body>
</html>

public class NameSearchFilterQualifiersDTO
{
    private DateTime? _dateFrom;
    private DateTime? _dateThru;

    public DateTime? DateFrom
    {
        get => _dateFrom ?? DateTime.MinValue;  // Default to MinValue if null
        set => _dateFrom = value ?? DateTime.MinValue; // Assign MinValue if null
    }

    public DateTime? DateThru
    {
        get => _dateThru ?? DateTime.MaxValue;  // Default to MaxValue if null
        set => _dateThru = value ?? DateTime.MaxValue; // Assign MaxValue if null
    }

    public string SSN { get; set; } = string.Empty; // Default empty string for SSN
}






To secure your Cosmos DB account key using Azure Service Principal in your (link unavailable) Core application, follow these steps:

*Step 1: Create an Azure Service Principal*

1. Go to Azure Portal ((link unavailable)) and navigate to Azure Active Directory.
2. Click on "App registrations" and then "New registration".
3. Fill in the required information, select "Accounts in this organizational directory only" and click "Register".
4. Note down the Client ID and Tenant ID.

*Step 2: Create a Client Secret*

1. In the App registrations page, find your newly created Service Principal.
2. Click on "Certificates & secrets".
3. Click "New client secret".
4. Add a description and click "Add".
5. Note down the Client secret value.

*Step 3: Configure Azure Service Principal permissions*

1. Go to your Cosmos DB account in Azure Portal.
2. Click on "Access control (IAM)".
3. Click "Add role assignment".
4. Select "Cosmos DB Account Reader and Data Contributor" role.
5. Select "User, group, or service principal" and enter your Service Principal's Client ID.
6. Click "Save".

*Step 4: Update your (link unavailable) Core application*

1. Install the `Microsoft.Azure.Services.AppAuthentication` NuGet package.
2. In your `Startup.cs` file, add the following code:
```
using Microsoft.Azure.Services.AppAuthentication;

public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(AzureServiceTokenProviderAuthenticationBuilderExtensions.AzureServiceTokenProviderAuthentication);
    services.AddCosmosDbContext(options =>
    {
        options.AccountEndpoint = Configuration["CosmosDB:Endpoint"];
        options.AccountKey = GetToken();
    });
}

private string GetToken()
{
    var azureServiceTokenProvider = new AzureServiceTokenProvider("(link unavailable)", Configuration["AzureAd:ClientId"], Configuration["AzureAd:ClientSecret"]);
    return azureServiceTokenProvider.GetAccessTokenAsync().Result;
}
```
*Step 5: Store Service Principal credentials securely*

1. Store your Client ID, Client Secret, and Tenant ID in Azure Key Vault or another secure storage.
2. Update your `appsettings.json` file to reference these values:
```
{
  "AzureAd": {
    "ClientId": "<Client ID>",
    "ClientSecret": "<Client Secret>",
    "TenantId": "<Tenant ID>"
  },
  "CosmosDB": {
    "Endpoint": "<Cosmos DB Endpoint>"
  }
}
```
*Step 6: Test your application*

Run your application and verify that it can connect to Cosmos DB using the Service Principal credentials.

By following these steps, you've successfully secured your Cosmos DB account key using Azure Service Principal in your (link unavailable) Core application.

Additional resources:

- (link unavailable)
- (link unavailable)
- (link unavailable)




Yes, you can directly use the **Service Principal** to access Azure resources, such as **Cosmos DB**, without Azure Key Vault. This can be done using **Azure Managed Identity** or **Service Principal credentials** to authenticate the application and obtain the Cosmos DB connection string.

### Steps to Securely Access Cosmos DB Using Service Principal in ASP.NET Core without Key Vault:

1. **Create a Service Principal in Azure:**
   You need a Service Principal to authenticate your ASP.NET Core application with Azure.

   **Via Azure CLI:**
   ```bash
   az ad sp create-for-rbac --name "<your-service-principal-name>" --role "Contributor" --scopes /subscriptions/<your-subscription-id>/resourceGroups/<your-resource-group>
   ```
   Save the following details:
   - **ClientId** (Service Principal ID)
   - **ClientSecret** (Service Principal Password)
   - **TenantId**

2. **Grant Service Principal Access to Cosmos DB:**
   Assign the Service Principal access to your Cosmos DB. You can do this using the Azure CLI or Portal.

   **Via Azure CLI:**
   ```bash
   az cosmosdb sql role definition create --account-name <cosmos-account-name> --resource-group <your-resource-group> --body @roleDefinition.json
   ```
   You'll need to define the necessary role that grants access to the Cosmos DB resources.

3. **Configure ASP.NET Core to Use Service Principal for Cosmos DB Access:**

   - Add the necessary packages to your project:
     - `Microsoft.Azure.Cosmos`
     - `Azure.Identity` (for using the Service Principal)

4. **Configure Authentication via Service Principal in ASP.NET Core:**

   **Update `appsettings.json`:**
   You can store the Service Principal credentials in `appsettings.json`, but ideally, if your app is running on Azure (App Service, etc.), you should use **Managed Identity** to avoid storing secrets in code.

   ```json
   {
     "AzureAd": {
       "ClientId": "<your-service-principal-client-id>",
       "ClientSecret": "<your-service-principal-client-secret>",
       "TenantId": "<your-tenant-id>"
     },
     "CosmosDb": {
       "AccountEndpoint": "<your-cosmosdb-endpoint>",
       "DatabaseId": "YourDatabaseId",
       "ContainerId": "YourContainerId"
     }
   }
   ```

5. **Connect to Cosmos DB Using Service Principal in `Program.cs`:**

   In `Program.cs`, you will authenticate with Azure using the Service Principal credentials and pass them to the Cosmos DB client.

   Example code:
   ```csharp
   using Azure.Identity;
   using Microsoft.Azure.Cosmos;

   var builder = WebApplication.CreateBuilder(args);

   // Read Cosmos DB configuration from appsettings
   var cosmosDbEndpoint = builder.Configuration["CosmosDb:AccountEndpoint"];
   var databaseId = builder.Configuration["CosmosDb:DatabaseId"];
   var containerId = builder.Configuration["CosmosDb:ContainerId"];

   // Authenticate using Service Principal
   var tenantId = builder.Configuration["AzureAd:TenantId"];
   var clientId = builder.Configuration["AzureAd:ClientId"];
   var clientSecret = builder.Configuration["AzureAd:ClientSecret"];

   var clientSecretCredential = new ClientSecretCredential(tenantId, clientId, clientSecret);

   // Use Cosmos DB client with Managed Identity or Service Principal
   var cosmosClientOptions = new CosmosClientOptions
   {
       TokenCredential = clientSecretCredential
   };

   var cosmosClient = new CosmosClient(cosmosDbEndpoint, cosmosClientOptions);

   // Example: Access the database and container
   var database = await cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
   var container = await database.Database.CreateContainerIfNotExistsAsync(containerId, "/partitionKey");

   // Use the app as needed
   var app = builder.Build();
   // ...
   ```

6. **Run the Application:**
   After configuring the application, you can run it, and it will use the Service Principal to securely connect to Cosmos DB.

### Notes:
- **Managed Identity** (recommended): If you deploy your app to an Azure environment (e.g., App Service, VM, AKS), you should use **Managed Identity** instead of storing the Service Principal credentials in `appsettings.json`. This removes the need to manage credentials in your app.

   To use **Managed Identity**, enable it in your Azure resource (App Service, VM, etc.), and replace the `ClientSecretCredential` in the code above with:

   ```csharp
   var credential = new DefaultAzureCredential();
   var cosmosClient = new CosmosClient(cosmosDbEndpoint, credential);
   ```

By using the Service Principal or Managed Identity to directly authenticate and access Cosmos DB, you avoid storing sensitive connection strings in configuration files, ensuring a more secure setup.



........
To use a Service Principal with Azure Key Vault for securely managing the Cosmos DB connection string in an ASP.NET Core application, you can follow these steps:

### Steps to Secure Cosmos DB Connection String Using a Service Principal in Azure Key Vault:

1. **Create a Service Principal in Azure:**
   A Service Principal is an identity used by applications to access Azure resources. To create one, use the Azure CLI or Azure Portal.
   
   **Via Azure CLI:**
   ```bash
   az ad sp create-for-rbac --name "<your-service-principal-name>" --role Contributor --scopes /subscriptions/<your-subscription-id>/resourceGroups/<your-resource-group>
   ```
   This will return the `appId`, `password`, and `tenant` values which are needed for authentication.

2. **Create an Azure Key Vault:**
   If you don't already have an Azure Key Vault, create one:
   ```bash
   az keyvault create --name <your-keyvault-name> --resource-group <your-resource-group>
   ```

3. **Store Cosmos DB Connection String in Key Vault:**
   Add the Cosmos DB connection string to the Key Vault:
   ```bash
   az keyvault secret set --vault-name <your-keyvault-name> --name "CosmosDBConnectionString" --value "<your-cosmos-db-connection-string>"
   ```

4. **Assign Permissions to the Service Principal:**
   Assign the Service Principal access to read secrets from the Key Vault:
   ```bash
   az keyvault set-policy --name <your-keyvault-name> --spn <your-service-principal-client-id> --secret-permissions get
   ```

5. **Configure ASP.NET Core Application to Use the Service Principal:**
   In your ASP.NET Core application, configure the application to authenticate using the Service Principal and retrieve the secret from Azure Key Vault.

   **Update `appsettings.json`:**
   You don't need the Cosmos DB connection string in `appsettings.json` anymore, but you need to store the Service Principal credentials (or use Managed Identity if running in Azure).
   ```json
   {
     "AzureAd": {
       "ClientId": "<your-service-principal-client-id>",
       "ClientSecret": "<your-service-principal-client-secret>",
       "TenantId": "<your-tenant-id>"
     },
     "KeyVault": {
       "VaultName": "<your-keyvault-name>"
     }
   }
   ```

6. **Configure Key Vault Access in `Program.cs` or `Startup.cs`:**
   Use the Azure SDK to authenticate the Service Principal and access the secret from Azure Key Vault. Add the required NuGet packages:
   - `Azure.Identity`
   - `Azure.Security.KeyVault.Secrets`

   Example code in `Program.cs` for setting up Key Vault access:

   ```csharp
   using Azure.Identity;
   using Azure.Security.KeyVault.Secrets;

   var builder = WebApplication.CreateBuilder(args);

   // Set up configuration to access Azure Key Vault
   var keyVaultName = builder.Configuration["KeyVault:VaultName"];
   var keyVaultUri = new Uri($"https://{keyVaultName}.vault.azure.net/");

   var clientSecretCredential = new ClientSecretCredential(
       builder.Configuration["AzureAd:TenantId"],
       builder.Configuration["AzureAd:ClientId"],
       builder.Configuration["AzureAd:ClientSecret"]
   );

   var secretClient = new SecretClient(vaultUri: keyVaultUri, credential: clientSecretCredential);
   var cosmosDbConnectionString = secretClient.GetSecret("CosmosDBConnectionString").Value.Value;

   // Add Cosmos DB connection string to configuration
   builder.Configuration["ConnectionStrings:CosmosDb"] = cosmosDbConnectionString;

   var app = builder.Build();
   // ...
   ```

7. **Use the Connection String in Your Code:**
   Now, wherever you need the Cosmos DB connection string, retrieve it from the configuration:
   ```csharp
   var cosmosDbConnectionString = builder.Configuration.GetConnectionString("CosmosDb");
   ```

By using a Service Principal to securely access the Cosmos DB connection string stored in Azure Key Vault, you're ensuring that sensitive credentials are not exposed in your source code or configuration files. This adds an extra layer of security to your ASP.NET Core application.















Using Azure Key Vault to store and retrieve connection strings in an ASP.NET Core API is a secure way to manage secrets like connection strings. Here's how you can do it:

### Step 1: Set Up Azure Key Vault
1. **Create a Key Vault**:
   - In the Azure portal, create a new Key Vault.
   - Note the name of the Key Vault and its URI.

2. **Add a Secret (Connection String)**:
   - In your Key Vault, go to the "Secrets" section.
   - Add a new secret with the connection string as its value. Give it a name like `MyDbConnectionString`.

### Step 2: Configure Your ASP.NET Core API

1. **Install Necessary NuGet Packages**:
   - You need the following packages:
     ```bash
     dotnet add package Azure.Extensions.AspNetCore.Configuration.Secrets
     dotnet add package Azure.Identity
     ```

2. **Configure Key Vault in `appsettings.json`**:
   - Optionally, store the Key Vault URI in `appsettings.json`:
     ```json
     {
       "KeyVault": {
         "Vault": "https://your-keyvault-name.vault.azure.net/"
       }
     }
     ```

3. **Update `Program.cs` or `Startup.cs`**:
   - Modify the code to integrate Key Vault:

     For .NET 6 and later, update the `Program.cs`:
     ```csharp
     using Azure.Identity;

     var builder = WebApplication.CreateBuilder(args);

     // Add Key Vault
     var keyVaultEndpoint = new Uri(builder.Configuration["KeyVault:Vault"]);
     builder.Configuration.AddAzureKeyVault(keyVaultEndpoint, new DefaultAzureCredential());

     // Register services and configure middleware
     builder.Services.AddControllers();
     // Other service registrations...

     var app = builder.Build();

     app.UseRouting();
     app.UseEndpoints(endpoints =>
     {
         endpoints.MapControllers();
     });

     app.Run();
     ```

     For .NET 5 or earlier, modify `Startup.cs`:
     ```csharp
     public void ConfigureServices(IServiceCollection services)
     {
         var keyVaultEndpoint = new Uri(Configuration["KeyVault:Vault"]);
         Configuration.AddAzureKeyVault(keyVaultEndpoint, new DefaultAzureCredential());

         services.AddControllers();
         // Other service registrations...
     }
     ```

4. **Access the Connection String in Code**:
   - Once configured, you can access the connection string from your Key Vault in your controllers or services like this:
     ```csharp
     public class MyService
     {
         private readonly string _connectionString;

         public MyService(IConfiguration configuration)
         {
             _connectionString = configuration["MyDbConnectionString"];
         }

         // Use _connectionString to interact with your database
     }
     ```

### Step 3: Deploy and Test
- Deploy your ASP.NET Core API to Azure.
- Ensure that your app has the appropriate permissions to access the Key Vault. You might need to set up a managed identity and grant it access to the Key Vault.

This setup will allow your API to securely retrieve the connection string from Azure Key Vault, avoiding hardcoding sensitive information in your application's codebase.







In Azure Functions, **Service Bus**, **triggers**, and **bindings** are concepts that help integrate your function apps with other services and enable seamless event-driven architectures. Here's what each of them means:

### 1. **Service Bus**
Azure Service Bus is a fully managed message broker that facilitates communication between different components of an application. It allows reliable message queuing, pub/sub messaging, and supports complex messaging patterns.

- **Queues**: Messages are sent to a queue and processed in a first-in, first-out (FIFO) manner. This is useful for decoupling different parts of an application.
- **Topics and Subscriptions**: These provide a publish-subscribe model, where messages are sent to a topic and can be received by multiple subscribers based on filtering rules.

### 2. **Triggers**
Triggers are what cause a function to run. In the context of Azure Functions, a trigger is an event or a message that starts the execution of a function.

- **Service Bus Trigger**: A Service Bus Trigger allows your Azure Function to respond to messages being sent to a Service Bus queue or topic. When a new message arrives, the function is automatically invoked to process it.

Triggers can be based on other types of events as well, such as HTTP requests, timers, Blob storage changes, and more.

### 3. **Bindings**
Bindings in Azure Functions are a way to declaratively connect other resources to your function. They let you interact with various services without having to write much boilerplate code for connectivity and data handling.

- **Input Binding**: An input binding allows your function to read data from a source, like a Service Bus message, a Cosmos DB document, or a Blob storage file, and pass it to the function.
  
- **Output Binding**: An output binding allows your function to send data to a destination, like writing a message to a Service Bus queue, storing data in a database, or sending an email.

Bindings simplify the code you need to write, as they abstract away the complexities of connecting to external resources.

### Example Scenario:
Imagine you have an Azure Function that processes orders:

- **Service Bus Trigger**: The function is triggered whenever a new order message is sent to a Service Bus queue.
- **Input Binding**: The message content is automatically passed to the function via an input binding.
- **Output Binding**: After processing, the function might use an output binding to send a confirmation message to another Service Bus queue or store the order details in a database.

By using triggers and bindings, you can build scalable, event-driven applications with minimal code and easy integration across Azure services.



The `AzureWebJobsStorage` setting is a critical configuration in Azure Functions and Azure WebJobs that specifies the connection string to an Azure Storage account. This storage account is used by Azure Functions for several essential purposes:

### 1. **Trigger Management**:
   - **Queue Trigger**: If your function is triggered by an Azure Queue message, the `AzureWebJobsStorage` setting provides the connection to the storage account where the queue is located.
   - **Timer Trigger**: Functions using a timer trigger also rely on `AzureWebJobsStorage` to maintain the schedule state and track execution history.
   - **Blob Trigger**: For blob-triggered functions, this setting links to the storage account where the blobs are stored.

### 2. **State Management**:
   - **Durable Functions**: If you're using Durable Functions, the `AzureWebJobsStorage` account is used to store the orchestration history, checkpoints, and state of the workflows.
   - **Checkpointing**: When your function processes a message (from a queue, for example), it creates checkpoints to ensure that if a failure occurs, it can resume processing from the last known successful state.

### 3. **Logging and Diagnostics**:
   - Azure Functions write logs, metrics, and other diagnostic data to Azure Storage. This is particularly useful for monitoring the performance and health of your functions, especially if you do not have Application Insights configured.

### 4. **Binding Data**:
   - The `AzureWebJobsStorage` setting is used by input and output bindings to connect to Azure Storage. For instance, if you have an output binding to write to a blob or queue, it uses this setting to know where to write the data.

### 5. **Support for Various Triggers and Bindings**:
   - Many other triggers and bindings in Azure Functions use `AzureWebJobsStorage` as the default connection string to access Azure Storage resources, like Table Storage.

### 6. **Development and Testing**:
   - In a development environment, `AzureWebJobsStorage` can be set to `UseDevelopmentStorage=true` to use the Azure Storage Emulator, which allows you to develop and test locally without requiring a live Azure Storage account.

### Summary:
`AzureWebJobsStorage` is integral to the operation of Azure Functions, providing a backbone for triggers, state management, logging, and binding data. It ensures that your functions can interact seamlessly with Azure Storage resources, maintain their state across executions, and handle failures gracefully.











var allpropertyIDs = docs?.Documents
    ?.Where(doc => doc.PropertyRefId != null)
    ?.SelectMany(doc => doc.PropertyRefId.ToString().Split(new[] { ',' }, StringSplitOptions.RemoveEmptyEntries))
    ?.Distinct()
    ?.ToList();

var allpropertyIDs = docs?.Documents
    ?.Where(doc => doc.PropertyRefId != null) // Filter out docs with null PropertyRefId
    ?.SelectMany(doc => doc.PropertyRefId.ToString().Split(new[] { ',' }, StringSplitOptions.RemoveEmptyEntries)) // Split and remove empty entries
    ?.Distinct()
    ?.ToList();




DECLARE @instrument_number AS [dbo].[udt_instrnum];
DECLARE @book_page AS [dbo].[udt_bookpage];

-- Insert values into the table variables
INSERT INTO @instrument_number (instrument_number) VALUES ('1706647');
INSERT INTO @book_page (book, page) VALUES ('', '');

-- Create a temporary table to store the result from the stored procedure
CREATE TABLE #TempResult (
    refid INT
    -- Other columns that the stored procedure returns should be declared here as well
);

-- Insert the result of the stored procedure into the temporary table
INSERT INTO #TempResult
EXEC dbo.usp_GetDocumentByInstrumentOrBookPage_v2_batch @FIPS = 1003, @instrument_number, @instrument, @book_page;

-- Now select only the refid column
SELECT refid FROM #TempResult;

-- Drop the temporary table after you're done
DROP TABLE #TempResult;





var allpropertyIDs = docs?.Documents?
    .SelectMany(doc => doc.PropertyRefId.ToString().Split(','))
    .Distinct()
    .ToList();

if (allpropertyIDs != null && allpropertyIDs.Any())
{
    var tasks = allpropertyIDs
        .Where(id => !string.IsNullOrWhiteSpace(id))
        .Select(async propertyID => 
            await documentDataService.GetGroupDocumentsByPropertyIdOrAPNSplitted(
                "",
                propertyID,
                null, 
                documentSearchRequest.CountyFips, 
                RequestSearchType.APN));

    var results = await Task.WhenAll(tasks);

    // Now `results` contains an array of all results returned by the method
}









int iterationCounter = 1; // Initialize the iteration counter

if (result.Documents != null)
{
    foreach (var doc in result.Documents)
    {
        doc.RecordNumber = iterationCounter; // Set the RecordNumber to the current iteration count
        mergedResults.Documents.Add(doc);
    }

    iterationCounter++; // Increment the iteration counter after processing all documents in the current iteration
}






var Apns = docs?.Documents?
    .Where(p => string.IsNullOrEmpty(p.LegalDescAPN))
    .Select(p => p.LegalDescAPN)
    .Distinct()
    .ToList();

Apns.Add("3000141012001007");
Apns.Add("10000929936200000");

foreach (string apn in Apns)
{
    // Assuming RemoveAPNSpecialChars() is an extension method
    var sanitizedAPN = apn.RemoveAPNSpecialChars();

    var result = await documentDataService.GetGroupDocumentsByPropertyIdOrAPNSplitted(
        searchId,
        documentSearchRequest.CountyFips,
        RequestSearchType.APN,
        sanitizedAPN,
        null
    );

    if (result != null)
    {
        // Initialize docinfo if it is null
        if (docinfo == null)
        {
            docinfo = result;
        }
        else
        {
            // Handle Documents
            if (result.Documents?.Count > 0)
            {
                foreach (var doc in result.Documents)
                {
                    doc.RecordNumber += docinfo.Documents.Last().RecordNumber;
                }
                docinfo.Documents.AddRange(result.Documents);
            }

            // Handle Property
            if (result.Property?.Count > 0)
            {
                foreach (var prop in result.Property)
                {
                    prop.RecordNumber += docinfo.Documents.Last().RecordNumber;
                }
                docinfo.Property.AddRange(result.Property);
            }

            // Handle Party
            if (result.Party?.Count > 0)
            {
                foreach (var party in result.Party)
                {
                    party.RecordNumber += docinfo.Documents.Last().RecordNumber;
                }
                docinfo.Party.AddRange(result.Party);
            }

            // Handle References
            if (result.References?.Count > 0)
            {
                foreach (var reference in result.References)
                {
                    reference.RecordNumber += docinfo.Documents.Last().RecordNumber;
                }
                docinfo.References.AddRange(result.References);
            }

            // Update UniqueDocumentCount
            docinfo.UniqueDocumentCount += result.UniqueDocumentCount;
        }
    }
}


..........

var mergedResults = new DBDocumentsModelSplitted
{
    Documents = new List<DBDocumentModelInfo>(),
    Party = new List<DBPartyModel>(),
    Property = new List<DBPropertyModel>(),
    References = new List<DBReferenceModel>(),
    UniqueDocumentCount = 0
};

foreach (var APNno in allAPNnos)
{
    if (!string.IsNullOrWhiteSpace(APNno))
    {
        var sanitizedAPNno = APNno.RemoveAPNSpecialChars();
        var result = await _documentDataService.GetGroupDocumentsByPropertyIdOrAPNSplitted(
            sanitizedAPNno, documentSearchRequest.CountyFips, RequestSearchType.APN);

        if (result != null)
        {
            mergedResults.Documents.AddRange(result.Documents);
            mergedResults.Party.AddRange(result.Party);
            mergedResults.Property.AddRange(result.Property);
            mergedResults.References.AddRange(result.References);
            mergedResults.UniqueDocumentCount += result.UniqueDocumentCount;
        }
    }
    else
    {
        throw new BadRequestException("EC-03", "Instrument Search", "No property found associated with the provided APN.");
    }
}

// Now pass the single merged result to the ConvertDBModelToDocumentEntitySplitted method
var multiResults = ConvertDBModelToDocumentEntitySplitted(mergedResults, searchId);













var allAPNnos = docs?.Documents?.Select(doc => doc.LegalDescAPN).ToList();
var mergedResults = new List<DBDocumentsModelSplitted>();

foreach (var APNno in allAPNnos)
{
    if (!string.IsNullOrWhiteSpace(APNno))
    {
        var sanitizedAPNno = APNno.RemoveAPNSpecialChars();
        var result = await _documentDataService.GetGroupDocumentsByPropertyIdOrAPNSplitted(
            sanitizedAPNno, documentSearchRequest.CountyFips, RequestSearchType.APN);

        // Assuming result is of type DBDocumentsModelSplitted
        if (result != null)
        {
            mergedResults.Add(result);
        }
    }
    else
    {
        throw new BadRequestException("EC-03", "Instrument Search", "No property found associated with the provided APN.");
    }
}

// Now pass the mergedResults to the ConvertDBModelToDocumentEntitySplitted method
var multiResults = ConvertDBModelToDocumentEntitySplitted(mergedResults, searchId);




// Initialize lists to hold merged results for each section
var mergedDocuments = new List<DocumentType>(); // Replace with your actual Document type
var mergedProperties = new List<PropertyType>(); // Replace with your actual Property type
var mergedParties = new List<PartyType>(); // Replace with your actual Party type
var mergedReferences = new List<ReferenceType>(); // Replace with your actual Reference type

int recordNoCounter = 1; // Start RecordNo from 1
bool isFirstAPN = true; // Flag to track if it's the first APN

foreach (var APNno in allAPNnos)
{
    if (!string.IsNullOrWhiteSpace(APNno))
    {var allAPNnos = docs?.Documents?.Select(doc => doc.LegalDescAPN).ToList();
var mergedResults = new List<DBDocumentsModelSplitted>();

foreach (var APNno in allAPNnos)
{
    if (!string.IsNullOrWhiteSpace(APNno))
    {
        var sanitizedAPNno = APNno.RemoveAPNSpecialChars();
        var result = await _documentDataService.GetGroupDocumentsByPropertyIdOrAPNSplitted(
            sanitizedAPNno, documentSearchRequest.CountyFips, RequestSearchType.APN);

        // Assuming result is of type DBDocumentsModelSplitted
        if (result != null)
        {
            mergedResults.Add(result);
        }
    }
    else
    {
        throw new BadRequestException("EC-03", "Instrument Search", "No property found associated with the provided APN.");
    }
}

// Now pass the mergedResults to the ConvertDBModelToDocumentEntitySplitted method
var multiResults = ConvertDBModelToDocumentEntitySplitted(mergedResults, searchId
        var sanitizedAPNno = APNno.RemoveAPNSpecialChars();

        var result = await _documentDataService.GetGroupDocumentsByPropertyIdOrAPNSplitted("",
            sanitizedAPNno, searchRequest.CountyFips, RequestSearchType.APN);

        if (result != null)
        {
            // Add Documents
            if (result.Documents != null)
            {
                foreach (var doc in result.Documents)
                {
                    if (!isFirstAPN)
                    {
                        doc.RecordNo = recordNoCounter++;
                    }
                    mergedDocuments.Add(doc);
                }
            }

            // Add Properties
            if (result.Property != null)
            {
                foreach (var prop in result.Property)
                {
                    if (!isFirstAPN)
                    {
                        prop.RecordNo = recordNoCounter++;
                    }
                    mergedProperties.Add(prop);
                }
            }

            // Add Parties
            if (result.Party != null)
            {
                foreach (var party in result.Party)
                {
                    if (!isFirstAPN)
                    {
                        party.RecordNo = recordNoCounter++;
                    }
                    mergedParties.Add(party);
                }
            }

            // Add References
            if (result.References != null)
            {
                foreach (var reference in result.References)
                {
                    if (!isFirstAPN)
                    {
                        reference.RecordNo = recordNoCounter++;
                    }
                    mergedReferences.Add(reference);
                }
            }
        }

        // After processing the first APN, set the flag to false
        isFirstAPN = false;
    }
}

// Now mergedDocuments, mergedProperties, mergedParties, and mergedReferences
// contain all the merged records, with RecordNo incremented for subsequent APNs.






// Initialize lists to hold merged results for each section
var mergedDocuments = new List<DocumentType>(); // Replace with your actual Document type
var mergedProperties = new List<PropertyType>(); // Replace with your actual Property type
var mergedParties = new List<PartyType>(); // Replace with your actual Party type
var mergedReferences = new List<ReferenceType>(); // Replace with your actual Reference type

int recordNoCounter = 1; // Start RecordNo from 1
bool isFirstAPN = true; // Flag to track if it's the first APN

foreach (var APNno in allAPNnos)
{
    if (!string.IsNullOrWhiteSpace(APNno))
    {
        var sanitizedAPNno = APNno.RemoveAPNSpecialChars();

        var result = await _documentDataService.GetGroupDocumentsByPropertyIdOrAPNSplitted("",
            sanitizedAPNno, searchRequest.CountyFips, RequestSearchType.APN);

        if (result != null)
        {
            // Add Documents
            if (result.Documents != null)
            {
                foreach (var doc in result.Documents)
                {
                    if (!isFirstAPN)
                    {
                        doc.RecordNo = recordNoCounter++;
                    }
                    mergedDocuments.Add(doc);
                }
            }

            // Add Properties
            if (result.Property != null)
            {
                foreach (var prop in result.Property)
                {
                    if (!isFirstAPN)
                    {
                        prop.RecordNo = recordNoCounter++;
                    }
                    mergedProperties.Add(prop);
                }
            }

            // Add Parties
            if (result.Party != null)
            {
                foreach (var party in result.Party)
                {
                    if (!isFirstAPN)
                    {
                        party.RecordNo = recordNoCounter++;
                    }
                    mergedParties.Add(party);
                }
            }

            // Add References
            if (result.References != null)
            {
                foreach (var reference in result.References)
                {
                    if (!isFirstAPN)
                    {
                        reference.RecordNo = recordNoCounter++;
                    }
                    mergedReferences.Add(reference);
                }
            }
        }

        // After processing the first APN, set the flag to false
        isFirstAPN = false;
    }
}



// Now mergedDocuments, mergedProperties, mergedParties, and mergedReferences
// contain all the merged records, with RecordNo incremented for subsequent APNs.








// Initialize lists to hold merged results for each section
var mergedDocuments = new List<DocumentType>(); // Replace with your actual Document type
var mergedProperties = new List<PropertyType>(); // Replace with your actual Property type
var mergedParties = new List<PartyType>(); // Replace with your actual Party type
var mergedReferences = new List<ReferenceType>(); // Replace with your actual Reference type

int recordNoCounter = 1; // Start RecordNo from 1

foreach (var APNno in allAPNnos)
{
    if (!string.IsNullOrWhiteSpace(APNno))
    {
        var sanitizedAPNno = APNno.RemoveAPNSpecialChars();

        var result = await _documentDataService.GetGroupDocumentsByPropertyIdOrAPNSplitted("",
            sanitizedAPNno, searchRequest.CountyFips, RequestSearchType.APN);

        if (result != null)
        {
            // Increment RecordNo and add Documents
            if (result.Documents != null)
            {
                foreach (var doc in result.Documents)
                {
                    doc.RecordNo = recordNoCounter++;
                    mergedDocuments.Add(doc);
                }
            }

            // Increment RecordNo and add Properties
            if (result.Property != null)
            {
                foreach (var prop in result.Property)
                {
                    prop.RecordNo = recordNoCounter++;
                    mergedProperties.Add(prop);
                }
            }

            // Increment RecordNo and add Parties
            if (result.Party != null)
            {
                foreach (var party in result.Party)
                {
                    party.RecordNo = recordNoCounter++;
                    mergedParties.Add(party);
                }
            }

            // Increment RecordNo and add References
            if (result.References != null)
            {
                foreach (var reference in result.References)
                {
                    reference.RecordNo = recordNoCounter++;
                    mergedReferences.Add(reference);
                }
            }
        }
    }
}

// After processing, mergedDocuments, mergedProperties, mergedParties, and mergedReferences
// contain all the merged and updated records.





// Initialize a list to hold the merged results
var mergedResults = new List<DBDocumentsModelSplitted>();

int recordNoCounter = 1; // Start the record number from 1

foreach (var APNno in allAPNnos)
{
    if (!string.IsNullOrWhiteSpace(APNno))
    {
        var sanitizedAPNno = APNno.RemoveAPNSpecialChars(); 

        var result = await _documentDataService.GetGroupDocumentsByPropertyIdOrAPNSplitted("",
            sanitizedAPNno, searchRequest.CountyFips, RequestSearchType.APN);

        if (result != null && result.Documents != null && result.Documents.Count > 0)
        {
            foreach (var doc in result.Documents)
            {
                // Increment RecordNo
                doc.RecordNo = recordNoCounter++;

                // Add to merged results
                mergedResults.Add(doc);
            }
        }
    }
}

// Now mergedResults contains all the records with incremented RecordNo














var allAPNnos = docs?.Documents?.Select(doc => doc.LegalDescAPN).ToList() ?? new List<string>();

foreach (var APNno in allAPNnos)
{
    if (!string.IsNullOrWhiteSpace(APNno))
    {
        var sanitizedAPNno = APNno.RemoveAPNSpecialchars(); // Assuming RemoveAPNSpecialchars() is an extension method

        var result = await _documentDataService.GetGroupDocumentsByPropertyIdOrAPNSplitted("",
            sanitizedAPNno, searchRequest.CountyFips, RequestSearchType.APN);
        
        // Process the result as needed
    }
}














bool isExist = context.Customers





 
    
    
    
    .Where(x => 
        (x.CustomerAccountNumber == customerUpdateRequests.CustomerAccount.Number || 
         x.SourceAccountNumber == customerUpdateRequests.SourceAccount.Number) &&
        (x.ValidationId == (byte)customerUpdateRequests.ValidationType || 
         x.ValidationId == null) &&
        (x.Notes == customerUpdateRequests.Notes || 
         x.Notes == null)
    )
    .Count() > 0;
var isDuplicate = await _context.Customers
    .Where(c => c.CustomerNumber == customerNumber 
                || c.SourceUID == sourceUID 
                || c.Mobile == mobile)
    .AnyAsync(c => c.Id != selectedRowId);





var isDuplicate = await _context.Customers
    .GroupBy(c => new { c.CustomerNumber, c.SourceUID, c.Mobile })
    .AnyAsync(g => g.Count() > 1);



wo-- Declare and populate the table-valued parameters
DECLARE @instrument_number AS [dbo].[udt_instrnum];
DECLARE @book_page AS [dbo].[udt_bookpage];

-- Example data for instrument numbers
INSERT INTO @instrument_number (instrument_number)
VALUES 
    ('123456789'),
    ('987654321');

-- Example data for books and pages
INSERT INTO @book_page (book, page)
VALUES 
    ('Book1', 'Page1'),
    ('Book2', 'Page2');

-- Now execute the stored procedure
EXEC [dbo].[SP_usp_GetDocumentByInstrument]
    @FIPS = 12345, -- Replace with actual FIPS value
    @instrument_number = @instrument_number,
    @book_page = @book_page;








using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.Linq;
using System.Text.Json;

[ApiController]
[Route("[controller]")]
public class UsersController : ControllerBase
{
    [HttpPost]
    public IActionResult Post([FromBody] JsonElement jsonElement)
    {
        // Check if the JSON has the 'Name' property as an array
        if (jsonElement.TryGetProperty("Name", out JsonElement namesElement) && namesElement.ValueKind == JsonValueKind.Array)
        {
            // Create a list to keep track of unique names
            var uniqueNameList = new List<string>();
            var duplicateNameMessages = new List<string>();

            // Iterate through each name in the array
            foreach (var name in namesElement.EnumerateArray())
            {
                // Convert the name object to a serialized string for comparison
                var nameString = JsonSerializer.Serialize(name);

                if (uniqueNameList.Contains(nameString))
                {
                    // If the name is already in the unique list, add a duplicate message
                    duplicateNameMessages.Add($"Duplicate entry detected: {nameString}");
                }
                else
                {
                    // Add the name to the unique list if not already present
                    uniqueNameList.Add(nameString);
                }
            }

            // Check for duplicates and process unique entries
            if (duplicateNameMessages.Any())
            {
                // Return duplicate messages if any duplicates were found
                return Ok(new { Messages = duplicateNameMessages });
            }
            else
            {
                // Process the unique names here
                // You can implement any processing logic you need for unique entries

                return Ok(new { Message = "User data processed successfully.", UniqueEntries = uniqueNameList });
            }
        }

        // Return an error if the 'Name' property is missing or not an array
        return BadRequest(new { Message = "'Name' property missing or not an array." });
    }
}


.......







[ApiController]
[Route("[controller]")]
public class DataController : ControllerBase
{
    private readonly SharedProperties _sharedProperties;
    private readonly IHttpClientFactory _httpClientFactory;

    public DataController(SharedProperties sharedProperties, IHttpClientFactory httpClientFactory)
    {
        _sharedProperties = sharedProperties;
        _httpClientFactory = httpClientFactory;
    }

    [HttpGet]
    public async Task<IActionResult> Get()
    {
        var apnSearchRequest = new { APN = "someValue" }; // Example request object
        var apnSearchResponse = new APNTypeAheadResponse();

        apnSearchResponse.APNs = await CallTypeAheadAPNSearchAzureApi(apnSearchRequest.APN);

        // Assign values to shared properties
        _sharedProperties.APNs = apnSearchResponse.APNs;

        return Ok(apnSearchResponse);
    }

    private async Task<List<string>> CallTypeAheadAPNSearchAzureApi(string apn)
    {
        var client = _httpClientFactory.CreateClient();
        var response = await client.GetAsync($"https://example.com/api/apnsearch?apn={apn}");
        if (response.IsSuccessStatusCode)
        {
            var data = await response.Content.ReadFromJsonAsync<List<string>>();
            return data;
        }
        return new List<string>();
    }
}






bool allSameTransactionHistoryId = itemResponses.All(item => item.transitionhystoryId == itemResponses.First().transitionhystoryId);





if (data.TitleDocs != null && data.TitleDocs.Count() > 0) 
{
    HashSet<int> existingDocumentIds = titledocs.Select(td => td.DocumentId).ToHashSet();

    // Pre-check for duplicates
    bool hasDuplicates = data.TitleDocs.Any(doc => existingDocumentIds.Contains(doc.DocumentId));

    if (hasDuplicates)
    {
        Console.WriteLine("One or more documents already present in the list. Skipping the loop.");
    }
    else
    {
        foreach (var doc in data.TitleDocs)
        {
            TitleDoc titledoc = new TitleDoc
            {
                DocumentId = doc.DocumentId,
                // Copy other properties if necessary
            };

            titledocs.Add(titledoc);
        }
    }
}







if (data.TitleDocs != null && data.TitleDocs.Count() > 0)
{
    HashSet<int> documentIds = new HashSet<int>();
    List<TitleDoc> titledocs = new List<TitleDoc>();

    // Loop to check for duplicates
    foreach (var doc in data.TitleDocs)
    {
        if (!documentIds.Contains(doc.DocumentId))
        {
            documentIds.Add(doc.DocumentId);
        }
        else
        {
            Console.WriteLine($"Document with ID {doc.DocumentId} is already present and will not be added to the list.");
        }
    }

    // Loop to add documents to titledocs
    foreach (var doc in data.TitleDocs)
    {
        if (documentIds.Contains(doc.DocumentId))
        {
            TitleDoc titledoc = new TitleDoc();
            titledoc = doc;
            titledocs.Add(titledoc);

            // Once added, remove it from the set to avoid duplicate entries
            documentIds.Remove(doc.DocumentId);
        }
    }
}










if (data.TitleDocs != null && data.TitleDocs.Count() > 0) 
{
    HashSet<int> documentIds = new HashSet<int>();
    List<TitleDoc> titledocs = new List<TitleDoc>();

    foreach (var doc in data.TitleDocs)
    {
        if (documentIds.Contains(doc.DocumentId))
        {
            Console.WriteLine($"Document with ID {doc.DocumentId} is already present and will not be added to the list.");
        }
        else
        {
            TitleDoc titledoc = new TitleDoc();
            titledoc = doc;
            titledocs.Add(titledoc);
            documentIds.Add(doc.DocumentId);
        }
    }
}






if (data.TitleDocs != null && data.TitleDocs.Count() > 0) 
{
    HashSet<int> documentIds = new HashSet<int>();
    List<TitleDoc> titledocs = new List<TitleDoc>();

    foreach (var doc in data.TitleDocs)
    {
        if (documentIds.Add(doc.DocumentId)) // Add returns false if the item already exists
        {
            titledocs.Add(doc);
        }
    }
}








DECLARE @dbName NVARCHAR(255);
DECLARE @sql NVARCHAR(MAX);
DECLARE @successLog TABLE (DatabaseName NVARCHAR(255));
DECLARE @errorLog TABLE (DatabaseName NVARCHAR(255), ErrorMsg NVARCHAR(MAX));

DECLARE db_cursor CURSOR FOR
SELECT name
FROM sys.databases;

OPEN db_cursor;
FETCH NEXT FROM db_cursor INTO @dbName;

WHILE @@FETCH_STATUS = 0
BEGIN
    BEGIN TRY
        SET @sql = 'USE [' + @dbName + '];';
        EXEC sp_executesql @sql;
        PRINT 'Successfully connected to ' + @dbName;
        INSERT INTO @successLog (DatabaseName)
        VALUES (@dbName);
    END TRY
    BEGIN CATCH
        PRINT 'Failed to connect to ' + @dbName;
        INSERT INTO @errorLog (DatabaseName, ErrorMsg)
        VALUES (@dbName, ERROR_MESSAGE());
    END CATCH;
    FETCH NEXT FROM db_cursor INTO @dbName;
END;

CLOSE db_cursor;
DEALLOCATE db_cursor;

-- Display the successful connections
PRINT 'Successful Connections:';
SELECT * FROM @successLog;

-- Display the failed connections
PRINT 'Failed Connections:';
SELECT * FROM @errorLog;








DECLARE @dbName NVARCHAR(255);
DECLARE @sql NVARCHAR(MAX);
DECLARE @errorLog TABLE (DatabaseName NVARCHAR(255), ErrorMsg NVARCHAR(MAX));

DECLARE db_cursor CURSOR FOR
SELECT name
FROM sys.databases;

OPEN db_cursor;
FETCH NEXT FROM db_cursor INTO @dbName;

WHILE @@FETCH_STATUS = 0
BEGIN
    BEGIN TRY
        SET @sql = 'USE [' + @dbName + '];';
        EXEC sp_executesql @sql;
        PRINT 'Successfully connected to ' + @dbName;
    END TRY
    BEGIN CATCH
        PRINT 'Failed to connect to ' + @dbName;
        INSERT INTO @errorLog (DatabaseName, ErrorMsg)
        VALUES (@dbName, ERROR_MESSAGE());
    END CATCH;
    FETCH NEXT FROM db_cursor INTO @dbName;
END;

CLOSE db_cursor;
DEALLOCATE db_cursor;

-- Display the error log
SELECT * FROM @errorLog;










using System;
using System.Collections.Generic;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""ΑΡΝ"": ""10-00092-993-620-0001"",
            ""ΑΡΝ"": ""10-00092-993-620-0002"",
            ""Details"": {
                ""ΑΡΝ"": ""10-00092-993-620-0003"",
                ""Nested"": {
                    ""ΑΡΝ"": ""10-00092-993-620-0004""
                }
            }
        }";

        // Parse JSON string
        JsonDocument doc = JsonDocument.Parse(json);
        JsonElement root = doc.RootElement;

        // Check for duplicate keys
        CheckForDuplicateKeys(root);
    }

    static void CheckForDuplicateKeys(JsonElement root)
    {
        Stack<JsonElement> stack = new Stack<JsonElement>();
        stack.Push(root);

        while (stack.Count > 0)
        {
            JsonElement element = stack.Pop();

            if (element.ValueKind == JsonValueKind.Object)
            {
                // Track seen keys in the current object
                HashSet<string> seenKeys = new HashSet<string>();
                List<string> duplicateKeys = new List<string>();

                foreach (JsonProperty property in element.EnumerateObject())
                {
                    if (!seenKeys.Add(property.Name))
                    {
                        duplicateKeys.Add(property.Name);
                    }
                }

                // Print duplicate keys found in the current object
                foreach (var key in duplicateKeys)
                {
                    Console.WriteLine($"Duplicate key found: {key}");
                }

                // Push nested objects onto the stack
                foreach (JsonProperty property in element.EnumerateObject())
                {
                    stack.Push(property.Value);
                }
            }
            else if (element.ValueKind == JsonValueKind.Array)
            {
                foreach (JsonElement item in element.EnumerateArray())
                {
                    stack.Push(item);
                }
            }
        }
    }
}


........




using System;
using System.Collections.Generic;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""ΑΡΝ"": ""10-00092-993-620-0001"",
            ""ΑΡΝ"": ""10-00092-993-620-0002"",
            ""Details"": {
                ""ΑΡΝ"": ""10-00092-993-620-0003"",
                ""Nested"": {
                    ""ΑΡΝ"": ""10-00092-993-620-0004""
                }
            }
        }";

        // Parse JSON string
        JObject parsedJson = JObject.Parse(json);

        // Check for duplicate keys
        CheckForDuplicateKeys(parsedJson);
    }

    static void CheckForDuplicateKeys(JToken token)
    {
        if (token.Type == JTokenType.Object)
        {
            // Track seen keys in the current object
            HashSet<string> seenKeys = new HashSet<string>();
            List<string> duplicateKeys = new List<string>();

            foreach (JProperty property in token.Children<JProperty>())
            {
                if (!seenKeys.Add(property.Name))
                {
                    duplicateKeys.Add(property.Name);
                }
            }

            // Print duplicate keys found in the current object
            foreach (var key in duplicateKeys)
            {
                Console.WriteLine($"Duplicate key found: {key}");
            }

            // Recursively check nested objects
            foreach (JProperty property in token.Children<JProperty>())
            {
                CheckForDuplicateKeys(property.Value);
            }
        }
        else if (token.Type == JTokenType.Array)
        {
            foreach (JToken item in token.Children())
            {
                CheckForDuplicateKeys(item);
            }
        }
    }
}


,.........


using System;
using System.Collections.Generic;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""ΑΡΝ"": ""10-00092-993-620-0001"",
            ""ΑΡΝ"": ""10-00092-993-620-0002"",
            ""Details"": {
                ""ΑΡΝ"": ""10-00092-993-620-0003"",
                ""Nested"": {
                    ""ΑΡΝ"": ""10-00092-993-620-0004""
                }
            }
        }";

        // Parse JSON string
        JsonDocument doc = JsonDocument.Parse(json);
        JsonElement root = doc.RootElement;

        // Check for duplicate keys
        CheckForDuplicateKeys(root);
    }

    static void CheckForDuplicateKeys(JsonElement element)
    {
        if (element.ValueKind == JsonValueKind.Object)
        {
            // Track seen keys in the current object
            HashSet<string> seenKeys = new HashSet<string>();
            List<string> duplicateKeys = new List<string>();

            foreach (JsonProperty property in element.EnumerateObject())
            {
                if (!seenKeys.Add(property.Name))
                {
                    duplicateKeys.Add(property.Name);
                }
            }

            // Print duplicate keys found in the current object
            foreach (var key in duplicateKeys)
            {
                Console.WriteLine($"Duplicate key found: {key}");
            }

            // Recursively check nested objects
            foreach (JsonProperty property in element.EnumerateObject())
            {
                CheckForDuplicateKeys(property.Value);
            }
        }
        else if (element.ValueKind == JsonValueKind.Array)
        {
            foreach (JsonElement item in element.EnumerateArray())
            {
                CheckForDuplicateKeys(item);
            }
        }
    }
}




using System;
using System.Collections.Generic;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""ΑΡΝ"": ""10-00092-993-620-0001"",
            ""ΑΡΝ"": ""10-00092-993-620-0002"",
            ""Details"": {
                ""ΑΡΝ"": ""10-00092-993-620-0003"",
                ""Nested"": {
                    ""ΑΡΝ"": ""10-00092-993-620-0004""
                }
            }
        }";

        // Parse JSON string
        JsonDocument doc = JsonDocument.Parse(json);
        JsonElement root = doc.RootElement;

        // Check for duplicate keys
        CheckForDuplicateKeys(root);
    }

    static void CheckForDuplicateKeys(JsonElement element)
    {
        if (element.ValueKind == JsonValueKind.Object)
        {
            // Track seen keys in the current object
            HashSet<string> seenKeys = new HashSet<string>();
            List<string> duplicateKeys = new List<string>();

            foreach (JsonProperty property in element.EnumerateObject())
            {
                if (!seenKeys.Add(property.Name))
                {
                    duplicateKeys.Add(property.Name);
                }
            }

            // Print duplicate keys found in the current object
            foreach (var key in duplicateKeys)
            {
                Console.WriteLine($"Duplicate key found: {key}");
            }

            // Recursively check nested objects
            foreach (JsonProperty property in element.EnumerateObject())
            {
                CheckForDuplicateKeys(property.Value);
            }
        }
        else if (element.ValueKind == JsonValueKind.Array)
        {
            foreach (JsonElement item in element.EnumerateArray())
            {
                CheckForDuplicateKeys(item);
            }
        }
    }
}





using System;
using System.Collections.Generic;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""ΑΡΝ"": ""10-00092-993-620-0001"",
            ""ΑΡΝ"": ""10-00092-993-620-0002"",
            ""Details"": {
                ""ΑΡΝ"": ""10-00092-993-620-0003""
            }
        }";

        // Parse JSON string and check for duplicate keys
        CheckForDuplicateKeys(json);
    }

    static void CheckForDuplicateKeys(string json)
    {
        HashSet<string> seenKeys = new HashSet<string>();
        Dictionary<string, int> duplicateCounts = new Dictionary<string, int>();

        // Remove whitespaces (optional step for cleaner JSON processing)
        json = json.Replace(" ", "");

        // Initialize variables to track current parsing state
        bool inObject = false;
        int objectDepth = 0;
        int startIndex = 0;

        for (int i = 0; i < json.Length; i++)
        {
            char currentChar = json[i];

            if (currentChar == '{')
            {
                if (!inObject)
                {
                    inObject = true;
                    objectDepth++;
                    startIndex = i + 1;
                }
                else
                {
                    objectDepth++;
                }
            }
            else if (currentChar == '}')
            {
                objectDepth--;

                if (objectDepth == 0)
                {
                    inObject = false;
                    string objectString = json.Substring(startIndex, i - startIndex + 1);

                    // Process the current object string for duplicate keys
                    ProcessObjectForDuplicates(objectString, seenKeys, duplicateCounts);
                }
            }
        }

        // Print duplicate keys found
        foreach (var kvp in duplicateCounts)
        {
            Console.WriteLine($"Duplicate key found: {kvp.Key}");
        }
    }

    static void ProcessObjectForDuplicates(string objectString, HashSet<string> seenKeys, Dictionary<string, int> duplicateCounts)
    {
        // Parse the object string into JSON element
        JsonDocument doc = JsonDocument.Parse(objectString);
        JsonElement root = doc.RootElement;

        // Check for duplicate keys within the object
        foreach (JsonProperty property in root.EnumerateObject())
        {
            string key = property.Name;

            if (!seenKeys.Add(key))
            {
                // Key is a duplicate
                if (duplicateCounts.ContainsKey(key))
                {
                    duplicateCounts[key]++;
                }
                else
                {
                    duplicateCounts[key] = 2; // Start count at 2 for the first duplicate found
                }
            }
        }
    }
}




...........
using System;
using System.Collections.Generic;
using System.Text.RegularExpressions;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""ΑΡΝ"": ""10-00092-993-620-0001"",
            ""ΑΡΝ"": ""10-00092-993-620-0002"",
            ""Details"": {
                ""ΑΡΝ"": ""10-00092-993-620-0003""
            }
        }";

        // Parse JSON string and check for duplicate keys
        CheckForDuplicateKeys(json);
    }

    static void CheckForDuplicateKeys(string json)
    {
        Dictionary<string, int> keyCounts = new Dictionary<string, int>();

        // Regular expression to match JSON object property names
        Regex regex = new Regex("\"([^\"]+)\":");

        // Find all matches of property names in JSON string
        MatchCollection matches = regex.Matches(json);

        foreach (Match match in matches)
        {
            string key = match.Groups[1].Value;

            if (keyCounts.ContainsKey(key))
            {
                keyCounts[key]++;
            }
            else
            {
                keyCounts[key] = 1;
            }
        }

        // Print keys that have more than one occurrence (duplicates)
        foreach (var kvp in keyCounts)
        {
            if (kvp.Value > 1)
            {
                Console.WriteLine($"Duplicate key found: {kvp.Key}");
            }
        }
    }
}



using System;
using System.Collections.Generic;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        [
            {
                ""ΑΡΝ"": ""10-00092-993-620-0001"",
                ""ΑΡΝ"": ""10-00092-993-620-0002""
            },
            {
                ""ΑΡΝ"": ""10-00092-993-620-0003""
            }
        ]";

        // Parse JSON
        JsonDocument doc = JsonDocument.Parse(json);
        JsonElement root = doc.RootElement;

        // Call the method to find and print duplicate property names in the same scope
        FindAndPrintDuplicateProperties(root);
    }

    static void FindAndPrintDuplicateProperties(JsonElement element)
    {
        switch (element.ValueKind)
        {
            case JsonValueKind.Object:
                HashSet<string> seenProperties = new HashSet<string>();
                foreach (JsonProperty property in element.EnumerateObject())
                {
                    string propertyName = property.Name;
                    if (!seenProperties.Add(propertyName)) // If it returns false, it means it's a duplicate
                    {
                        Console.WriteLine($"Duplicate property found: {propertyName}");
                    }
                }
                break;
            case JsonValueKind.Array:
                foreach (JsonElement item in element.EnumerateArray())
                {
                    // Recursively check each item in the array
                    FindAndPrintDuplicateProperties(item);
                }
                break;
            default:
                // For primitive types and unsupported kinds, we don't need to check for duplicates.
                break;
        }
    }
}


using System;
using System.Collections.Generic;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""UserId"": ""skunarrajak@firstam.com"",
            ""Branch"": ""ANE"",
            ""IsMergeResults"": true,
            ""Property"": [
                {
                    ""ΑΡΝ"": ""10-00092-993-620-0000""
                },
                {
                    ""ΑΡΝ"": ""10-00092-993-620-0000""
                }
            ],
            ""Details"": {
                ""APN"": ""123""
            }
        }";

        // Parse JSON
        JsonDocument doc = JsonDocument.Parse(json);
        JsonElement root = doc.RootElement;

        // Call the method to find and print duplicate property names
        FindAndPrintDuplicateProperties(root);
    }

    static void FindAndPrintDuplicateProperties(JsonElement element, HashSet<string> seenProperties = null)
    {
        if (seenProperties == null)
            seenProperties = new HashSet<string>();

        switch (element.ValueKind)
        {
            case JsonValueKind.Object:
                foreach (JsonProperty property in element.EnumerateObject())
                {
                    string propertyName = property.Name;
                    if (!seenProperties.Add(propertyName)) // If it returns false, it means it's a duplicate
                    {
                        Console.WriteLine($"Duplicate property found: {propertyName}");
                    }

                    // Recursively check nested properties
                    FindAndPrintDuplicateProperties(property.Value, seenProperties);
                }
                break;
            case JsonValueKind.Array:
                foreach (JsonElement item in element.EnumerateArray())
                {
                    // Recursively check items in array
                    FindAndPrintDuplicateProperties(item, seenProperties);
                }
                break;
            default:
                // For primitive types and unsupported kinds, we don't need to check for duplicates.
                break;
        }
    }
}
.....
using System;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""UserId"": ""skunarrajak@firstam.com"",
            ""Branch"": ""ANE"",
            ""IsMergeResults"": true,
            ""Property"": [
                {
                    ""ΑΡΝ"": ""10-00092-993-620-0000""
                },
                {
                    ""ΑΡΝ"": ""10-00092-993-620-0000""
                }
            ],
            ""Details"": {
                ""APN"": ""123""
            }
        }";

        // Parse JSON
        JsonDocument doc = JsonDocument.Parse(json);
        JsonElement root = doc.RootElement;

        // Call the method to iterate and print all property names
        PrintJsonPropertyNames(root);
    }

    static void PrintJsonPropertyNames(JsonElement element, string indent = "")
    {
        switch (element.ValueKind)
        {
            case JsonValueKind.Object:
                foreach (JsonProperty property in element.EnumerateObject())
                {
                    Console.WriteLine($"{indent}{property.Name}");
                    PrintJsonPropertyNames(property.Value, indent + "  ");
                }
                break;
            case JsonValueKind.Array:
                foreach (JsonElement item in element.EnumerateArray())
                {
                    PrintJsonPropertyNames(item, indent + "  ");
                }
                break;
            default:
                // For primitive types and unsupported kinds, we don't need to print anything.
                break;
        }
    }
}

......



using System;
using System.Text.Json;

class Program
{
    static void Main()
    {
        string json = @"
        {
            ""UserId"": ""skunarrajak@firstam.com"",
            ""Branch"": ""ANE"",
            ""IsMergeResults"": true,
            ""Property"": [
                {
                    ""ΑΡΝ"": ""10-00092-993-620-0000""
                },
                {
                    ""ΑΡΝ"": ""10-00092-993-620-0000""
                }
            ],
            ""Details"": {
                ""APN"": ""123""
            }
        }";

        // Parse JSON
        JsonDocument doc = JsonDocument.Parse(json);
        JsonElement root = doc.RootElement;

        // Call the method to iterate and print all properties
        PrintJsonProperties(root);
    }

    static void PrintJsonProperties(JsonElement element, string indent = "")
    {
        switch (element.ValueKind)
        {
            case JsonValueKind.Object:
                foreach (JsonProperty property in element.EnumerateObject())
                {
                    Console.WriteLine($"{indent}{property.Name}:");
                    PrintJsonProperties(property.Value, indent + "  ");
                }
                break;
            case JsonValueKind.Array:
                foreach (JsonElement item in element.EnumerateArray())
                {
                    PrintJsonProperties(item, indent + "  ");
                }
                break;
            case JsonValueKind.String:
                Console.WriteLine($"{indent}{element.GetString()}");
                break;
            case JsonValueKind.Number:
                Console.WriteLine($"{indent}{element.GetDecimal()}");
                break;
            case JsonValueKind.True:
            case JsonValueKind.False:
                Console.WriteLine($"{indent}{element.GetBoolean()}");
                break;
            case JsonValueKind.Null:
                Console.WriteLine($"{indent}null");
                break;
            default:
                Console.WriteLine($"{indent}Unsupported ValueKind: {element.ValueKind}");
                break;
        }
    }
}






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

                    // Check for duplicate properties
                    var propertyNames = new HashSet<string>();
                    if (HasDuplicateProperties(json, propertyNames))
                    {
                        context.Response.ContentType = "application/json"; // Set content type
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync("{ \"error\": \"Duplicate properties are not allowed.\" }");
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

        private bool HasDuplicateProperties(JToken token, HashSet<string> propertyNames)
        {
            if (token is JObject jsonObject)
            {
                foreach (var property in jsonObject.Properties())
                {
                    var upperCaseName = property.Name.ToUpper();
                    if (!propertyNames.Add(upperCaseName))
                    {
                        return true;
                    }

                    // Check nested properties
                    if (property.Value is JObject || property.Value is JArray)
                    {
                        var nestedPropertyNames = new HashSet<string>();
                        if (HasDuplicateProperties(property.Value, nestedPropertyNames))
                        {
                            return true;
                        }
                    }
                }
            }
            else if (token is JArray jsonArray)
            {
                foreach (var item in jsonArray)
                {
                    if (HasDuplicateProperties(item, propertyNames))
                    {
                        return true;
                    }
                }
            }

            return false;
        }
    }
}



.......

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

                    // Check for duplicate properties
                    var propertyNames = new HashSet<string>();
                    if (HasDuplicateProperties(json, propertyNames))
                    {
                        context.Response.ContentType = "application/json"; // Set content type
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync("{ \"error\": \"Duplicate properties are not allowed.\" }");
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

        private bool HasDuplicateProperties(JObject jsonObject, HashSet<string> propertyNames)
        {
            var stack = new Stack<JToken>();
            stack.Push(jsonObject);

            while (stack.Count > 0)
            {
                var current = stack.Pop();

                if (current is JObject obj)
                {
                    foreach (var property in obj.Properties())
                    {
                        var upperCaseName = property.Name.ToUpper();
                        if (!propertyNames.Add(upperCaseName))
                        {
                            return true;
                        }

                        if (property.Value is JObject || property.Value is JArray)
                        {
                            stack.Push(property.Value);
                        }
                    }
                }
                else if (current is JArray array)
                {
                    foreach (var item in array)
                    {
                        stack.Push(item);
                    }
                }
            }

            return false;
        }
    }
}





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

                    // Check for duplicate properties
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

        private string CheckForDuplicateProperties(JToken jsonToken)
        {
            if (jsonToken is JObject jsonObject)
            {
                var propertyNames = new HashSet<string>();
                foreach (var property in jsonObject.Properties())
                {
                    var upperCaseName = property.Name.ToUpper();
                    if (!propertyNames.Add(upperCaseName))
                    {
                        return $"Duplicate property '{property.Name}' is not allowed.";
                    }

                    // Check nested objects
                    var nestedErrorMessage = CheckForDuplicateProperties(property.Value);
                    if (nestedErrorMessage != null)
                    {
                        return nestedErrorMessage;
                    }
                }
            }
            else if (jsonToken is JArray jsonArray)
            {
                foreach (var item in jsonArray)
                {
                    var nestedErrorMessage = CheckForDuplicateProperties(item);
                    if (nestedErrorMessage != null)
                    {
                        return nestedErrorMessage;
                    }
                }
            }

            return null;
        }
    }
}







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

                    // Check for duplicate properties
                    if (HasDuplicateProperties(json))
                    {
                        context.Response.ContentType = "application/json"; // Set content type
                        context.Response.StatusCode = StatusCodes.Status400BadRequest;
                        await context.Response.WriteAsync("{ \"error\": \"Duplicate properties are not allowed.\" }");
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

        private bool HasDuplicateProperties(JToken jsonToken)
        {
            var propertyNames = new HashSet<string>();

            var queue = new Queue<JToken>();
            queue.Enqueue(jsonToken);

            while (queue.Count > 0)
            {
                var current = queue.Dequeue();

                if (current is JObject jsonObject)
                {
                    foreach (var property in jsonObject.Properties())
                    {
                        var upperCaseName = property.Name.ToUpper();
                        if (!propertyNames.Add(upperCaseName))
                        {
                            return true;
                        }

                        queue.Enqueue(property.Value);
                    }
                }
                else if (current is JArray jsonArray)
                {
                    foreach (var item in jsonArray)
                    {
                        queue.Enqueue(item);
                    }
                }
            }

            return false;
        }
    }
}




,..............
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
