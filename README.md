import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;

public class ReportUrlExtractor {
    public static void main(String[] args) {
        String html = taxdueResponseBody; // Your HTML content here

        Document doc = Jsoup.parse(html);

        Element div7 = doc.getElementById("datalet_div_7");
        String url7 = extractFullUrl(div7);

        Element div8 = doc.getElementById("datalet_div_8");
        String url8 = extractFullUrl(div8);

        System.out.println("Generate Report URL 1 (div_7): " + url7);
        System.out.println("Generate Report URL 2 (div_8): " + url8);
    }

    private static String extractFullUrl(Element div) {
        if (div == null) return null;

        String htmlContent = div.html();

        String marker = "window.open(";
        int start = htmlContent.indexOf(marker);
        if (start == -1) return null;

        int openParen = htmlContent.indexOf("(", start);
        int closeParen = htmlContent.indexOf(")", openParen);
        if (openParen == -1 || closeParen == -1) return null;

        String expression = htmlContent.substring(openParen + 1, closeParen).trim();

        if (expression.startsWith("\"")) expression = expression.substring(1);
        if (expression.endsWith("\"")) expression = expression.substring(0, expression.length() - 1);

        expression = expression.replaceAll("\"\\s*\\+\\s*", "{");
        expression = expression.replaceAll("\\s*\\+\\s*\"", "}");
        expression = expression.replace("\\\"", "\"").replaceAll("\\s+", "");

        return expression;
    }
}
