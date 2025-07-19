BooksPages = entity.InstrumentSearch.BooksPages?.Select(x => new InstrumentBookPagesDTO
{
    PageNumber = x.PageNumber,
    BookNumber = x.BookNumber
}).ToList()
