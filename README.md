Add-Migration InitialAppMigration -Context AppDbContext -OutputDir Migrations\App
Add-Migration InitialLoggingMigration -Context LoggingDbContext -OutputDir Migrations\Logging
Update-Database -Context AppDbContext
Update-Database -Context LoggingDbContext

