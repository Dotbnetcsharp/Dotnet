Task<TrackedStatus?> GetDetailedByCorrelationIdAsync(string correlationId);

public async Task<TrackedStatus?> GetDetailedByCorrelationIdAsync(string correlationId)
{
    return await _context.TrackedStatuses
        .Include(x => x.APNSearch)
        .Include(x => x.Address)
        .Include(x => x.Owner)
        .FirstOrDefaultAsync(x => x.CorrelationId == correlationId);
}


[HttpGet("{id}/details")]
public async Task<IActionResult> GetDetails(string id)
{
    var entity = await _repository.GetDetailedByCorrelationIdAsync(id);
    if (entity == null) return NotFound();

    var dto = new TrackedStatusDetailsDto
    {
        CorrelationId = entity.CorrelationId,
        Status = entity.Status,
        Message = entity.Message,
        User = entity.User,
        Branch = entity.Branch,
        APNSearch = entity.APNSearch != null ? new APNSearchDto
        {
            SearchedAPN = entity.APNSearch.SearchedAPN,
            AssessorId = entity.APNSearch.AssessorId,
            ParcelNumber = entity.APNSearch.ParcelNumber
        } : null,
        Address = entity.Address != null ? new AddressDto
        {
            Line1 = entity.Address.Line1,
            City = entity.Address.City,
            State = entity.Address.State,
            ZipCode = entity.Address.ZipCode
        } : null,
        Owner = entity.Owner != null ? new OwnerDto
        {
            FullName = entity.Owner.FullName,
            Phone = entity.Owner.Phone,
            Email = entity.Owner.Email
        } : null
    };

    return Ok(dto);
}


builder.Services.AddScoped<ITrackedStatusRepository, TrackedStatusRepository>();

