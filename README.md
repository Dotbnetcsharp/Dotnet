public String validateAPN(String apn) {
    String formattedAPN = apn;
    try {
        if (apn == null || apn.trim().isEmpty()) {
            return apn;
        }

        String rawAPN = apn.trim();
        int spaceCount = rawAPN.length() - rawAPN.replace(" ", "").length();

        // Case 1: ANANNN → keep as-is
        if (rawAPN.matches("^[A-Z]\\d[A-Z]\\d{3}$")) {
            formattedAPN = rawAPN;
        }

        // Case 2: ANSNNN → replace 1 space with hyphen
        else if (rawAPN.matches("^[A-Z]\\d{1,2} \\d{3}$") && spaceCount == 1) {
            formattedAPN = rawAPN.replace(" ", "-");
        }

        // Case 3: ANSSNNA → replace 2 spaces with 1 hyphen (e.g., L7  19A → L7-19A)
        else if (spaceCount == 2) {
            formattedAPN = rawAPN.replaceAll(" ", "-").replaceFirst("-+", "-");
        }

        // Case 4: ANASNN → replace 1 space with hyphen (e.g., L4C 23 → L4C-23)
        else if (rawAPN.matches("^[A-Z]\\d[A-Z] \\d{2}$") && spaceCount == 1) {
            formattedAPN = rawAPN.replace(" ", "-");
        }

        // Case 5: ANSSNN → replace 2 spaces with 1 hyphen (e.g., L7  23 → L7-23)
        else if (rawAPN.matches("^[A-Z]\\d{1,2}  \\d{2}$") && spaceCount == 2) {
            formattedAPN = rawAPN.replaceAll(" ", "-").replaceFirst("-+", "-");
        }

        // If nothing matches, return original
        else {
            formattedAPN = rawAPN;
        }

        requestConf.debug("Formatted APN: " + formattedAPN);
    } catch (Exception e) {
        requestConf.setExceptionType(e);
        requestConf.debug("Error in Validating APN: " + e.getLocalizedMessage());
    }

    return formattedAPN;
}
