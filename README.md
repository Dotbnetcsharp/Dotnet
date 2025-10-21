string BuildExecScriptWithTVP(string procedureName, DynamicParameters parameters)
{
    var sb = new StringBuilder();
    var execParts = new List<string>();
    int tvpCounter = 1;

    foreach (var name in parameters.ParameterNames)
    {
        var value = parameters.Get<dynamic>(name);

        // Detect if it's a DataTable (TVP)
        if (value is DataTable dt)
        {
            string tvpVar = $"@tvp{tvpCounter}";
            string tvpTypeName = ((SqlMapper.ICustomQueryParameter)parameters.Lookup(name)).ToString();

            // You can log or manually specify the TVP type name if known
            sb.AppendLine($"DECLARE {tvpVar} AS {tvpTypeName};");

            foreach (DataRow row in dt.Rows)
            {
                var cols = string.Join(", ", row.ItemArray.Select(v =>
                    v == null || v == DBNull.Value ? "NULL" :
                    v is string ? $"'{v.ToString().Replace("'", "''")}'" :
                    v is bool b ? (b ? "1" : "0") :
                    v is DateTime d ? $"'{d:yyyy-MM-dd HH:mm:ss}'" :
                    v.ToString()));

                sb.AppendLine($"INSERT INTO {tvpVar} VALUES ({cols});");
            }

            execParts.Add($"{name} = {tvpVar}");
            tvpCounter++;
        }
        else
        {
            string formattedValue = value == null ? "NULL"
                : value is string ? $"'{value.Replace("'", "''")}'"
                : value is bool b ? (b ? "1" : "0")
                : value is DateTime d ? $"'{d:yyyy-MM-dd HH:mm:ss}'"
                : value.ToString();

            execParts.Add($"{name} = {formattedValue}");
        }
    }

    sb.AppendLine($"EXEC {procedureName} {string.Join(", ", execParts)};");
    return sb.ToString();
}

var execScript = BuildExecScript("SP_NSXStarterSourceInfo", parameters);
Console.WriteLine(execScript); // or log it to file

await connection.ExecuteAsync("SP_NSXStarterSourceInfo", parameters, commandType: CommandType.StoredProcedure);
