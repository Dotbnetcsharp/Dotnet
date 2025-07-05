export default  interface TrackedMessage {
  message?: string;
  correlationId: string;
  status?: string;
  user?: string;
  branch?: string;
  dateFrom?: string;
  searchType?: string; 
}
export interface TrackedStatusDetails {
  correlationId: string;
  status: string;
  message: string;
  user?: string;
  branch?: string;
  dateFrom?: string;
  searchType?: string;

  apns?: APNSearch[];
  owners?: Owner[];
  addresses?: Address[];
}
// Interfaces.ts

export interface APNSearch {
  id: number;
  searchedAPN: string;
  searchDate?: string;
  county?: string;
  state?: string;
  // add only what's needed in UI
}

export interface Owner {
  id: number;
  name: string;
  type?: string;
  phone?: string;
  email?: string;
  // optional fields if needed
}

export interface Address {
  id: number;
  street: string;
  city?: string;
  zip?: string;
  state?: string;
  // optional fields as needed
}
