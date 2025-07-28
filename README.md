public static string GetExecutableSql(SqlCommand command)
{
    if (command == null)
        throw new ArgumentNullException(nameof(command));

    var builder = new StringBuilder();

    builder.AppendLine($"EXEC {command.CommandText}");

    var paramStrings = new List<string>();
    foreach (SqlParameter param in command.Parameters)
    {
        string formattedValue;

        if (param.Value == null || param.Value == DBNull.Value)
        {
            formattedValue = "NULL";
        }
        else if (param.SqlDbType == SqlDbType.VarChar ||
                 param.SqlDbType == SqlDbType.NVarChar ||
                 param.SqlDbType == SqlDbType.Char ||
                 param.SqlDbType == SqlDbType.NChar ||
                 param.SqlDbType == SqlDbType.Text ||
                 param.SqlDbType == SqlDbType.NText ||
                 param.SqlDbType == SqlDbType.UniqueIdentifier ||
                 param.SqlDbType == SqlDbType.DateTime ||
                 param.SqlDbType == SqlDbType.Date)
        {
            formattedValue = $"'{param.Value.ToString().Replace("'", "''")}'";
        }
        else
        {
            formattedValue = param.Value.ToString();
        }

        paramStrings.Add($"@{param.ParameterName} = {formattedValue}");
    }

    builder.AppendLine(string.Join(",\n", paramStrings));
    return builder.ToString();
}
