protected void btndownload_Click(object sender, EventArgs e)
{
    try
    {
        bindData(); // Ensure your GridView is properly populated before exporting.

        using (ExcelPackage excelPackage = new ExcelPackage())
        {
            // Create a worksheet
            ExcelWorksheet worksheet = excelPackage.Workbook.Worksheets.Add("ICO Sequence Tracking Report");

            // Add headers
            for (int i = 0; i < grdProjQueue.HeaderRow.Cells.Count; i++)
            {
                worksheet.Cells[1, i + 1].Value = grdProjQueue.HeaderRow.Cells[i].Text;
                worksheet.Cells[1, i + 1].Style.Font.Bold = true;
                worksheet.Cells[1, i + 1].Style.Fill.PatternType = OfficeOpenXml.Style.ExcelFillStyle.Solid;
                worksheet.Cells[1, i + 1].Style.Fill.BackgroundColor.SetColor(System.Drawing.Color.FromArgb(70, 92, 113));
                worksheet.Cells[1, i + 1].Style.Font.Color.SetColor(System.Drawing.Color.White);
            }

            // Add data
            for (int i = 0; i < grdProjQueue.Rows.Count; i++)
            {
                for (int j = 0; j < grdProjQueue.Rows[i].Cells.Count; j++)
                {
                    var cellText = grdProjQueue.Rows[i].Cells[j].Text.Trim();

                    // Remove HTML tags and replace line breaks
                    cellText = System.Text.RegularExpressions.Regex.Replace(cellText, "<.*?>", string.Empty);
                    cellText = cellText.Replace("\r\n", " ").Replace("\n", " ");

                    worksheet.Cells[i + 2, j + 1].Value = cellText;
                    worksheet.Cells[i + 2, j + 1].Style.WrapText = false; // Prevent wrapping
                }
            }

            // Auto-fit columns to adjust for content
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
