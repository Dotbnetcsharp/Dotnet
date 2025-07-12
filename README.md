const updateOrAddMessage = (data: TrackedMessage) => {
  setMessages((prev) => {
    const index = prev.findIndex((m) => m.correlationId === data.correlationId);

    if (index !== -1) {
      const updated = [...prev];
      updated[index] = {
        ...updated[index],
        status: data.status,
      };
      console.log("🔁 Updated message:", updated[index]);
      return updated;
    } else {
      const newMessage = { ...data }; // this might be incomplete
      console.log("➕ New message added:", newMessage);
      return [...prev, newMessage];
    }
  });
};
