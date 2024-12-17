// Create the SQL command string
string sqlCommandText = "EXEC YourStoredProcedure ";

// Append each parameter to the SQL string
foreach (SqlParameter param in da.SelectCommand.Parameters)
{
    sqlCommandText += $"{param.ParameterName} = '{param.Value}', ";
}

// Remove the trailing comma and space
sqlCommandText = sqlCommandText.TrimEnd(',', ' ');

// Assuming you have a SqlConnection object (connection)
using (SqlCommand sqlCommand = new SqlCommand(sqlCommandText, connection))
{
    try
    {
        connection.Open();
        int rowsAffected = sqlCommand.ExecuteNonQuery(); // Execute the command
        Console.WriteLine($"Rows affected: {rowsAffected}");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error executing stored procedure: {ex.Message}");
    }
}
