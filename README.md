
boolean isInt = str != null && str.matches("[-+]?\\d{1,3}(,\\d{3})*(\\.0+)?|[-+]?\\d+(\\.0+)?");
boolean isInt = str != null && !str.isEmpty() && str.matches("-?\\d+");
