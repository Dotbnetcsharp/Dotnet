private List<SAValuesInd> getSAValues(String SAresponse) {
    List<SAValuesInd> saValuesList2 = new ArrayList<>();
    String description1 = null, amount = null;

    try {
        String txtSAValues = SAresponse
                .substring(SAresponse.indexOf("PROPERTY TAX BY JURISDICTION"),
                        SAresponse.indexOf("14. TOTAL PROPERTY TAX AND SPECIAL ASSESSMENTS"))
                .replace("PROPERTY TAX BY JURISDICTION", "")
                .replace("12. TOTAL PROPERTY TAX BEFORE SPECIAL ASSESSMENTS", "")
                .trim();

        // Split into lines
        String[] arr = txtSAValues.split("\n");

        for (String line : arr) {
            String trimmedLine = line.trim();
            if (trimmedLine.isEmpty()) continue;

            // If this line starts with "number."
            if (trimmedLine.matches("^\\d+\\..*")) {
                // Check if this main row has sub descriptions
                if (trimmedLine.matches(".*\\s+[A-Z]\\.\\s+.*")) {
                    // Split by sub description (A., B., etc.)
                    String[] subRows = trimmedLine.split("(?=\\s+[A-Z]\\.\\s+)");
                    for (String subRow : subRows) {
                        subRow = subRow.trim();
                        if (!subRow.isEmpty()) {
                            addRowToList(subRow, saValuesList2);
                        }
                    }
                } else {
                    // No sub description, process directly
                    addRowToList(trimmedLine, saValuesList2);
                }
            }
        }
    } catch (Exception e) {
        e.printStackTrace();
    }

    return saValuesList2;
}

private void addRowToList(String row, List<SAValuesInd> saValuesList2) {
    String[] parts = row.trim().split("\\s+");
    if (parts.length < 2) return;

    // Take last value as amount
    String amount = parts[parts.length - 1].replaceAll("[^0-9\\.]+", "");

    // Build description without last value
    String description1 = row.substring(0, row.lastIndexOf(parts[parts.length - 1])).trim();
    description1 = description1.replace("$", "");

    // Remove numbering like "6." or "A."
    if (description1.contains(".")) {
        description1 = description1.split("\\.", 2)[1].trim();
    }

    SAValuesInd saValueObject = SAValuesInd.getSAValuesIndObject(
            null, "Taxing Entity", formatAmount(amount), null, null, null, null
    );
    saValuesList2.add(saValueObject);
}
