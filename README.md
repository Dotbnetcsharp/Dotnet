int totalRowCount = 0;

foreach (DataTable table in dsTemp.Tables)
{
    totalRowCount += table.Rows.Count;  // Add the row count of each table
}

Console.WriteLine("Total Row Count: " + totalRowCount);
