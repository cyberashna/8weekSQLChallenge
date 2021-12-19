#### 1. How many customers has Foodie-Fi ever had?

   
```SQL
    SELECT COUNT(DISTINCT customer_id) AS unique_customer FROM foodie_fi.subscriptions;
```
### Results:
| unique_customer |
| --------------- |
| 1000            |

### Answer:
There are 1000 unique customers

#### 2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value
```SQL
    SELECT TO_CHAR(start_date, 'Month') AS month_name, COUNT(*) FROM foodie_fi.subscriptions s
    WHERE plan_id = '0' 
    GROUP BY TO_CHAR(start_date, 'Month'), EXTRACT( MONTH FROM start_date)
    ORDER BY EXTRACT( MONTH FROM start_date) ASC;
```
### Results: 

| month_name | count |
| ---------- | ----- |
| January    | 88    |
| February   | 68    |
| March      | 94    |
| April      | 81    |
| May        | 88    |
| June       | 79    |
| July       | 89    |
| August     | 88    |
| September  | 87    |
| October    | 79    |
| November   | 75    |
| December   | 84    |

#### 3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name
```SQL
    SELECT plan_name,count(*) FROM foodie_fi.subscriptions s
    JOIN foodie_fi.plans p
    ON s.plan_id = p.plan_id
    WHERE start_date > '2020-12-31'
    GROUP BY plan_name,p.plan_id
    ORDER BY p.plan_id ASC;
```
##### Results:  

| plan_name     | count |
| ------------- | ----- |
| basic monthly | 8     |
| pro monthly   | 60    |
| pro annual    | 63    |
| churn         | 71    |

### Seems that there were no trial plans started after 2020 

#### 4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
