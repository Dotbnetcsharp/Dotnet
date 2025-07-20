import { createApi, fetchBaseQuery } from "@reduxjs/toolkit/query/react";
import { API_URL2 } from "../config";

export const reportApi = createApi({
  reducerPath: "reportApi",
  baseQuery: fetchBaseQuery({
    baseUrl: API_URL2,
    prepareHeaders: (headers) => {
      const token = sessionStorage.getItem("jwtToken");
      if (token) {
        headers.set("Authorization", `Bearer ${token}`);
      }
      return headers;
    },
  }),
  endpoints: (builder) => ({
    getReportPath: builder.mutation<any, { correlationId: string; isReportGenerate: boolean }>({
      query: (body) => ({
        url: "/api/trackedstatus/reportpath",
        method: "POST",
        body,
      }),
    }),
  }),
});

export const { useGetReportPathMutation } = reportApi;
