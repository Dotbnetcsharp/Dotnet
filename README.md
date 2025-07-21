// src/pages/GraphPage.tsx
import React, { useState, useMemo } from "react";
import { useGetTrackedStatusesQuery } from "../Apis/messageApi";
import { TrackedMessage } from "../Interfaces";
import {
  LineChart,
  Line,
  XAxis,
  YAxis,
  Tooltip,
  Legend,
  ResponsiveContainer,
} from "recharts";
import { format, parseISO } from "date-fns";

const COLORS = [
  "#8884d8", "#82ca9d", "#ffc658", "#ff7f50",
  "#8dd1e1", "#d0ed57", "#a4de6c", "#d8854f",
];

// Only monthly grouping
const groupByMonth = (messages: TrackedMessage[]): any[] => {
  const grouped: Record<string, Record<string, number>> = {};

  messages.forEach((msg) => {
    if (!msg.status || !msg.dateFrom) return;

    let date: Date;
    try {
      date = parseISO(msg.dateFrom);
      if (isNaN(date.getTime())) return;
    } catch {
      return;
    }

    const monthKey = format(date, "MMM"); // Jan, Feb, etc.

    if (!grouped[monthKey]) grouped[monthKey] = {};
    grouped[monthKey][msg.status] = (grouped[monthKey][msg.status] || 0) + 1;
  });

  return Object.entries(grouped).map(([month, counts]) => ({
    month,
    ...counts,
  }));
};

const GraphPage: React.FC = () => {
  const { data: messages = [], isLoading, isError } = useGetTrackedStatusesQuery();

  const chartData = useMemo(() => groupByMonth(messages), [messages]);

  const statuses = useMemo(
    () =>
      Array.from(new Set(messages.map((msg) => msg.status).filter(Boolean))) as string[],
    [messages]
  );

  return (
    <div className="container mt-4">
      <h2>Number of New Customers (Monthly)</h2>

      {isLoading ? (
        <p>Loading...</p>
      ) : isError ? (
        <p className="text-danger">Failed to load data.</p>
      ) : chartData.length === 0 ? (
        <p>No data to display.</p>
      ) : (
        <ResponsiveContainer width="100%" height={400}>
          <LineChart data={chartData}>
            <XAxis dataKey="month" />
            <YAxis allowDecimals={false} />
            <Tooltip />
            <Legend />
            {statuses.map((status, index) => (
              <Line
                key={status}
                type="monotone"
                dataKey={status}
                stroke={COLORS[index % COLORS.length]}
                strokeWidth={2}
                dot={{ r: 3 }}
              />
            ))}
          </LineChart>
        </ResponsiveContainer>
      )}
    </div>
  );
};

export default GraphPage;
