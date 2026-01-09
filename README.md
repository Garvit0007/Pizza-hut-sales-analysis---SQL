This comprehensive **Pizza Sales Analysis** provides a 360-degree view of business performance using MySQL. It bridges the gap between raw data and strategic decision-making by covering revenue, operations, and customer behavior.

---

## 1. Executive Summary & Schema

The analysis focuses on extracting value from transactional tables (`orders`, `pizzas`, and `order_details`). By joining these tables, we can answer critical questions about financial health and operational efficiency.

---

## 2. Financial & Pricing Performance

Understanding how much money is coming in and the price points that drive it.

* **Total Revenue:** The bottom-line gross earnings.
* **Highest Price:** Identifying the premium product in the catalog.
* **Common Price (Mode):** Determining the most frequent price point customers encounter.

```sql
-- Total Revenue & Average Order Value (AOV)
SELECT 
    ROUND(SUM(od.quantity * p.price), 2) AS total_revenue,
    ROUND(SUM(od.quantity * p.price) / COUNT(DISTINCT od.order_id), 2) AS avg_order_value
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id;

-- Highest Priced Pizza
SELECT name, price FROM pizza_types 
JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
ORDER BY price DESC LIMIT 1;

```

---

## 3. Product Popularity (Top 5)

Identifying the "Star" products helps with inventory management and marketing focus.

```sql
SELECT 
    pt.name, 
    SUM(od.quantity) AS total_quantity 
FROM pizza_types pt
JOIN pizzas p ON pt.pizza_type_id = p.pizza_type_id
JOIN order_details od ON p.pizza_id = od.pizza_id
GROUP BY pt.name 
ORDER BY total_quantity DESC 
LIMIT 5;

```

---

## 4. Operational Efficiency & Time Distribution

Analyzing **when** sales happen and **how many** fail allows for better staffing and quality control.

* **Hourly Distribution:** Reveals "rush hours" (e.g., the 6:00 PM dinner peak).
* **Cancellation Analysis:** Measures revenue leakage and service gaps.

```sql
-- Hourly Distribution
SELECT HOUR(order_time) AS order_hour, COUNT(order_id) AS order_count
FROM orders
GROUP BY order_hour
ORDER BY order_hour;

-- Cancellation Rate
SELECT 
    order_status, 
    COUNT(*) AS total_count,
    ROUND(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM orders), 2) AS percentage
FROM orders
GROUP BY order_status;

```

---

## 5. Summary Insight Table

| Metric Category | Key SQL Insight | Business Action |
| --- | --- | --- |
| **Revenue** | Total & Avg Order Value | Set sales targets for next quarter. |
| **Pricing** | Highest vs. Common Price | Adjust "Meal Deal" prices to match the mode. |
| **Volume** | Top 5 Best-Sellers | Ensure supply chain priority for these ingredients. |
| **Time** | Hourly Sales Peaks | Increase kitchen staff during peak hours. |
| **Status** | Cancellation % | Investigate reasons for orders not being completed. |

---

**Would you like me to generate a specific SQL query to find the "Revenue Loss" specifically from those cancelled orders?**
