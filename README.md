USE [ntpdatamart];
GO

CREATE USER [INTIL\jpavani] FOR LOGIN [INTIL\jpavani];
ALTER ROLE db_datareader ADD MEMBER [INTIL\jpavani];
ALTER ROLE db_datawriter ADD MEMBER [INTIL\jpavani];
