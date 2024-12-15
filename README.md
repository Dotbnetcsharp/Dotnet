var apiResultCount = apiResult.Count();

int indexCountValue = indexCount.HasValue ? indexCount.Value : appResultsLimit.APTypeAheadSearchResultLimit;

if (apiResultCount == indexCountValue)
{
    throw new BadRequestException("EC-02", "APN Type Ahead", 
        $"Your search has exceeded {indexCountValue} results.");
}
