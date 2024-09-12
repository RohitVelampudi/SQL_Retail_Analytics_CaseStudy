# SQL_Retail_Analytics_CaseStudy

## 1. Identify Duplicates

The script first identifies duplicate `TransactionID` values in the `Sales_Transaction` table using the following query:

````sql
select TransactionID, count(*)
from Sales_Transaction
group by TransactionID
having count(*) > 1;
````

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

## 2. Price Discrepancy Query

Identify discrepancies in the price of the same product between the `sales_transaction` and `product_inventory` tables. Update those discrepancies to ensure that the prices match in both tables.

```sql
SELECT 
    st.TransactionID, 
    st.Price AS TransactionPrice, 
    pi.Price AS InventoryPrice 
FROM 
    Sales_Transaction st 
JOIN 
    Product_Inventory pi 
ON 
    st.ProductID = pi.ProductID 
AND 
    st.Price <> pi.Price;
```

| TransactionID | TransactionPrice | InventoryPrice |
|---------------|------------------|----------------|
| 4968          | 9312             | 93.12          |
| 4754          | 9312             | 93.12          |
| 4532          | 9312             | 93.12          |
| ...           | ...              | ...            |

| TransactionID | CustomerID | ProductID | QuantityPurchased | TransactionDate | Price |
|---------------|------------|-----------|-------------------|-----------------|-------|
| 1             | 103        | 120       | 3                 | 2023-01-01      | 30.43 |
| 2             | 436        | 126       | 1                 | 2023-01-01      | 15.19 |
| 3             | 861        | 55        | 3                 | 2023-01-01      | 67.76 |
| ...           | ...        | ...       | ...               | ...             | ...   |

## 3. Identify Null Values

To count the number of records where the `Location` column is null, use the following query:

```sql
SELECT COUNT(*) 
FROM customer_profiles 
WHERE Location IS NULL;
```
| count(*) |
|----------|
|       13 |

| CustomerID | Age | Gender | Location | JoinDate  |
|------------|-----|--------|----------|-----------|
| 1          | 63  | Other  | East     | 01/01/20  |
| 2          | 63  | Male   | North    | 02/01/20  |
| 3          | 34  | Other  | North    | 03/01/20  |
| 4          | 19  | Other  | unknown  | 04/01/20  |
| 5          | 57  | Male   | North    | 05/01/20  |
| 6          | 22  | Other  | South    | 06/01/20  |
| 7          | 56  | Other  | East     | 07/01/20  |
| 8          | 65  | Female | East     | 08/01/20  |
| 9          | 33  | Male   | West     | 09/01/20  |
| 10         | 34  | Male   | East     | 10/01/20  |


## 4. Cleaning the DATE Column in the Dataset

```sql
alter table Sales_transaction add TransactionDate_updated DATE;
update Sales_transaction set TransactionDate_updated=DATE_FORMAT(TransactionDate, '%Y-%m-%d');
select * from Sales_transaction;
```
+---------------+------------+-----------+-------------------+-----------------+-------+-------------------------+
| TransactionID | CustomerID | ProductID | QuantityPurchased | TransactionDate | Price | TransactionDate_updated |
+---------------+------------+-----------+-------------------+-----------------+-------+-------------------------+
|             1 |        103 |       120 |                 3 | 2023-01-01      | 30.43 | 2023-01-01              |
|             2 |        436 |       126 |                 1 | 2023-01-01      | 15.19 | 2023-01-01              |
|             3 |        861 |        55 |                 3 | 2023-01-01      | 67.76 | 2023-01-01              |
|             4 |        271 |        27 |                 2 | 2023-01-01      | 65.77 | 2023-01-01              |
|             5 |        107 |       118 |                 1 | 2023-01-01      | 14.55 | 2023-01-01              |
|             6 |         72 |        53 |                 1 | 2023-01-01      | 26.27 | 2023-01-01              |
|             7 |        701 |        39 |                 2 | 2023-01-01      | 95.92 | 2023-01-01              |
|             8 |         21 |        65 |                 4 | 2023-01-01      | 17.19 | 2023-01-01              |
|             9 |        615 |       145 |                 4 | 2023-01-01      | 66.00 | 2023-01-01              |
|            10 |        122 |       158 |                 2 | 2023-01-01      | 22.27 | 2023-01-01              |
|            11 |        467 |       181 |                 2 | 2023-01-01      | 69.00 | 2023-01-01              |
+---------------+------------+-----------+-------------------+-----------------+-------+-------------------------+



