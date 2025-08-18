else {
    if (sectionValue.startsWith("SPECIAL ASSESSMENTS")) continue;

    String[] parts = sectionValue.split("\\s+");
    int size = parts.length;

    if (size > 3) {

        // if "SPECIAL ASSESSMENTS" appears inside the line, drop it *before* splitting
        if (sectionValue.contains("SPECIAL ASSESSMENTS")) {
            sectionValue = sectionValue.replaceAll("SPECIAL ASSESSMENTS", "").trim();
            parts = sectionValue.split("\\s+");
            size  = parts.length;
        }

        // last token
        String lastToken  = parts[size - 1];
        String prevToken  = (size > 2 ? parts[size - 2] : "");

        // ----- no numeric at the end => description only
        if (!lastToken.matches("^[0-9.,]+$")) {
            // remove "6." prefix and use the rest as description
            String[] tmp = sectionValue.split("\\.\\s", 2);
            String description = (tmp.length > 1 ? tmp[1] : tmp[0]).trim();

            SAValuesInd object = SAValuesInd.getSAValuesIndObject(
                    null, "Taxing Entities",
                    description, null, null, null, null);
            saValuesList2.add(object);
            continue;
        }

        // ----- last token IS numeric => extract amount(s)
        String raw  = lastToken;
        String raw2 = (prevToken.matches("^[0-9.,]+$") ? prevToken : "");

        String amount  = raw.replaceAll("[^0-9\\.]+", "");
        String amount2 = raw2.replaceAll("[^0-9\\.]+", "");

        // remove the ORIGINAL tokens (raw, raw2)
        String description1 = sectionValue.replace(raw,"").replace(raw2,"").trim();

        // remove *only* trailing "SPECIAL ASSESSMENTS"
        description1 = description1.replaceAll("\\d+(\\.\\d+)?\\s+SPECIAL ASSESSMENTS\\s*$","").trim();

        // remove "$" and split off the "number."
        description1 = description1.replace("($)","");
        String[] tmp = description1.split("\\.\\s",2);
        String description = (tmp.length > 1 ? tmp[1] : tmp[0]).trim();

        SAValuesInd object = SAValuesInd.getSAValuesIndObject(
                null,
                "Taxing Entities",
                description,
                formatAmount(amount),
                (amount2.isEmpty() ? null : formatAmount(amount2)),
                null, null);
        saValuesList2.add(object);
    }
}
