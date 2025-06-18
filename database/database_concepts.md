create table property ( ID serial primary key, UniqueID UUID, PropertyName varchar(100), PropertyLocation varchar(100))
There is a field like text[] to have array of strings int[] to have array of numbers

## Normalization
Normalization helps to avoid redudancy in table data.
Improves data integrity
simplifies database design.
## Denormalization
for performance imporovement instead of joining different tables to get records, still can go with single table.

https://www.youtube.com/watch?v=9hfjC-BpY20

## Queries 1 - with GROUP BY

### Exercise 1 : Find the total number of transactions for each customer. Show customer name and transaction count.
select c.name as "user_name", count(transaction_id) from transaction t join customer c on c.customer_id=t.user_id group by c.name 
![image](https://github.com/user-attachments/assets/8266fd6c-772e-4c49-ab23-a81d655b11cc)

### Exercise 2 : Calculate the total quantity of products sold in each product category.
select p.product_category, sum(t.quantity) as "sold_qty" from transaction t join product p on t.product_id=p.product_id group by p.product_category;
![image](https://github.com/user-attachments/assets/dc968c07-29fd-4d2b-9353-10dc534b096b)

### Exercise 3 : Count how many members vs non-members are in the customer table.
select is_member,count(*) from customer group by is_member;
![image](https://github.com/user-attachments/assets/9fc93b07-65ad-4101-ae9c-6f9172ba823d)

### Exercise 4 : Find the avg price of product in each category
select product_category,avg(price) as "avg_price" from product group by product_category;
![image](https://github.com/user-attachments/assets/184f3228-aa64-4ab6-978b-d38a77e663f7)

## Queries 2 - GROUP BY with HAVING 
### Exercise 5 : Find customers who have made more than 3 transactions. show customer name and transaction count.
select c.name, count(t.transaction_id) from "transaction" t join customer c on t.user_id=c.customer_id group by c.name having count(*) >= 3;
![image](https://github.com/user-attachments/assets/8368bdb0-1c15-4ecf-a675-65061364318b)

### Exercise 6 : Find product categories where the average product price is greater than $100.
select product_category,avg(price) as "avg_price" from product group by product_category having avg(price)>100;
![image](https://github.com/user-attachments/assets/4769f7d9-9faf-4ccd-b2e2-40a6d09d1378)

## Queries 3 - Subquery
### Exercise 7 : Find all customers who have made atleast one transaction.
select c.name from customer c where exists (select 1 from transaction t where t.user_id=c.customer_id)
![image](https://github.com/user-attachments/assets/dcf6d8ab-413c-4049-9c2d-795f26786ade)

select c.name from (select t.user_id, count(t.transaction_id) from transaction t group by t.user_id having count(t.transaction_id)>=1 ) x join customer c on x.user_id=c.customer_id

![image](https://github.com/user-attachments/assets/8c2bc8b8-5263-4bfe-9050-becc3a4a782c)

### Exercise 8 : Find all products that have never been sold. use NOT EXISTS
select name from product p  where not exists (select 1 from transaction t where t.product_id=p.product_id)
![image](https://github.com/user-attachments/assets/c37e8d69-6a21-432a-9508-0a2e90ba7b8b)

### Exercise 9 : Find the most expensive product in each category using correlated query.
select p.product_category, p.name,p.price from product p where p.price = (select max(p2.price) from product p2 where p2.product_category=p.product_category)
![image](https://github.com/user-attachments/assets/d066d0b1-0c5b-42b1-bf3a-f4fa4d4b025b)

to select 2nd max product,
select p.product_category, p.name from (select name, product_category, row_number() over (partition by product_category order by price desc) as ranked from product) p where  p.ranked=2
![image](https://github.com/user-attachments/assets/645df59a-6cf0-4504-b2ef-7b9190884665)


Write a SQL query that list the first debit card opened by each cardholder. Output columns: cardholder_id, name (cardholder), debit_card_id

select a.cardholder_id, a.name, b.debit_card_id from cardholder a, 
JOIN
    "transaction" t ON c.cardholder_id = t.cardholder_id
JOIN
    debit_card dc ON t.debit_card_id = dc.debit_card_id
    
WHERE
    dc.date_opened = (
        SELECT MIN(dc_inner.date_opened)
        FROM debit_card dc_inner
        JOIN "transaction" t_inner ON dc_inner.debit_card_id = t_inner.debit_card_id
        WHERE t_inner.cardholder_id = c.cardholder_id
    )

Write a SQL query to list each cardholder and the number of times their transactions were rejected due to an incorrect PIN entry. Output columns: cardholder_id, name (cardholder), no_of_rejections

select  c.cardholder_id, c.name, count(transaction_id) from "transaction" t join cardholder c on t.cardholder_id=c.cardholder_id JOIN
    debit_card dc ON t.debit_card_id = dc.debit_card_id where is_rejected=True and dc.pin != t.pin_entered group by c.cardholder_id,c.name

Write a SQL query to list each cardholder and the number of times their transactions were rejected because they were double charged the same amount on the same day, and the transaction was rejected, within the last month. Output columns: cardholder_id, name (cardholder), no_of_rejections
SELECT c.cardholder_id,
       c.name,
       COUNT(*) AS no_of_rejections
FROM cardholder c
JOIN "transaction" t ON c.cardholder_id = t.cardholder_id
WHERE t.is_rejected = TRUE
  AND t.transaction_date >= CURRENT_DATE - INTERVAL '1 month'
  AND EXISTS (
      SELECT 1
      FROM "transaction" t2
      WHERE t2.cardholder_id = t.cardholder_id
        AND t2.debit_card_id = t.debit_card_id
        AND t2.charged_amt = t.charged_amt
        AND t2.is_rejected = FALSE
        AND t2.transaction_date <= t.transaction_date
    )
GROUP BY c.cardholder_id, c.name;



Answer the following questions.

customer table
Column	Type
customer_id	INTEGER
name	VARCHAR
is_member	BOOLEAN

product table
Column	Type
product_id	INTEGER
name	VARCHAR
price	FLOAT
product_category	VARCHAR

transaction table
Column	Type
transaction_id	INTEGER
user_id	INTEGER
created_at	TIMESTAMP
product_id	INTEGER
quantity	INTEGER


1.Write a SQL query that retrieves the top 3 most-purchased products by quantity, specifically for customers who are members. Output columns: product_id, product_name, total_quantity_purchased
SELECT 
    p.product_id, 
    p.name AS product_name, 
    SUM(t.quantity) AS total_quantity_purchased
FROM 
    customer c
JOIN 
    'transaction' t ON c.customer_id = t.user_id
JOIN 
    product p ON t.product_id = p.product_id
WHERE 
    c.is_member = TRUE
GROUP BY 
    p.product_id, p.name
ORDER BY 
    total_quantity_purchased DESC
LIMIT 3;

2. Write a SQL query that shows the average price per transaction for each product category in the last 30 days. Round the average price per transaction to two decimal points. Output columns: product_category, average_price

SELECT 
    p.product_category, 
    ROUND(AVG(p.price), 2) AS average_price
FROM 
    'transaction' t
JOIN 
    product p ON t.product_id = p.product_id
WHERE 
    t.created_at >= DATE('now', '-30 days')
GROUP BY 
    p.product_category;

3. Write a SQL query that contains each member’s most commonly purchased product. Output columns: customer_id, member_name, product_id, product_name

WITH product_counts AS (
    SELECT 
        c.customer_id,
        c.name AS member_name,
        t.product_id,
        p.name AS product_name,
        SUM(t.quantity) AS total_quantity
    FROM 
        customer c
    JOIN 
        'transaction' t ON c.customer_id = t.user_id
    JOIN 
        product p ON t.product_id = p.product_id
    WHERE 
        c.is_member = TRUE
    GROUP BY 
        c.customer_id, t.product_id
),
ranked_products AS (
    SELECT 
        customer_id,
        member_name,
        product_id,
        product_name,
        total_quantity,
        ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY total_quantity DESC) AS rank
    FROM 
        product_counts
)
SELECT 
    customer_id,
    member_name,
    product_id,
    product_name
FROM 
    ranked_products
WHERE 
    rank = 1;


4.Write a SQL query that identifies the most commonly purchased item as a non-member and as a member per product category, and the price difference between those items per category. Output columns: product_category, non_member_product, member_product, price_diff
WITH member_products AS (
    SELECT 
        p.product_category,
        p.product_id,
        p.name AS member_product,
        SUM(t.quantity) AS total_quantity
    FROM 
        customer c
    JOIN 
        'transaction' t ON c.customer_id = t.user_id
    JOIN 
        product p ON t.product_id = p.product_id
    WHERE 
        c.is_member = TRUE
    GROUP BY 
        p.product_category, p.product_id
),
non_member_products AS (
    SELECT 
        p.product_category,
        p.product_id,
        p.name AS non_member_product,
        SUM(t.quantity) AS total_quantity
    FROM 
        customer c
    JOIN 
        'transaction' t ON c.customer_id = t.user_id
    JOIN 
        product p ON t.product_id = p.product_id
    WHERE 
        c.is_member = FALSE
    GROUP BY 
        p.product_category, p.product_id
),
ranked_member_products AS (
    SELECT 
        product_category,
        member_product,
        total_quantity,
        ROW_NUMBER() OVER (PARTITION BY product_category ORDER BY total_quantity DESC) AS rank
    FROM 
        member_products
),
ranked_non_member_products AS (
    SELECT 
        product_category,
        non_member_product,
        total_quantity,
        ROW_NUMBER() OVER (PARTITION BY product_category ORDER BY total_quantity DESC) AS rank
    FROM 
        non_member_products
)
SELECT 
    m.product_category,
    m.member_product,
    nm.non_member_product,
    ROUND(p2.price - p1.price, 2) AS price_diff
FROM 
    ranked_member_products m
JOIN 
    ranked_non_member_products nm ON m.product_category = nm.product_category
JOIN 
    product p1 ON nm.non_member_product = p1.name
JOIN 
    product p2 ON m.member_product = p2.name
WHERE 
    m.rank = 1 AND nm.rank = 1;


5.Write a SQL query to find the the list of products where the quantity purchased in these 4 weeks (28 days) is greater than the quantity purchased in the preceding 4 weeks, and find the percentage increase in quantity sold. Output columns: product_id, product_name, quantity_8_to_4_weeks_ago, quantity_4_to_0_weeks_ago, percentage_increase

WITH quantities AS (
    SELECT 
        t.product_id,
        SUM(CASE 
                WHEN t.created_at >= DATE('now', '-28 days') THEN t.quantity 
                ELSE 0 
            END) AS quantity_4_to_0_weeks_ago,
        SUM(CASE 
                WHEN t.created_at < DATE('now', '-28 days') AND t.created_at >= DATE('now', '-56 days') THEN t.quantity 
                ELSE 0 
            END) AS quantity_8_to_4_weeks_ago
    FROM 
        'transaction' t
    GROUP BY 
        t.product_id
)
SELECT 
    q.product_id,
    p.name AS product_name,
    q.quantity_8_to_4_weeks_ago,
    q.quantity_4_to_0_weeks_ago,
    ROUND(((q.quantity_4_to_0_weeks_ago - q.quantity_8_to_4_weeks_ago) * 100.0 / q.quantity_8_to_4_weeks_ago), 2) AS percentage_increase
FROM 
    quantities q
JOIN 
    product p ON q.product_id = p.product_id
WHERE 
    q.quantity_4_to_0_weeks_ago > q.quantity_8_to_4_weeks_ago
    AND q.quantity_8_to_4_weeks_ago > 0;


