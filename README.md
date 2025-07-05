import React, { useEffect, useState } from "react";
import * as signalR from "@microsoft/signalr";
import { BarChart2, TrendingUp } from "lucide-react";
import { TrackedMessage } from "../Interfaces";
import { API_BASE_URL } from "../config";
import { useGetTrackedStatusesQuery } from "../Apis/messageApi";
import { getStatusBadge } from "../Utility/statusBadgeHelper";
import { useNavigate } from "react-router-dom";
import DatePicker from "react-datepicker";
import "react-datepicker/dist/react-datepicker.css";
import toast from "react-hot-toast";

const MainContent: React.FC = () => {
  // ✅ 1. RTK Query + Refresh Support
  const { data: initialData = [], isLoading, error, refetch } = useGetTrackedStatusesQuery();

  const [messages, setMessages] = useState<TrackedMessage[]>([]);
  const [selectedIds, setSelectedIds] = useState<string[]>([]);
  const [currentPage, setCurrentPage] = useState(1);
  const itemsPerPage = 10;
  const [sortBy, setSortBy] = useState<keyof TrackedMessage | "">("");
  const [sortOrder, setSortOrder] = useState<"asc" | "desc">("asc");
  const [searchQuery, setSearchQuery] = useState("");
  const [filterStatus, setFilterStatus] = useState<string>("All");
  const [fromDate, setFromDate] = useState<Date | null>(null);   // ✅ 2. Date From
  const [toDate, setToDate] = useState<Date | null>(null);       // ✅ 2. Date To

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

          // ✅ 4. Show notification if status is completed and selected
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

    connection.start().catch(console.error);
    return () => connection.stop();
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

  // ✅ Apply filters (search, status, date)
  const filtered = messages.filter((msg) => {
    const matchStatus =
      filterStatus === "All" || msg.status?.toLowerCase() === filterStatus.toLowerCase();
    const matchSearch =
      msg.message?.toLowerCase().includes(searchQuery.toLowerCase()) ||
      msg.correlationId?.toLowerCase().includes(searchQuery.toLowerCase());
    const matchFrom = fromDate ? new Date(msg.dateFrom ?? "") >= fromDate : true;
    const matchTo = toDate ? new Date(msg.dateFrom ?? "") <= toDate : true;
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

  return (
    <main className="main users chart-page" id="skip-target">
      <div className="container">
        <h2 className="main-title">Dashboard</h2>

        {/* ✅ Toolbar: Search, Filter, Date Picker, Refresh, Notify */}
        <div className="d-flex flex-wrap justify-content-between align-items-center gap-2 mb-3">
          {/* Search box */}
          <div className="input-group input-group-sm w-50">
            <input
              type="text"
              className="form-control"
              placeholder="🔍 Search by message or ID"
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
            />
            <span className="input-group-text">🔍</span>
          </div>

          {/* Status filter */}
          <select
            className="form-select form-select-sm w-auto"
            value={filterStatus}
            onChange={(e) => setFilterStatus(e.target.value)}
          >
            <option value="All">All Statuses</option>
            <option value="Completed">Completed</option>
            <option value="InProgress">In Progress</option>
            <option value="Scheduled">Scheduled</option>
            <option value="Error">Error</option>
            <option value="Unknown">Unknown</option>
          </select>

          {/* ✅ From - To Date */}
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

          {/* ✅ Refresh button */}
          <button className="btn btn-outline-primary btn-sm" onClick={refetch}>
            🔄 Refresh
          </button>

          {/* ✅ Notification button */}
          <button
            className="btn btn-outline-success btn-sm"
            onClick={() => toast("📢 Waiting for status to complete...")}
          >
            🔔 Notify
          </button>
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
                    />
                    Select
                  </label>
                </th>
                <th onClick={() => handleSort("message")}>Message</th>
                <th onClick={() => handleSort("correlationId")}>Correlation ID</th>
                <th onClick={() => handleSort("status")}>Status</th>
                <th>User</th>
                <th>Branch</th>
                <th onClick={() => handleSort("dateFrom")}>Date From</th>
              </tr>
            </thead>
            <tbody>
              {paginated.map((msg, i) => (
                <tr
                  key={i}
                  onClick={() => handleRowClick(msg)}
                  className={selectedIds.includes(msg.correlationId) ? "active" : ""}
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
                  <td>{msg.message ?? "-"}</td>
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

          <div className="pagination d-flex justify-content-center mt-3">
            {Array.from({ length: totalPages }, (_, i) => (
              <button
                key={i}
                className={`btn btn-sm mx-1 ${
                  currentPage === i + 1 ? "btn-primary" : "btn-outline-primary"
                }`}
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
