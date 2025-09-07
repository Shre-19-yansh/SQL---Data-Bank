A. Customer Nodes Exploration
1.	How many unique nodes are there on the Data Bank system?
```SQL
SELECT DISTINCT(node_id) 
FROM customer_nodes
ORDER BY node_id;
```

<img width="125" height="167" alt="image" src="https://github.com/user-attachments/assets/d95935d8-0b6e-451d-b92c-601189b96740" />

- Total 5 unique nodes which are 1, 2, 3, 4, 5


2.	What is the number of nodes per region?
```SQL
SELECT region_id, COUNT(node_id)
FROM customer_nodes
GROUP BY region_id
ORDER BY region_id;
```

<img width="292" height="178" alt="image" src="https://github.com/user-attachments/assets/aeff3811-ff59-42f1-b7dd-4a267f3cb726" />

- Region 1 has 770 nodes
- Region 2 has 735 nodes
- Region 3 has 714 nodes
- Region 4 has 665 nodes
- Region 5 has 616 nodes

3.	How many customers are allocated to each region?
```SQL
SELECT regions.region_name,regions.region_id, COUNT(node_id)
FROM customer_nodes
JOIN regions
ON customer_nodes.region_id=regions.region_id
GROUP BY regions.region_id, regions.region_name
ORDER BY regions.region_id,regions.region_name;
```
- Region 1, Australia has 770 nodes
- Region 2, America has 735 nodes
- Region 3, Africa has 714 nodes
- Region 4, Asia has 665 nodes
- Region 5, Europe has 616 nodes 

<img width="417" height="170" alt="image" src="https://github.com/user-attachments/assets/3621fd62-3721-4beb-b4ff-de49c06ba1aa" />

 
4.	How many days on average are customers reallocated to a different node?
```SQL
WITH node_days AS (
  SELECT 
    customer_id, 
    node_id,
    end_date - start_date AS days_in_node
  FROM data_bank.customer_nodes
  WHERE end_date != '9999-12-31'
  GROUP BY customer_id, node_id, start_date, end_date
) 
, total_node_days AS (
  SELECT 
    customer_id,
    node_id,
    SUM(days_in_node) AS total_days_in_node
  FROM node_days
  GROUP BY customer_id, node_id
)

SELECT ROUND(AVG(total_days_in_node)) AS avg_node_reallocation_days
FROM total_node_days;
```
<img width="289" height="70" alt="image" src="https://github.com/user-attachments/assets/445d829c-cebe-401f-b5b1-2eca1af58011" />

- Average node reallocation days is 77
 
5.	What is the median, 80th and 95th percentile for this same reallocation days metric for each region?


B. Customer Transactions

1.	What is the unique count and total amount for each transaction type?
```SQL
SELECT txn_type, COUNT(txn_type) AS Total_Count, SUM(txn_amount) AS Total_Amount
FROM customer_transactions
GROUP BY txn_type
ORDER BY txn_type;
```
<img width="401" height="126" alt="image" src="https://github.com/user-attachments/assets/9cf1fe35-1fc8-4f6c-a382-43242eacef5a" />

- Deposit's total count is 2671 and total amount is 13,59,168
- Purchase's total count is 1617 and total amount is 8,06,537
- Withdrawal's total count is 1580 and total amount is 7,93,003
 
2.	What is the average total historical deposit counts and amounts for all customers?
```SQL
SELECT 
    AVG(deposit_count) AS Avg_Deposit_Count,
    AVG(total_deposit_amount) AS Avg_Deposit_Amount
FROM (
    SELECT 
        customer_id,
        COUNT(*) AS deposit_count,
        SUM(txn_amount) AS total_deposit_amount
    FROM customer_transactions
    WHERE txn_type = 'deposit'
    GROUP BY customer_id
) AS customer_summary;
```
<img width="409" height="64" alt="image" src="https://github.com/user-attachments/assets/d982c418-2f28-48f4-9005-0306940d64e5" />

- Average deposit count is 5.3420
- Average deposit amount is 2718.3360
  
 
3.	For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?
```SQL
SELECT 
    Months,
    COUNT(*) AS Qualified_Customers
FROM (
    SELECT 
        customer_id,
        MONTH(txn_date) AS Months,
        SUM(txn_type = 'deposit') AS Total_dep,
        SUM(txn_type = 'purchase') AS Total_pur,
        SUM(txn_type = 'withdrawal') AS Total_with
    FROM customer_transactions
    GROUP BY customer_id, MONTH(txn_date)
    HAVING Total_dep > 1 AND (Total_pur >= 1 OR Total_with >= 1)
) AS filtered_customers
GROUP BY Months
ORDER BY Months;
```
 <img width="304" height="142" alt="image" src="https://github.com/user-attachments/assets/92e2d77d-7c6f-44f9-806d-71e27b8845e1" />

- 168 qualified customer in month 1
- 181 qualified customer in month 2
- 192 qualified customer in month 3
- 70 qualified customer in month 4
  
4.	What is the closing balance for each customer at the end of the month?
```SQL
SELECT 
    customer_id, MONTH(txn_date) AS Months,
    SUM( CASE when txn_type = 'deposit' THEN txn_amount ELSE 0 END )- (
    SUM(CASE WHEN txn_type = 'purchase' THEN txn_amount ELSE 0 END) + SUM(CASE WHEN txn_type = 'withdrawal' THEN txn_amount ELSE 0 END) ) AS Closing_Balance
FROM customer_transactions
GROUP BY customer_id, Months
ORDER BY customer_id, Months
LIMIT 10;
```
<img width="389" height="306" alt="image" src="https://github.com/user-attachments/assets/1f749d68-172c-480c-841a-6d5fb4c278ff" />

- Customer 1 had a closing balance of 312 in month 1.
- Customer 1 had a closing balance of -952 in month 3.
- Customer 2 had a closing balance of 549 in month 1.
- Customer 2 had a closing balance of 61 in month 3.
- Customer 3 had a closing balance of 144 in month 1.
- Customer 3 had a closing balance of -965 in month 2.
- Customer 3 had a closing balance of -401 in month 3.
- Customer 3 had a closing balance of 493 in month 4.
- Customer 4 had a closing balance of 848 in month 1.
- Customer 4 had a closing balance of -193 in month 3.

 
5.	What is the percentage of customers who increase their closing balance by more than 5%?
```SQL
WITH monthly_balance AS (
    SELECT 
        customer_id,
        DATE_FORMAT(txn_date, '%Y-%m') AS month,
        SUM(CASE WHEN txn_type = 'deposit' THEN txn_amount ELSE 0 END) -
        (
            SUM(CASE WHEN txn_type = 'purchase' THEN txn_amount ELSE 0 END) +
            SUM(CASE WHEN txn_type = 'withdrawal' THEN txn_amount ELSE 0 END)
        ) AS closing_balance
    FROM customer_transactions
    GROUP BY customer_id, DATE_FORMAT(txn_date, '%Y-%m')
),

balance_change AS (
    SELECT 
        customer_id,
        month,
        closing_balance,
        LAG(closing_balance) OVER (PARTITION BY customer_id ORDER BY month) AS prev_balance
    FROM monthly_balance
),

percentage_change AS (
    SELECT 
        customer_id,
        month,
        closing_balance,
        prev_balance,
        CASE 
            WHEN prev_balance IS NOT NULL AND prev_balance > 0 THEN 
                ((closing_balance - prev_balance) / prev_balance) * 100
            ELSE NULL
        END AS pct_change
    FROM balance_change
),

qualified_customers AS (
    SELECT DISTINCT customer_id
    FROM percentage_change
    WHERE pct_change > 5
)

SELECT 
    (COUNT(DISTINCT qualified_customers.customer_id) * 100.0) / 
    COUNT(DISTINCT monthly_balance.customer_id) AS percent_of_customers
FROM monthly_balance
LEFT JOIN qualified_customers ON monthly_balance.customer_id = qualified_customers.customer_id;
```
<img width="235" height="57" alt="image" src="https://github.com/user-attachments/assets/ca6dc075-e958-4b13-af98-c4feadc32395" />

- 22.40 is the percentage of customer who increased their closing balance by more than 5%.


