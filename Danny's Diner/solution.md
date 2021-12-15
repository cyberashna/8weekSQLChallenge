
### 1. What is the total amount each customer spent at the restaurant?

```SQL
SELECT customer_id,SUM(price) AS total_sales
FROM dannys_diner.sales
JOIN dannys_diner.menu
ON sales.product_id = menu.product_id
GROUP BY customer_id 
ORDER BY customer_id ASC
```
| customer_id | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

- Customer A spent $76.
- Customer B spent $74.
- Customer C spent $36.
***
### 2. How many days has each customer visited the restaurant?
````sql
SELECT customer_id,COUNT(DISTINCT order_date) AS days_visited
FROM dannys_diner.sales
JOIN dannys_diner.menu
ON sales.product_id = menu.product_id
GROUP BY customer_id 
ORDER BY customer_id ASC
````
# Results: 
| customer_id | visit_count |
| ----------- | ----------- |
| A           | 4          |
| B           | 6          |
| C           | 2          |
## Answer:

- Customer A visited 4 times.
- Customer B visited 6 times.
- Customer C visited 2 times.
***
### 3. What was the first item from the menu purchased by each customer?
```sql
WITH product as ( SELECT customer_id,product_name,RANK() OVER (PARTITION BY customer_id ORDER BY order_date ASC) as R, order_date
FROM dannys_diner.sales sa
JOIN dannys_diner.menu me
ON sa.product_id = me.product_id)
SELECT customer_id, product_name,order_date FROM product 
WHERE R = 1;
```
### Answer:
| customer_id | product_name | order_date               |
| ----------- | ------------ | ------------------------ |
| A           | curry        | 2021-01-01T00:00:00.000Z |
| A           | sushi        | 2021-01-01T00:00:00.000Z |
| B           | curry        | 2021-01-01T00:00:00.000Z |
| C           | ramen        | 2021-01-01T00:00:00.000Z |
| C           | ramen        | 2021-01-01T00:00:00.000Z |

Double checking if customer C really had two ramen orders on one day (out of curiousity)
```sql
SELECT * FROM dannys_diner.sales
WHERE customer_id = 'C'
```

# Results
| customer_id | order_date               | product_id |
| ----------- | ------------------------ | ---------- |
| C           | 2021-01-01T00:00:00.000Z | 3          |
| C           | 2021-01-01T00:00:00.000Z | 3          |
| C           | 2021-01-07T00:00:00.000Z | 3          |

Seems like they did in fact have two orders

## Answer 
- Customer A's first items were curry and sushi 
- Customer B's first item was curry 
- Customer C's first item was ramen

### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?
```sql
  SELECT product_name ,COUNT(sa.product_id) FROM dannys_diner.sales sa
  JOIN dannys_diner.menu me
  ON sa.product_id = me.product_id 
  GROUP BY product_name;
  ```
# Results:
| product_name | count |
| ------------ | ----- |
| ramen        | 8     |
| sushi        | 3     |
| curry        | 4     |

## Answer: 
Ramen is the most purchased item, it was purchased a total of eight times. 

### 5. Which item was the most popular for each customer?
```sql
WITH pop as (SELECT
  	customer_id,product_name,count(sa.product_id) as item_count, rank() OVER (PARTITION BY customer_id ORDER BY count(sa.product_id) DESC) as R
FROM dannys_diner.sales sa
JOIN dannys_diner.menu me
ON sa.product_id = me.product_id 
GROUP BY customer_id, product_name)
SELECT customer_id,product_name as fav_item, item_count FROM pop
WHERE R = '1';
```
# Results:
| customer_id | fav_item | item_count |
| ----------- | -------- | ---------- |
| A           | ramen    | 3          |
| B           | ramen    | 2          |
| B           | curry    | 2          |
| B           | sushi    | 2          |
| C           | ramen    | 3          |

## Answer: 
-Customer A enjoys ramen the most 
-Customer B enjoys all three items on the menu 
-Customer C enjoys ramen the most

### 6. Which item was purchased first by the customer after they became a member?
```sql
    WITH dates as 
    (SELECT sa.customer_id, order_date, product_name, rank() OVER (PARTITION BY sa.customer_id ORDER BY order_date ASC) as R FROM dannys_diner.sales sa
    JOIN dannys_diner.members mem
    ON sa.customer_id = mem.customer_id
    AND sa.order_date >= mem.join_date
    JOIN dannys_diner.menu me
    ON sa.product_id = me.product_id)
    SELECT customer_id,product_name, order_date FROM dates
    WHERE R = '1';
```
# Results:

| customer_id | product_name | order_date               |
| ----------- | ------------ | ------------------------ |
| A           | curry        | 2021-01-07T00:00:00.000Z |
| B           | sushi        | 2021-01-11T00:00:00.000Z |

## Answer: 
-Customer A's first order after membership was curry 
-Customer B's first order after membership was sushi

















