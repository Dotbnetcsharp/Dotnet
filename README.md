<div className="d-flex align-items-start justify-content-between flex-wrap gap-2">

  {/* 🔔 Notification Button */}
  <button
    className="gray-circle-btn dropdown-btn"
    title="To messages"
    type="button"
    style={{ background: "red" }}
    onClick={toggleNotification}
  >
    <span className="sr-only">To messages</span>
    <span className="icon notification active" aria-hidden="true" />
  </button>

  {/* 🧭 Refresh Button */}
  <button type="button" className="btn btn-primary">
    <RefreshCw size={20} />
  </button>

  {/* 📅 From Date Picker */}
  <div className="d-flex align-items-center">
    <DatePickerComponent>
      <span
        className="bg-success text-white px-3 py-2 rounded"
        style={{ marginTop: "-5px" }}
      >
        <Calendar1Icon size={24} style={{ marginRight: 5 }} />
        From Date
      </span>
    </DatePickerComponent>
  </div>

  {/* 📅 To Date Picker */}
  <div className="d-flex align-items-center">
    <DatePickerComponent>
      <span
        className="bg-success text-white px-3 py-2 rounded"
        style={{ marginTop: "-5px" }}
      >
        <Calendar1Icon size={24} style={{ marginRight: 5 }} />
        To Date
      </span>
    </DatePickerComponent>
  </div>

  {/* 🔍 Search Bar */}
  <div className="d-flex align-items-center">
    <i data-feather="search" aria-hidden="true"></i>
    <input
      className="form-control"
      type="text"
      placeholder="Search..."
      required
      style={{ width: "200px", marginLeft: "8px" }}
    />
  </div>
</div>
