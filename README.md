import java.util.*;

public class TaxTableExtractor {

    public static void main(String[] args) {
        // Step 1: Get full text
        String fullText = scrapperStripper.stripTags(pdfReader1);

        // Step 2: Split text into lines
        List<String> lines = Arrays.asList(fullText.split("\\r?\\n"));

        boolean inSection = false;

        for (String line : lines) {
            line = line.trim();

            if (line.toUpperCase().startsWith("DESCRIPTION")) {
                inSection = true;
                continue;
            }

            if (line.toUpperCase().contains("TOTAL DUE")) {
                break;
            }

            if (inSection && !line.isEmpty()) {
                String description = "";
                String amount = "";

                if (line.contains("$")) {
                    int dollarIndex = line.lastIndexOf("$");
                    amount = line.substring(dollarIndex + 1).replace(",", "").trim();
                    String beforeAmount = line.substring(0, dollarIndex).trim();
                    description = beforeAmount.replaceAll("\\d+\\.\\d+$", "").trim();
                } else if (line.contains("-")) {
                    int dashIndex = line.lastIndexOf("-");
                    amount = line.substring(dashIndex + 1).replace(",", "").replace("$", "").trim();
                    description = line.substring(0, dashIndex).trim();
                }

                if (!description.isEmpty() && !amount.isEmpty()) {
                    System.out.println("Description: " + description);
                    System.out.println("Amount: " + amount);
                    System.out.println("-----");
                }
            }
        }
    }
}
