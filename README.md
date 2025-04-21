

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
