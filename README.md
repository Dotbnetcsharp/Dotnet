public async Task<StarterCompanyDetailsResponse> GetCompanyDetails(NCSCompanyRequestDTO companyRequestDTO)
{
    try
    {
        companyRequestDTO.Products ??= new Products();

        // Convert object to JSON
        var json = JsonConvert.SerializeObject(companyRequestDTO);
        var content = new StringContent(json, Encoding.UTF8, "application/json");

        // Build URL (same as before)
        var fullURL = $"{_ncsapiconfiguration.Value.BaseUrl}/starter-company/details";
        Console.WriteLine($"[INFO] Calling API: {fullURL}");

        // --- FIX START ---
        // Prevent "TaskCanceledException" on second call
        _httpClient.DefaultRequestHeaders.ConnectionClose = true; 
        _httpClient.Timeout = TimeSpan.FromMinutes(2);            // increase timeout
        _httpClient.DefaultRequestHeaders.ExpectContinue = false; // avoid slow handshake

        using var cts = new CancellationTokenSource(TimeSpan.FromMinutes(2)); // custom timeout
        // --- FIX END ---

        // Make API call
        var response = await _httpClient.PostAsync(fullURL, content, cts.Token);
        Console.WriteLine($"[INFO] API responded with status: {response.StatusCode}");

        response.EnsureSuccessStatusCode();

        var responseJson = await response.Content.ReadAsStringAsync();

        var responseObject = JsonConvert.DeserializeObject<StarterCompanyDetailsResponse>(responseJson);

        return responseObject ?? new StarterCompanyDetailsResponse();
    }
    catch (TaskCanceledException ex)
    {
        Console.WriteLine($"[ERROR] Request timed out or canceled: {ex.Message}");
        throw new Exception("The API request was canceled or took too long. Check timeout or server performance.", ex);
    }
    catch (HttpRequestException ex)
    {
        Console.WriteLine($"[ERROR] HTTP request failed: {ex.Message}");
        throw new Exception("An HTTP error occurred while calling the API.", ex);
    }
    catch (Exception ex)
    {
        Console.WriteLine($"[ERROR] Unexpected error in GetCompanyDetails: {ex.Message}");
        throw;
    }
}
