const updateOrAddMessage = (data: TrackedMessage) => {
  setMessages((prev) => {
    const index = prev.findIndex((m) => m.correlationId === data.correlationId);

    if (index !== -1) {
      const updated = [...prev];

      // Only update status, don't overwrite other fields
      updated[index] = {
        ...updated[index],
        status: data.status, // only update the field you received
      };

      return updated;
    } else {
      // Push new message if not found (but assume it's partial!)
      return [...prev, data]; // Optional: enrich this if needed
    }
  });
};
