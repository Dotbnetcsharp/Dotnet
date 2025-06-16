private String prepareParcelNumberForURL(String parcelNum) throws Exception {
    String formattedAPN = null;
    try {
        // Rule 1: Format - NN-NN-N-NNN-NNN.NNN → Should become: NN NN N NNN NNN.NNN
        if (parcelNum.matches("\\d{2}-\\d{2}-\\d-\\d{3}-\\d{3}\\.\\d{3}")) {
            formattedAPN = parcelNum.replace("-", " ");
            requestConf.debug("Formatted APN (hyphenated): " + formattedAPN);

        // Rule 2: Format - 15-digit numeric → Should become: NN NN NN N NNN NNN.DDD
        } else if (parcelNum.matches("\\d{15}")) {
            String part1 = parcelNum.substring(0, 2);
            String part2 = parcelNum.substring(2, 4);
            String part3 = parcelNum.substring(4, 6);
            String part4 = parcelNum.substring(6, 7);
            String part5 = parcelNum.substring(7, 10);
            String part6 = parcelNum.substring(10, 13);
            String part7 = parcelNum.substring(13); // 13th to 15th digit

            formattedAPN = String.format("%s %s %s %s %s %s.%s", 
                part1, part2, part3, part4, part5, part6, part7);
            requestConf.debug("Formatted APN (numeric 15-digit): " + formattedAPN);

        } else {
            // Default fallback if pattern doesn't match
            formattedAPN = requestConf.getPropertyId().replace("_", "").trim();
            requestConf.debug("Formatted APN (default): " + formattedAPN);
        }
    } catch (Exception e) {
        requestConf.debug("Error formatting APN: " + e.getMessage());
    }

    return formattedAPN;
}
