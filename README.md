1. Accept input from the frontend – a document number (idxnsxinsureddocno).


2. Search this document number in the Azure index (typeahead search).


3. Get the matching result from Azure.


4. Check what kind of document it is:

If it's linked to a property (DTDB) → Use property search logic.

If it's linked to a starter (loan doc or bp) → Use starter search logic.



5. Return the result back to the frontend based on the correct logic.

