using Microsoft.AspNetCore.Mvc;
using MyServiceBusApp.Model;
using MyServiceBusApp.DTOs;
using MyServiceBusApp.Repositories;

namespace MyServiceBusApp.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class TrackedStatusController : ControllerBase
    {
        private readonly ITrackedStatusRepository _repository;

        public TrackedStatusController(ITrackedStatusRepository repository)
        {
            _repository = repository;
        }

        // ✅ Basic table list (lightweight)
        [HttpGet]
        public async Task<IActionResult> GetAll()
        {
            var data = await _repository.GetAllAsync();

            var result = data.Select(x => new TrackedStatusDto
            {
                CorrelationId = x.CorrelationId,
                Status = x.Status,
                ErrorTitle = x.ErrorTitle,
                ErrorMessage = x.ErrorMessage,
                ErrorData = x.ErrorData,
                CountyFips = x.CountyFips,
                StateCode = x.StateCode,
                SearchType = x.SearchType,
                DateFrom = x.DateFrom,
                DateThru = x.DateThru,
                Branch = x.Branch,
                User = x.User,
                ClientIp = x.ClientIp,
                OrderNo = x.OrderNo,
                SearchedAPN = x.APNSearch?.SearchedAPN
            });

            return Ok(result);
        }

        // ✅ Full detail view with nested children
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
    }
}
