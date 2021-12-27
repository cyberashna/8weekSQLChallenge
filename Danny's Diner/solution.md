
## 1. What is the total amount each customer spent at the restaurant?

```SQL
SELECT customer_id,SUM(price) AS total_sales
FROM dannys_diner.sales
JOIN dannys_diner.menu
ON sales.product_id = menu.product_id
GROUP BY customer_id 
ORDER BY customer_id ASC
```
#### Results:
| customer_id | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

### Answer:
- Customer A spent $76.
- Customer B spent $74.
- Customer C spent $36.

## 2. How many days has each customer visited the restaurant?
````sql
SELECT customer_id,COUNT(DISTINCT order_date) AS days_visited
FROM dannys_diner.sales
JOIN dannys_diner.menu
ON sales.product_id = menu.product_id
GROUP BY customer_id 
ORDER BY customer_id ASC
````
#### Results: 
| customer_id | visit_count |
| ----------- | ----------- |
| A           | 4          |
| B           | 6          |
| C           | 2          |
### Answer:

- Customer A visited 4 times.
- Customer B visited 6 times.
- Customer C visited 2 times.
***
## 3. What was the first item from the menu purchased by each customer?
```sql
WITH product as ( SELECT customer_id,product_name,RANK() OVER (PARTITION BY customer_id ORDER BY order_date ASC) as R, order_date
FROM dannys_diner.sales sa
JOIN dannys_diner.menu me
ON sa.product_id = me.product_id)
SELECT customer_id, product_name,order_date FROM product 
WHERE R = 1;
```
#### Results:
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

## Results
| customer_id | order_date               | product_id |
| ----------- | ------------------------ | ---------- |
| C           | 2021-01-01T00:00:00.000Z | 3          |
| C           | 2021-01-01T00:00:00.000Z | 3          |
| C           | 2021-01-07T00:00:00.000Z | 3          |

Seems like they did in fact have two orders

### Answer 
- Customer A's first items were curry and sushi 
- Customer B's first item was curry 
- Customer C's first item was ramen

## 4. What is the most purchased item on the menu and how many times was it purchased by all customers?
```sql
  SELECT product_name ,COUNT(sa.product_id) FROM dannys_diner.sales sa
  JOIN dannys_diner.menu me
  ON sa.product_id = me.product_id 
  GROUP BY product_name;
  ```
#### Results:
| product_name | count |
| ------------ | ----- |
| ramen        | 8     |
| sushi        | 3     |
| curry        | 4     |

### Answer: 
Ramen is the most purchased item, it was purchased a total of eight times. 

## 5. Which item was the most popular for each customer?
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
#### Results:
| customer_id | fav_item | item_count |
| ----------- | -------- | ---------- |
| A           | ramen    | 3          |
| B           | ramen    | 2          |
| B           | curry    | 2          |
| B           | sushi    | 2          |
| C           | ramen    | 3          |

### Answer: 
-Customer A enjoys ramen the most 
-Customer B enjoys all three items on the menu 
-Customer C enjoys ramen the most

## 6. Which item was purchased first by the customer after they became a member?
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
#### Results:

| customer_id | product_name | order_date               |
| ----------- | ------------ | ------------------------ |
| A           | curry        | 2021-01-07T00:00:00.000Z |
| B           | sushi        | 2021-01-11T00:00:00.000Z |

### Answer: 
-Customer A's first order after membership was curry 
-Customer B's first order after membership was sushi
***
## 7. Which item was purchased just before the customer became a member?
```sql
    WITH dates as 
    (SELECT sa.customer_id, order_date, product_name, rank() OVER (PARTITION BY sa.customer_id ORDER BY order_date DESC) as R FROM dannys_diner.sales sa
    JOIN dannys_diner.members mem
    ON sa.customer_id = mem.customer_id
    AND sa.order_date < mem.join_date
    JOIN dannys_diner.menu me
    ON sa.product_id = me.product_id)
    SELECT customer_id,product_name, order_date FROM dates
    WHERE R = '1';
 ```

#### Results:

| customer_id | product_name | order_date               |
| ----------- | ------------ | ------------------------ |
| A           | sushi        | 2021-01-01T00:00:00.000Z |
| A           | curry        | 2021-01-01T00:00:00.000Z |
| B           | sushi        | 2021-01-04T00:00:00.000Z |

### Answer:
-- Customer A purchased curry and sushi just before becoming a member
-- Customer B purchased sushi just before becoming a member

## 8. What is the total items and amount spent for each member before they became a member?
```sql
    SELECT sa.customer_id,COUNT(sa.product_id), SUM(price) FROM dannys_diner.sales sa
    JOIN dannys_diner.members mem
    ON sa.customer_id = mem.customer_id
    AND sa.order_date < mem.join_date
    JOIN dannys_diner.menu me
    ON sa.product_id = me.product_id
    GROUP BY sa.customer_id;
```
#### Results: 

| customer_id | count | sum |
| ----------- | ----- | --- |
| B           | 3     | 40  |
| A           | 2     | 25  |

### Answer: 
-- Customer B spent 40 dollars on 3 items 
-- Customer A spent 25 dollars on 2 items 
***
## 9.  If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
```SQL
    WITH product_points  AS (SELECT sa.customer_id, CASE product_name WHEN 'sushi' THEN SUM(price)*20 ELSE SUM(price)*10 END AS points 
    FROM dannys_diner.sales sa            
    JOIN dannys_diner.menu me
    ON sa.product_id = me.product_id
    GROUP BY customer_id, product_name)
    SELECT customer_id, SUM(points) AS total_points FROM product_points
    GROUP BY customer_id;
 ```
#### Results: 

| customer_id | total_points |
| ----------- | ------------ |
| B           | 940          |
| C           | 360          |
| A           | 860          |

### Answer: 
-- Customer A has 860 points 
-- Customer B has 940 points
-- Customer C has 360 points

10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

```SQL

    WITH dates AS 
    (
     SELECT *, 
      join_date + INTERVAL'6 days' AS valid_date, 
      '2021-01-31'::DATE AS last_date
     FROM dannys_diner.members AS m
    )
    ,points as (SELECT d.customer_id, sa.order_date, d.join_date, 
     d.valid_date, d.last_date, men.product_name, men.price,
     SUM(CASE
      WHEN men.product_name = 'sushi' THEN 2 * 10 * men.price
      WHEN sa.order_date BETWEEN d.join_date AND d.valid_date THEN 2 * 10 * men.price
      ELSE 10 * men.price
      END) AS points
    FROM dates AS d
    JOIN dannys_diner.sales AS sa
     ON d.customer_id = sa.customer_id
    JOIN dannys_diner.menu AS men
     ON sa.product_id = men.product_id
    WHERE sa.order_date < d.last_date
    GROUP BY d.customer_id, sa.order_date, d.join_date, d.valid_date, d.last_date, men.product_name, men.price)
    SELECT customer_id,SUM(points) 
    FROM points
    GROUP BY customer_id;
  ```
#### Results:
| customer_id | sum  |
| ----------- | ---- |
| A           | 1370 |
| B           | 820  |






















