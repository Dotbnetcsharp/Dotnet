
import java.util.regex.*;

public class Main {
    public static void main(String[] args) {
        String cookie = "dnn IsMobile=False; language=en-US; ASPXANONYMOUS=XG0-Je8_EIofYM1Wi7unGf2bulV6g-wDr1zXEWak1X6anmJSqGyzHr1euQkvnWqr6f2nfg4oPIiHCK8-0Fv3a1w4QFCj2yazEyI2ew4K4Ytbecjm0; ASP.NET_SessionId=m1r2jpxosz42sswp10uugvlw";

        Pattern pattern = Pattern.compile("ASP.NET_SessionId=([^;]+)");
        Matcher matcher = pattern.matcher(cookie);

        if (matcher.find()) {
            System.out.println("ASP.NET SessionId: " + matcher.group(1));
        } else {
            System.out.println("ASP.NET SessionId not found.");
        }
    }
}




Dev Note: The API first checks for DocumentNumber in the request. If DocumentNumber is not provided, then as a second priority, it will check for CmtDocumentNo and fetch records using it.



Dev Note: In the request, the documentno field is incorrect. We need to pass DocumentNumber instead, as it is correctly defined in the DTO.


using System;
using System.Text.RegularExpressions;

public class InstrumentParserService
{
    public string ParseInstrumentInput(string input)
    {
        if (string.IsNullOrWhiteSpace(input)) return "Invalid input";

        // Remove all delimiters
        string cleanedInput = Regex.Replace(input, @"[\/.,:\s\-_\\n]", "");

        // Extract year (first 4 or last 4 digits)
        if (cleanedInput.Length > 4)
        {
            string firstFour = cleanedInput.Substring(0, 4);
            string lastFour = cleanedInput.Substring(cleanedInput.Length - 4);

            if (IsValidYear(firstFour)) return $"{firstFour}.{cleanedInput.Substring(4)}";
            if (IsValidYear(lastFour)) return $"{lastFour}.{cleanedInput.Substring(0, cleanedInput.Length - 4)}";
        }

        return "Invalid format";
    }

    private bool IsValidYear(string yearStr) => int.TryParse(yearStr, out int year) && year >= 2020 && year <= DateTime.Now.Year;
}





Dev Note:

Instrument search type information is not available in type-ahead results. We are only using it to pass as a parameter to the stored procedure (SP).



Task: Standardizing Instrument Number Lookup in the NTP API

Your system needs to support instrument number lookups in multiple formats. Users can enter instrument numbers with or without delimiters, and the system should correctly identify, format, and return standardized values.


---

Supported Formats & How to Handle Them

1. Year-Instrument Number Format (YYYY.InstrumentNumber)

✅ What to do?

Users enter the year first, followed by the instrument number.

The input can have delimiters (/ . \ : - _).


✅ Example Inputs → Expected Outputs:
| Input | Standardized Output | |--------|---------------------| | 2024/123456 | 2024.123456 | | 2023-987654 | 2023.987654 | | 2025.654321 | 2025.654321 |

✅ Implementation Steps:

1. Extract the year (first four digits, must be between 2020 and the current year).


2. Extract the instrument number (remaining digits).


3. Format the output as YYYY.InstrumentNumber.



✅ How to validate?

Enter values with different delimiters and verify that the API correctly formats them.



---

2. Instrument Number - Year Format (Reverse Format)

✅ What to do?

Users enter the instrument number first, followed by the year.

The input can have delimiters (/ . \ : - _).


✅ Example Inputs → Expected Outputs:
| Input | Standardized Output | |--------|---------------------| | 123456-2024 | 2024.123456 | | 987654/2025 | 2025.987654 | | 654321.2023 | 2023.654321 |

✅ Implementation Steps:

1. Extract the year (last four digits, must be between 2020 and the current year).


2. Extract the instrument number (remaining digits).


3. Rearrange it into standard format → YYYY.InstrumentNumber.



✅ How to validate?

Enter values in reverse format and ensure the API correctly identifies the year and instrument number.



---

3. Year-Instrument Number Without Delimiters

✅ What to do?

Users may not use any delimiters, e.g., 2024123456.

The system should detect the first four digits as the year and the rest as the instrument number.


✅ Example Inputs → Expected Outputs:
| Input | Standardized Output | |--------|---------------------| | 2024123456 | 2024.123456 | | 2023987654 | 2023.987654 | | 2025654321 | 2025.654321 |

✅ Implementation Steps:

1. Extract the first four digits as the year (check if it is between 2020 and the current year).


2. Extract the remaining digits as the instrument number.


3. Format as YYYY.InstrumentNumber.



✅ How to validate?

Test inputs without delimiters and check if the API correctly identifies the year and instrument number.



---

4. Instrument Number - Year Without Delimiters (Reverse Format)

✅ What to do?

Users enter the instrument number first, followed by the year, without delimiters.

Example: 1234562024.


✅ Example Inputs → Expected Outputs:
| Input | Recognized Year | Instrument Number | Standardized Output | |--------|---------------|------------------|---------------------| | 1234562024 | 2024 | 123456 | 2024.123456 | | 9876542025 | 2025 | 987654 | 2025.987654 | | 6543212023 | 2023 | 654321 | 2023.654321 |

✅ Implementation Steps:

1. Identify the last four digits as the year (must be between 2020 and the current year).


2. Extract the remaining digits as the instrument number.


3. Rearrange it into standard format → YYYY.InstrumentNumber.



✅ How to validate?

Enter values without delimiters in reverse format and verify that the system correctly identifies the year and instrument number.



---

5. Book-Page Format Lookup

✅ What to do?

Allow users to input book and page numbers separately.

Example:

Book: "ABC123"

Page: "45"


The system should return matching records based on book and page number.


✅ How to validate?

Enter different book and page values and ensure the API correctly finds matching data.



---

Final Steps to Implement in .NET Core API

1. Parse the input string to detect whether it follows:

Year-InstrumentNumber

InstrumentNumber-Year

With or without delimiters



2. Extract and validate the year (2020–current year) and instrument number.


3. Standardize the output format as YYYY.InstrumentNumber.


4. Write unit tests to check all scenarios.


5. Ensure API returns correct standardized values for each input format.




---

Example API Response

Input: 123456-2024

Output:

{
  "input": "123456-2024",
  "standardized": "2024.123456",
  "status": "success"
}


---

This ensures that no matter how users enter the instrument number, your API will always return a consistent, standardized format.

Let me know if you need help with implementation or code samples!


