builder.Services.Configure<ConnectionStrings>(
    builder.Configuration.GetSection("ConnectionStrings"));

// AppDbContext - your local DB (DefaultConnection)
builder.Services.AddDbContext<AppDbContext>((sp, options) =>
{
    var config = sp.GetRequiredService<IOptions<ConnectionStrings>>();
    options.UseSqlServer(config.Value.DefaultConnection);
});

// NTPDbContext - your external DB (NTPDatabase)
builder.Services.AddDbContext<NTPDbContext>((sp, options) =>
{
    var config = sp.GetRequiredService<IOptions<ConnectionStrings>>();
    options.UseSqlServer(config.Value.NTPDatabase);
});
