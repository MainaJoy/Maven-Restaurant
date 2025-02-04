## Maven-Restaurant
A MySQL project on Maven Restaurant.

The following are the business questions and how I solved them.
```sql
-- Objective 1: Explore the items table
-- View the menu_items table and write a query to find the number of items on the menu
SELECT COUNT(*) AS number_of_items
FROM menu_items;

-- What are the least and most expensive items on the menu?
SELECT 
	  MAX(price) AS `most expensive item`,
      MIN(price) AS `least expnesive item`
FROM menu_items;

-- How many Italian dishes are on the menu? What are the least and most expensive Italian dishes on the menu?
SELECT COUNT(*) AS `italian dishes`,
       MAX(price) AS `most expensive italian dish`,
       MIN(price) AS `least expensive italian dish`
FROM menu_items
WHERE category LIKE '%Italian%';

-- How many dishes are in each category? What is the average dish price within each category?
SELECT category,
       COUNT(*) AS `number of dishes`,
       ROUND(AVG(price),2) AS `avg price`
FROM menu_items
GROUP BY category;

-- Objective 2: Explore the orders table
-- View the order_details table. What is the date range of the table?
SELECT MIN(order_date) AS `earliest order`,
       MAX(order_date) AS `latest order`
FROM order_details;

-- How many orders were made within this date range? How many items were ordered within this date range?
SELECT COUNT(DISTINCT order_id) AS `no of orders`,
       COUNT(item_id) AS `no of items`
FROM order_details;

-- Which orders had the most number of items?
SELECT order_id, COUNT(item_id) AS `no of items`
FROM order_details
GROUP BY order_id
ORDER BY 2 DESC;

-- How many orders had more than 12 items?
SELECT COUNT(*) AS `orders with items > 12`
FROM 
(SELECT order_id, COUNT(item_id) AS `no of items`
FROM order_details
GROUP BY order_id
HAVING COUNT(item_id) > 12
ORDER BY 2 DESC) sub
;

-- Objective 3: Analyze customer behavior
-- Combine the menu_items and order_details tables into a single table
SELECT *
FROM order_details AS od
LEFT JOIN menu_items AS mi
ON mi.menu_item_id = od.item_id;

-- What were the least and most ordered items? What categories were they in?
-- least ordered

SELECT category,
       mi.item_name, 
       COUNT(*) AS num_items
FROM order_details AS od
LEFT JOIN menu_items AS mi
ON mi.menu_item_id = od.item_id
GROUP BY 1,2
ORDER BY 3;

-- most ordered
SELECT category,
       mi.item_name, 
       COUNT(*) AS num_items
FROM order_details AS od
LEFT JOIN menu_items AS mi
ON mi.menu_item_id = od.item_id
GROUP BY 1,2
ORDER BY 3 DESC;

-- What were the top 5 orders that spent the most money?
SELECT order_id,
		SUM(price) AS amount_spent
FROM order_details AS od
LEFT JOIN menu_items AS mi
ON mi.menu_item_id = od.item_id
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;

-- View the details of the highest spend order. Which specific items were purchased?
WITH highest_id AS(
SELECT od.order_id
FROM order_details AS od
LEFT JOIN menu_items AS mi
ON mi.menu_item_id = od.item_id
GROUP BY 1
ORDER BY SUM(mi.price) DESC
LIMIT 1)
SELECT od.order_details_id, od.order_id, od.order_date, od.order_time, od.item_id, mi.item_name, mi.category, mi.price
FROM order_details AS od
LEFT JOIN menu_items AS mi
ON od.item_id = mi.menu_item_id
INNER JOIN highest_id hd
ON od.order_id = hd.order_id
;

-- BONUS: View the details of the top 5 highest spend orders
WITH highest_id AS(
SELECT od.order_id
FROM order_details AS od
LEFT JOIN menu_items AS mi
ON mi.menu_item_id = od.item_id
GROUP BY 1
ORDER BY SUM(mi.price) DESC
LIMIT 5)
SELECT od.order_details_id, od.order_id, od.order_date, od.order_time, od.item_id, mi.item_name, mi.category, mi.price
FROM order_details AS od
LEFT JOIN menu_items AS mi
ON od.item_id = mi.menu_item_id
INNER JOIN highest_id hd
ON od.order_id = hd.order_id;
```
