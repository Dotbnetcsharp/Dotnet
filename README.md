import React, { useEffect, useState } from "react";
import "bootstrap/dist/css/bootstrap.min.css";

interface SavedRequest {
  name: string;
  url: string;
  method: string;
  body: string;
  headers: { key: string; value: string }[];
}

const ApiTester: React.FC = () => {
  const [method, setMethod] = useState("GET");
  const [url, setUrl] = useState("");
  const [token, setToken] = useState("");
  const [body, setBody] = useState("{}");
  const [headers, setHeaders] = useState([{ key: "", value: "" }]);

  const [status, setStatus] = useState("");
  const [loading, setLoading] = useState(false);
  const [response, setResponse] = useState<any>(null);
  const [pdfUrl, setPdfUrl] = useState<string | null>(null);

  const [savedRequests, setSavedRequests] = useState<SavedRequest[]>([]);
  const [showModal, setShowModal] = useState(false);
  const [newReqName, setNewReqName] = useState("");

  // Load saved requests from localStorage
  useEffect(() => {
    const saved = JSON.parse(localStorage.getItem("apiSavedRequests") || "[]");
    setSavedRequests(saved);
  }, []);

  const getStatusColor = () => {
    if (loading) return "orange";
    if (status.startsWith("2")) return "green";
    if (status) return "red";
    return "gray";
  };

  const handleSendRequest = async () => {
    setLoading(true);
    setStatus("Loading...");
    setResponse(null);
    setPdfUrl(null);

    try {
      const fetchHeaders: any = {
        "Content-Type": "application/json",
      };

      if (token) {
        fetchHeaders["Authorization"] = `Bearer ${token}`;
      }

      headers.forEach((h) => {
        if (h.key.trim()) {
          fetchHeaders[h.key] = h.value;
        }
      });

      const res = await fetch(url, {
        method,
        headers: fetchHeaders,
        body: method !== "GET" ? body : undefined,
      });

      const contentType = res.headers.get("content-type") || "";
      const resText = await res.text();

      setStatus(`${res.status} ${res.statusText}`);

      if (contentType.includes("application/pdf")) {
        const blob = new Blob([resText], { type: "application/pdf" });
        const pdf = URL.createObjectURL(blob);
        setPdfUrl(pdf);
      } else if (contentType.includes("application/json")) {
        setResponse(JSON.parse(resText));
      } else {
        setResponse({ raw: resText });
      }
    } catch (err: any) {
      setStatus("Request Failed");
      setResponse({ error: err.message });
    } finally {
      setLoading(false);
    }
  };

  const handleAddHeader = () => {
    setHeaders([...headers, { key: "", value: "" }]);
  };

  const handleChangeHeader = (index: number, field: string, value: string) => {
    const updated = [...headers];
    updated[index][field as "key" | "value"] = value;
    setHeaders(updated);
  };

  const handleSaveRequest = () => {
    const newReq: SavedRequest = {
      name: newReqName,
      url,
      method,
      body,
      headers,
    };
    const updated = [...savedRequests, newReq];
    setSavedRequests(updated);
    localStorage.setItem("apiSavedRequests", JSON.stringify(updated));
    setShowModal(false);
    setNewReqName("");
  };

  const handleLoadSavedRequest = (req: SavedRequest) => {
    setUrl(req.url);
    setMethod(req.method);
    setBody(req.body);
    setHeaders(req.headers.length ? req.headers : [{ key: "", value: "" }]);
  };

  return (
    <div className="container-fluid">
      <div className="row">
        {/* Sidebar */}
        <div className="col-md-3 border-end" style={{ height: "100vh", overflowY: "auto" }}>
          <div className="p-3">
            <h5>📂 Saved Requests</h5>
            {savedRequests.map((req, i) => (
              <button
                key={i}
                className="btn btn-sm btn-outline-secondary w-100 text-start mb-2"
                onClick={() => handleLoadSavedRequest(req)}
              >
                {req.name}
              </button>
            ))}
            <button className="btn btn-primary w-100 mt-3" onClick={() => setShowModal(true)}>
              ➕ New Request
            </button>
          </div>
        </div>

        {/* Main Panel */}
        <div className="col-md-9 p-4">
          <h3>🧪 Global API Tester</h3>

          {/* URL + Method */}
          <div className="row mb-3">
            <div className="col-md-2">
              <select className="form-select" value={method} onChange={(e) => setMethod(e.target.value)}>
                <option>GET</option>
                <option>POST</option>
                <option>PUT</option>
                <option>DELETE</option>
              </select>
            </div>
            <div className="col-md-10">
              <input
                type="text"
                className="form-control"
                placeholder="Request URL"
                value={url}
                onChange={(e) => setUrl(e.target.value)}
              />
            </div>
          </div>

          {/* Token */}
          <div className="mb-3">
            <label>🔐 Token (Bearer):</label>
            <input
              type="text"
              className="form-control"
              value={token}
              onChange={(e) => setToken(e.target.value)}
            />
          </div>

          {/* Headers */}
          <div className="mb-3">
            <label>📋 Headers:</label>
            {headers.map((h, i) => (
              <div className="row mb-2" key={i}>
                <div className="col">
                  <input
                    type="text"
                    placeholder="Key"
                    className="form-control"
                    value={h.key}
                    onChange={(e) => handleChangeHeader(i, "key", e.target.value)}
                  />
                </div>
                <div className="col">
                  <input
                    type="text"
                    placeholder="Value"
                    className="form-control"
                    value={h.value}
                    onChange={(e) => handleChangeHeader(i, "value", e.target.value)}
                  />
                </div>
              </div>
            ))}
            <button className="btn btn-sm btn-outline-secondary" onClick={handleAddHeader}>
              ➕ Add Header
            </button>
          </div>

          {/* Body */}
          {method !== "GET" && (
            <div className="mb-3">
              <label>📝 Body:</label>
              <textarea
                className="form-control"
                rows={6}
                value={body}
                onChange={(e) => setBody(e.target.value)}
              />
            </div>
          )}

          {/* Send Button */}
          <button className="btn btn-success me-2" onClick={handleSendRequest}>
            🚀 Send Request
          </button>

          <span className="ms-2">
            <strong>Status:</strong>{" "}
            <span style={{ color: getStatusColor() }}>{status || "Idle"}</span>
          </span>

          {/* Response Viewer */}
          {response && (
            <div className="mt-4">
              <h5>📦 Response:</h5>
              <pre className="bg-light p-3 border rounded" style={{ whiteSpace: "pre-wrap" }}>
                {JSON.stringify(response, null, 2)}
              </pre>
            </div>
          )}

          {/* PDF Viewer */}
          {pdfUrl && (
            <div className="mt-4">
              <h5>📄 PDF Preview:</h5>
              <iframe src={pdfUrl} width="100%" height="600px" title="PDF Viewer" />
            </div>
          )}
        </div>
      </div>

      {/* Save Modal */}
      {showModal && (
        <div
          className="modal d-block"
          tabIndex={-1}
          style={{ backgroundColor: "rgba(0,0,0,0.5)" }}
        >
          <div className="modal-dialog">
            <div className="modal-content">
              <div className="modal-header">
                <h5 className="modal-title">Save New Request</h5>
                <button type="button" className="btn-close" onClick={() => setShowModal(false)}></button>
              </div>
              <div className="modal-body">
                <div className="mb-2">
                  <label>Request Name:</label>
                  <input
                    className="form-control"
                    placeholder="My Sample Request"
                    value={newReqName}
                    onChange={(e) => setNewReqName(e.target.value)}
                  />
                </div>
                <div className="mb-2">
                  <label>Request URL:</label>
                  <input
                    className="form-control"
                    value={url}
                    onChange={(e) => setUrl(e.target.value)}
                  />
                </div>
                <div className="mb-2">
                  <label>Method:</label>
                  <select
                    className="form-control"
                    value={method}
                    onChange={(e) => setMethod(e.target.value)}
                  >
                    <option>GET</option>
                    <option>POST</option>
                    <option>PUT</option>
                    <option>DELETE</option>
                  </select>
                </div>
              </div>
              <div className="modal-footer">
                <button className="btn btn-secondary" onClick={() => setShowModal(false)}>
                  Cancel
                </button>
                <button className="btn btn-primary" onClick={handleSaveRequest}>
                  Save
                </button>
              </div>
            </div>
          </div>
        </div>
      )}
    </div>
  );
};

export default ApiTester;
