import React, { useEffect, useState } from "react";
import axios from "axios";
import * as signalR from "@microsoft/signalr";

function App() {
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    // Load initial messages
    axios
      .get("https://localhost:7237/api/TrackedStatus")
      .then((res) => {
        setMessages(res.data);
      })
      .catch((err) => {
        console.error("Error loading messages from DB:", err);
      });

    // Setup SignalR connection
    const connection = new signalR.HubConnectionBuilder()
      .withUrl("https://localhost:7237/messageHub")
      .withAutomaticReconnect()
      .build();

    // Handle real-time status updates
    const updateOrAddMessage = (data) => {
      console.log("📥 SignalR - ReceivedStatusUpdate:", data);

      setMessages((prev) => {
        const index = prev.findIndex(
          (msg) => msg.correlationId === data.correlationId
        );

        if (index !== -1) {
          // Update existing message
          const updated = [...prev];
          updated[index] = { ...updated[index], ...data };
          return updated;
        } else {
          // Add new message
          return [...prev, data];
        }
      });
    };

    // SignalR receive handlers
    connection.on("ReceiveMessage", updateOrAddMessage);
    connection.on("ReceiveStatusUpdate", updateOrAddMessage);

    connection
      .start()
      .then(() => console.log("✅ SignalR connected"))
      .catch((err) =>
        console.error("❌ SignalR connection error:", err.message)
      );

    // Cleanup on component unmount
    return () => {
      connection.stop();
    };
  }, []);

  return (
    <div style={{ padding: 20 }}>
      <h2>Live Service Bus Messages</h2>
      <ul>
        {messages.map((msg, i) => (
          <li key={i}>
            <strong>Message:</strong> {msg.text || msg.message} <br />
            <strong>Correlation ID:</strong> {msg.correlationId} <br />
            <strong>Status:</strong> {msg.status || "Unknown"}
            <hr />
          </li>
        ))}
      </ul>
    </div>
  );
}

export default App;
