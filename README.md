"AzureStaterDocumentSearchIndex": {
  "DocsURL": "indexes/idxnsxinsureddocno20250522",
  "ApiMethod": "GET",
  "SearchMode": "&searchMode=all",
  "QueryType": "&queryType=full",
  "Search": "&search=#DocumentNumber#",
  "SearchFields": "&searchFields=DocumentNumber",
  "Filter": "&$filter=Fips eq '#COUNTYFIPS#' and ((search.in(PolicyType, 'owner,lender') and PolicyAmount ge #MINAMOUNT# and PolicyAmount le #MAXAMOUNT# and PolicyDate ge #DATEFROM# and PolicyDate le #DATETHRU#) or FilterFlag eq 0)",
  "OrderBy": "&$orderby=DocumentNumber",
  "Count": "&$count=true",
  "TopResultsToReturn": "&$top=300"
}
