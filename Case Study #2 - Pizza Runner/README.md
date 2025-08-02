# Case Study #2 - Pizza Runner

<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/85a77a6d-a064-4e84-a1e3-79096d0760c2"></td>
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
    <td><img src="https://github.com/user-attachments/assets/ac235443-b4e0-4c36-85f3-14725741312a" width="500"/></td>
    <td><img src="https://github.com/user-attachments/assets/d1e2a0cf-a34a-4cac-a21a-3d394375fb6d" width="180"/></td>
    <td><img src="https://github.com/user-attachments/assets/1b0e3690-21c9-493c-a645-69b1eb44f6c3" width="400"/></td>
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
     <td><img src="https://github.com/user-attachments/assets/f9e0e98a-6d7d-416f-80aa-2361e63726fe" width="420"/></td>
    <td><img src="https://github.com/user-attachments/assets/7b669665-6d75-4f42-a8ef-0bd0e535c4e7" width="180"/></td>
    <td><img src="https://github.com/user-attachments/assets/e6103fc4-3247-4bfa-a274-34c8a9520b6e" width="180"/></td>
    <td><img src="https://github.com/user-attachments/assets/090817a5-9c45-4ec5-8dba-06299d38cce3" width="180"/></td>
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
    - Create a new Temporary Table with all the columns.
    - Remove `null` values and empty strings '' and replace them with *no values NULL*.

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
  - Remove the units from the cells and place them in the title of the columns.
  - Change the data types to numeric in the columns `distance` and `duration`.
  - Change the data type to date/time in the column `pickup_time`. 

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

- [Pizza Metrics](https://github.com/EleniAndr/A.-Pizza-Metrics/blob/main/README.md#a-pizza-metrics)
- [Runner and Customer Experience]()
- [Ingredient Optimisation]()
- [Pricing and Ratings]()
- [Bonus DML Challenges (DML = Data Manipulation Language)]()
