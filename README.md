return new AddressDocumentsResponseDto
{
    Addresses = !string.IsNullOrWhiteSpace(addressesMapped?.AddressLine) 
        ? addressesMapped 
        : null,

    StarterPropertys = string.IsNullOrWhiteSpace(addressesMapped?.AddressLine) 
        ? starterPropertys 
        : null,

    Status = itemResponse.Status,
    SearchCriteria = sc.ToList(),
    RunHistory = runhistories,

    PlantCurrency = new PlantCurrency
    {
        PlantFromDate = countyDetails.Any() ? countyc : null,
        PlantThruDate = countyDetails.Any() ? countyd : null,
        PlantThruInst = countyDetails.Any() ? countydInst : null
    }
};
