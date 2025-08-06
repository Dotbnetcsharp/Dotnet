import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;

public class ReportUrlExtractor {
    public static void main(String[] args) {
        String html = taxdueResponseBody; // HTML content from response

        // Parse HTML
        Document doc = Jsoup.parse(html);

        // Extract from datalet_div_7
        Element div7 = doc.getElementById("datalet_div_7");
        String url7 = extractFullUrl(div7);

        // Extract from datalet_div_8
        Element div8 = doc.getElementById("datalet_div_8");
        String url8 = extractFullUrl(div8);

        System.out.println("Generate Report URL 1 (div_7): " + url7);
        System.out.println("Generate Report URL 2 (div_8): " + url8);
    }

    private static String extractFullUrl(Element div) {
        if (div == null) return null;

        // Convert inner HTML to string
        String htmlContent = div.html();

        // Find start of window.open("...") call
        String marker = "window.open(\"";
        int start = htmlContent.indexOf(marker);
        if (start == -1) return null;

        start += marker.length();
        int end = htmlContent.indexOf("\"", start);
        if (end == -1) return null;

        String foundUrl = htmlContent.substring(start, end); // e.g. "/ssrs/inline/..."

        // Try to extract domain from somewhere in this div
        // Look for a <a> tag or <script> or any full URL in the div
        String fullDomain = extractDomainFromDiv(htmlContent);

        if (foundUrl.startsWith("http")) {
            return foundUrl; // already full
        } else if (fullDomain != null) {
            return fullDomain + foundUrl;
        } else {
            return foundUrl; // fallback to relative
        }
    }

    private static String extractDomainFromDiv(String htmlContent) {
        // Look for any http or https link in the HTML to guess the domain
        String token = "https://";
        int index = htmlContent.indexOf(token);
        if (index == -1) return null;

        int end = htmlContent.indexOf("/", index + token.length());
        if (end == -1) return null;

        // We now have: https://domain.com
        return htmlContent.substring(index, end);
    }
}
