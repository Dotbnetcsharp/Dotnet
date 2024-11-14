Developer Note: This is not a bug but expected behavior in the addressTypeAhead API. The entered address has multiple associated records with different APNs (Assessor's Parcel Numbers) and property IDs, resulting in multiple suggestions.

Developer Note: The issue arises because the address entered has multiple associated records with different APNs (Assessor's Parcel Numbers) and property IDs. As a result, the API returns all matching suggestions instead of an exact match. Consider implementing logic to prioritize or filter for exact matches when multiple records share similar address details.


