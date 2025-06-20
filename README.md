
import React, { useEffect, useState } from 'react';
import axios from 'axios';
import * as signalR from '@microsoft/signalr';

function App() {
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    // Load old messages from DB
    axios.get("https://localhost:7237/api/TrackedStatus")
      .then(res => {
        setMessages(res.data);
      })
      .catch(err => {
        console.error("Error loading messages from DB:", err);
      });

    // Setup SignalR connection
    const connection = new signalR.HubConnectionBuilder()
      .withUrl("https://localhost:7237/messageHub")
      .withAutomaticReconnect()
      .build();

    // Handle message updates (ReceiveMessage)
    connection.on("ReceiveMessage", (data) => {
      updateOrAddMessage(data);
    });

    // Optional: handle status updates if using ReceiveStatusUpdate separately
    connection.on("ReceiveStatusUpdate", (data) => {
      updateOrAddMessage(data);
    });

    const updateOrAddMessage = (data) => {
      setMessages(prev => {
        const index = prev.findIndex(msg => msg.correlationId === data.correlationId);
        if (index !== -1) {
          // Update existing
          const updated = [...prev];
          updated[index] = { ...updated[index], ...data };
          return updated;
        } else {
          // Add new
          return [...prev, data];
        }
      });
    };

    connection.start()
      .then(() => console.log("✅ Connected to SignalR hub"))
      .catch(err => console.error("❌ SignalR connection error:", err));

    return () => {
      connection.stop();
    };
  }, []);

  return (
    <div style={{ padding: 20 }}>
      <h2>📡 Live Service Bus Messages</h2>
      <ul>
        {messages.map((msg, i) => (
          <li key={i}>
            <strong>Message:</strong> {msg.text || 'N/A'} <br />
            <strong>Correlation ID:</strong> {msg.correlationId} <br />
            <strong>Status:</strong> {msg.status || 'Unknown'}
          </li>
        ))}
      </ul>
    </div>
  );
}

export default App;
