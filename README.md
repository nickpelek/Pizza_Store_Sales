# Project Overview

This data analysis project delves into the operational dynamics of a pizza store throughout 2015. By examining various aspects of the store's data, we aim to uncover **significant trends**, **identify areas for improvement**, and offer **data-driven recommendations** to optimize its performance. Ultimately, this project seeks to provide actionable insights that can inform strategic **decision-making** and contribute to the store's **success**.

Insights and recommendations will be provided by answering some key questions, such as:
- How many orders does the store have on average each day?
- Which are the peak hours of the store?
- How many pizzas are typically in an order? 
- Which are the store's top 3 bestselling pizzas?
- Compared to store's bestselling pizzas, which ones are less popular?
- How much revenue did the pizza store make in 2015?
- Can we indentify any seasonality in revenue?


## Table of contents
- [Tools Used](#Tools-Used)
- [Data Structure](#Data-Structure)
- [Data Analysis Process (*SQL Queries*)](#Data-Analysis-Process-SQL-Queries)
- [Project Summary](#Project-Summary)
- [Recommendations](#Recommendations)


## Tools used
- **MS Excel** (*Data exploring - Formatting*)
- **MySQL workbench** (*Data Analysis*)
- **Tableau Public** (*Creating Reports*)
> ***You can see the interactive Tableau Public Dashboard* [here](https://public.tableau.com/app/profile/nickpelek/viz/PizzaStoreSalesProject/Dashboard1)**  
## Data Structure
Pizza store sales database structure consists of four tables: *orders*, *order_details*, *pizza* and *pizza_types* with row counts **21.350**, **48.620**, **96** and **32** respectively.
![imageedit_1_6745671806](https://github.com/user-attachments/assets/83721a4d-867e-4cad-8d7e-847aae42d131)


## Data Analysis Process (*SQL Queries*)
> In this section we'll break down the project questions with SQL queries to find some useful insights.
#### -How many orders does the store have on average each day?
```sql
WITH avg_orders AS(
	SELECT DATE(date_time) AS days, COUNT(*) AS total_orders FROM orders
	GROUP BY days)
SELECT ROUND(AVG(total_orders),0) AS avg_orders_per_day FROM avg_orders;
```
![query 1 (avg pizzas per day)](https://github.com/user-attachments/assets/9c1c23cd-397f-4afb-b818-28f078d3b571)

> Pizza store has on average **60 orders per day**

#### -Which are the peak hours of the store?
> *When total orders are: **< 1000 = Not peak hour , [1000, 2000] = Mid peak hour, > 2000 = Peak hour***
```sql
SELECT HOUR(date_time) AS hours, COUNT(order_id) AS total_orders,
(CASE
    WHEN COUNT(order_id) < 1000 THEN 'Not peak hour'
    WHEN COUNT(order_id)  BETWEEN 1000 AND 2000 THEN 'Mid peak hour'
    WHEN COUNT(order_id) > 2000 THEN 'Peak hour'
END) AS hour_performance FROM orders
GROUP BY hours
ORDER BY hours;
```
![query 2 (peak hours)](https://github.com/user-attachments/assets/aac2dd87-0b6f-4555-a21e-28f85b486857)

> Store's **peak hours** are:<br>
> **1) 12:00 p.m.<br>
> 2) 13:00 p.m.<br>
> 3) 17:00 p.m.<br>
> 4) 18:00 p.m.<br>
> 5) 19:00 p.m.**

#### -How many pizzas are typically in an order? 
```sql
WITH avg_pizzas AS
	(SELECT order_id, SUM(quantity) AS total_pizzas FROM order_details
	GROUP BY order_id)
SELECT ROUND(AVG(total_pizzas),0) AS pizzas_per_order FROM avg_pizzas;
```
![query 3 (avg pizzas per order)](https://github.com/user-attachments/assets/8f94e3cf-e73f-483c-a9c5-217c8beadd3d)

> There are **2 pizzas** on average in every order
#### -Which are the store's top 3 bestselling pizzas?
```sql
SELECT pt.name, SUM(od.quantity) AS total_pizzas FROM pizza_types AS pt
JOIN pizza AS p
ON pt.pizza_type_id = p.pizza_type_id
JOIN order_details AS od
ON od.pizza_id = p.pizza_id
GROUP BY pt.name
ORDER BY total_pizzas DESC
LIMIT 3;
```
![query 4 (3 top selling pizzas)](https://github.com/user-attachments/assets/0517195a-b1c2-4c3e-95a3-40b6c35d48a5)

> Store's **top 3 bestsellers** are: <br>
> 1) **The Classic Deluxe Pizza** (*with 2453 pizzas sold*)<br>
> 2) **The Barbecue Chicken Pizza** (*with 2432 pizzas sold*)<br>
> 3) **The Hawaiian Pizza** (*with 2422 pizzas sold*)
#### -Compared to store's bestselling pizzas, which ones are less popular?
```sql
SELECT pt.name, SUM(od.quantity) AS total_pizzas FROM pizza_types AS pt
JOIN pizza AS p
ON pt.pizza_type_id = p.pizza_type_id
JOIN order_details AS od
ON od.pizza_id = p.pizza_id
GROUP BY pt.name
ORDER BY total_pizzas;
```
![query 5 (bottom selling pizzas)](https://github.com/user-attachments/assets/7736cef6-a7e9-4e2c-b84d-a51a1212f548)

> Store's **less popular** pizzas are:<br>
> 1) **The Brie Carre Pizza** (*with 490 pizzas sold*)
> 2) **The Mediterranean Pizza** (*with 934 pizzas sold*)
> 3) **The Calabrese Pizza** (*with 937 pizzas sold*)
									   
#### -How much revenue did the pizza store make in 2015?
```sql
SELECT YEAR(o.date_time) AS year_2015, ROUND(SUM(p.price * od.quantity),0) AS revenue FROM orders AS o
JOIN order_details AS od
ON o.order_id = od.order_id
JOIN pizza AS p
ON od.pizza_id = p.pizza_id
GROUP BY year_2015;
```
![query 6 (2015 revenue)](https://github.com/user-attachments/assets/c55ac53d-4833-4b9a-8648-c6e02af8ae56)

> Store's **revenue** in 2015 was **817.860**
#### -Can we indentify any seasonality in revenue?
```sql
SELECT MONTHNAME(o.date_time) AS months, ROUND(SUM(p.price * od.quantity),0) AS revenue FROM orders AS o
JOIN order_details AS od
ON o.order_id = od.order_id
JOIN pizza AS p
ON od.pizza_id = p.pizza_id
GROUP BY months;

```
![query 7 (revenue seasonality)](https://github.com/user-attachments/assets/013b5656-ca34-424c-9003-21ba6f3f3e3c)

> Seasonality in revenue starts **from *spring* till the end of the *summer***, where revenue gets higher than usual (with the exceptions of **January & November**).

## Project Summary
> Moving on to the **summary** of this project, here are some **key points** of it:

- The Pizza store has on average 60 orders per day, while the most orders (***store's peak hours*** 🕐) occur at: ***12:00**, **13:00**, **17:00**, **18:00**, **19:00***
- On *average*, there are **2 pizzas** per order 
- The store's **best-selling** pizzas are: ***1) The Classic Deluxe Pizza (with 2453 pizzas sold)**, **2) The Barbecue Chicken Pizza (with 2432 pizzas sold)**, **3) The Hawaiian Pizza (with 2422 pizzas sold)***. While store's **less popular** pizzas are: ***1) The Brie Carre Pizza (with 490 pizzas sold)**, **2) The Mediterranean Pizza (with 934 pizzas sold)**, **3) The Calabrese Pizza (with 937 pizzas sold)***
- The store generated ***$817,860*** in pizza sales during the year *2015*.
- The store experiences **revenue increases** during the ***spring*** and ***summer*** months, with the exception of *January* and *November*.

![Pizza sales Dashboard](https://github.com/user-attachments/assets/0baa8bbf-7996-4566-84b6-c5a85d6111fb)

## Recommendations
- Implement ***promotions*** or ***special offers*** during store hours with less orders to **attract more customers**.
- Utilize **social media platforms** to promote ***new pizzas***, ***special offers*** and engage with customers.
- Implement a **loyalty program** to reward repeat customers and encourage them to return.
- Ensure **adequate staffing** during peak hours to handle increased customer demand and maintain efficient service.
- Consider adding a **special offer** or **discount** to the **less popular** pizzas to boost their sales. If there aren’t any positive signs, consider **excluding** them from store’s menu. 

----
💻📊📈😄
