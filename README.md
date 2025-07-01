using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using MyServiceBusApp.Model;

namespace MyServiceBusApp.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class TrackedStatusController : ControllerBase
    {
        private readonly AppDbContext _context;

        public TrackedStatusController(AppDbContext context)
        {
            _context = context;
        }

        [HttpGet]
        public async Task<IActionResult> GetAll()
        {
            var data = await _context.TrackedStatuses
                .Include(x => x.APNSearch)
                .OrderByDescending(x => x.LastUpdated)
                .ToListAsync();

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

        [HttpGet("{id}")]
        public async Task<IActionResult> GetByCorrelationId(string id)
        {
            var record = await _context.TrackedStatuses
                .Include(x => x.APNSearch)
                .FirstOrDefaultAsync(x => x.CorrelationId == id);

            if (record == null)
                return NotFound();

            var result = new TrackedStatusDto
            {
                CorrelationId = record.CorrelationId,
                Status = record.Status,
                ErrorTitle = record.ErrorTitle,
                ErrorMessage = record.ErrorMessage,
                ErrorData = record.ErrorData,
                CountyFips = record.CountyFips,
                StateCode = record.StateCode,
                SearchType = record.SearchType,
                DateFrom = record.DateFrom,
                DateThru = record.DateThru,
                Branch = record.Branch,
                User = record.User,
                ClientIp = record.ClientIp,
                OrderNo = record.OrderNo,
                SearchedAPN = record.APNSearch?.SearchedAPN
            };

            return Ok(result);
        }
    }
}
