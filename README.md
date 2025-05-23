We have implemented a condition to suppress the error message in specific scenarios.
If the input is invalid and the BypassIndex flag is set to true, then the message "Property not found on current Assessor file." will not be shown

// Duplicate detection
var duplicateGroups = searchItems
    .GroupBy(x => x.AddressSearch?.SearchedAddress)
    .Where(g => g.Count() > 1)
    .ToList();

if (duplicateGroups.Any())
{
    Console.WriteLine("Duplicates detected:");
    foreach (var group in duplicateGroups)
    {
        Console.WriteLine($" - Address: {group.Key}, Count: {group.Count()}");
        foreach (var item in group)
        {
            Console.WriteLine($"   - Duplicate ID: {item.Id}");
        }
    }
}


..
Dev Note:
This behavior is as per FAD design. In the Owner Search response, we do not receive a PropertyId or MatchedPropertyId, so we display the error message: "Property not found on current Assessor file."
However, in the APN and Address Search responses, the PropertyId is present, so no such error message is displayed.


DECLARE @sql NVARCHAR(MAX) = ''

-- Generate DROP TABLE statements for all user tables
SELECT @sql += 'DROP TABLE [' + SCHEMA_NAME(schema_id) + '].[' + name + '];' + CHAR(13)
FROM sys.tables

-- Disable foreign key constraints first
EXEC sp_msforeachtable 'ALTER TABLE ? NOCHECK CONSTRAINT ALL'

-- Execute the DROP TABLE statements
EXEC sp_executesql @sql


The index count refers only to index-based results and does not apply to direct document fetches. Since the address '12493 JOHN ARTHUR' was an exact match from typeahead, it returned exact match results. Therefore, the document was fetched directly from the stored procedure.







> "The index count refers only to index-based results and does not apply to direct document results. Since the address '1238 John Rather' was an exact match during typeahead (FAD), it did not return any index-based results. Instead, the document was fetched directly from the stored procedure."
>
> 






function handleDataTypeChange() {
    var datatype = document.getElementById('<%= ddlDataType.ClientID %>').value;
    var maxlengthContainer = document.getElementById('<%= maxlengthContainer.ClientID %>');
    var txtMaxLength = document.getElementById('<%= txtmaxlength.ClientID %>');
    var rfvMaxLength = document.getElementById('<%= rfvMaxLength.ClientID %>');

    if (!maxlengthContainer || !txtMaxLength || !rfvMaxLength) return;

    if (datatype === 'date') {
        maxlengthContainer.style.display = 'none';
        txtMaxLength.removeAttribute('required');
        ValidatorEnable(document.getElementById('<%= rfvMaxLength.ClientID %>'), false);
    } else {
        maxlengthContainer.style.display = 'block';
        txtMaxLength.setAttribute('required', 'required');
        ValidatorEnable(document.getElementById('<%= rfvMaxLength.ClientID %>'), true);
    }
}
var maxlengthContainer = document.getElementById('<%= maxlengthContainer.ClientID %>');
if (!maxlengthContainer) return;

if (datatype === 'date') {
    maxlengthContainer.style.display = 'none';
} else {
    maxlengthContainer.style.display = 'block';
}



var maxlengthContainer = document.getElementById('maxlengthContainer');
if (!maxlengthContainer) return; // Or handle gracefully

if (datatype === 'date') {
    maxlengthContainer.style.display = 'none';
} else {
    maxlengthContainer.style.display = 'block';
}

var maxlengthContainer = document.getElementById('maxlengthContainer');
if (!maxlengthContainer) return; // Or handle gracefully

if (datatype === 'date') {
    maxlengthContainer.style.display = 'none';
} else {
    maxlengthContainer.style.display = 'block';
}

<%@ Page Language="C#" AutoEventWireup="true" CodeFile="DynamicNewentryPopup.aspx.cs" Inherits="DynamicNewentryPopup" %>

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
    <title>Dynamic Entry Form</title>

    <!-- Meta for responsive design -->
    <meta name="viewport" content="width=device-width, initial-scale=1" />

    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet" />

    <!-- Custom CSS -->
    <style>
        .custom-label {
            font-size: 14px;
            color: #333;
        }

        .custom-input {
            border-radius: 6px;
            padding: 8px;
        }
    </style>

    <!-- JS: Numeric Only Validation -->
    <script type="text/javascript">
        function isNumberKey(evt) {
            var charCode = (evt.which) ? evt.which : evt.keyCode;
            if (charCode < 48 || charCode > 57)
                return false;
            return true;
        }
    </script>
</head>
<body>
    <form id="form1" runat="server">
        <div class="container mt-4">
            <div class="row">
                <div class="col-md-12">
                    <!-- Dynamic Fields Will Be Added to this Panel -->
                    <asp:PlaceHolder ID="phNewentryFields" runat="server" />
                </div>
            </div>
        </div>
    </form>

    <!-- Optional: Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>

protected void LoadNewEntryField(string Projectname)
{
    phNewentryFields.Controls.Clear();

    DBobjects dbobj = new DBobjects();
    DataSet dsFieldDec = dbobj.GetFieldDecData(Projectname, "New Entry");

    if (dsFieldDec != null && dsFieldDec.Tables.Count > 0 && dsFieldDec.Tables[0].Rows.Count > 0)
    {
        string rawData = dsFieldDec.Tables[0].Rows[0]["FieldData"].ToString();
        string[] fieldRows = rawData.Split(new[] { '/' }, StringSplitOptions.RemoveEmptyEntries);

        int fieldCounter = 0;
        Panel currentRow = null;

        foreach (string row in fieldRows)
        {
            if (!string.IsNullOrWhiteSpace(row))
            {
                string cleaned = row.Replace("(", "").Replace(")", "").Replace("\"", "");
                string[] pairs = cleaned.Split(',');

                Dictionary<string, string> FieldDict = new Dictionary<string, string>(StringComparer.OrdinalIgnoreCase);
                foreach (string pair in pairs)
                {
                    string[] kv = pair.Split(':');
                    if (kv.Length == 2)
                    {
                        string key = kv[0].Trim();
                        string value = kv[1].Trim();
                        FieldDict[key] = value;
                    }
                }

                // Extract field values
                string fieldname = FieldDict.ContainsKey("FieldName") ? FieldDict["FieldName"] : "unnamed";
                string fieldType = FieldDict.ContainsKey("FieldType") ? FieldDict["FieldType"] : "Textbox";
                string fieldFormat = FieldDict.ContainsKey("FieldFormat") ? FieldDict["FieldFormat"] : "AlphaNumeric";
                string Maxlength = FieldDict.ContainsKey("MaxLength") ? FieldDict["MaxLength"] : "50";

                // Start new row every 2 fields
                if (fieldCounter % 2 == 0)
                {
                    currentRow = new Panel { CssClass = "row mb-3" };
                    phNewentryFields.Controls.Add(currentRow);
                }

                Panel colPanel = new Panel { CssClass = "col-md-6 mb-2" };
                Panel formGroup = new Panel { CssClass = "form-group" };

                // Label
                Label lbl = new Label
                {
                    Text = fieldname + ":",
                    CssClass = "form-label fw-bold mb-1",
                    Width = Unit.Pixel(120)
                };
                formGroup.Controls.Add(lbl);

                // Input Control
                Control inputControl;

                if (fieldType.Equals("Dropdown", StringComparison.OrdinalIgnoreCase))
                {
                    DropDownList ddl = new DropDownList
                    {
                        ID = "ddl_New_" + fieldname,
                        CssClass = "form-select w-100",
                        Width = Unit.Percentage(100)
                    };

                    ddl.Items.Add(new ListItem("Select", ""));
                    ddl.Items.Add(new ListItem("Option 1", "1"));
                    ddl.Items.Add(new ListItem("Option 2", "2"));

                    inputControl = ddl;
                }
                else
                {
                    TextBox txt = new TextBox
                    {
                        ID = "txt_New_" + fieldname,
                        CssClass = "form-control w-100",
                        Width = Unit.Percentage(100)
                    };

                    int maxlen = 50;
                    if (int.TryParse(Maxlength, out maxlen))
                    {
                        txt.MaxLength = maxlen;
                    }

                    if (fieldFormat.Equals("Numeric", StringComparison.OrdinalIgnoreCase))
                    {
                        txt.Attributes["onkeypress"] = "return isNumberKey(event)";
                        txt.Attributes["placeholder"] = "Enter number";
                    }
                    else if (fieldFormat.Equals("Date", StringComparison.OrdinalIgnoreCase))
                    {
                        txt.Attributes["type"] = "date";
                        txt.Attributes["placeholder"] = "MM/DD/YYYY";
                    }
                    else
                    {
                        txt.Attributes["placeholder"] = "Enter text";
                    }

                    inputControl = txt;
                }

                formGroup.Controls.Add(inputControl);
                colPanel.Controls.Add(formGroup);
                currentRow.Controls.Add(colPanel);

                fieldCounter++;
            }
        }
    }
}

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>

if (fieldCounter % 2 == 0)
{
    currentRow = new Panel { CssClass = "row mb-3" };
    phNewentryFields.Controls.Add(currentRow);
}

Panel colPanel = new Panel { CssClass = "col-md-6 mb-2" };

Panel formGroup = new Panel { CssClass = "form-group" };
formGroup.Controls.Add(lbl);
formGroup.Controls.Add(ddl); // or txtBox

colPanel.Controls.Add(formGroup);
currentRow.Controls.Add(colPanel);




<style>
    .custom-label {
        font-size: 14px;
        color: #333;
    }
    .custom-input {
        border-radius: 6px;
        padding: 8px;
    }
</style>
Label lbl = new Label
{
    Text = fieldname + ":",
    CssClass = "form-label fw-bold mb-1 custom-label"
};

DropDownList ddl = new DropDownList
{
    ID = "ddl_" + fieldname,
    CssClass = "form-select custom-input w-100"
};



..
List<APNMainEntity> apns = new List<APNMainEntity>();

if (searchRequest.Qualifiers.RecordsPerPage != null && searchRequest.Qualifiers.CurrentPage != null)
{
    var apnQuery = documentContainer
        .GetItemLinqQueryable<APNMainEntity>()
        .Where(p => p.PartitionKey == itemResponse.DocumentsSearchId.ToString() ||
                   (itemResponse.Matchcorelationid != null && p.PartitionKey == itemResponse.Matchcorelationid))
        .Skip((int)searchRequest.Qualifiers.RecordsPerPage * ((int)searchRequest.Qualifiers.CurrentPage - 1))
        .Take((int)searchRequest.Qualifiers.RecordsPerPage);

    var apnsIterator = apnQuery.ToFeedIterator();
    var addressResponse = await apnsIterator.ReadNextAsync();
    apns = addressResponse.ToList();
}
else
{
    var apnQuery = documentContainer
        .GetItemLinqQueryable<APNMainEntity>()
        .Where(p => p.PartitionKey == itemResponse.DocumentsSearchId.ToString() ||
                   (itemResponse.Matchcorelationid != null && p.PartitionKey == itemResponse.Matchcorelationid));

    var apnIterator = apnQuery.ToFeedIterator();
    var addressResponse = await apnIterator.ReadNextAsync();
    apns = addressResponse.ToList();
}


List<AddressMainEntity> addresses = new List<AddressMainEntity>();

if (searchRequest.Qualifiers.RecordsPerPage != null && searchRequest.Qualifiers.CurrentPage != null)
{
    var addressQuery = documentContainer
        .GetItemLinqQueryable<AddressMainEntity>()
        .Where(p => p.PartitionKey == itemResponse.DocumentSearchId.ToString())
        .Skip((int)searchRequest.Qualifiers.RecordsPerPage * ((int)searchRequest.Qualifiers.CurrentPage - 1))
        .Take((int)searchRequest.Qualifiers.RecordsPerPage);

    var addressIterator = addressQuery.ToFeedIterator();
    var addressResponse = await addressIterator.ReadNextAsync();

    addresses = addressResponse.ToList();
}
else
{
    var addressQuery = documentContainer
        .GetItemLinqQueryable<AddressMainEntity>()
        .Where(p => p.PartitionKey == itemResponse.DocumentSearchId.ToString());

    var addressIterator = addressQuery.ToFeedIterator();
    var addressResponse = await addressIterator.ReadNextAsync();

    addresses = addressResponse.ToList();
}






var query = documentContainer
    .GetItemLinqQueryable<AddressMainEntity>()
    .Where(p => p.PartitionKey == itemResponse.DocumentSearchId.ToString())
    .Skip((int)searchRequest.Qualifiers.RecordsPerPage * ((int)searchRequest.Qualifiers.CurrentPage - 1))
    .Take((int)searchRequest.Qualifiers.RecordsPerPage);

var iterator = query.ToFeedIterator();
var response = await iterator.ReadNextAsync();
var addresses = response.ToList();




var query = documentContainer
    .GetItemLinqQueryable<AddressMainEntity>()
    .Where(p => p.PartitionKey == itemResponse.DocumentSearchId.ToString())
    .Skip((int)searchRequest.Qualifiers.RecordsPerPage * ((int)searchRequest.Qualifiers.CurrentPage - 1))
    .Take((int)searchRequest.Qualifiers.RecordsPerPage);
