useEffect(() => {
  const connection = new signalR.HubConnectionBuilder()
    .withUrl("https://localhost:7237/messageHub", {
      withCredentials: true, // ✅ needed for SignalR + CORS
    })
    .withAutomaticReconnect()
    .build();

  connection.on("ReceiveMessage", async (data: TrackedMessage) => {
    console.log("✅ ReceiveMessage", data);
    try {
      const response = await fetch(`${API_BASE_URL}/api/trackedstatus/${data.correlationId}`);
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

  connection.on("ReceiveStatusUpdate", (data: TrackedMessage) => {
    console.log("✅ ReceiveStatusUpdate", data); // 🔍 Add this to verify
    setMessages((prev) =>
      prev.map((msg) =>
        msg.correlationId === data.correlationId ? { ...msg, status: data.status } : msg
      )
    );
  });

  connection
    .start()
    .then(() => console.log("✅ SignalR connected"))
    .catch((err) => console.error("❌ SignalR connection error:", err));

  return () => {
    connection.stop();
  };
}, []);
