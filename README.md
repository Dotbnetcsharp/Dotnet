// ... imports remain unchanged ...

const GraphPage = () => {
  const { data = [], isLoading } = useGetTrackedStatusesQuery();
  const currentYear = new Date().getFullYear();

  const searchTypes = useMemo(() => {
    const typeSet = new Set<string>();
    data.forEach((msg: TrackedMessage) => {
      if (msg.searchType) typeSet.add(msg.searchType);
    });
    return Array.from(typeSet);
  }, [data]);

  const chartData = useMemo(() => {
    const monthMap: Record<string, any> = {};

    for (let i = 0; i < 12; i++) {
      const monthKey = `${currentYear}-${String(i + 1).padStart(2, "0")}`;
      monthMap[monthKey] = { month: monthKey };
      searchTypes.forEach((type) => {
        monthMap[monthKey][type] = 0;
      });
    }

    data.forEach((msg: TrackedMessage) => {
      if (!msg.dateFrom || !msg.searchType) return;
      const date = parseISO(msg.dateFrom);
      if (isNaN(date.getTime()) || getYear(date) !== currentYear) return;

      const monthKey = format(date, "yyyy-MM");
      if (!monthMap[monthKey]) return;

      monthMap[monthKey][msg.searchType] += 1;
    });

    return Object.values(monthMap).sort((a: any, b: any) =>
      a.month.localeCompare(b.month)
    );
  }, [data, searchTypes, currentYear]);

  // Extended color list (20+ distinct colors)
  const colors = [
    "#007bff", "#ff7300", "#82ca9d", "#8884d8", "#ffc658", "#d62728", "#2ca02c", "#17becf",
    "#8c564b", "#e377c2", "#7f7f7f", "#bcbd22", "#1f77b4", "#f1c40f", "#e74c3c", "#2ecc71",
    "#9b59b6", "#16a085", "#f39c12", "#34495e"
  ];

  return (
    <div style={{ width: "100%", height: 500 }}>
      <h3 style={{ textAlign: "center", marginBottom: 20 }}>
        {currentYear} Monthly Search Type Count
      </h3>
      {isLoading ? (
        <p>Loading...</p>
      ) : (
        <ResponsiveContainer>
          <LineChart data={chartData}>
            <XAxis dataKey="month" />
            <YAxis allowDecimals={false} />
            <Tooltip />
            <Legend />
            {searchTypes.map((type, index) => (
              <Line
                key={type}
                type="monotone"
                dataKey={type}
                stroke={colors[index % colors.length]}
                strokeWidth={2}
                dot={{ r: 5, strokeWidth: 2 }}
                activeDot={{ r: 6 }}
                isAnimationActive={false} // Avoid animation hiding low dots
              />
            ))}
          </LineChart>
        </ResponsiveContainer>
      )}
    </div>
  );
};

export default GraphPage;
