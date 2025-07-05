import React, { useEffect, useState } from "react";
import { useParams } from "react-router-dom";
import { API_BASE_URL } from "../config";

const DetailsAPN: React.FC = () => {
  const { id } = useParams<{ id: string }>();
  const [data, setData] = useState<any>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    if (!id) return;

    const fetchData = async () => {
      try {
        const response = await fetch(`${API_BASE_URL}/api/trackedstatus/${id}`);
        const json = await response.json();
        setData(json);
      } catch (err) {
        console.error("Failed to fetch APN details", err);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [id]);

  if (loading) return <p className="p-4">Loading...</p>;
  if (!data) return <p className="text-danger p-4">No data found.</p>;

  return (
    <div className="container mt-4">
      <h2 className="mb-4">APN Search Details</h2>

      <div className="card shadow-sm">
        <div className="card-body">
          <div className="row mb-3">
            <div className="col-md-6"><strong>Correlation ID:</strong> {data.correlationId}</div>
            <div className="col-md-6"><strong>Status:</strong> {data.status}</div>
          </div>
          <div className="row mb-3">
            <div className="col-md-6"><strong>User:</strong> {data.user ?? "-"}</div>
            <div className="col-md-6"><strong>Branch:</strong> {data.branch ?? "-"}</div>
          </div>
          <div className="row mb-3">
            <div className="col-md-6"><strong>Search Type:</strong> {data.searchType ?? "-"}</div>
            <div className="col-md-6"><strong>County FIPS:</strong> {data.countyFips ?? "-"}</div>
          </div>
          <div className="row mb-3">
            <div className="col-md-6"><strong>Error Title:</strong> {data.errorTitle ?? "-"}</div>
            <div className="col-md-6"><strong>Error Message:</strong> {data.errorMessage ?? "-"}</div>
          </div>
          <div className="row mb-3">
            <div className="col-md-6"><strong>Date From:</strong> {data.dateFrom ? new Date(data.dateFrom).toLocaleDateString() : "-"}</div>
            <div className="col-md-6"><strong>Date Thru:</strong> {data.dateThru ? new Date(data.dateThru).toLocaleDateString() : "-"}</div>
          </div>
          <div className="row mb-3">
            <div className="col-md-12">
              <strong>APNs:</strong>{" "}
              {data.apns?.length ? data.apns.join(", ") : <span className="text-muted">None</span>}
            </div>
          </div>
        </div>
      </div>

      <div className="mt-4">
        <a href="/" className="btn btn-outline-primary">⬅ Back to Dashboard</a>
      </div>
    </div>
  );
};

export default DetailsAPN;
