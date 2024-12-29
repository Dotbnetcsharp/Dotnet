protected void btndownload_Click(object sender, EventArgs e)
{
    try
    {
        // Set response headers for Excel
        Response.Clear();
        Response.Buffer = true;
        Response.AddHeader("content-disposition", "attachment;filename=Report_" + DateTime.Now.ToString("yyyyMMddHHmmss") + ".xls");
        Response.Charset = "";
        Response.ContentType = "application/vnd.ms-excel";
        Response.ContentEncoding = System.Text.Encoding.UTF8;

        // Add BOM for proper rendering
        Response.BinaryWrite(System.Text.Encoding.UTF8.GetPreamble());

        // Render GridView to HTML
        using (StringWriter sw = new StringWriter())
        {
            using (HtmlTextWriter hw = new HtmlTextWriter(sw))
            {
                // Disable paging and sorting for exporting
                grdProjQueue.AllowPaging = false;
                grdProjQueue.AllowSorting = false;
                bindData();
                grdProjQueue.DataBind();

                // Apply header styles
                grdProjQueue.HeaderRow.Style.Add("background-color", "#465c71");
                grdProjQueue.HeaderRow.Style.Add("color", "#ffffff");
                grdProjQueue.HeaderRow.Style.Add("font-weight", "bold");

                // Apply alternating row styles
                foreach (GridViewRow row in grdProjQueue.Rows)
                {
                    row.Attributes.Add("style", "border:1px solid #000;");
                    if (row.RowIndex % 2 == 0)
                    {
                        row.Style.Add("background-color", "#f2f2f2");
                    }
                }

                // Render the GridView
                grdProjQueue.RenderControl(hw);

                // Add style for rendering HTML properly in Excel
                Response.Write("<style> .text { mso-number-format:\"\\@\"; } </style>");
                Response.Output.Write(sw.ToString());
            }
        }

        Response.Flush();
        Response.End();
    }
    catch (Exception ex)
    {
        // Handle exceptions
        Logger.WriteLog(ex.Message + " " + DateTime.Now + " " + ex.StackTrace);
    }
}

// Override the VerifyRenderingInServerForm method
public override void VerifyRenderingInServerForm(Control control)
{
    // Required to avoid the "GridView must be placed inside a form tag" error
}
