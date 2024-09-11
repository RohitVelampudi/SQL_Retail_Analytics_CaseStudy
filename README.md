# SQL_Retail_Analytics_CaseStudy

## 1. Identify Duplicates

The script first identifies duplicate `TransactionID` values in the `Sales_Transaction` table using the following query:

```sql
select TransactionID, count(*)
from Sales_Transaction
group by TransactionID
having count(*) > 1;

| TransactionID | count(*) |
|---------------|----------|
| 4999          | 2        |
| 5000          | 2        |

| TransactionID | CustomerID | ProductID | QuantityPurchased | TransactionDate | Price |
|---------------|------------|-----------|-------------------|-----------------|-------|
| 1             | 103        | 120       | 3                 | 2023-01-01      | 30.43 |
| 2             | 436        | 126       | 1                 | 2023-01-01      | 15.19 |
| 3             | 861        | 55        | 3                 | 2023-01-01      | 67.76 |
| 4             | 271        | 27        | 2                 | 2023-01-01      | 65.77 |
| 5             | 107        | 118       | 1                 | 2023-01-01      | 14.55 |
| 6             | 72         | 53        | 1                 | 2023-01-01      | 26.27 |
| 7             | 701        | 39        | 2                 | 2023-01-01      | 95.92 |
| 8             | 21         | 65        | 4                 | 2023-01-01      | 17.19 |
| 9             | 615        | 145       | 4                 | 2023-01-01      | 66.00 |
| 10            | 122        | 158       | 2                 | 2023-01-01      | 22.27 |
