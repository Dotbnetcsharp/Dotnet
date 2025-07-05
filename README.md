connection.on("ReceiveMessage", async (data: TrackedMessage) => {
  try {
    const response = await fetch(`${API_BASE_URL}/api/trackedstatus/${data.correlationId}/details`);
    const enriched = await response.json();

    setMessages((prev) => {
      const exists = prev.some((m) => m.correlationId === enriched.correlationId);
      return exists ? prev : [enriched, ...prev];
    });
  } catch {
    setMessages((prev) => {
      const exists = prev.some((m) => m.correlationId === data.correlationId);
      return exists ? prev : [data, ...prev];
    });
  }
});
