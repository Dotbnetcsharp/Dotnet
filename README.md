// Parse the HTML response from the profile page
Document doc = Jsoup.parse(profileResponseBody);

// Base URL for relative links
String baseUrl = "https://assessment.nnva.gov/PT/";

// Individual variables
String valuesLink = null;
String salesLink = null;
String taxesDueDetailLink = null;

// Extract "Values" link
Element valuesEl = doc.select("a:containsOwn(Values)").first();
if (valuesEl != null) {
    valuesLink = valuesEl.attr("href");
    if (!valuesLink.startsWith("http")) {
        valuesLink = baseUrl + valuesLink.replaceFirst("^\\./", "");
    }
}

// Extract "Sales" link
Element salesEl = doc.select("a:containsOwn(Sales)").first();
if (salesEl != null) {
    salesLink = salesEl.attr("href");
    if (!salesLink.startsWith("http")) {
        salesLink = baseUrl + salesLink.replaceFirst("^\\./", "");
    }
}

// Extract "Taxes Due Detail" link
Element taxesEl = doc.select("a:containsOwn(Taxes Due Detail)").first();
if (taxesEl != null) {
    taxesDueDetailLink = taxesEl.attr("href");
    if (!taxesDueDetailLink.startsWith("http")) {
        taxesDueDetailLink = baseUrl + taxesDueDetailLink.replaceFirst("^\\./", "");
    }
}

// Print for verification
System.out.println("Values Link: " + valuesLink);
System.out.println("Sales Link: " + salesLink);
System.out.println("Taxes Due Detail Link: " + taxesDueDetailLink);
