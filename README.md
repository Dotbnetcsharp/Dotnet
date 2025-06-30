NSX Starter API – User Story Full Explanation

🧑‍💼 Business Need

As an NSX APT user, you want to search for starter information using just a Property ID and Product Type, and receive detailed results including images, data, and policies.


---

🔹 Step-by-Step Functional Requirement

🔸 1. Property Search Must Support:

Current NTP Property ID Search

Scheduled Search


🔍 Must also support searching using the FA's BefJD value.


---

🔸 2. Validation Rules

Use validation rules from PropertyIDSearch (User Story #693557), except for these adjustments:

If a disallowed field is sent for a product, return:

> "Searched parameters not available for specified product. Please adjust request."



If TitleOfficer is provided for starter-only products → ❌ error.



---

🔸 3. Supported Starter-Only Products

✅ Product 1: Starter Search Index Single Properties (PIQ)

Returns list of starters for one specific Property ID (PIQ)


✅ Product 2: Starter Search with Surrounding Properties

Returns index for PIQ and neighboring properties via:

Radius from coordinates

Matching Subdivision



✅ Product 3: Specific Starter Request

Returns either:

Image only

Image + Data



✅ Product 4: Recommended Direct Starter

Returns best-fit starter, either:

Image only

Image + Data



✅ Product 5: Recommended Direct Starter or Recommended AP Starter (Subdivided Only)

Returns image or image + data

If not found on PIQ, search adjacent properties



---

🔸 4. Search Qualifiers Allowed (only for some products)

Qualifier	Description

StarterType	e.g., Subdivision, Parcel, Recommended
StarterSource	Internal, External, etc.
TitleCompanyName	Allowed only for some products



---

📥 Request Behavior

🔸 If the product allows qualifiers:

✅ Use them

🔸 If a qualifier is invalid for the product:

❌ Return:

> "Searched parameters not available for specified product. Please adjust request."




---

📤 Response Rules

✅ Always Return:

1. Searched parameters (to allow correlation).


2. Summary cross-reference (property/starter count).


3. Run history (only for orders, not investigations).




---

🏡 Property Search Scenarios

📍 A. No Properties Found

Return:


> "No property match found for <X> parameters entered"




---

📍 B. Multiple Properties Found

Return:


> "Multiple property matches, refine search"



Include:

Property ID

APN

Address

Assessed Owner




---

📍 C. One Property Found → No Starters

Return:


> "No starter match found for the property in question"




---

📦 Starter Data Returned Per Product

🟢 PIQ and Surrounding Properties

Return:

FIPS, State, County, APN

Address (with full breakdown)

Owner name

Subdivision

Legal description

Coordinates

Shane Data (when available)

Starter Index:

Policy Type

Starter Source

Title Company

Policy Details

Commitment/Order/Policy Number

Insured Parties

Recommendation Status

Purchased by Company?




---

🟢 Specific Starter Request, Recommended Starters

Return:

Everything from above +

Full Address split:

Street Number, Name, Type, Post Direction, Unit #


Subdivision + Parcel Details

Full Legal Info (Lot, Block, Plat, Township, Tract, etc.)

Coordinates (Lat/Lon)

Starter Image

Main Starter Info:

Policy Type, Company, Date, Amount, Order #

Loan Info (Loan #, Amount, Date, Instrument No., Book/Page)

Parties (Entity Role, Names)


Additional Flags:

Exam Code

Remarks

Suspend/Error

Property Flags:

Orphan

Commercial

CCR

Claim

MLS Active

Easement

Facts, Corrected File, Completed Claim





---

✅ Special Behavior for Recommended Starters

🔹 If multiple starters found:

> "Multiple starter matches, refine search"



🔹 If none found:

> "No starter match found for the property in question"



🔹 If PIQ has no recommended starter:

Look at adjacent properties

If found → return that REFID’s property data + starter



---

🧪 Acceptance Criteria – You Know It's Done When:

✅ For any Property ID + Product:

You receive a result:

1. Starter data for valid matches


2. Property index if multiple


3. Starter index if multiple starters


4. Appropriate error if:

No property

No starter

Invalid field for product

TitleOfficer sent with starter-only product




✅ If Image expected → It is returned

✅ If Data expected → It is returned

✅ If both missing → Message shown


---

🧾 Error Message Reference

Case	Message

❌ Property not found	"No property match found for <X> parameters entered"
❌ Multiple properties	"Multiple property matches, refine search"
❌ No starter	"No starter match found for the property in question"
❌ Multiple starters	"Multiple starter matches, refine search"
❌ Disallowed field	"Searched parameters not available for specified product. Please adjust request."
❌ TitleOfficer invalid	"Title Officer is not a starter qualifier"
❌ FIPS/County/State missing	"FIPS/State/County needed"
❌ Missing key node	"Key Node not provided"

