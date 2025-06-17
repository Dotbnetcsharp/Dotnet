The code changes have been completed and are ready in our local environment. Currently, we are facing an issue with .NET Core 8.0 in the development environment. Dharani is working on fixing the issue. Once it's resolved and everything works fine, we will push the code to the development environment



MergeSearchId = Guid.TryParse(guid, out var parsedGuid) ? parsedGuid : (Guid?)null,

Matchcorrelationid = Guid.TryParse(matchcorrelationid, out var parsedMatchCorrGuid) ? parsedMatchCorrGuid : (Guid?)null,

ParentSearchId = Guid.TryParse(parentsearchid, out var parsedParentGuid) ? parsedParentGuid : (Guid?)null,
