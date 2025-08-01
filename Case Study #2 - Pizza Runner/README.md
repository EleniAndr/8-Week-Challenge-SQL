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
  - In the exclusions and extras columns, there are a few `null` values and empty/missing strings.

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

  - Course of action:
    - Remove t 

#### Table: runner_orders

<h2 align="center">
Case Study Questions
</h2>

This case study has LOTS of questions - they are broken up by area of focus, including:

- [Pizza Metrics](https://github.com/EleniAndr/A.-Pizza-Metrics/blob/main/README.md#a-pizza-metrics)
- [Runner and Customer Experience]()
- [Ingredient Optimisation]()
- [Pricing and Ratings]()
- [Bonus DML Challenges (DML = Data Manipulation Language)]()
