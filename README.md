private String prepareParcelNumberForURL(String parcelNum) throws Exception {
    String formattedAPN = null;

    try {
        // Rule B: Hyphenated APN Format (e.g. 02-08-27-4-000-113.000)
        if (parcelNum.matches("\\d{2}-\\d{2}-\\d{2}-\\d-\\d{3}-\\d{3}\\.\\d{3,4}")) {
            formattedAPN = parcelNum.replace("-", " ");
            requestConf.debug("Formatted APN (hyphenated): " + formattedAPN);

        // Rule C: 18-digit continuous format (remove first 3 chars, format as NN NN NN N NNN NNN.NNN)
        } else if (parcelNum.matches("\\d{18}")) {
            String trimmed = parcelNum.substring(3);  // Remove first 3 digits

            String part1 = trimmed.substring(0, 2);   // e.g., 98
            String part2 = trimmed.substring(2, 4);   // e.g., 21
            String part3 = trimmed.substring(4, 6);   // e.g., 09
            String part4 = trimmed.substring(6, 7);   // e.g., 3
            String part5 = trimmed.substring(7, 10);  // e.g., 000
            String part6 = trimmed.substring(10, 13); // e.g., 021
            String part7 = trimmed.substring(13, 16); // e.g., 000

            formattedAPN = String.format("%s %s %s %s %s %s.%s",
                    part1, part2, part3, part4, part5, part6, part7);

            requestConf.debug("Formatted APN (numeric 18-digit): " + formattedAPN);

        } else {
            // Fallback for unexpected input
            formattedAPN = requestConf.getProperty("apn").replace("-", "").trim();
            requestConf.debug("Formatted APN (default): " + formattedAPN);
        }

    } catch (Exception e) {
        requestConf.debug("Error formatting APN: " + e.getMessage());
    }

    return formattedAPN;
}
