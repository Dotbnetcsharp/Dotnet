{reportJson && (
  <div className="mb-4 position-relative">
    <h5>API Response</h5>

    {/* Clipboard Icon with Bootstrap tooltip */}
    <i
      className="bi bi-clipboard fs-5 text-primary position-absolute"
      style={{ top: '45px', right: '15px', cursor: 'pointer', zIndex: 10 }}
      title="Copy to clipboard"
      data-bs-toggle="tooltip"
      onClick={() =>
        navigator.clipboard.writeText(JSON.stringify(reportJson, null, 2))
      }
    ></i>

    {/* JSON Output Box */}
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
  </div>
)}
