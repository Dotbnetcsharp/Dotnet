for (int i = 0; i < length; i++) {
    String[] arr = txtSAValues.split("\n");
    String[] SAValues = arr[i].split("\t");
    String str = arr[i].trim();
    int size = str.split("\t").length;

    if (size >= 3) {
        // Extract amounts
        amount = SAValues[size - 1].replaceAll("[^0-9\\.]+", "");
        String amount2 = SAValues[size - 2].replaceAll("[^0-9\\.]+", "");

        // Remove amounts from the original string
        description1 = arr[i].replace(amount, "").replace(amount2, "").trim();
        description1 = description1.replace("$", "").trim();

        // Remove unwanted tokens: (), A, B, slashes
        description1 = description1.replaceAll("\\(.*?\\)", "").trim();
        description1 = description1.replaceAll("\\b(A|B|C)\\b", "").trim();
        description1 = description1.replaceAll("\\s*/\\s*", " ").trim();
        description1 = description1.replaceFirst("^\\d+[A-Za-z]?\\.?\\s*", "").trim(); // remove leading numbers like 6. or 10A.

        // Handle main/sub descriptions with colon
        if (description1.contains(":")) {
            String[] parts = description1.split(":", 2);
            String mainPart = parts[0].trim();
            String subPart = parts[1].replaceFirst("^[A-Za-z]\\.\\s*", "").trim(); // remove A., B., C. if present
            if (!subPart.isEmpty()) {
                description1 = mainPart + " - " + subPart;
            } else {
                description1 = mainPart;
            }
        }

        // Ignore rows like "B." with no actual description
        if (description1.matches("^[A-Za-z]\\.?$")) {
            continue; // skip adding this entry
        }

        // Final cleanup
        description1 = description1.replaceAll("\\s*\\.\\s*", " ").trim();
        description1 = description1.replaceAll("\\s{2,}", " ").trim();

        // Create and add the object
        SAValuesInd saValueObject = SAValuesInd.getSAValuesIndObject(
            null,
            "Taxing Entities",
            description1,
            formatAmount(amount),
            null, null, null, null
        );
        saValuesList2.add(saValueObject);
    }
}
