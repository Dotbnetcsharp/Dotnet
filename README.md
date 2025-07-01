public class TrackedStatusDto
{
    public string? CorrelationId { get; set; }
    public string? Status { get; set; }
    public string? ErrorTitle { get; set; }
    public string? ErrorMessage { get; set; }
    public string? ErrorData { get; set; }
    public string? CountyFips { get; set; }
    public string? StateCode { get; set; }
    public string? SearchType { get; set; }
    public DateTime? DateFrom { get; set; }
    public DateTime? DateThru { get; set; }
    public string? Branch { get; set; }
    public string? User { get; set; }
    public string? ClientIp { get; set; }
    public string? OrderNo { get; set; }
    public string? SearchedAPN { get; set; }
}
