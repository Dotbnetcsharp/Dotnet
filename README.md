int length = txtSAValues.split("\n").length;
for (int i = 0; i < length; i++) {
    String[] arr = txtSAValues.split("\n");
    String[] SAValues = arr[i].split(" ");
    String str = arr[i].trim();
    int size = str.split(" ").length;

    if (size > 3) {
        // Extract amounts
        String amount = SAValues[size - 1].replaceAll("[^0-9\\.]+", "");
        String amount2 = SAValues[size - 2].replaceAll("[^0-9\\.]+", "");

        // Remove amounts from the original string
        String description1 = arr[i].replace(amount, "").replace(amount2, "").trim();
        description1 = description1.replace("($)", "").trim();

        // Remove unwanted tokens: (), A, B, slashes
        description1 = description1.replaceAll("\\(.*?\\)", "").trim();
        description1 = description1.replaceAll("\\b(A|B)\\b", "").trim();
        description1 = description1.replaceAll("\\s*/\\s*", " ").trim();

        // Detect main & sub-description
        String description = description1;
        if (description.contains(".")) {
            // Split only once after the first dot
            String[] parts = description.split("\\.", 2);
            if (parts.length > 1) {
                // Join as "Description - Sub-description"
                description = parts[0].trim() + " - " + parts[1].trim();
            }
        }

        // Create and add the object
        SAValuesInd saValueObject = SAValuesInd.getSAValuesIndObject(
            null,
            "Taxing Entity",  // Type
            description,      // Description
            formatAmount(amount),
            null,
            null,
            null
        );
        saValuesList2.add(saValueObject);
    }
}
