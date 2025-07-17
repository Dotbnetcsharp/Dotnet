public String validateAPN(String apn) {
    String formattedAPN = apn;
    try {
        // Clean input: remove underscores and extra spaces
        String cleanApn = apn.trim().replaceAll("_", "").replaceAll(" +", "");

        // Case 1: NNN-A-NNN or NNN-A-NNNA → 107-A-011 → 107   A     011
        if (cleanApn.matches("\\d{3}-[A-Z]-\\d{3}[A-Z]?")) {
            String[] parts = cleanApn.split("-");
            formattedAPN = parts[0] + "   " + parts[1] + "     " + parts[2];
        }

        // Case 2: NNNNANNN-NNN → 103A208-006 → 103A208     006
        else if (cleanApn.matches("\\d{3}[A-Z]\\d{3}-\\d{3}")) {
            String[] parts = cleanApn.split("-");
            formattedAPN = parts[0] + "     " + parts[1];
        }

        // Case 3: NNN-NN-NNN → 107-02-042 → 107  02     042
        else if (cleanApn.matches("\\d{3}-\\d{2}-\\d{3}")) {
            String[] parts = cleanApn.split("-");
            formattedAPN = parts[0] + "  " + parts[1] + "     " + parts[2];
        }

        // Case 4: NNNA-NN-NNN → 105A-02-035 → 105A 02     035
        else if (cleanApn.matches("\\d{3}[A-Z]-\\d{2}-\\d{3}")) {
            String[] parts = cleanApn.split("-");
            formattedAPN = parts[0] + " " + parts[1] + "     " + parts[2];
        }

        // Case 5: NNNAN-A-NNN → 103A2-A-293 → 103A2  A     293
        else if (cleanApn.matches("\\d{4}[A-Z]-[A-Z]-\\d{3}")) {
            String[] parts = cleanApn.split("-");
            formattedAPN = parts[0] + "  " + parts[1] + "     " + parts[2];
        }

        // ✅ Case 6: NNNANNNANNN-NNN → 091A102B002-016 → 091A102B002 016
        else if (cleanApn.matches("\\d{3}[A-Z]\\d{3}[A-Z]\\d{3}-\\d{3}")) {
            String[] parts = cleanApn.split("-");
            formattedAPN = parts[0] + " " + parts[1];
        }

        // Fallback: leave unchanged
        else {
            formattedAPN = apn;
        }

        requestConf.debug("Formatted APN: " + formattedAPN);
    } catch (Exception e) {
        requestConf.setExceptionType(e);
        requestConf.debug("Error in Validating APN: " + e.getLocalizedMessage());
    }

    return formattedAPN;
}
