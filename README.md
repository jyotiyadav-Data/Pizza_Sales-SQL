# Pizza_Sales-SQL

# Pizza Sales Analysis (SQL)

This repository contains a SQL-based data analysis project focused on answering real business questions using pizza sales data.

The goal of this project is straightforward: **use SQL queries to extract meaningful insights from raw transactional data** — no dashboards, no overcomplication.

---

## Project Overview

The analysis covers:

* Total orders and total revenue
* Highest-priced pizza
* Most commonly ordered pizza size
* Top-selling pizzas by quantity and revenue
* Category-wise sales performance
* Order distribution by hour and by day
* Cumulative revenue trends over time

All analysis is performed using **SQL queries with joins, aggregations, grouping, and window functions**.

---

## Dataset Description

The dataset consists of multiple relational tables, including:

* `orders`
* `order_details`
* `pizzas`
* `pizza_types`

These tables are joined as required to answer business-focused questions.

---

## SQL Queries Used

Below are the **exact SQL-style queries** used in this project, written in a clean, interview-acceptable format. These match the logic shown in the PDF.

---

### 1. Retrieve the total number of orders placed

```sql
SELECT COUNT(order_id) AS total_orders
FROM orders;
```

---

### 2. Calculate the total revenue generated from pizza sales

```sql
SELECT 
    ROUND(SUM(order_details.quantity * pizzas.price), 2) AS total_revenue
FROM order_details
JOIN pizzas 
    ON order_details.pizza_id = pizzas.pizza_id;
```

---

### 3. Identify the highest-priced pizza

```sql
SELECT 
    pizza_types.name, 
    pizzas.price
FROM pizza_types
JOIN pizzas 
    ON pizza_types.pizza_type_id = pizzas.pizza_type_id
ORDER BY pizzas.price DESC
LIMIT 1;
```

---

### 4. Identify the most commonly ordered pizza size

```sql
SELECT 
    pizzas.size, 
    COUNT(order_details.order_details_id) AS order_count
FROM pizzas
JOIN order_details 
    ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_count DESC
LIMIT 1;
```

---

### 5. List the top 5 most ordered pizza types along with their quantity

```sql
SELECT 
    pizza_types.name, 
    SUM(order_details.quantity) AS total_quantity
FROM pizza_types
JOIN pizzas 
    ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN order_details 
    ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizza_types.name
ORDER BY total_quantity DESC
LIMIT 5;
```

---

### 6. Find the total quantity ordered for each pizza category

```sql
SELECT 
    pizza_types.category, 
    SUM(order_details.quantity) AS total_quantity
FROM pizza_types
JOIN pizzas 
    ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN order_details 
    ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizza_types.category
ORDER BY total_quantity DESC;
```

---

### 7. Determine the distribution of orders by hour of the day

```sql
SELECT 
    HOUR(order_time) AS order_hour, 
    COUNT(order_id) AS order_count
FROM orders
GROUP BY order_hour
ORDER BY order_hour;
```

---

### 8. Find category-wise distribution of pizzas ordered

```sql
SELECT 
    pizza_types.category, 
    COUNT(order_details.order_details_id) AS order_count
FROM pizza_types
JOIN pizzas 
    ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN order_details 
    ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizza_types.category;
```

---

### 9. Calculate the average number of pizzas ordered per day

```sql
SELECT 
    ROUND(AVG(daily_quantity), 2) AS avg_pizzas_per_day
FROM (
    SELECT 
        orders.order_date, 
        SUM(order_details.quantity) AS daily_quantity
    FROM orders
    JOIN order_details 
        ON orders.order_id = order_details.order_id
    GROUP BY orders.order_date
) AS daily_orders;
```

---

### 10. Determine the top 3 most ordered pizza types based on revenue

```sql
SELECT 
    pizza_types.name, 
    ROUND(SUM(order_details.quantity * pizzas.price), 2) AS revenue
FROM pizza_types
JOIN pizzas 
    ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN order_details 
    ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizza_types.name
ORDER BY revenue DESC
LIMIT 3;
```

---

### 11. Calculate the percentage contribution of each pizza category to total revenue

```sql
SELECT 
    pizza_types.category,
    ROUND(
        (SUM(order_details.quantity * pizzas.price) /
        (SELECT SUM(order_details.quantity * pizzas.price)
         FROM order_details
         JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id)) * 100, 2
    ) AS revenue_percentage
FROM pizza_types
JOIN pizzas 
    ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN order_details 
    ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizza_types.category
ORDER BY revenue_percentage DESC;
```

---

### 12. Analyze cumulative revenue generated over time

```sql
SELECT 
    order_date,
    SUM(revenue) OVER (ORDER BY order_date) AS cumulative_revenue
FROM (
    SELECT 
        orders.order_date,
        SUM(order_details.quantity * pizzas.price) AS revenue
    FROM orders
    JOIN order_details 
        ON orders.order_id = order_details.order_id
    JOIN pizzas 
        ON order_details.pizza_id = pizzas.pizza_id
    GROUP BY orders.order_date
) AS daily_revenue;
```

---

### 13. Determine the top 3 pizzas by revenue within each category

```sql
SELECT name, category, revenue
FROM (
    SELECT 
        pizza_types.name,
        pizza_types.category,
        ROUND(SUM(order_details.quantity * pizzas.price), 2) AS revenue,
        RANK() OVER (
            PARTITION BY pizza_types.category
            ORDER BY SUM(order_details.quantity * pizzas.price) DESC
        ) AS rnk
    FROM pizza_types
    JOIN pizzas 
        ON pizza_types.pizza_type_id = pizzas.pizza_type_id
    JOIN order_details 
        ON pizzas.pizza_id = order_details.pizza_id
    GROUP BY pizza_types.name, pizza_types.category
) ranked_pizzas
WHERE rnk <= 3;

---

## Full Project Report (PDF)

The complete project report with all SQL queries and outputs is available here:

👉 **LinkedIn Project Post:** 👉 [LinkedIn Project Post](https://www.linkedin.com/posts/jyotiyadav-data_pizzasales-sql-activity-7407644957299138560-aLWB)

> Note: LinkedIn sometimes restricts PDF preview access for non-logged-in users. Please open the link while logged into LinkedIn to view the full PDF.

---

## Key Learnings

- Writing clean and readable SQL queries
- Using joins effectively across multiple tables
- Translating business questions into SQL logic
- Understanding sales and demand patterns through data

---

## Tools Used

- SQL (MySQL/PostgreSQL compatible queries)

---

## Author

JYOTI YADAV

---

If you are reviewing this project for hiring or evaluation purposes, feel free to check the full PDF report linked above.

```
