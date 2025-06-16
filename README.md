MergeSearchId = Guid.TryParse(guid, out var parsedGuid) ? parsedGuid : (Guid?)null,

Matchcorrelationid = Guid.TryParse(matchcorrelationid, out var parsedMatchCorrGuid) ? parsedMatchCorrGuid : (Guid?)null,

ParentSearchId = Guid.TryParse(parentsearchid, out var parsedParentGuid) ? parsedParentGuid : (Guid?)null,
