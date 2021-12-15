
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



