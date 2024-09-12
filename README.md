# SQL_Retail_Analytics_CaseStudy

## 1. Identify Duplicates

The script first identifies duplicate `TransactionID` values in the `Sales_Transaction` table using the following query:

````sql
select TransactionID, count(*)
from Sales_Transaction
group by TransactionID
having count(*) > 1;
````

Output:

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

Output:

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
Output:

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
Output:

| TransactionID | CustomerID | ProductID | QuantityPurchased | TransactionDate | Price | TransactionDate_updated |
|---------------|------------|-----------|-------------------|-----------------||------|-------------------------|
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


## 5. Summarizing Total Sales and Quantities Sold per Product

```sql
SELECT ProductID, 
       SUM(QuantityPurchased) AS TotalUnitsSold, 
       SUM(Price * QuantityPurchased) AS TotalSales 
FROM Sales_transaction 
GROUP BY ProductID 
ORDER BY TotalSales DESC;
```
This query summarizes the total sales and quantities sold per product by the company. It calculates:

- **TotalUnitsSold**: The total number of units sold for each product.
- **TotalSales**: The total revenue generated for each product, calculated by multiplying the price by the quantity purchased.

The results are grouped by `ProductID` and ordered by `TotalSales` in descending order to show the highest-grossing products first.

Output:

| ProductID | TotalUnitsSold | TotalSales         |
|-----------|----------------|--------------------|
|        17 |            100 |               9450 |
|        87 |             92 |  7817.239999999998 |
|       179 |             86 |  7388.259999999998 |
|        96 |             72 | 7132.3200000000015 |
|        54 |             86 | 7052.8600000000015 |
|       187 |             82 |  6915.880000000003 |
|       156 |             76 |  6827.840000000002 |
|        57 |             78 |  6622.199999999999 |
|       200 |             69 |  6479.790000000001 |
|       127 |             68 |  6415.799999999999 |
|        28 |             69 |  6386.640000000001 |
|       106 |             63 |  6262.829999999999 |
|       104 |             72 |  6230.160000000001 |
|       195 |             87 |  6229.199999999999 |
|       103 |             66 |            6191.46 |
|        85 |             62 |  6188.219999999998 |
|       190 |             66 |            6126.12 |
|        75 |             63 |  6094.619999999998 |
|        36 |             61 |  6067.060000000001 |
|       119 |             67 |  6052.109999999999 |
|       116 |             75 |  6033.750000000001 |
|        76 |             64 |               6032 |

## 6. Counting the Number of Transactions per Customer to Understand Purchase Frequency

```sql
SELECT CustomerID, 
       COUNT(TransactionID) AS NumberOfTransactions
FROM Sales_transaction
GROUP BY CustomerID
ORDER BY NumberOfTransactions DESC;
```

This query counts the number of transactions made by each customer to understand their purchase frequency. It calculates:

- **NumberOfTransactions**: The total number of transactions for each customer.

The results are grouped by `CustomerID` and ordered by `NumberOfTransactions` in descending order to identify the customers with the highest transaction frequency.

Output:

| CustomerID | NumberOfTransactions |
|------------|----------------------|
|        664 |                   14 |
|        958 |                   12 |
|         99 |                   12 |
|        113 |                   12 |
|        929 |                   12 |
|        936 |                   12 |
|        670 |                   12 |
|         39 |                   12 |
|        277 |                   11 |
|        476 |                   11 |
|        776 |                   11 |
|        727 |                   11 |
|        648 |                   11 |
|        613 |                   11 |
|        268 |                   11 |
|        881 |                   11 |
|        659 |                   11 |
|        704 |                   11 |
|         75 |                   11 |


## 7. Evaluating Product Category Performance Based on Total Sales

```sql
SELECT pi.Category,  
       SUM(st.QuantityPurchased) AS TotalUnitsSold,  
       SUM(st.QuantityPurchased * st.Price) AS TotalSales
FROM Sales_transaction st 
JOIN product_inventory pi 
ON pi.ProductID = st.ProductID 
GROUP BY pi.Category
ORDER BY TotalSales DESC;
```

This query evaluates the performance of product categories based on total sales, helping to identify which categories should be promoted in marketing campaigns. It calculates:

- **TotalUnitsSold**: The total number of units sold in each product category.
- **TotalSales**: The total sales revenue generated for each product category.

The results are grouped by `Category` and ordered by `TotalSales` in descending order to highlight the most successful product categories.

Output:

| Category        | TotalUnitsSold | TotalSales         |
|-----------------|----------------|--------------------|
| Home & Kitchen  |           3477 | 217755.93999999945 |
| Electronics     |           3037 |  177548.4799999996 |
| Clothing        |           2810 | 162874.21000000057 |
| Beauty & Health |           3001 | 143824.98999999947 |


## 8. Finding the Top 10 Products with the Highest Total Sales Revenue

```sql
SELECT ProductID,  
       SUM(QuantityPurchased * Price) AS TotalRevenue
FROM Sales_transaction
GROUP BY ProductID
ORDER BY TotalRevenue DESC
LIMIT 10;
```

This query finds the top 10 products with the highest total sales revenue from the sales transactions. It helps the company identify high-sales products that need to be prioritized to further increase revenue. It calculates:

- **TotalRevenue**: The total revenue generated by each product.

The results are grouped by `ProductID`, ordered by `TotalRevenue` in descending order, and limited to the top 10 products.

Output:

| ProductID | TotalRevenue       |
|-----------|--------------------|
|        17 |               9450 |
|        87 |  7817.239999999998 |
|       179 |  7388.259999999998 |
|        96 | 7132.3200000000015 |
|        54 | 7052.8600000000015 |
|       187 |  6915.880000000003 |
|       156 |  6827.840000000002 |
|        57 |  6622.199999999999 |
|       200 |  6479.790000000001 |
|       127 |  6415.799999999999 |


## 9. Finding the Ten Products with the Least Amount of Units Sold

```sql
SELECT ProductID,  
       SUM(QuantityPurchased) AS TotalUnitsSold
FROM Sales_transaction
GROUP BY ProductID
HAVING TotalUnitsSold > 0
ORDER BY TotalUnitsSold ASC
LIMIT 10;
```

This query finds the ten products with the least number of units sold, provided that at least one unit was sold for those products. It calculates:

- **TotalUnitsSold**: The total number of units sold for each product.

The results are grouped by `ProductID`, filtered to only include products with more than zero units sold, ordered by `TotalUnitsSold` in ascending order, and limited to the ten products with the fewest sales.

Output:

| ProductID | TotalUnitsSold |
|-----------|----------------|
|       142 |             27 |
|        33 |             31 |
|       174 |             33 |
|       159 |             35 |
|        60 |             35 |
|        41 |             35 |
|        91 |             35 |
|       198 |             36 |
|       124 |             39 |
|       163 |             39 |

## 10. Identifying Sales Trend to Understand Revenue Pattern

```sql
SELECT TransactionDate AS DATETRANS,
       COUNT(TransactionID) AS Transaction_count,
       SUM(QuantityPurchased) AS TotalUnitsSold,
       SUM(QuantityPurchased * Price) AS TotalSales
FROM Sales_transaction
GROUP BY TransactionDate
ORDER BY TransactionDate DESC;
```

This query identifies the sales trend to understand the revenue pattern of the company. It calculates:

- **Transaction_count**: The number of transactions on each date.
- **TotalUnitsSold**: The total number of units sold on each date.
- **TotalSales**: The total revenue generated on each date.

The results are grouped by `TransactionDate` and ordered by `TransactionDate` in descending order to show the most recent dates first.

Output:


| DATETRANS  | Transaction_count | TotalUnitsSold | TotalSales         |
|------------|-------------------|----------------|--------------------|
| 2023-07-28 |                 8 |             18 | 1158.8600000000001 |
| 2023-07-27 |                24 |             58 | 3065.8099999999995 |
| 2023-07-26 |                24 |             58 | 3168.0400000000004 |
| 2023-07-25 |                24 |             54 |            2734.26 |
| 2023-07-24 |                24 |             63 |  3691.079999999999 |
| 2023-07-23 |                24 |             57 | 3578.5800000000004 |
| 2023-07-22 |                24 |             62 |             3350.8 |
| 2023-07-21 |                24 |             61 |            3443.72 |
| 2023-07-20 |                24 |             60 |            3216.57 |
| 2023-07-19 |                24 |             52 | 2068.5000000000005 |
| 2023-07-18 |                24 |             57 | 3251.0699999999997 |
| 2023-07-17 |                24 |             56 | 3051.5099999999998 |
| 2023-07-16 |                24 |             66 |            3145.46 |

## 11. Understanding Month-on-Month Growth Rate of Sales

```sql
WITH MonthlySales AS (
    SELECT
        MONTH(TransactionDate) AS month,
        SUM(QuantityPurchased * Price) AS total_sales
    FROM
        Sales_transaction
    GROUP BY
        MONTH(TransactionDate)
),
GrowthRate AS (
    SELECT
        month,
        total_sales,
        LAG(total_sales) OVER (ORDER BY month) AS previous_month_sales
    FROM
        MonthlySales
)
SELECT
    month,
    total_sales,
    previous_month_sales,
    CASE
        WHEN previous_month_sales IS NULL OR previous_month_sales = 0 THEN NULL
        ELSE ((total_sales - previous_month_sales) / previous_month_sales) * 100
    END AS mom_growth_percentage
FROM
    GrowthRate
ORDER BY
    month;
```

This query calculates the month-on-month growth rate of sales to understand the growth trend of the company. It calculates:

- **total_sales**: The total sales for each month.
- **previous_month_sales**: The total sales for the previous month.
- **mom_growth_percentage**: The month-on-month growth percentage, showing how sales have changed compared to the previous month.

The results are grouped by month and ordered by month to track the growth trend over time.

Output:

| month | total_sales        | previous_month_sales | mom_growth_percentage |
|-------|--------------------|----------------------|-----------------------|
|     1 | 104289.17999999993 |                 NULL |                  NULL |
|     2 |  96690.98999999995 |   104289.17999999993 |    -7.285693491884769 |
|     3 |          103271.49 |    96690.98999999995 |     6.805701337839299 |
|     4 | 101561.09000000014 |            103271.49 |    -1.656217025628141 |
|     5 | 102998.83999999995 |   101561.09000000014 |    1.4156504228142972 |
|     6 |          102210.28 |   102998.83999999995 |   -0.7656008553105592 |
|     7 |  90981.75000000004 |            102210.28 |   -10.985714939827927 |

## 12. Identifying High Frequency Purchase Customers

```sql
SELECT CustomerID, 
       COUNT(TransactionID) AS NumberOfTransactions,
       SUM(QuantityPurchased * Price) AS TotalSpent
FROM Sales_transaction
GROUP BY CustomerID
HAVING TotalSpent > 1000 AND NumberOfTransactions > 10
ORDER BY TotalSpent DESC;
```

This query identifies customers who are high-frequency purchasers by describing the number of transactions and the total amount spent. It filters customers based on:

- **TotalSpent**: Customers who have spent more than 1000 units of currency.
- **NumberOfTransactions**: Customers who have made more than 10 transactions.

The results are grouped by `CustomerID` and ordered by `TotalSpent` in descending order to highlight the highest spending customers.

Output:

| CustomerID | NumberOfTransactions | TotalSpent         |
|------------|----------------------|--------------------|
|        936 |                   12 | 2834.4700000000003 |
|        664 |                   14 |            2519.04 |
|        670 |                   12 |            2432.15 |
|         39 |                   12 |            2221.29 |
|        958 |                   12 |            2104.71 |
|         75 |                   11 | 1862.7299999999998 |
|        476 |                   11 | 1821.4399999999998 |
|        929 |                   12 |            1798.42 |
|        881 |                   11 | 1713.2300000000002 |
|        704 |                   11 |            1628.34 |
|        648 |                   11 | 1572.9999999999998 |
|        776 |                   11 | 1551.0100000000002 |
|         99 |                   12 | 1547.3599999999997 |
|        113 |                   12 |            1525.46 |
|        613 |                   11 | 1451.2700000000002 |
|        727 |                   11 | 1415.6499999999999 |
|        676 |                   11 | 1196.9699999999998 |
|        277 |                   11 | 1163.3799999999999 |


## 13. Identifying Occasional Customers with Low Purchase Frequency

```sql
SELECT CustomerID, 
       COUNT(TransactionID) AS NumberOfTransactions,
       SUM(QuantityPurchased * Price) AS TotalSpent
FROM Sales_transaction
GROUP BY CustomerID
HAVING NumberOfTransactions <= 2
ORDER BY NumberOfTransactions ASC, TotalSpent DESC;
```

This query identifies customers who are occasional or have low purchase frequency by describing the number of transactions and the total amount spent. It filters customers based on:

- **NumberOfTransactions**: Customers who have made 2 or fewer transactions.
- **TotalSpent**: The total amount spent by each customer.

The results are grouped by `CustomerID` and ordered first by `NumberOfTransactions` in ascending order and then by `TotalSpent` in descending order to highlight those with the fewest transactions but potentially high spending.

Output:

| CustomerID | NumberOfTransactions | TotalSpent         |
+------------+----------------------+--------------------+
|         94 |                    1 |             360.64 |
|        181 |                    1 |             298.23 |
|        979 |                    1 |             265.16 |
|        317 |                    1 |             257.73 |
|        479 |                    1 |             254.91 |
|        799 |                    1 | 254.70000000000002 |
|         45 |                    1 | 241.35000000000002 |
|        890 |                    1 |              171.4 |
|        528 |                    1 |             170.36 |
|        ... |                   .. |                ... |
|        534 |                    2 |             565.96 |
|        591 |                    2 |             538.79 |
|        347 |                    2 |             530.48 |
|        245 |                    2 |             522.53 |
|        241 |                    2 |             492.18 |
|        ... |                   .. |                ... |

## 14. Identifying Repeat Purchases by Each Customer for Each Product

```sql
SELECT CustomerID, 
       ProductID,
       COUNT(TransactionID) AS TimesPurchased
FROM Sales_transaction
GROUP BY CustomerID, ProductID
HAVING TimesPurchased > 1
ORDER BY TimesPurchased DESC;
```

This query describes the total number of purchases made by each customer for each `ProductID` to understand repeat customers. It calculates:

- **TimesPurchased**: The number of times each customer has purchased each product.

The results are grouped by `CustomerID` and `ProductID`, filtered to show only those with more than one purchase, and ordered by `TimesPurchased` in descending order to highlight the most frequent repeat purchases.

Output:

| CustomerID | ProductID | TimesPurchased |
+------------+-----------+----------------+
|        685 |       192 |              3 |
|        758 |        31 |              2 |
|         75 |        47 |              2 |
|        233 |        68 |              2 |
|        133 |       147 |              2 |
|        602 |       101 |              2 |
|        584 |        83 |              2 |
|        302 |        11 |              2 |
|        467 |        93 |              2 |
|        227 |        40 |              2 |
|        974 |        27 |              2 |
|        506 |       105 |              2 |
|        912 |       103 |              2 |
|        229 |        32 |              2 |
|        ... |       ... |             .. |

## 15. Understanding Customer Loyalty Based on Purchase Duration

```sql
WITH purchase_dates AS (
    SELECT CustomerID,
           MIN(STR_TO_DATE(TransactionDate, '%Y-%m-%d')) AS FirstPurchase,
           MAX(STR_TO_DATE(TransactionDate, '%Y-%m-%d')) AS LastPurchase
    FROM Sales_transaction
    GROUP BY CustomerID
)
SELECT CustomerID,
       FirstPurchase,
       LastPurchase,
       DATEDIFF(LastPurchase, FirstPurchase) AS DaysBetweenPurchases
FROM purchase_dates
WHERE DATEDIFF(LastPurchase, FirstPurchase) > 0
ORDER BY DaysBetweenPurchases DESC;
```

This query describes the duration between the first and last purchase of each customer to understand their loyalty. It calculates:

- **FirstPurchase**: The date of the customer's first purchase.
- **LastPurchase**: The date of the customer's last purchase.
- **DaysBetweenPurchases**: The number of days between the first and last purchase.

The results are grouped by `CustomerID`, filtered to show only those with a positive duration between purchases, and ordered by `DaysBetweenPurchases` in descending order to highlight customers with the longest periods between their first and last purchases.

Output:

| CustomerID | FirstPurchase | LastPurchase | DaysBetweenPurchases |
|------------|---------------|--------------|----------------------|
|        215 | 2023-01-01    | 2023-07-28   |                  208 |
|        414 | 2023-01-02    | 2023-07-26   |                  205 |
|        664 | 2023-01-01    | 2023-07-24   |                  204 |
|        701 | 2023-01-01    | 2023-07-23   |                  203 |
|        277 | 2023-01-02    | 2023-07-24   |                  203 |
|         22 | 2023-01-02    | 2023-07-24   |                  203 |
|        976 | 2023-01-02    | 2023-07-24   |                  203 |
|        647 | 2023-01-03    | 2023-07-25   |                  203 |
|        162 | 2023-01-05    | 2023-07-27   |                  203 |
|        806 | 2023-01-02    | 2023-07-23   |                  202 |
|        511 | 2023-01-02    | 2023-07-23   |                  202 |
|        703 | 2023-01-05    | 2023-07-26   |                  202 |
|        188 | 2023-01-06    | 2023-07-27   |                  202 |
|        380 | 2023-01-06    | 2023-07-27   |                  202 |
|        566 | 2023-01-04    | 2023-07-24   |                  201 |
|        748 | 2023-01-02    | 2023-07-21   |                  200 |
|        687 | 2023-01-03    | 2023-07-22   |                  200 |
|        943 | 2023-01-04    | 2023-07-23   |                  200 |
|         65 | 2023-01-06    | 2023-07-25   |                  200 |
|        648 | 2023-01-06    | 2023-07-25   |                  200 |

## 16. Segmenting Customers Based on Total Quantity Purchased and Counting Customers in Each Segment

```sql
CREATE TABLE customer_SEGMENT AS
SELECT 
    CustomerID,
    CASE 
        WHEN TotalQuantity > 30 THEN 'High'
        WHEN TotalQuantity BETWEEN 10 AND 30 THEN 'Med'
        WHEN TotalQuantity BETWEEN 1 AND 10 THEN 'Low'
        ELSE 'None'
    END AS CustomerSegment
FROM (
    SELECT 
        CustomerID,
        SUM(QuantityPurchased) AS TotalQuantity
    FROM 
        Sales_transaction
    GROUP BY 
        CustomerID
) AS totquant;

SELECT 
    CustomerSegment, 
    COUNT(*) AS NumberOfCustomers
FROM 
    customer_SEGMENT
GROUP BY 
    CustomerSegment;
```

This query segments customers based on the total quantity of products they have purchased and counts the number of customers in each segment. The segments are:

- **High**: Customers who purchased more than 30 units.
- **Med**: Customers who purchased between 10 and 30 units.
- **Low**: Customers who purchased between 1 and 10 units.
- **None**: Customers who purchased 0 units or none.

The results are grouped by `CustomerSegment` 

| CustomerSegment | COUNT(*) |
|-----------------|----------|
| Med             |      627 |
| Low             |      355 |
| High            |        7 |

