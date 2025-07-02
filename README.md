import React, { useEffect, useState } from "react";
import * as signalR from "@microsoft/signalr";
import { BarChart2, TrendingUp } from "lucide-react";
import { TrackedMessage } from "../Interfaces";
import { API_BASE_URL } from "../config";
import { useGetTrackedStatusesQuery } from "../Apis/messageApi";
import { getStatusBadge } from "../Utility/statusBadgeHelper";

const MainContent: React.FC = () => {
  const { data: initialData = [], isLoading, error } = useGetTrackedStatusesQuery();
  const [messages, setMessages] = useState<TrackedMessage[]>([]);
  const [selectedIds, setSelectedIds] = useState<string[]>([]);

  const [currentPage, setCurrentPage] = useState(1);
  const itemsPerPage = 10;

  const [sortBy, setSortBy] = useState<keyof TrackedMessage | "">("");
  const [sortOrder, setSortOrder] = useState<"asc" | "desc">("asc");

  const [searchQuery, setSearchQuery] = useState("");
  const [filterStatus, setFilterStatus] = useState<string>("All");

  useEffect(() => {
    if (initialData.length) {
      setMessages(initialData);
    }
  }, [initialData]);

  const upsertMessage = (updated: TrackedMessage) => {
    setMessages((prev) => {
      const index = prev.findIndex((m) => m.correlationId === updated.correlationId);
      if (index === -1) {
        return [updated, ...prev];
      } else {
        const newMessages = [...prev];
        newMessages[index] = { ...newMessages[index], ...updated };
        return newMessages;
      }
    });
  };

  useEffect(() => {
    const connection = new signalR.HubConnectionBuilder()
      .withUrl(`${API_BASE_URL}/messageHub`)
      .withAutomaticReconnect()
      .build();

    const fetchAndUpsert = async (correlationId: string) => {
      try {
        const response = await fetch(`${API_BASE_URL}/api/trackedstatus/${correlationId}`);
        const updated = await response.json();
        upsertMessage(updated);
      } catch (err) {
        console.error("Fetch failed for", correlationId);
      }
    };

    connection.on("ReceiveMessage", (data: TrackedMessage) => {
      fetchAndUpsert(data.correlationId);
    });

    connection.on("ReceiveStatusUpdate", (data: TrackedMessage) => {
      fetchAndUpsert(data.correlationId);
    });

    connection.start().catch((err) => console.error("SignalR error:", err));
    return () => {
      connection.stop();
    };
  }, []);

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

  const filtered = messages.filter((msg) => {
    const matchStatus =
      filterStatus === "All" || msg.status?.toLowerCase() === filterStatus.toLowerCase();
    const matchSearch =
      msg.message?.toLowerCase().includes(searchQuery.toLowerCase()) ||
      msg.correlationId?.toLowerCase().includes(searchQuery.toLowerCase());
    return matchStatus && matchSearch;
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

        <div className="row stat-cards">
          <div className="col-md-6 col-xl-3">
            <article className="stat-cards-item">
              <div className="stat-cards-icon primary">
                <BarChart2 size={24} />
              </div>
              <div className="stat-cards-info">
                <p className="stat-cards-info__num">1478 286</p>
                <p className="stat-cards-info__title">Total visits</p>
                <p className="stat-cards-info__progress">
                  <span className="stat-cards-info__profit success">
                    <TrendingUp size={16} /> 4.07%
                  </span>
                  Last month
                </p>
              </div>
            </article>
          </div>
        </div>

        {/* Filters */}
        <div className="d-flex justify-content-between mb-3">
          <input
            type="text"
            placeholder="Search message or ID"
            value={searchQuery}
            onChange={(e) => setSearchQuery(e.target.value)}
            className="form-control w-50"
          />
          <select
            className="form-select w-25"
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
                <th onClick={() => handleSort("message")}>Message</th>
                <th onClick={() => handleSort("correlationId")}>Correlation ID</th>
                <th onClick={() => handleSort("status")}>Status</th>
                <th>User</th>
                <th>Branch</th>
                <th>Search Type</th>
                <th>County FIPS</th>
                <th>Error Title</th>
                <th>Date From</th>
                <th>APNs</th>
              </tr>
            </thead>
            <tbody>
              {paginated.map((msg, i) => (
                <tr key={i} className={selectedIds.includes(msg.correlationId) ? "active" : ""}>
                  <td>
                    <label className="users-table__checkbox">
                      <input
                        type="checkbox"
                        className="check"
                        checked={selectedIds.includes(msg.correlationId)}
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
                  <td>{msg.searchType ?? "-"}</td>
                  <td>{msg.countyFips ?? "-"}</td>
                  <td>{msg.errorTitle ?? "-"}</td>
                  <td>{msg.dateFrom ? new Date(msg.dateFrom).toLocaleDateString() : "-"}</td>
                  <td>{msg.apns?.length ? msg.apns.join(", ") : "-"}</td>
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
