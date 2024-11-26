---

Currently, our API returns up to 6,000 results (APN, Address, and Owner). We need to increase this limit to 10,000.

To achieve this, we are adding a new property in the JSON request called documentsCount.

The user can specify any value between 1 and 10,000.

For example, if the user sets documentsCount to 10, the API will return only 10 results.


Questions:

1. If the user does not provide a value for documentsCount, what should be the default?

Should it remain at the current limit of 50, even though this does not match our existing logic of returning up to 6,000 results?



2. Currently, the payload is capped at 50, but we have 6,000 results. How should this be handled to align with the updated logic?
