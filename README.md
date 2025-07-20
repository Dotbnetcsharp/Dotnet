// src/pages/GraphPage.tsx
import React, { useState, useMemo } from "react";
import { useGetTrackedStatusesQuery } from "../Apis/messageApi";
import { TrackedMessage } from "../Interfaces";
import {
  BarChart,
  Bar,
  XAxis,
  YAxis,
  Tooltip,
  Legend,
  ResponsiveContainer,
} from "recharts";
import { format, parseISO, getWeek } from "date-fns";

// Define the types of aggregation
type AggregationType = "month" | "week";

// Color palette for consistent coloring
const COLORS = [
  "#8884d8", "#82ca9d", "#ffc658", "#ff7f50",
  "#8dd1e1", "#d0ed57", "#a4de6c", "#d8854f",
];

const groupMessages = (
  messages: TrackedMessage[],
  type: AggregationType
): any[] => {
  const grouped: Record<string, Record<string, number>> = {};

  messages.forEach((msg) => {
    if (!msg.searchType || !msg.dateFrom) return;

    let date: Date;
    try {
      date = parseISO(msg.dateFrom);
      if (isNaN(date.getTime())) return; // invalid date
    } catch {
      return;
    }

    const period =
      type === "month"
        ? format(date, "yyyy-MM")
        : `Week-${getWeek(date)}-${format(date, "yyyy")}`;

    if (!grouped[period]) grouped[period] = {};
    grouped[period][msg.searchType] = (grouped[period][msg.searchType] || 0) + 1;
  });

  return Object.entries(grouped).map(([period, counts]) => ({
    period,
    ...counts,
  }));
};

const GraphPage: React.FC = () => {
  const { data: messages = [], isLoading, isError } = useGetTrackedStatusesQuery();
  const [aggregation, setAggregation] = useState<AggregationType>("month");

  const chartData = useMemo(() => groupMessages(messages, aggregation), [messages, aggregation]);

  const searchTypes = useMemo(
    () =>
      Array.from(
        new Set(messages.map((msg) => msg.searchType).filter(Boolean))
      ) as string[],
    [messages]
  );

  return (
    <div className="container mt-4">
      <h2>Search Type Trends ({aggregation})</h2>

      <div className="mb-3">
        <button
          className={`btn btn-sm me-2 ${aggregation === "month" ? "btn-primary" : "btn-outline-primary"}`}
          onClick={() => setAggregation("month")}
        >
          Monthly
        </button>
        <button
          className={`btn btn-sm ${aggregation === "week" ? "btn-primary" : "btn-outline-primary"}`}
          onClick={() => setAggregation("week")}
        >
          Weekly
        </button>
      </div>

      {isLoading ? (
        <p>Loading...</p>
      ) : isError ? (
        <p className="text-danger">Failed to load data.</p>
      ) : chartData.length === 0 ? (
        <p>No data to display.</p>
      ) : (
        <ResponsiveContainer width="100%" height={400}>
          <BarChart data={chartData}>
            <XAxis dataKey="period" />
            <YAxis allowDecimals={false} />
            <Tooltip />
            <Legend />
            {searchTypes.map((type, index) => (
              <Bar
                key={type}
                dataKey={type}
                stackId="a"
                fill={COLORS[index % COLORS.length]}
              />
            ))}
          </BarChart>
        </ResponsiveContainer>
      )}
    </div>
  );
};

export default GraphPage;
