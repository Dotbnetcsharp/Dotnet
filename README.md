export interface TrackedMessage {
  correlationId: string;
  message?: string;
  status?: string;
  errorTitle?: string;
  errorMessage?: string;
  errorData?: string;
  countyFips?: string;
  stateCode?: string;
  searchType?: string;
  dateFrom?: string;
  dateThru?: string;
  branch?: string;
  user?: string;
  clientIp?: string;
  orderNo?: string;
  lastUpdated?: string;
  apns?: string[];

  apnSearch?: {
    searchedAPN: string;
    [key: string]: any;
  };
  address?: {
    street?: string;
    city?: string;
    state?: string;
    zip?: string;
    [key: string]: any;
  };
  owner?: {
    name?: string;
    contact?: string;
    [key: string]: any;
  };
}

setSelectedIds((prev) => (prev.length === messages.length ? [] : messages.map((m) => m.correlationId)));

const response = await fetch(`${API_BASE_URL}/api/trackedstatus/${data.correlationId}/details`);

