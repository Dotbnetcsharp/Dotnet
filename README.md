Add-Migration InitialAppMigration -Context AppDbContext -OutputDir Migrations\App
Add-Migration InitialLoggingMigration -Context LoggingDbContext -OutputDir Migrations\Logging
Update-Database -Context AppDbContext
Update-Database -Context LoggingDbContext

dotnet ef migrations add InitialAppMigration --context AppDbContext --output-dir Migrations/App
dotnet ef migrations add InitialLoggingMigration --context LoggingDbContext --output-dir Migrations/Logging

