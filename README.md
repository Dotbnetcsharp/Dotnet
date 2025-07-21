import React, { useMemo, useState } from "react";
import {
  LineChart,
  Line,
  XAxis,
  YAxis,
  Tooltip,
  Legend,
  ResponsiveContainer,
} from "recharts";
import { format, parseISO, getWeek } from "date-fns";
import { useGetTrackedStatusesQuery } from "../Apis/messageApi";
import { TrackedMessage } from "../Interfaces";

// Define the type for grouping
type AggregationType = "month" | "week";

// Colors for the lines
const COLORS = [
  "#8884d8", "#82ca9d", "#ff6c58", "#ff7f50",
  "#8dd1e1", "#d0ed57", "#a4de6c", "#d8854f"
];

// Grouping function
const groupMessages = (
  messages: TrackedMessage[],
  type: AggregationType
): any[] => {
  const grouped: Record<string, Record<string, number>> = {};

  messages.forEach((msg) => {
    if (!msg.searchType || !msg.creationDate) return;

    let date: Date;
    try {
      date = parseISO(msg.creationDate);
      if (isNaN(date.getTime())) return;
    } catch {
      return;
    }

    const period =
      type === "month"
        ? format(date, "yyyy-MM")
        : `Week-${getWeek(date)}-${format(date, "yyyy")}`;

    if (!grouped[period]) grouped[period] = {};
    grouped[period][msg.searchType] =
      (grouped[period][msg.searchType] || 0) + 1;
  });

  return Object.entries(grouped).map(([period, counts]) => ({
    period,
    ...counts,
  }));
};

const GraphPage: React.FC = () => {
  const { data: messages = [], isLoading, isError } = useGetTrackedStatusesQuery();
  const [aggregation, setAggregation] = useState<AggregationType>("month");

  const chartData = useMemo(
    () => groupMessages(messages, aggregation),
    [messages, aggregation]
  );

  const searchTypes = useMemo(
    () =>
      Array.from(new Set(messages.map((msg) => msg.searchType).filter(Boolean))) as string[],
    [messages]
  );

  return (
    <div className="container mt-4">
      <h2 className="text">Search Type Trends ({aggregation})</h2>

      <div className="mb-3">
        <button
          className={`btn btn-sm me-2 ${
            aggregation === "month" ? "btn-primary" : "btn-outline-primary"
          }`}
          onClick={() => setAggregation("month")}
        >
          Monthly
        </button>
        <button
          className={`btn btn-sm ${
            aggregation === "week" ? "btn-primary" : "btn-outline-primary"
          }`}
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
          <LineChart data={chartData}>
            <XAxis dataKey="period" />
            <YAxis allowDecimals={false} />
            <Tooltip />
            <Legend />
            {searchTypes.map((type, index) => (
              <Line
                key={type}
                type="monotone"
                dataKey={type}
                stroke={COLORS[index % COLORS.length]}
                strokeWidth={2}
                dot={{ r: 3 }}
                activeDot={{ r: 6 }}
              />
            ))}
          </LineChart>
        </ResponsiveContainer>
      )}
    </div>
  );
};

export default GraphPage;
