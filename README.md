stopwatch.Start();
var pattern = new Regex("[-, .\\t\\r\\n]{2,3}");
using (var connection = new SqlConnection(_connectionStrings.NeoDatabase))
{
    var tblInstr = new DataTable();
    var tblBookpage = new DataTable();
    
    // Define table structures to match the SQL table types
    tblInstr.Columns.Add(new DataColumn("instrument_number", typeof(string)));
    tblBookpage.Columns.Add(new DataColumn("book", typeof(string)));
    tblBookpage.Columns.Add(new DataColumn("page", typeof(string)));

    // Fill the tables with data
    foreach (var name in documentModel.DocumentNumbers)
    {
        tblInstr.Rows.Add(name.RemoveAPNSpecialChars().Trim());
    }

    foreach (var bp in documentModel.BooksPages)
    {
        tblBookpage.Rows.Add(bp.Book, bp.Page);
    }

    // Prepare stored procedure execution
    var spName = StoredProceduresName.NEO.SP_usp_GetDocumentByInstrument;
    var fips = documentModel.CountyFips;

    // Generate and log the full EXEC query
    var execQuery = GenerateExecQuery(spName, fips, tblInstr, tblBookpage);
    Console.WriteLine(execQuery); // Log the query

    // Execute the query
    var query = await connection.QueryMultipleAsync(
        spName,
        new
        {
            FIPS = fips,
            instrument_number = tblInstr.AsTableValuedParameter("[dbo].[udt_instrument]"),
            book_page = tblBookpage.AsTableValuedParameter("[dbo].[udt_bookpage]")
        },
        commandTimeout: 30,
        commandType: CommandType.StoredProcedure);

    var docs = await query.ReadAsync<DBDocumentModel>();
    var refs = await query.ReadAsync<DBReferenceModel>();
    var count = await query.ReadFirstOrDefaultAsync<DBDocumentCount>();

    return new DBDocumentsModel();
}

static string GenerateExecQuery(string spName, string fips, DataTable tblInstr, DataTable tblBookpage)
{
    var sb = new StringBuilder();

    // Stored procedure name
    sb.AppendLine($"EXEC {spName}");
    sb.AppendLine($"    @FIPS = '{fips}',");

    // Instrument table parameter
    sb.AppendLine($"    @instrument_number = (SELECT * FROM (VALUES");

    for (int i = 0; i < tblInstr.Rows.Count; i++)
    {
        var row = tblInstr.Rows[i];
        sb.Append($"        ('{row["instrument_number"]}')");
        if (i < tblInstr.Rows.Count - 1) sb.Append(",");
        sb.AppendLine();
    }

    sb.AppendLine($"    ) AS T(instrument_number)),");
    
    // Book page table parameter
    sb.AppendLine($"    @book_page = (SELECT * FROM (VALUES");

    for (int i = 0; i < tblBookpage.Rows.Count; i++)
    {
        var row = tblBookpage.Rows[i];
        sb.Append($"        ('{row["book"]}', '{row["page"]}')");
        if (i < tblBookpage.Rows.Count - 1) sb.Append(",");
        sb.AppendLine();
    }

    sb.AppendLine($"    ) AS T(book, page));");

    return sb.ToString();
}




.........
using System;
using System.Data;
using System.Data.SqlClient;

stopwatch.Start();
var pattern = new Regex("[-, .\\t\\r\\n]{2,3}");
using (var connection = new SqlConnection(_connectionStrings.NeoDatabase))
{
    var tblInstr = new DataTable();
    var tblBookpage = new DataTable();
    tblInstr.Columns.Add(new DataColumn("instrument_number", typeof(string)));
    tblBookpage.Columns.Add(new DataColumn("book", typeof(string)));
    tblBookpage.Columns.Add(new DataColumn("page", typeof(string)));

    foreach (var name in documentModel.DocumentNumbers)
    {
        tblInstr.Rows.Add(name.RemoveAPNSpecialChars().Trim());
    }

    foreach (var bp in documentModel.BooksPages)
    {
        tblBookpage.Rows.Add(bp.Book, bp.Page);
    }

    var query = await connection.QueryMultipleAsync(StoredProceduresName.NEO.SP_usp_GetDocumentByInstrument,
        new
        {
            FIPS = documentModel.CountyFips,
            instrument_number = tblInstr.AsTableValuedParameter("[dbo].[udt_instrument]"),
            book_page = tblBookpage.AsTableValuedParameter("[dbo].[udt_bookpage]")
        },
        commandTimeout: 30,
        commandType: CommandType.StoredProcedure);

    // Log the SP name and parameters
    LogStoredProcedureDetails(
        StoredProceduresName.NEO.SP_usp_GetDocumentByInstrument,
        documentModel.CountyFips,
        tblInstr,
        tblBookpage
    );

    var docs = await query.ReadAsync<DBDocumentModel>();
    var refs = await query.ReadAsync<DBReferenceModel>();
    var count = await query.ReadFirstOrDefaultAsync<DBDocumentCount>();

    return new DBDocumentsModel();
}

static void LogStoredProcedureDetails(string spName, string fips, DataTable tblInstr, DataTable tblBookpage)
{
    Console.WriteLine($"Executing Stored Procedure: {spName}");
    Console.WriteLine($"Parameter: FIPS, Value: {fips}");

    Console.WriteLine($"Parameter: @instrument_number (Table-Valued)");
    foreach (DataRow row in tblInstr.Rows)
    {
        Console.WriteLine($"  instrument_number: {row["instrument_number"]}");
    }

    Console.WriteLine($"Parameter: @book_page (Table-Valued)");
    foreach (DataRow row in tblBookpage.Rows)
    {
        Console.WriteLine($"  book: {row["book"]}, page: {row["page"]}");
    }
}

Dev Note:
In the stage environment, Cosmos DB is unable to process large data requests simultaneously. We tested the same request locally by manually setting the RU/s to 40,000, and we successfully retrieved all 3,000 records. However, in the stage environment, the RU/s is set to only 4,000, which is causing the error: "Request rate is large."

Fix:
To resolve this issue, we need to increase the RU/s scale in the stage and other environments.

Reference:
Please find the attached screenshot for the local test results.
