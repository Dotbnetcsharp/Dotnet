connection.on("ReceiveStatusUpdate", async (data: TrackedMessage) => {
  try {
    const response = await fetch(`${API_BASE_URL}/api/trackedstatus/${data.correlationId}/details`);
    const updated = await response.json();

    setMessages((prev) =>
      prev.map((msg) =>
        msg.correlationId === updated.correlationId ? { ...msg, ...updated } : msg
      )
    );
  } catch (err) {
    console.error("Error updating status from SignalR:", err);
  }
});
