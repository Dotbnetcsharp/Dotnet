InstrumentSearch = entity.InstrumentSearch != null
    ? new InstrumentSearchDTO
    {
        // 1. The GUID key
        SearchItemId     = entity.InstrumentSearch.Id,

        // 2. Link back to the parent TrackedStatus if you want it
        ParentSearchItem = entity,  

        // 3. Project BooksPages → InstrumentBooksPagesDTO
        BooksPages       = entity.InstrumentSearch.BooksPages == null
            ? null
            : entity.InstrumentSearch.BooksPages
                .Select(bp => new InstrumentBooksPagesDTO
                {
                    // avoid infinite loop by not setting InstrumentItem, or set to null
                    InstrumentItem = null,  
                    Book           = bp.Book,
                    Page           = bp.Page
                })
                .ToList(),

        // 4. Project DocumentNumbers → InstrumentDocumentNumbersDTO
        DocumentNumbers  = entity.InstrumentSearch.DocumentNumbers == null
            ? null
            : entity.InstrumentSearch.DocumentNumbers
                .Select(dn => new InstrumentDocumentNumbersDTO
                {
                    InstrumentItem = null,  // same here
                    Number         = dn.Number,
                    Type           = dn.Type
                })
                .ToList()
    }
    : null,
