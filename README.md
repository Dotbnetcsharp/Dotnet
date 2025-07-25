const [useMergedId, setUseMergedId] = useState<boolean>(false);

{mergedId && (
  <label>
    <input
      type="checkbox"
      checked={useMergedId}
      onChange={() => setUseMergedId((prev) => !prev)}
    />
    Use Merged Report
  </label>
)}

const handleGetReport = async () => {
  if (!id) return;

  setReportJson(null);
  setPdfPath(null);

  try {
    const finalId: string = useMergedId && mergedId ? mergedId : id;

    const result = await getReportPath({
      Id: finalId,
      GenerateReport: true,
    }).unwrap();

    setReportJson(result);

    if (result.ReportPath) {
      setPdfPath(result.ReportPath);
    }
  } catch (err) {
    console.error("Error fetching report:", err);
  }
};
