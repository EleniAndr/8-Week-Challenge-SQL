# Case Study #2 - Pizza Runner

<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/565c436d-c652-4cb3-b3b8-daac1eb65323"></td>
    <td>
      <p><strong>Case - Problem Statement</strong></p>
      <p>Danny was inspired by an Instagram post — “80s Retro Styling and Pizza Is The Future!”. He loved the idea but knew pizza alone wouldn’t secure funding. So, he combined it with an Uber-style delivery model and launched *Pizza Runner*. He recruited runners to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house), built a mobile app, and, as a data scientist, began collecting data for his business. He requires assistance to clean his data and apply some basic calculations so he can better direct his runners and optimise Pizza Runner’s operations.</p>
    </td>
  </tr>
</table>

- There are 6 datasets for this case study: **runners**, **customer_orders**, **runner_orders**, **pizza_names**, **pizza_recipes**, **pizza_toppings**. The entity relationship diagram and example data tables can be inspected below.

<table>
  <tr>
    <th>Entity Relationship Diagram</th>
    <th>Table 1: runners</th>
    <th>Table 2: customer_orders (preview)</th>
  </tr>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/0aa1efec-a806-42e0-b864-bfd548ae679e" width="500"/></td>
    <td><img src="https://github.com/user-attachments/assets/6ace82dd-00aa-4058-a18c-a6afac0487b4" width="180"/></td>
    <td><img src="https://github.com/user-attachments/assets/e22b134b-0c7f-4a74-9a17-fbee88db8f5b" width="400"/></td>
  </tr>
</table>

<table>
  <tr>
    <th>Table 3: runner_orders (preview)</th>
    <th>Table 4: pizza_names</th>
    <th>Table 5: pizza_recipes</th>
    <th>Table 6: pizza_toppings</th>
  </tr>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/e0bcded2-9d56-4fe3-b66c-51e9c6ac404f" width="420"/></td>
    <td><img src="https://github.com/user-attachments/assets/2bfbed19-015e-4238-9aa9-b38ed3e42828" width="180"/></td>
    <td><img src="https://github.com/user-attachments/assets/a507d429-384e-4e96-b7a8-095ece41839c" width="180"/></td>
    <td><img src="https://github.com/user-attachments/assets/0f95f44f-b6b8-4593-a7e2-4bdbd3ee4b54" width="180"/></td>
  </tr>
</table>

Press this [link](), where a fully functioning SQL editor is available to easily access these example datasets.

### 🧹 Data Cleaning and Transformation
Before diving into SQL, let’s take a moment to explore the data.

#### Table: customer_orders
  - In the `exclusions` and `extras` columns, there are a few `null` values and `empty/missing` strings.

| order_id | customer_id | pizza_id | exclusions | extras | order_time          |
| -------- | ----------- | -------- | ---------- | ------ | ------------------- |
| 1        | 101         | 1        |            |        | 2020-01-01 18:05:02 |
| 2        | 101         | 1        |            |        | 2020-01-01 19:00:52 |
| 3        | 102         | 1        |            |        | 2020-01-02 23:51:23 |
| 3        | 102         | 2        |            |        | 2020-01-02 23:51:23 |
| 4        | 103         | 1        | 4          |        | 2020-01-04 13:23:46 |
| 4        | 103         | 1        | 4          |        | 2020-01-04 13:23:46 |
| 4        | 103         | 2        | 4          |        | 2020-01-04 13:23:46 |
| 5        | 104         | 1        | null       | 1      | 2020-01-08 21:00:29 |
| 6        | 101         | 2        | null       | null   | 2020-01-08 21:03:13 |
| 7        | 105         | 2        | null       | 1      | 2020-01-08 21:20:29 |
| 8        | 102         | 1        | null       | null   | 2020-01-09 23:54:33 |
| 9        | 103         | 1        | 4          | 1, 5   | 2020-01-10 11:22:59 |
| 10       | 104         | 1        | null       | null   | 2020-01-11 18:34:49 |
| 10       | 104         | 1        | 2, 6       | 1, 4   | 2020-01-11 18:34:49 |

#### Course of action:
  - Create a `Temporary Table` called `customer_orders_clean` that includes all columns from the original table.
  - Remove `null` values and `empty strings ''` and replace them with *no values NULL*.

#### 💻 SQL Query
```sql
CREATE TEMPORARY TABLE customer_orders_clean AS
SELECT order_id, customer_id, pizza_id,
CASE 
  WHEN exclusions LIKE 'null' OR exclusions = '' THEN null
  ELSE exclusions
  END AS exclusions,
CASE  
  WHEN extras LIKE 'null' OR extras = '' THEN null
  ELSE extras
  END AS extras, 
  order_time
FROM pizza_runner.customer_orders;
```

#### New table: customer_orders_clean

| order_id | customer_id | pizza_id | exclusions | extras | order_time          |
| -------- | ----------- | -------- | ---------- | ------ | ------------------- |
| 1        | 101         | 1        |            |        | 2020-01-01 18:05:02 |
| 2        | 101         | 1        |            |        | 2020-01-01 19:00:52 |
| 3        | 102         | 1        |            |        | 2020-01-02 23:51:23 |
| 3        | 102         | 2        |            |        | 2020-01-02 23:51:23 |
| 4        | 103         | 1        | 4          |        | 2020-01-04 13:23:46 |
| 4        | 103         | 1        | 4          |        | 2020-01-04 13:23:46 |
| 4        | 103         | 2        | 4          |        | 2020-01-04 13:23:46 |
| 5        | 104         | 1        |            | 1      | 2020-01-08 21:00:29 |
| 6        | 101         | 2        |            |        | 2020-01-08 21:03:13 |
| 7        | 105         | 2        |            | 1      | 2020-01-08 21:20:29 |
| 8        | 102         | 1        |            |        | 2020-01-09 23:54:33 |
| 9        | 103         | 1        | 4          | 1, 5   | 2020-01-10 11:22:59 |
| 10       | 104         | 1        |            |        | 2020-01-11 18:34:49 |
| 10       | 104         | 1        | 2, 6       | 1, 4   | 2020-01-11 18:34:49 |

#### Table: runner_orders
- Create a new `Temporary Table` called `runner_orders_clean` that includes all columns from the original table.
- In the `pickup_time` column, there are a few `null values`.
- In the `distance` column, there are some `null values` and the `km` unit appears inconsistently.
- In the `duration` column, there are some `null values` and the `minutes` unit appears inconsistently.
- In the `cancellation` column, there are a few `null values`.

| order_id | runner_id | pickup_time         | distance | duration   | cancellation            |
| -------- | --------- | ------------------- | -------- | ---------- | ----------------------- |
| 1        | 1         | 2020-01-01 18:15:34 | 20km     | 32 minutes |                         |
| 2        | 1         | 2020-01-01 19:10:54 | 20km     | 27 minutes |                         |
| 3        | 1         | 2020-01-03 00:12:37 | 13.4km   | 20 mins    |                         |
| 4        | 2         | 2020-01-04 13:53:03 | 23.4     | 40         |                         |
| 5        | 3         | 2020-01-08 21:10:57 | 10       | 15         |                         |
| 6        | 3         | null                | null     | null       | Restaurant Cancellation |
| 7        | 2         | 2020-01-08 21:30:45 | 25km     | 25mins     | null                    |
| 8        | 2         | 2020-01-10 00:15:02 | 23.4 km  | 15 minute  | null                    |
| 9        | 2         | null                | null     | null       | Customer Cancellation   |
| 10       | 1         | 2020-01-11 18:50:20 | 10km     | 10minutes  | null                    |

#### Course of action:
  - Replace all `null values` with *no values NULL*.
  - Strip units from individual cell entries and include them in the column headers instead.
  - Convert the data types to `FLOAT` & `INTEGER` in the columns `distance` and `duration` respectively.
  - Convert the `pickup_time` column to a proper `date/time` data type.

#### 💻 SQL Query
```sql
CREATE TEMPORARY TABLE runner_orders_clean AS
SELECT order_id, runner_id,
CASE 
  WHEN pickup_time LIKE 'null' THEN null
  ELSE pickup_time
  END AS pickup_time,
CASE  
  WHEN distance LIKE 'null' THEN null
  WHEN distance LIKE '%km%' THEN TRIM('km' from distance)
  ELSE distance
  END AS distance_km,
CASE 
  WHEN duration LIKE 'null' THEN null
  WHEN duration LIKE '%mins' THEN TRIM('mins' from duration)
  WHEN duration LIKE '%minute' THEN TRIM('minute' from duration)
  WHEN duration LIKE '%minutes' THEN TRIM('minutes' from duration)
  ELSE duration
  END AS duration_min,
CASE 
  WHEN cancellation LIKE 'null' OR cancellation='' THEN null
  ELSE cancellation
  END AS cancellation
FROM pizza_runner.runner_orders;

ALTER TABLE runner_orders_clean
ALTER COLUMN pickup_time TYPE TIMESTAMP USING pickup_time::timestamp,
ALTER COLUMN distance_km TYPE FLOAT USING distance_km::float,
ALTER COLUMN duration_min TYPE INTEGER USING duration_min::integer;
```

#### New table: runner_orders_clean

| order_id | runner_id | pickup_time         | distance_km | duration_min | cancellation            |
| -------- | --------- | ------------------- | ----------- | ------------ | ----------------------- |
| 1        | 1         | 2020-01-01 18:15:34 | 20          | 32           |                         |
| 2        | 1         | 2020-01-01 19:10:54 | 20          | 27           |                         |
| 3        | 1         | 2020-01-03 00:12:37 | 13.4        | 20           |                         |
| 4        | 2         | 2020-01-04 13:53:03 | 23.4        | 40           |                         |
| 5        | 3         | 2020-01-08 21:10:57 | 10          | 15           |                         |
| 6        | 3         |                     |             |              | Restaurant Cancellation |
| 7        | 2         | 2020-01-08 21:30:45 | 25          | 25           |                         |
| 8        | 2         | 2020-01-10 00:15:02 | 23.4        | 15           |                         |
| 9        | 2         |                     |             |              | Customer Cancellation   |
| 10       | 1         | 2020-01-11 18:50:20 | 10          | 10           |                         |

<h2 align="center">
Case Study Questions
</h2>

This case study has LOTS of questions - they are broken up by area of focus, including:

- [A. Pizza Metrics](https://github.com/EleniAndr/A.-Pizza-Metrics/blob/main/README.md#a-pizza-metrics)
- [B. Runner and Customer Experience]()
- [C. Ingredient Optimisation]()
- [D. Pricing and Ratings]()
- [E. Bonus DML Challenges (DML = Data Manipulation Language)]()
