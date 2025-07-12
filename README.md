import React, { useEffect, useState } from "react";
import { useParams } from "react-router-dom";
import { API_BASE_URL } from "../config";

const DetailsAPN: React.FC = () => {
  const { id } = useParams<{ id: string }>();
  const [data, setData] = useState<any>(null);
  const [loading, setLoading] = useState(true);

  const [reportJson, setReportJson] = useState<any>(null);
  const [pdfPath, setPdfPath] = useState<string | null>(null);
  const [reportLoading, setReportLoading] = useState(false);

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

  const handleGetReport = async () => {
    if (!id) return;

    setReportLoading(true);
    setReportJson(null);
    setPdfPath(null);

    try {
      const response = await fetch(`${API_BASE_URL}/api/trackedstatus/reportpath`, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          correlationId: id,
          isReportGenerate: true,
        }),
      });

      const result = await response.json();
      setReportJson(result);

      if (response.ok && result.reportPath && result.reportPath.startsWith("http")) {
        setPdfPath(result.reportPath);
      }
    } catch (err) {
      console.error("Error fetching report:", err);
      setReportJson({ error: "Failed to generate report." });
    } finally {
      setReportLoading(false);
    }
  };

  if (loading) return <p className="p-4">Loading...</p>;
  if (!data) return <p className="text-danger p-4">No data found.</p>;

  return (
    <div className="container mt-4">
      <h2 className="mb-4 main-change">APN Search Details</h2>

      <div className="card shadow-sm mb-4">
        <div className="card-body">
          <div className="row mb-3">
            <div className="col-md-6"><strong>Correlation ID:</strong> {data.correlationId}</div>
            <div className="col-md-6"><strong>Status:</strong> {data.status}</div>
          </div>

          <div className="mt-3">
            <button
              className="btn btn-success"
              onClick={handleGetReport}
              disabled={reportLoading}
            >
              {reportLoading ? "Generating Report..." : "Get Report"}
            </button>
          </div>
        </div>
      </div>

      {reportJson && (
        <div className="mb-4">
          <h5>API Response</h5>
          <div
            style={{
              height: "500px",
              overflowY: "auto",
              background: "#f8f9fa",
              border: "1px solid #ccc",
              padding: "10px",
              fontFamily: "monospace",
              whiteSpace: "pre-wrap",
            }}
          >
            {JSON.stringify(reportJson, null, 2)}
          </div>

          {/* Show PDF button only if path exists */}
          {pdfPath && (
            <div className="mt-3">
              <button
                className="btn btn-primary"
                onClick={() => window.open(pdfPath, "_blank")}
              >
                View PDF
              </button>
            </div>
          )}
        </div>
      )}

      <div className="mt-3">
        <a href="/" className="btn btn-outline-primary">⬅ Back to Dashboard</a>
      </div>
    </div>
  );
};

export default DetailsAPN;
