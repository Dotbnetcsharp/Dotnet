
return new AddressDocumentsResponseDto
{
    Addresses = addressesMapped != null && addressesMapped.Any() ? addressesMapped : null,
    StarterPropertys = (addressesMapped == null || !addressesMapped.Any()) ? apnsMapped : null,

    Status = itemResponse.Status,
    SearchCriteria = sc.ToList(),
    RunHistory = runhistories,

    PlantCurrency = new PlantCurrency
    {
        PlantFromDate = countyDetails.Any() ? countyDetails.First()?.CountyToDate : null,
        PlantThruDate = countyDetails.Any() ? countyDetails.First()?.CountyToDate : null,
        PlantThruInst = countyDetails.Any() ? countyDetails.First()?.CountyInstrumentThru : null,
        PlantThruInstDocumentType = countyDetails.Any() ? countyDetails.First()?.CountyInstrumentThruDocumentType : null,
    },

    Qualifiers = new ResponseQualifiersDTO
    {
        // Fill if required
    }
};
