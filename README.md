String originalApn = apn.trim();
String apnFormatted = originalApn;

// Tax ID Patterns to preserve
String taxIdPattern1 = "\\d{4}-\\d{2}-\\d{3}-[A-Z]{1}[A-Z0-9]{2}-\\d-\\d{3}-\\d{2}"; // NNNN-NN-NNA-NNA-N-NNN-NN
String taxIdPattern2 = "\\d{4}-\\d{2}-\\d{3}-\\d{3}-\\d-\\d{3}-\\d{2}";              // NNNN-NN-NNN-NNN-N-NNN-NN

// Preserve as-is if matches tax format
if (!originalApn.matches(taxIdPattern1) && !originalApn.matches(taxIdPattern2)) {
    // It's likely an APN, so trim leading zeros
    while (apnFormatted.startsWith("0") && apnFormatted.length() > 1) {
        apnFormatted = apnFormatted.substring(1);
    }
}

// Determine if it's an APN search
boolean isAPNSearch = !Character.isDigit(originalApn.charAt(0));
