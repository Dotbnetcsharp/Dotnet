const statusCounts = React.useMemo(() => {
  return messages.reduce(
    (acc, msg) => {
      const status = msg.status?.toLowerCase();
      if (status === "completed") acc.completed += 1;
      else if (status === "inprogress") acc.inProgress += 1;
      else if (status === "scheduled") acc.scheduled += 1;
      else if (status === "error") acc.error += 1;
      return acc;
    },
    { completed: 0, inProgress: 0, scheduled: 0, error: 0 }
  );
}, [messages]);



<div className="row stat-cards">
  <div className="col-md-6 col-xl-3">
    <article
      className={`stat-cards-item ${filterStatus.toLowerCase() === "completed" ? "active" : ""}`}
      onClick={() =>
        setFilterStatus((prev) => (prev.toLowerCase() === "completed" ? "All" : "Completed"))
      }
      style={{ cursor: "pointer" }}
    >
      <div className="stat-cards-icon success">
        <i data-feather="check-circle"></i>
      </div>
      <div className="stat-cards-info">
        <p className="stat-cards-info__num">{statusCounts.completed}</p>
        <p className="stat-cards-info__title">Completed</p>
      </div>
    </article>
  </div>

  <div className="col-md-6 col-xl-3">
    <article
      className={`stat-cards-item ${filterStatus.toLowerCase() === "inprogress" ? "active" : ""}`}
      onClick={() =>
        setFilterStatus((prev) => (prev.toLowerCase() === "inprogress" ? "All" : "InProgress"))
      }
      style={{ cursor: "pointer" }}
    >
      <div className="stat-cards-icon warning">
        <i data-feather="loader"></i>
      </div>
      <div className="stat-cards-info">
        <p className="stat-cards-info__num">{statusCounts.inProgress}</p>
        <p className="stat-cards-info__title">In Progress</p>
      </div>
    </article>
  </div>

  <div className="col-md-6 col-xl-3">
    <article
      className={`stat-cards-item ${filterStatus.toLowerCase() === "scheduled" ? "active" : ""}`}
      onClick={() =>
        setFilterStatus((prev) => (prev.toLowerCase() === "scheduled" ? "All" : "Scheduled"))
      }
      style={{ cursor: "pointer" }}
    >
      <div className="stat-cards-icon info">
        <i data-feather="clock"></i>
      </div>
      <div className="stat-cards-info">
        <p className="stat-cards-info__num">{statusCounts.scheduled}</p>
        <p className="stat-cards-info__title">Scheduled</p>
      </div>
    </article>
  </div>

  <div className="col-md-6 col-xl-3">
    <article
      className={`stat-cards-item ${filterStatus.toLowerCase() === "error" ? "active" : ""}`}
      onClick={() =>
        setFilterStatus((prev) => (prev.toLowerCase() === "error" ? "All" : "Error"))
      }
      style={{ cursor: "pointer" }}
    >
      <div className="stat-cards-icon danger">
        <i data-feather="alert-circle"></i>
      </div>
      <div className="stat-cards-info">
        <p className="stat-cards-info__num">{statusCounts.error}</p>
        <p className="stat-cards-info__title">Errors</p>
      </div>
    </article>
  </div>
</div>
