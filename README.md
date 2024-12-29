protected void btndownload_Click(object sender, EventArgs e)
{
    try
    {
        strexport = true;

        string strDate = DateTime.Today.ToShortDateString();
        string[] strDateEle = strDate.Split('/');
        string strate = "(" + strDateEle[0] + strDateEle[1] + strDateEle[2] + ")";

        Response.ClearContent();
        Response.AddHeader("content-disposition", string.Format("attachment; filename={0}", "ICOSequenceTrackingReport" + strate + ".xls"));
        HttpContext.Current.Response.Buffer = true;
        Response.ContentType = "application/ms-excel";
        Response.Charset = string.Empty;
        Response.ContentEncoding = System.Text.Encoding.Unicode;
        Response.BinaryWrite(System.Text.Encoding.Unicode.GetPreamble());

        StringWriter stringWriter = new StringWriter();
        HtmlTextWriter htmlTextWriter = new HtmlTextWriter(stringWriter);

        bindData();

        grdProjQueue.AllowPaging = false;
        grdProjQueue.AllowSorting = false;
        grdProjQueue.DataBind();

        // Change header background color
        grdProjQueue.HeaderRow.Style.Add("background-color", "#FFFFFF");

        // Apply styles to GridView header cells
        for (int index = 0; index < grdProjQueue.HeaderRow.Cells.Count; index++)
        {
            grdProjQueue.HeaderRow.Cells[index].Style.Add("background-color", "#465c71");
            grdProjQueue.HeaderRow.Cells[index].Style.Add("color", "#ffffff");
        }

        // Apply styles to GridView data cells to prevent wrapping
        foreach (GridViewRow row in grdProjQueue.Rows)
        {
            foreach (TableCell cell in row.Cells)
            {
                cell.Style.Add("mso-number-format", "\\@"); // Treat content as text
                cell.Style.Add("white-space", "nowrap");   // Prevent text wrapping
                cell.Style.Add("width", "150px");         // Adjust cell width as needed
            }
        }

        strexport = false;
        grdProjQueue.RenderControl(htmlTextWriter);

        // Write custom styles for Excel
        Response.Write("<style> TD { mso-number-format:\\@; white-space: nowrap; } </style>");
        Response.Write(stringWriter.ToString());
        Response.End();
    }
    catch (Exception ex)
    {
        Logger.WriteLog(ex.Message + " " + DateTime.Now);
    }
}
