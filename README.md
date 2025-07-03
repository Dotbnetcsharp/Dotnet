
Message = result.Item1.Count() > 0 
    ? null 
    : "No property match found for " + 
        (itemResponse.SearchType == "STARTER_PROPERTY" 
            ? itemResponse.StarterProperty?.Address 
            : itemResponse.AddressSearch?.SearchedAddress) 
        + " search parameter.",
