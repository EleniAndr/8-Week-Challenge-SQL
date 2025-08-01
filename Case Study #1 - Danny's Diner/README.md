# Case Study #1 - Danny's Diner

<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/90492d3d-f48d-4868-b81c-09e5fc742c5e"></td>
    <td>
      <p><strong>Case - Problem Statement</strong></p>
      <p>Danny loves Japanese food, so he opens up a cute little restaurant serving his three favorite dishes: sushi, curry, and ramen. After a few months of running Danny’s Diner, he collects some basic data but isn’t sure how to use it. He’s looking for help to answer a few simple questions that will allow him to improve the experience for his loyal customers. Due to privacy concerns, he shares only a sample of the data — but hopes it’s enough to write fully functioning SQL queries to help him answer his questions!</p>
    </td>
  </tr>
</table>

- There are 3 key datasets for this case study: **sales**, **menu**, **members**. The entity relationship diagram and example data can be inspected below.

<table>
  <tr>
    <th>Entity Relationship Diagram</th>
    <th>Table 1: sales (preview)</th>
    <th>Table 2: menu</th>
    <th>Table 3: members</th>
  </tr>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/246ce3f6-b9bd-43ea-bf19-641a0e2ffc3d" width="350"/></td>
    <td><img src="https://github.com/user-attachments/assets/9254d11a-572b-4c2e-9113-b38ab40fda4c" width="200"/></td>
    <td><img src="https://github.com/user-attachments/assets/43d07543-5d5d-4f38-9835-29a2147308bc" width="200"/></td>
    <td><img src="https://github.com/user-attachments/assets/493768cb-a4bd-4625-b458-030145d6d3ad" width="150"/></td>

  </tr>
</table>

## Case Study Questions & Answers
 Press this [link](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138), where a fully functioning SQL editor is available to easily access these example datasets.
### 1. What is the total amount each customer spent at the restaurant?

#### 💻 SQL Query
```sql
    SELECT customer_id, SUM(price) as total_amount
    FROM dannys_diner.sales as ds
    JOIN dannys_diner.menu as dm 
    	ON ds.product_id=dm.product_id
    GROUP BY customer_id
    ORDER BY customer_id;
```
#### 🖊 Result

| customer_id | total_amount |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

#### 📜 Explanation 
- Inner join the `sales` and `menu` tables on customer_id.
- Calculate the total amount spent by each customer with `SUM(price)` and rename the column to total_amount.
- Group and order by `customer_id` to display each customer's total spending at the restaurant.

### Answer
- Customer A spent 76$.
- Customer B spent 74$.
- Customer C spent 36$.
---

### 2. How many days has each customer visited the restaurant?

#### 💻 SQL Query
```sql
SELECT customer_id, COUNT(DISTINCT(order_date)) as days
FROM dannys_diner.sales
GROUP BY customer_id
ORDER BY customer_id;
```
#### 🖊 Result
| customer_id | days |
| ----------- | ---- |
| A           | 4    |
| B           | 6    |
| C           | 3    |
#### 📜 Explanation
- Count the days with `COUNT(DISTINCT(order_date))` so as not to count the same day multiple times.
- Group and order by `customer_id`.
### Answer
- Customer A has visited the restaurant 4 times.
- Customer B has visited the restaurant 6 times.
- Customer C has visited the restaurant 3 times.
---
### 3. What was the first item from the menu purchased by each customer?

#### 💻 SQL Query
```sql
WITH first_item AS (
  SELECT customer_id, MIN(order_date) AS first_day
  FROM dannys_diner.sales
  GROUP BY customer_id
)

SELECT fi.customer_id, fi.first_day, dm.product_name
FROM first_item as fi
JOIN dannys_diner.sales as ds
  ON fi.customer_id = ds.customer_id AND fi.first_day = ds.order_date
JOIN dannys_diner.menu dm
  ON ds.product_id = dm.product_id
ORDER BY customer_id;
```
#### 🖊 Result
| customer_id | first_day  | product_name |
| ----------- | ---------- | ------------ |
| A           | 2021-01-01 | sushi        |
| A           | 2021-01-01 | curry        |
| B           | 2021-01-01 | curry        |
| C           | 2021-01-01 | ramen        |
| C           | 2021-01-01 | ramen        |
#### 📜 Explanation
- Find the earliest order date for each customer using the temporary table `first_item`.
- Join the tables `first_item` and `sales` to retrieve the corresponding `product_id` for each customer's first order.
- Join the tables `sales` and `menu` to get the `product_name` for each product_id. 
### Answer
- Customer A's first purchase from the menu was sushi and curry.
- Customer B's first purchase from the menu was curry.
- Customer C's first purchase from the menu was ramen twice.

For customers A and C, it's unclear whether their orders were placed simultaneously or during separate visits on the same day. To determine this with certainty, the exact time of each order would need to be provided.

---
### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?

#### 💻 SQL Query
```sql
SELECT
    product_name,
    COUNT(ds.product_id) as times_purchased
FROM dannys_diner.menu dm
JOIN dannys_diner.sales ds ON
	dm.product_id=ds.product_id
GROUP BY dm.product_id, product_name
ORDER BY dm.product_id DESC
LIMIT 1;
```
#### 🖊 Result
| product_name | times_purchased |
| ------------ | --------------- |
| ramen        | 8               |
#### 📜 Explanation
- Count how many times each item has been purchased with `COUNT(ds.product_id)`, where ds. is needed in front of the product_id to count how many times each product was sold.
- Join the tables menu and sales to retrieve the `product_name` for each product_id.
- Order the product_id column in descending order and show only the first item on the list, which should be the most purchased.
### Answer
- The most purchased item on the menu is ramen, and it was purchased 8 times by all customers.
---
### 5. Which item was the most popular for each customer?

#### 💻 SQL Query
```sql
WITH item_count_bycustomer AS (
SELECT sales.customer_id, sales.product_id, product_name, COUNT(sales.product_id) as times_purchased,
   DENSE_RANK() OVER (
      PARTITION BY sales.customer_id 
      ORDER BY COUNT(sales.customer_id) DESC) AS rank
FROM dannys_diner.sales
JOIN dannys_diner.menu
	ON sales.product_id = menu.product_id
GROUP BY customer_id, product_name, sales.product_id
)

SELECT customer_id, product_name, times_purchased
FROM item_count_bycustomer
WHERE rank = 1
ORDER BY times_purchased DESC
;
```
#### 🖊 Result
| customer_id | product_name | times_purchased |
| ----------- | ------------ | --------------- |
| A           | ramen        | 3               |
| C           | ramen        | 3               |
| B           | curry        | 2               |
| B           | sushi        | 2               |
| B           | ramen        | 2               |

#### 📜 Explanation
- Find how many times each customer purchased each item using the CTE `item_count_bycustomer`.
- Give a ranking number to each row per customer_id using `DENSE_RANK`.
- Inner join the tables sales and menu to get the `product_name` for each product_id.
- Filter with `rank=1` to show the most purchased item(s) for each customer.
### Answer
- Customer A's most popular item was ramen.
- Customer C also preferred ramen.
- Customer B ordered an equal number of times curry, sushi, and ramen, showing no clear preference. 
---
### 6. Which item was purchased first by the customer after they became a member?

#### 💻 SQL Query
```sql
WITH first_order AS (
  SELECT sales.customer_id, sales.product_id, sales.order_date, members.join_date,
    ROW_NUMBER() OVER (
      PARTITION BY sales.customer_id
      ORDER BY sales.order_date ASC) AS rank
  FROM dannys_diner.sales
  JOIN dannys_diner.members
    ON sales.customer_id = members.customer_id
  WHERE sales.order_date > members.join_date
)

SELECT first_order.customer_id, menu.product_name, first_order.order_date, first_order.join_date 
FROM first_order 
JOIN dannys_diner.menu
  ON first_order.product_id = menu.product_id
WHERE rank = 1
ORDER BY customer_id;
```
#### 🖊 Result

| customer_id | product_name | order_date | join_date  |
| ----------- | ------------ | ---------- | ---------- |
| A           | ramen        | 2021-01-10 | 2021-01-07 |
| B           | sushi        | 2021-01-11 | 2021-01-09 |

#### 📜 Explanation
- Create a CTE named `first_order` to identify the first order made after each customer became a member.
- Use the window function `ROW_NUMBER()` to assign a unique rank to each order per customer_id, ordering them by order_date in ascending order.
- Perform an `INNER JOIN` between the sales and members tables on customer_id, and apply a `WHERE` clause to include only orders made after the join_date (sales.order_date > members.join_date).
- Join the `first_order` with the `menu` table on `product_id` to retrieve the name of the purchased product.
- Filter the result with `WHERE rank = 1` to show only the first order per customer after membership.
- Finally, `ORDER BY` customer_id to display the results.
### Answer
- The item that was purchased first after Customer A became a member is ramen.
- The item that was purchased first after Customer B became a member is sushi.
---
### 7. Which item was purchased just before the customer became a member?

#### 💻 SQL Query
```sql
WITH first_order_prior_member AS (
  SELECT sales.customer_id, sales.product_id, sales.order_date, members.join_date,
    ROW_NUMBER() OVER (
      PARTITION BY sales.customer_id
      ORDER BY sales.order_date ASC) AS rank
  FROM dannys_diner.sales
  JOIN dannys_diner.members
    ON sales.customer_id = members.customer_id
  WHERE sales.order_date < members.join_date
)

SELECT first_order_prior_member.customer_id, menu.product_name,
       first_order_prior_member.order_date, first_order_prior_member.join_date 
FROM first_order_prior_member 
JOIN dannys_diner.menu
  ON first_order_prior_member.product_id = menu.product_id
WHERE rank = 1
ORDER BY customer_id;
```
#### 🖊 Result
| customer_id | product_name | order_date | join_date  |
| ----------- | ------------ | ---------- | ---------- |
| A           | sushi        | 2021-01-01 | 2021-01-07 |
| B           | sushi        | 2021-01-04 | 2021-01-09 |

#### 📜 Explanation
- Create a CTE named `first_order_prior_member` to identify the first order made before each customer became a member.
- Use the window function `ROW_NUMBER()` to assign a unique rank to each order per customer_id, ordering them by order_date in descending order.
- Perform an `INNER JOIN` between the sales and members tables on customer_id, and apply a `WHERE` clause to include only orders made before the join_date (sales.order_date < members.join_date).
- Join the `first_order` with the `menu` table on `product_id` to retrieve the name of the purchased product.
- Filter the result with `WHERE rank = 1` to show only the order per customer just before membership.
- Finally, `ORDER BY` customer_id to display the results.
### Answer
The item that both customers purchased just before becoming members was sushi.

---
### 8. What is the total items and amount spent for each member before they became a member?

#### 💻 SQL Query
```sql
SELECT sales.customer_id, COUNT(sales.product_id) as total_items, SUM(menu.price) as total_amount 
FROM dannys_diner.sales
JOIN dannys_diner.menu
  ON sales.product_id = menu.product_id
JOIN dannys_diner.members
  ON sales.customer_id = members.customer_id
WHERE sales.order_date < members.join_date
GROUP BY sales.customer_id
ORDER BY sales.customer_id;
```
#### 🖊 Result
| customer_id | total_items | total_amount |
| ----------- | ----------- | ------------ |
| A           | 2           | 25           |
| B           | 3           | 40           |

#### 📜 Explanation
- Use `COUNT(sales.product_id)` to calculate how many items each customer purchased, and rename it as `total_items`.
- Use `SUM(menu.price)` to calculate the total amount each customer spent, and rename it as `total_amount`.
- Perform `INNER JOIN`s between the `sales` and `menu` tables on product_id, and between the `sales` and `members` tables on customer_id.
- Apply a `WHERE` clause to filter for orders placed before each customer became a member using `sales.order_date < members.join_date`.
- `GROUP` and `ORDER BY` customer_id to organize the results by customer.

### Answer
- Customer A purchased 2 items before becoming a member, spending a total of 25$.
- Customer B purchased 3 items before becoming a member, spending a total of 40$.
---
### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

#### 💻 SQL Query
```sql
WITH point_count AS (
SELECT sales.customer_id,
    SUM(CASE 
      WHEN sales.product_id = 2 OR sales.product_id = 3 THEN menu.price*10
      WHEN sales.product_id = 1 THEN menu.price*20
    END) AS points
FROM dannys_diner.sales
JOIN dannys_diner.menu
  ON sales.product_id = menu.product_id
GROUP BY sales.customer_id, sales.product_id
ORDER BY sales.customer_id)

SELECT customer_id,
       SUM(points) as total_points
FROM point_count
GROUP BY customer_id
ORDER BY customer_id;
```
#### 🖊 Result

| customer_id | total_points |
| ----------- | ------------ |
| A           | 860          |
| B           | 940          |
| C           | 360          |

#### 📜 Explanation
- Create a `CTE` named `point_count` to calculate the total points earned by each customer per item.
- Use two `CASE statements` to assign point values based on the product:
    - For curry (product_id = 2) and ramen (product_id = 3), each $1 spent earns 10 points, so multiply the price by 10.
    - For sushi (product_id = 1), there's a 2x bonus, so multiply the price by 20.
- Use an `INNER JOIN` between the `sales` and `menu` tables on `product_id` to access both purchase and pricing information.
- Calculate with `SUM(points)` the `total_points` and organize the results with `GROUP/ORDER BY customer_id`.
### Answer
- Customer A has 860 points.
- Customer B has 940 points.
- Customer C has 360 points.
---
### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customers A and B have at the end of January?

#### 💻 SQL Query
```sql
WITH point_count AS (
SELECT sales.customer_id,
    SUM(CASE
      WHEN sales.product_id = 1 THEN menu.price*20
      WHEN sales.order_date BETWEEN members.join_date AND 
           (members.join_date + INTERVAL '6 days') THEN menu.price*20
      ELSE menu.price*10
    END) AS points
FROM dannys_diner.sales
JOIN dannys_diner.menu
  ON sales.product_id = menu.product_id
JOIN dannys_diner.members
  ON sales.customer_id = members.customer_id
  AND members.join_date <= sales.order_date
WHERE EXTRACT(MONTH FROM sales.order_date) = 1
GROUP BY sales.customer_id, sales.product_id, members.join_date, sales.order_date
)

SELECT customer_id,
       SUM(points) as total_points
FROM point_count
GROUP BY customer_id
ORDER BY customer_id;
```
#### 🖊 Result
| customer_id | total_points |
| ----------- | ------------ |
| A           | 1020         |
| B           | 320          |

#### 📜 Explanation
- Create a `CTE` named `point_count` to calculate the total points earned by each customer per item.
- Use `CASE statements` to assign point values based on the product:
    - For sushi (product_id = 1), there's a 2x bonus, so multiply the price by 20.
    - For any item ordered within the first week after becoming a member (join_date plus the next 6 days), multiply all of the items' prices by 20.
    - For all other orders (e.g, curry and ramen) after the first week, multiply the prices by 10.
- Use an `INNER JOIN` between the `sales` and `menu` tables on `product_id` to access both purchase and pricing information, as well as `sales` and `members` tables to access join_date and order_date.
    - Use the condition `members.join_date <= sales.order_date` to ensure only orders placed after a customer became a member are considered.
 - Use the `WHERE` clause `EXTRACT(MONTH FROM sales.order_date) = 1` to filter orders placed in January.
 - Calculate with `SUM(points)` the `total_points` and organize the results with `GROUP/ORDER BY customer_id`.
### Answer
- Customer A has 1020 points.
- Customer B has 320 points.
---
<h2 align="center">
Bonus Questions
</h2>

**Create basic data tables that Danny and his team can use to quickly derive insights without needing to join the underlying tables using SQL.**
### Join All The Things

```sql
WITH is_member AS (
  SELECT sales.customer_id, sales.order_date, sales.product_id,
    CASE
       WHEN sales.customer_id = 'A' AND members.join_date <= sales.order_date THEN 'Y'
       WHEN sales.customer_id = 'B' AND members.join_date <= sales.order_date THEN 'Y'
       ELSE 'N'
    END AS member
  FROM dannys_diner.sales 
  LEFT JOIN dannys_diner.members
       ON sales.customer_id = members.customer_id
)

SELECT is_member.customer_id, is_member.order_date, menu.product_name, menu.price, is_member.member
FROM is_member
JOIN dannys_diner.menu
  ON is_member.product_id = menu.product_id
ORDER BY is_member.customer_id, is_member.order_date;
```
#### 🧮 Result table 

| customer_id | order_date | product_name | price | member |
| ----------- | ---------- | ------------ | ----- | ------ |
| A           | 2021-01-01 | sushi        | 10    | N      |
| A           | 2021-01-01 | curry        | 15    | N      |
| A           | 2021-01-07 | curry        | 15    | Y      |
| A           | 2021-01-10 | ramen        | 12    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| B           | 2021-01-01 | curry        | 15    | N      |
| B           | 2021-01-02 | curry        | 15    | N      |
| B           | 2021-01-04 | sushi        | 10    | N      |
| B           | 2021-01-11 | sushi        | 10    | Y      |
| B           | 2021-01-16 | ramen        | 12    | Y      |
| B           | 2021-02-01 | ramen        | 12    | Y      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-07 | ramen        | 12    | N      |

#### 📜 Explanation
- Create a CTE named `is_member` to assign a value of 'Y' or 'N' depending on whether the customer was a member at the time of placing the order.
- Use `LEFT JOIN` between the tables `sales` and `members` to retain all sales information.
- In the final `SELECT`, return only the relevant columns from the `is_member` CTE, such as `customer_id`, `order_date`, `product_name`, `price`, and `member`.
- `INNER JOIN` the tables `is_member` and `menu` to access the product names. 
- `ORDER BY` `customer_id` and `order_date` to organize the results.

### Rank All The Things
**Danny also requires further information about the `ranking` of customer products, but he purposely does not need the ranking for non-member purchases, so he expects null ranking values for the records when customers are not yet part of the loyalty program.**

```sql
WITH is_member AS (
  SELECT sales.customer_id, sales.order_date, sales.product_id,
    CASE
  	   WHEN sales.customer_id = 'A' AND members.join_date <= sales.order_date THEN 'Y'
       WHEN sales.customer_id = 'B' AND members.join_date <= sales.order_date THEN 'Y'
       ELSE 'N'
    END AS member
  FROM dannys_diner.sales 
  LEFT JOIN dannys_diner.members
	ON sales.customer_id = members.customer_id
)

SELECT is_member.customer_id, is_member.order_date, menu.product_name, menu.price, is_member.member, 
	CASE
      WHEN member = 'N' THEN NULL
      ELSE RANK() OVER (
      PARTITION BY is_member.customer_id, is_member.member
      ORDER BY is_member.order_date)
    END as ranking
FROM is_member
JOIN dannys_diner.menu
	ON is_member.product_id = menu.product_id
ORDER BY is_member.customer_id, is_member.order_date;
```
#### 🧮 Result table

| customer_id | order_date | product_name | price | member | ranking |
| ----------- | ---------- | ------------ | ----- | ------ | ------- |
| A           | 2021-01-01 | sushi        | 10    | N      | null    |
| A           | 2021-01-01 | curry        | 15    | N      | null    |
| A           | 2021-01-07 | curry        | 15    | Y      | 1       |
| A           | 2021-01-10 | ramen        | 12    | Y      | 2       |
| A           | 2021-01-11 | ramen        | 12    | Y      | 3       |
| A           | 2021-01-11 | ramen        | 12    | Y      | 3       |
| B           | 2021-01-01 | curry        | 15    | N      | null    |
| B           | 2021-01-02 | curry        | 15    | N      | null    |
| B           | 2021-01-04 | sushi        | 10    | N      | null    |
| B           | 2021-01-11 | sushi        | 10    | Y      | 1       |
| B           | 2021-01-16 | ramen        | 12    | Y      | 2       |
| B           | 2021-02-01 | ramen        | 12    | Y      | 3       |
| C           | 2021-01-01 | ramen        | 12    | N      | null    |
| C           | 2021-01-01 | ramen        | 12    | N      | null    |
| C           | 2021-01-07 | ramen        | 12    | N      | null    |

#### 📜 Explanation
- Create a CTE named `is_member` to assign a value of 'Y' or 'N' depending on whether the customer was a member at the time of placing the order.
- Use `LEFT JOIN` between the tables `sales` and `members` to retain all sales information.
- In the final `SELECT`, return only the relevant columns from the `is_member` CTE.
- Use `CASE statements` to assign ranking values. All non-member purchases get null ranking values. For members `PARTITION BY customer_id, member` and `ORDER BY order_date`.
- `INNER JOIN` the tables `is_member` and `menu` to access the product names.
- `ORDER BY` `customer_id` and `order_date` to organize the results.
