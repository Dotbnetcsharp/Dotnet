private String prepareParcelNumberForURL(String parcelNum) throws Exception {
    if (parcelNum == null) return "";

    parcelNum = parcelNum.replaceAll("[\\s-]", "").trim(); // remove spaces and hyphens

    StringBuilder prepareStr = new StringBuilder();

    // Case 1: Continuous numeric APN like "2104180003001400"
    if (parcelNum.length() >= 16 && parcelNum.matches("\\d+")) {
        prepareStr.append(parcelNum.charAt(0));
        prepareStr.append(parcelNum.charAt(1));
        prepareStr.append(" "); // after 2nd

        prepareStr.append(parcelNum.charAt(2));
        prepareStr.append(parcelNum.charAt(3));
        prepareStr.append(" "); // after 4th

        prepareStr.append(parcelNum.charAt(4));
        prepareStr.append(parcelNum.charAt(5));
        prepareStr.append(" "); // after 6th

        prepareStr.append(parcelNum.charAt(6));
        prepareStr.append(" "); // after 7th

        prepareStr.append(parcelNum.charAt(7));
        prepareStr.append(parcelNum.charAt(8));
        prepareStr.append(parcelNum.charAt(9));
        prepareStr.append(" "); // after 10th

        prepareStr.append(parcelNum.charAt(10));
        prepareStr.append(parcelNum.charAt(11));
        prepareStr.append(parcelNum.charAt(12));
        prepareStr.append("."); // after 13th

        prepareStr.append(parcelNum.charAt(13));
        prepareStr.append(parcelNum.charAt(14));
        prepareStr.append(parcelNum.charAt(15));
    } 
    // Case 2: Hyphenated APN like "21-04-19-0-000-025.0000"
    else if (parcelNum.contains(".")) {
        // remove last dot segment
        int dotIndex = parcelNum.indexOf('.');
        if (dotIndex != -1) {
            parcelNum = parcelNum.substring(0, dotIndex);
        }
        // replace hyphens with space
        parcelNum = parcelNum.replace("-", " ");
        prepareStr.append(parcelNum);
    }

    return prepareStr.toString();
}

propId = propId.replaceAll(" ", "").replace("\t", "").replace("-", "");
propId = prepareParcelNumberForURL(propId);

