import React, { useEffect, useState } from "react";
import * as signalR from "@microsoft/signalr";
import { Bell, RefreshCw, Search } from "lucide-react";
import { TrackedMessage } from "../Interfaces";
import { API_BASE_URL } from "../config";
import { useGetTrackedStatusesQuery } from "../Apis/messageApi";
import { getStatusBadge } from "../Utility/statusBadgeHelper";
import { useNavigate } from "react-router-dom";
import DatePicker from "react-datepicker";
import toast from "react-hot-toast";
import "react-datepicker/dist/react-datepicker.css";
 
const MainContent: React.FC = () => {
  const { data: initialData = [], isLoading, error, refetch } = useGetTrackedStatusesQuery();
  const [messages, setMessages] = useState<TrackedMessage[]>([]);
  const [selectedIds, setSelectedIds] = useState<string[]>([]);
  const [currentPage, setCurrentPage] = useState(1);
  const itemsPerPage = 10;
  const [sortBy, setSortBy] = useState<keyof TrackedMessage | "">("");
  const [sortOrder, setSortOrder] = useState<"asc" | "desc">("asc");
  const [searchQuery, setSearchQuery] = useState("");
  const [filterStatus, setFilterStatus] = useState<string>("All");
  const [fromDate, setFromDate] = useState<Date | null>(null);
  const [toDate, setToDate] = useState<Date | null>(null);
  const navigate = useNavigate();
 
  useEffect(() => {
    setMessages(initialData);
  }, [initialData]);
 
  useEffect(() => {
    const connection = new signalR.HubConnectionBuilder()
      .withUrl(`${API_BASE_URL}/messageHub`)
      .withAutomaticReconnect()
      .build();
 
    const updateOrAddMessage = (data: TrackedMessage) => {
      setMessages((prev) => {
        const index = prev.findIndex((m) => m.correlationId === data.correlationId);
        if (index !== -1) {
          const updated = [...prev];
          updated[index] = { ...updated[index], ...data };
 
          // 🔔 Notify if completed
          if (
            data.status?.toLowerCase() === "completed" &&
            selectedIds.includes(data.correlationId)
          ) {
            toast.success(`✅ Request completed: ${data.correlationId}`);
          }
 
          return updated;
        } else {
          return [data, ...prev];
        }
      });
    };
 
    connection.on("ReceiveMessage", updateOrAddMessage);
    connection.on("ReceiveStatusUpdate", updateOrAddMessage);
 
    connection.start().catch((err) => console.error("❌ SignalR error:", err));
 
    return () => {
      connection.stop();
    };
  }, [selectedIds]);
 
  const toggleSelectAll = () => {
    if (selectedIds.length === messages.length) {
      setSelectedIds([]);
    } else {
      setSelectedIds(messages.map((m) => m.correlationId));
    }
  };
 
  const toggleSelectOne = (id: string) => {
    setSelectedIds((prev) =>
      prev.includes(id) ? prev.filter((i) => i !== id) : [...prev, id]
    );
  };
 
  const handleSort = (column: keyof TrackedMessage) => {
    if (sortBy === column) {
      setSortOrder((prev) => (prev === "asc" ? "desc" : "asc"));
    } else {
      setSortBy(column);
      setSortOrder("asc");
    }
  };
 
  const handleRowClick = (msg: TrackedMessage) => {
    const type = msg.message?.toLowerCase();
    if (type === "sas") navigate(`/details/apn/${msg.correlationId}`);
    else if (type === "owner") navigate(`/details/owner/${msg.correlationId}`);
    else if (type === "address") navigate(`/details/address/${msg.correlationId}`);
    else navigate(`/details/${msg.correlationId}`);
  };
 
  const filtered = messages.filter((msg) => {
    const matchStatus =
      filterStatus === "All" || msg.status?.toLowerCase() === filterStatus.toLowerCase();
    const matchSearch =
      msg.message?.toLowerCase().includes(searchQuery.toLowerCase()) ||
      msg.correlationId?.toLowerCase().includes(searchQuery.toLowerCase()) ;
 
    const matchFrom = fromDate ? new Date(msg.dateFrom || "") >= fromDate : true;
    const matchTo = toDate ? new Date(msg.dateFrom || "") <= toDate : true;
 
    return matchStatus && matchSearch && matchFrom && matchTo;
  });
 
  const sorted = [...filtered].sort((a, b) => {
    if (!sortBy) return 0;
    const aValue = a[sortBy] ?? "";
    const bValue = b[sortBy] ?? "";
    return sortOrder === "asc"
      ? String(aValue).localeCompare(String(bValue))
      : String(bValue).localeCompare(String(aValue));
  });
 
  const paginated = sorted.slice((currentPage - 1) * itemsPerPage, currentPage * itemsPerPage);
  const totalPages = Math.ceil(sorted.length / itemsPerPage);
 

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

  return (
    <main className="main users chart-page" id="skip-target">
      <div className="container">
        <h2 className="main-title">Dashboard</h2>
 
       
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


<button
  className="btn btn-outline-secondary"
  onClick={() => {
    setSearchQuery("");
    setFilterStatus("All");
    setCurrentPage(1);
    setFromDate(null);
    setToDate(null);
  }}
>
  Reset Filters
</button>


        {/* Filter Toolbar */}
        <div className="d-flex justify-content-between align-items-center mb-3 flex-wrap gap-2">
          <div className="d-flex gap-2">
            <button className="btn btn-sm btn-outline-primary" onClick={() => refetch()}>
              <RefreshCw size={16} /> Refresh
            </button>
            <button
              className="btn btn-sm btn-outline-success"
              onClick={() => toast("📢 Waiting for status completion...")}
            >
              <Bell size={16} /> Notify
            </button>
          </div>
          <div className="d-flex gap-2 align-items-center">
            <DatePicker
              selected={fromDate}
              onChange={(date) => setFromDate(date)}
              placeholderText="From Date"
              className="form-control form-control-sm"
            />
            <DatePicker
              selected={toDate}
              onChange={(date) => setToDate(date)}
              placeholderText="To Date"
              className="form-control form-control-sm"
            />
            <div className="input-group input-group-sm">
               <div className="search-wrapper">
            <i data-feather="search" aria-hidden="true"></i>
            <input type="text" placeholder="Enter keywords ..." required />
          </div>
              <span className="input-group-text"><Search size={16} /></span>
            </div>
          </div>
        </div>
 
        {/* Table */}
        <div className="users-table table-wrapper">
          <table className="posts-table">
            <thead>
              <tr className="users-table-info">
                <th>
                  <label className="users-table__checkbox ms-20">
                    <input
                      type="checkbox"
                      className="check-all"
                      checked={selectedIds.length === messages.length && messages.length > 0}
                      onChange={toggleSelectAll}
                    /> Select
                  </label>
                </th>
                <th onClick={() => handleSort("searchType")}>searchType</th>
                <th onClick={() => handleSort("correlationId")}>Correlation ID</th>
                <th onClick={() => handleSort("status")}>Status</th>
                <th>User</th>
                <th>Branch</th>
                <th onClick={() => handleSort("dateFrom")}>Date From</th>
                <th>APNs</th>
              </tr>
            </thead>
            <tbody>
              {paginated.map((msg, i) => (
                <tr
                  key={i}
                  className={selectedIds.includes(msg.correlationId) ? "active" : ""}
                  onClick={() => handleRowClick(msg)}
                  style={{ cursor: "pointer" }}
                >
                  <td>
                    <label className="users-table__checkbox">
                      <input
                        type="checkbox"
                        className="check"
                        checked={selectedIds.includes(msg.correlationId)}
                        onClick={(e) => e.stopPropagation()}
                        onChange={() => toggleSelectOne(msg.correlationId)}
                      />
                    </label>
                  </td>
                  <td>{msg.searchType ?? "-"}</td>
                  <td>{msg.correlationId}</td>
                  <td>
                    <span className={`badge ${getStatusBadge(msg.status).className}`}>
                      {getStatusBadge(msg.status).text}
                    </span>
                  </td>
                  <td>{msg.user ?? "-"}</td>
                  <td>{msg.branch ?? "-"}</td>
                  <td>{msg.dateFrom ? new Date(msg.dateFrom).toLocaleDateString() : "-"}</td>
                 
                </tr>
              ))}
            </tbody>
          </table>
 
          {isLoading && <p>Loading data...</p>}
          {error && <p className="text-danger">Error fetching data.</p>}
 
          {/* Pagination */}
          <div className="pagination d-flex justify-content-center mt-3">
            {Array.from({ length: totalPages }, (_, i) => (
              <button
                key={i}
                className={`btn btn-sm mx-1 ${currentPage === i + 1 ? "btn-primary" : "btn-outline-primary"}`}
                onClick={() => setCurrentPage(i + 1)}
              >
                {i + 1}
              </button>
            ))}
          </div>
        </div>
      </div>
    </main>
  );
};
 
export default MainContent;
