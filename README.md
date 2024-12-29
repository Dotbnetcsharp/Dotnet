protected void btndownload_Click(object sender, EventArgs e)
{
    try
    {
        bindData(); // Ensure the GridView is properly populated.

        using (ExcelPackage excelPackage = new ExcelPackage())
        {
            // Create a worksheet
            ExcelWorksheet worksheet = excelPackage.Workbook.Worksheets.Add("ICO Sequence Tracking Report");

            // Add headers with styles
            for (int i = 0; i < grdProjQueue.HeaderRow.Cells.Count; i++)
            {
                var headerText = grdProjQueue.HeaderRow.Cells[i].Text.Trim();

                worksheet.Cells[1, i + 1].Value = headerText;
                worksheet.Cells[1, i + 1].Style.Font.Bold = true;
                worksheet.Cells[1, i + 1].Style.Fill.PatternType = OfficeOpenXml.Style.ExcelFillStyle.Solid;
                worksheet.Cells[1, i + 1].Style.Fill.BackgroundColor.SetColor(System.Drawing.Color.FromArgb(70, 92, 113));
                worksheet.Cells[1, i + 1].Style.Font.Color.SetColor(System.Drawing.Color.White);
                worksheet.Cells[1, i + 1].Style.HorizontalAlignment = OfficeOpenXml.Style.ExcelHorizontalAlignment.Center;
            }

            // Add data rows
            for (int i = 0; i < grdProjQueue.Rows.Count; i++)
            {
                for (int j = 0; j < grdProjQueue.Rows[i].Cells.Count; j++)
                {
                    var cellText = grdProjQueue.Rows[i].Cells[j].Text.Trim();

                    // Decode HTML and handle special characters
                    cellText = System.Web.HttpUtility.HtmlDecode(cellText);

                    // Replace line breaks with spaces to keep data in one cell
                    cellText = cellText.Replace("\r\n", " ").Replace("\n", " ");

                    worksheet.Cells[i + 2, j + 1].Value = cellText;
                    worksheet.Cells[i + 2, j + 1].Style.WrapText = true; // Enable wrapping for large text
                }
            }

            // Auto-fit columns
            worksheet.Cells[worksheet.Dimension.Address].AutoFitColumns();

            // Prepare the response
            Response.Clear();
            Response.ContentType = "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet";
            Response.AddHeader("content-disposition", "attachment; filename=ICOSequenceTrackingReport.xlsx");

            // Write the Excel file to the response
            Response.BinaryWrite(excelPackage.GetAsByteArray());
            Response.End();
        }
    }
    catch (Exception ex)
    {
        Logger.WriteLog(ex.Message + " " + DateTime.Now);
    }
}
