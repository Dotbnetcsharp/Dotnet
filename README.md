Dev Note:
In the stage environment, Cosmos DB is unable to process large data requests simultaneously. We tested the same request locally by manually setting the RU/s to 40,000, and we successfully retrieved all 3,000 records. However, in the stage environment, the RU/s is set to only 4,000, which is causing the error: "Request rate is large."

Fix:
To resolve this issue, we need to increase the RU/s scale in the stage and other environments.

Reference:
Please find the attached screenshot for the local test results.
