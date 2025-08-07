# A. Pizza Metrics
 
## Case Study Questions & Answers

### 1. How many pizzas were ordered?

#### 💻 SQL Query
```sql
SELECT COUNT(order_id) AS total_pizzas_ordered
FROM customer_orders_clean;
```
#### 🖊 Result

| total_pizzas_ordered |
| -------------------- |
| 14                   |

#### 📜 Explanation 
- Use `COUNT(order_id)` to calculate the total number of pizzas that were ordered.
- Retrieve the information from the temporary table `customer_orders_clean`, which was created at the beginning of the session.
### Answer
- A total of 14 pizzas were ordered.
---

### 2. How many unique customer orders were made?
#### 💻 SQL Query
```sql
SELECT COUNT(DISTINCT(order_id)) AS unique_orders
FROM customer_orders_clean;
```

#### 🖊 Result

| unique_orders |
| ------------- |
| 10            |

#### 📜 Explanation 
- Use `COUNT(DISTINCT(order_id))` to calculate the orders that were made.

### Answer
- 10 orders were made.
---

### 3. How many successful orders were delivered by each runner?
#### 💻 SQL Query
```sql
SELECT runner_id, COUNT(order_id) as successful_orders
FROM runner_orders_clean
WHERE cancellation IS null
GROUP BY runner_id;
```
#### 🖊 Result

| runner_id | successful_orders |
| --------- | ----------------- |
| 1         | 4                 |
| 2         | 3                 |
| 3         | 1                 |

#### 📜 Explanation 
- Use `COUNT(order_id)` to calculate how many orders were placed.
- Use `WHERE cancellation IS null` clause to remove all orders that were cancelled.
### Answer
- Runner 1 has 4 successful orders.
- Runner 2 has 3 successful orders.
- Runner 3 has 1 successful order.

### 4. How many of each type of pizza was delivered?
#### 💻 SQL Query
```sql
SELECT pizza_name, COUNT(customer_orders_clean.pizza_id) as pizza_count
FROM customer_orders_clean
JOIN pizza_names 
	ON customer_orders_clean.pizza_id = pizza_names.pizza_id
JOIN runner_orders_clean
	ON customer_orders_clean.order_id = runner_orders_clean.order_id
WHERE cancellation is null
GROUP BY pizza_name
ORDER BY pizza_name;
```
#### 🖊 Result

| pizza_name | pizza_count |
| ---------- | ----------- |
| Meatlovers | 9           |
| Vegetarian | 3           |

#### 📜 Explanation
- Use `COUNT(customer_orders_clean.pizza_id)` to count the number of pizzas that were ordered.
- `INNER JOIN` the tables `customer_orders_clean` and `runner_orders_clean` on `order_id` to access the `cancellation` column.
- Use a `WHERE` clause to count out the cancelled orders.
- `INNER JOIN` the tables `customer_orders_clean` and `pizza_names` on `pizza_id` to get the names of each pizza id.
- `GROUP` and `ORDER BY` `pizza_name` to organize the results.
### Answer
- Meatlovers pizza was delivered 9 times.
- Vegetarian pizza was delivered 3 times.
---

### 5. How many Vegetarian and Meatlovers were ordered by each customer?
#### 💻 SQL Query
```sql
SELECT customer_id, pizza_name, COUNT(customer_orders_clean.pizza_id) as pizzas_ordered
FROM customer_orders_clean
JOIN pizza_names 
	ON customer_orders_clean.pizza_id = pizza_names.pizza_id
GROUP BY customer_id, pizza_name
ORDER BY customer_id;
```
#### 🖊 Result

| customer_id | pizza_name | pizzas_ordered |
| ----------- | ---------- | -------------- |
| 101         | Meatlovers | 2              |
| 101         | Vegetarian | 1              |
| 102         | Meatlovers | 2              |
| 102         | Vegetarian | 1              |
| 103         | Meatlovers | 3              |
| 103         | Vegetarian | 1              |
| 104         | Meatlovers | 3              |
| 105         | Vegetarian | 1              |

#### 📜 Explanation
- Use `COUNT(customer_orders_clean.pizza_id)` to count the number of pizzas that were ordered.
- `INNER JOIN` the tables `customer_orders_clean` and `pizza_names` on `pizza_id` to get the names of each pizza id.
- `GROUP` and `ORDER BY` `customer_id` to show the results for each customer.

### Answer
- Customer 101 ordered Meatlovers 2 times and Vegetarian 1 time.
- Customer 102 also ordered Meatlovers 2 times and Vegetarian 1 time.
- Customer 103 ordered Meatlovers 3 times and Vegetarian once.
- Customer 104 ordered Meatlovers 3 times.
- Customer 105 ordered Vegetarian 1 time.

### 6. What was the maximum number of pizzas delivered in a single order?
#### 💻 SQL Query
```sql
SELECT customer_orders_clean.order_id, COUNT(customer_orders_clean.pizza_id) as max_pizzas_delivered
FROM customer_orders_clean
JOIN runner_orders_clean 
	ON customer_orders_clean.order_id = runner_orders_clean.order_id
WHERE cancellation IS null
GROUP BY customer_orders_clean.order_id
ORDER BY max_pizzas_delivered DESC
LIMIT 1;
```
#### 🖊 Result

| order_id | max_pizzas_delivered |
| -------- | -------------------- |
| 4        | 3                    |

#### 📜 Explanation
- Use `COUNT(customer_orders_clean.pizza_id)` to count the number of pizzas that were ordered.
- `INNER JOIN` the tables `customer_orders_clean` and `runner_orders_clean` on `order_id` to get the information for cancelled orders.
- Use `WHERE cancellation IS null` to count out the cancelled orders.
- `GROUP BY` `order_id` to show the results for each order.
- `ORDER BY` `max_pizzas_ordered` in descending order.
- `LIMIT 1` to show only the first result, which is the maximum number of pizzas delivered.
### Answer
The maximum number of pizzas that were delivered in a single order is 3.

### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
#### 💻 SQL Query
```sql
SELECT customer_orders_clean.customer_id,
SUM(CASE 
	WHEN exclusions IS NOT null OR extras IS NOT null THEN 1
    ELSE 0
  END) AS at_least_1_change,
  SUM(CASE 
	WHEN exclusions IS null AND extras IS null THEN 1
    ELSE 0
  END) AS no_changes
FROM customer_orders_clean
JOIN runner_orders_clean 
	ON customer_orders_clean.order_id = runner_orders_clean.order_id
WHERE cancellation IS null
GROUP BY customer_orders_clean.customer_id
ORDER BY customer_orders_clean.customer_id;
```
#### 🖊 Result

| customer_id | at_least_1_change | no_changes |
| ----------- | ----------------- | ---------- |
| 101         | 0                 | 2          |
| 102         | 0                 | 3          |
| 103         | 3                 | 0          |
| 104         | 2                 | 1          |
| 105         | 1                 | 0          |

#### 📜 Explanation
- Use `CASE WHEN...ELSE` to create two columns:
  - `at_least_1_change`: counts orders where ingredients were either excluded (exclusions) or added (extras).
  - `no_changes`: counts orders with no modifications.
- `INNER JOIN` the tables `customer_orders_clean` and `runner_orders_clean` on `order_id` to get the information for cancelled orders.
- Filter out cancelled orders with `WHERE cancellation IS null`.
- `GROUP/ORDER BY` customer_id.

### Answer
- Customer 101: No changes in their orders.
- Customer 102: No changes in their orders.
- Customer 103: Made 3 changes in their orders.
- Customer 104: No changes in 1 order and made 3 changes in another.
- Customer 105: Made 1 change in their orders.

### 8. How many pizzas were delivered that had both exclusions and extras?
#### 💻 SQL Query
```sql
SELECT
  SUM(CASE 
	WHEN exclusions IS NOT null AND extras IS NOT null THEN 1
    ELSE 0
  END) AS extras_and_exclusions
FROM customer_orders_clean
JOIN runner_orders_clean 
	ON customer_orders_clean.order_id = runner_orders_clean.order_id
WHERE cancellation IS null;
```
#### 🖊 Result

| extras_and_exclusions |
| --------------------- |
| 1                     |

#### 📜 Explanation
- Use `CASE WHEN...ELSE` to create the column:
  - `extras_and_exclusions`: counts orders where ingredients were excluded (exclusions) and added (extras).
- `INNER JOIN` the tables `customer_orders_clean` and `runner_orders_clean` on `order_id` to get the information for cancelled orders.
- Filter out cancelled orders with `WHERE cancellation IS null`.
### Answer
1 pizza was delivered that had both exclusions and extras.

### 9. What was the total volume of pizzas ordered for each hour of the day?
#### 💻 SQL Query
```sql
SELECT 
	EXTRACT(HOUR FROM order_time) AS hour_of_day, 
    COUNT(order_id) as orders_amount
FROM customer_orders_clean
GROUP BY hour_of_day
ORDER BY hour_of_day;
```
#### 🖊 Result

| hour_of_day | orders_amount |
| ----------- | ------------- |
| 11          | 1             |
| 13          | 3             |
| 18          | 3             |
| 19          | 1             |
| 21          | 3             |
| 23          | 3             |

#### 📜 Explanation
- Separate the hour from `order_time` into a column called `hour_of_day`.
- Count the orders.
- `GROUP/ORDER BY` `hour_of_day`.

### Answer
- The most pizzas were ordered at 13:00, 18:00, 21:00, and 23:00.
- The least pizzas were ordered at 11:00 and 19:00.

### 10. What was the volume of orders for each day of the week?
#### 💻 SQL Query
```sql
SELECT 
  --CAST(order_time AS DATE) AS full_date,
  TO_CHAR(order_time, 'Day') AS day_of_week,
  COUNT(DISTINCT(order_id)) as total_orders
FROM customer_orders_clean
GROUP BY day_of_week
ORDER BY total_orders DESC;
```
#### 🖊 Result

| day_of_week | total_orders |
| ----------- | ------------ |
| Wednesday   | 5            |
| Saturday    | 2            |
| Thursday    | 2            |
| Friday      | 1            |

#### 📜 Explanation
- Use `TO_CHAR(order_time, 'Day')` to give the full name of the day (e.g, Monday,...).[^1]
- Use `COUNT(DISTINCT(order_id))` to count how many orders were made each day.
- `GROUP BY` day.
- `ORDER BY` orders from most to least.
### Answer
- The highest amount of orders was made on a Wednesday, 5 orders.
- On Saturday and Thursday, 2 orders were made.
- On Friday, only 1 order was made.

[^1]: The dates on the table shown on the site and on the SQL editor that is provided are different. The day/month is the same, but the year shown on the site is 2021, whereas on the editor it is 2020. 
