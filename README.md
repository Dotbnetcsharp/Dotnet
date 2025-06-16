var legalInfo = StarterPropertySearchRequestDto.StarterLegalLibrary?.FirstOrDefault();

StarterProperty = new StarterPropertyEntity
{
    Address = StarterPropertySearchRequestDto.Address,
    StreetNumber = StarterPropertySearchRequestDto.StreetNumber,
    StreetName = StarterPropertySearchRequestDto.StreetName,
    StreetType = StarterPropertySearchRequestDto.StreetType,
    PreDirection = StarterPropertySearchRequestDto.PreDirection,
    PostDirection = StarterPropertySearchRequestDto.PostDirection,
    UnitNumber = StarterPropertySearchRequestDto.UnitNumber,
    OwnerName = StarterPropertySearchRequestDto.OwnerName,
    LegalId = StarterPropertySearchRequestDto.LegalId,
    BypassIndex = StarterPropertySearchRequestDto.BypassIndex,
    ProductCodeDescription = StarterPropertySearchRequestDto.ProductCodeDescription,

    // StarterLegalLibrary safe access
    Block = legalInfo?.Block,
    Lot = legalInfo?.Lot,
    MapBook = legalInfo?.MapBook,
    MapPage = legalInfo?.MapPage,
    Section = legalInfo?.Section,
    Township = legalInfo?.Township,
    Range = legalInfo?.Range,
    Quarter = legalInfo?.Quarter,
    Half = legalInfo?.Half,
    MiningClaim = legalInfo?.MiningClaim,
    SubParcel = legalInfo?.SubParcel,
    CommonLot = legalInfo?.CommonLot,
    Acreage = legalInfo?.Acreage
};
