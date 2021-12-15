
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
#### Answer:
| customer_id | visit_count |
| ----------- | ----------- |
| A           | 4          |
| B           | 6          |
| C           | 2          |

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








