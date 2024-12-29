private void ApplyHtmlToExcel(ExcelRange cell, string htmlContent)
{
    // Decode the HTML
    string decodedHtml = System.Web.HttpUtility.HtmlDecode(htmlContent);

    // Check if the content contains a table
    if (decodedHtml.Contains("<table"))
    {
        // Use HtmlAgilityPack to parse the table
        HtmlAgilityPack.HtmlDocument doc = new HtmlAgilityPack.HtmlDocument();
        doc.LoadHtml(decodedHtml);

        // Find the table node
        var tableNode = doc.DocumentNode.SelectSingleNode("//table");
        if (tableNode != null)
        {
            // Parse table content and render it in Excel
            var rows = tableNode.SelectNodes(".//tr");
            if (rows != null)
            {
                int currentRow = cell.Start.Row; // Start from the cell's row
                int currentCol = cell.Start.Column;

                foreach (var row in rows)
                {
                    var cols = row.SelectNodes(".//td|.//th");
                    if (cols != null)
                    {
                        int colIndex = currentCol;
                        foreach (var col in cols)
                        {
                            // Get the cell value
                            string cellValue = col.InnerText.Trim();

                            // Write the value to the Excel cell
                            cell.Worksheet.Cells[currentRow, colIndex].Value = cellValue;

                            // Apply basic styling (e.g., bold for <th>)
                            if (col.Name == "th")
                            {
                                cell.Worksheet.Cells[currentRow, colIndex].Style.Font.Bold = true;
                                cell.Worksheet.Cells[currentRow, colIndex].Style.HorizontalAlignment = OfficeOpenXml.Style.ExcelHorizontalAlignment.Center;
                                cell.Worksheet.Cells[currentRow, colIndex].Style.Fill.PatternType = OfficeOpenXml.Style.ExcelFillStyle.Solid;
                                cell.Worksheet.Cells[currentRow, colIndex].Style.Fill.BackgroundColor.SetColor(System.Drawing.Color.LightGray);
                            }

                            // Move to the next column
                            colIndex++;
                        }
                    }

                    // Move to the next row
                    currentRow++;
                }
            }
        }
    }
    else
    {
        // If not a table, apply basic HTML parsing
        decodedHtml = decodedHtml.Replace("<br>", Environment.NewLine);

        if (decodedHtml.Contains("<b>"))
        {
            decodedHtml = decodedHtml.Replace("<b>", "").Replace("</b>", "");
            cell.Style.Font.Bold = true;
        }

        // Set the final value
        cell.Value = decodedHtml;

        // Enable text wrapping for multiline content
        cell.Style.WrapText = true;
    }
}
