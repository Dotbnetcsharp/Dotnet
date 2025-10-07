public class NCSSearchService
{
    private readonly NCSapiConfiguration _ncsApiConfiguration;
    private readonly HttpClient _httpClient;

    public NCSSearchService(IOptions<NCSapiConfiguration> ncsapiConfiguration, HttpClient httpClient)
    {
        _ncsApiConfiguration = ncsapiConfiguration.Value;
        _httpClient = httpClient;
    }

    public async Task<StarterCompanyDetailsResponse> NCSCompanyRequestsAsync(NCSCompanyRequestsDTO companyRequestsDTO)
    {
        try
        {
            companyRequestsDTO.Products ??= new Products();
            companyRequestsDTO.Products.StarterIQ = true;

            var json = JsonConvert.SerializeObject(companyRequestsDTO);
            var content = new StringContent(json, Encoding.UTF8, "application/json");

            var fullUrl = $"{_ncsApiConfiguration.Instance.TrimEnd('/')}/api/Company/getCompanyDetails";
            Console.WriteLine($"Calling API: {fullUrl}");

            var response = await _httpClient.PostAsync(fullUrl, content);
            response.EnsureSuccessStatusCode();

            var responseJson = await response.Content.ReadAsStringAsync();
            return JsonConvert.DeserializeObject<StarterCompanyDetailsResponse>(responseJson);
        }
        catch (TaskCanceledException ex)
        {
            Console.WriteLine($"Request timed out or canceled: {ex.Message}");
            throw;
        }
    }
}
