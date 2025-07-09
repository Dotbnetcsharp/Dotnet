using System;
using System.Data.SqlClient;

class Program
{
    static void Main()
    {
        // Replace with your actual SQL Server connection string
        string connectionString = "Server=YOUR_SERVER_NAME;Database=datastore;Integrated Security=True;";

        using (SqlConnection connection = new SqlConnection(connectionString))
        {
            try
            {
                connection.Open();
                Console.WriteLine("
                Connection to SQL Server established successfully.");
            }
            catch (Exception ex)
            {
                Console.WriteLine("Failed to connect to SQL Server.");
                Console.WriteLine($"Error: {ex.Message}");
            }
        }

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
}
