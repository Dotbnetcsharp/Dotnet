useEffect(() => {
  if (data) {
    console.log("Data from state:", data); // ✅ LOG WHEN data updates
  }
}, [data]);
