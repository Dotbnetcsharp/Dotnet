var StarterPropertySearch = new StarterPropertySearchEntity
{
    SearchItemId = correlationGuid,
    BypassIndex = false, // set true if needed
    APN = result.StarterPropertySearch.APN,
    PropertyId = result.StarterPropertySearch.PropertyId,
    Address = result.StarterPropertySearch.Address,
    StreetNumber = result.StarterPropertySearch.StreetNumber,
    StreetName = result.StarterPropertySearch.StreetName,
    PreDirection = result.StarterPropertySearch.PreDirection,
    StreetType = result.StarterPropertySearch.StreetType,
    PostDirection = result.StarterPropertySearch.PostDirection,
    UnitNumber = result.StarterPropertySearch.UnitNumber,
    OwnerName = result.StarterPropertySearch.OwnerName,
    StarterSource = result.StarterPropertySearch.StarterSource,
    RecommendedStarter = result.StarterPropertySearch.RecommendedStarter,
    StarterTitleCompany = result.StarterPropertySearch.StarterTitleCompany,
    RadiusForNeighboringStarters = result.StarterPropertySearch.RadiusForNeighboringStarters,
    Subvidision = result.StarterPropertySearch.Subvidision,
    MinAmount = result.StarterPropertySearch.MinAmount,
    MaxAmount = result.StarterPropertySearch.MaxAmount,
    RecommendedAPStarter = result.StarterPropertySearch.RecommendedAPStarter,
    PolicyTypeOwner = result.StarterPropertySearch.PolicyTypeOwner,
    PolicyTypeLender = result.StarterPropertySearch.PolicyTypeLender,
    PolicyTypeCommitment = result.StarterPropertySearch.PolicyTypeCommitment
};
