[05/07, 12:03] pavani: SELECT name, sid, type_desc 
FROM sys.server_principals 
WHERE name LIKE '%jpavani%';
[05/07, 12:03] pavani: USE [YourDatabaseName];
SELECT name, sid, type_desc 
FROM sys.database_principals 
WHERE name LIKE '%jpavani%';
