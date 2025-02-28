import java.text.DecimalFormat;

DecimalFormat df = new DecimalFormat("0.00");
double taxValue = ScraperUtils.getDoubleValue(installmentAmt1) + ScraperUtils.getDoubleValue(installmentAmt2);
totalTax1 = df.format(taxValue);
