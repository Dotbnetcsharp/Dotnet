private String validateAPN() {
    String propId = null;
    String errorMessage = null;

    try {
        propId = requestConf.getPropertyId().trim();

        // Case 1: Contains dot or hyphen (e.g., 006.00-01-24)
        if (propId.matches(".*[.-].*")) {
            // Remove dots and hyphens
            String cleaned = propId.replaceAll("[.-]", "");

            // Should be exactly 9 digits
            if (cleaned.matches("\\d{9}")) {
                propId = cleaned;
                requestConf.debug("Formatted Property ID (patterned): " + propId);
            } else {
                errorMessage = "Invalid formatted Parcel Number";
            }

        // Case 2: Only digits, exactly 9 characters
        } else if (propId.matches("\\d{9}")) {
            requestConf.debug("Formatted Property ID (plain numeric): " + propId);

        } else {
            errorMessage = "Invalid Parcel Number";
        }

    } catch (Exception e) {
        requestConf.setExceptionType(e);
        requestConf.debug("Exception in Validating APN");
    }

    if (StringUtils.isNotBlank(errorMessage)) {
        return errorMessage;
    } else {
        return propId;
    }
}
