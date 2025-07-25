const shownToasts = useRef<Set<string>>(new Set());

if (
  data.status?.toLowerCase() === "completed" &&
  selectedIds.includes(data.correlationId) &&
  !shownToasts.current.has(data.correlationId)
) {
  toastNotify(`Request completed: ${data.correlationId}`);
  shownToasts.current.add(data.correlationId);
}
