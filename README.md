public class NSXApiRequest
{
    public string OrderNumber { get; set; }
    public string PolicyNumber { get; set; }
    public string CommitmentNumber { get; set; }

    [JsonPropertyName("Insured LoanNumber")]
    public string InsuredLoanNumber { get; set; }

    public string PropertyId { get; set; }
    public Guid StarterId { get; set; }
    public string ProductCode { get; set; }
    public string Book { get; set; }
    public string Page { get; set; }
    public string DocumentNumber { get; set; }
    public string InstrumentSearchType { get; set; }
    public string CMTDocumentNumber { get; set; }
    public string DocCMTId { get; set; }
    public bool Runasproperty { get; set; }
    public StarterQualifiers StarterQualifiers { get; set; }
}
