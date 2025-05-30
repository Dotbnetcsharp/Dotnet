{
  "fromDate": "2024-05-01T00:00:00",
  "toDate": "2024-05-28T23:59:59"
}
using ReportGenerator.Api.Services;

var builder = WebApplication.CreateBuilder(args);

// Add services
builder.Services.AddControllers();
builder.Services.AddSignalR();
builder.Services.AddSingleton<ReportQueueSender>();
builder.Services.AddHostedService<ReportQueueProcessor>();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();
app.MapHub<ReportHub>("/reportHub");
app.UseSwagger();
app.UseSwaggerUI();

app.Run();
