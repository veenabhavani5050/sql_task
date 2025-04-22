# sql_task


-- 1. Create the database
CREATE DATABASE ecommerce;

-- 2. Show all databases
SHOW DATABASES;

-- 3. Use the ecommerce database
USE ecommerce;

-- 4. Create the customers table
CREATE TABLE customers (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    address VARCHAR(255)
);

-- 5. Create the orders table
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    total_amount DECIMAL(10,2),
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);

-- 6. Create the products table
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    price DECIMAL(10,2),
    description TEXT
);

-- 7. Show all tables in the ecommerce database
SHOW TABLES;

-- 8. Describe the structure of the 'products' table
DESCRIBE products;

-- 9. Insert  data into customers
INSERT INTO customers (name, email, address) VALUES
('Veena', 'veena@gmail.com', '1/23 Hosur'),
('Aayath', 'aayath@gmail.com', '1/24 Hosur'),
('Alina', 'alina@gmail.com', '1/25 Hosur');

-- 10. Insert  data into products
INSERT INTO products (name, price, description) VALUES
('Eyeliner', 30.00, 'A description'),
('Lipstick', 60.00, 'B description'),
('Nail Polish', 50.00, 'C description'),
('Makeup Kit', 80.00, 'D description');

-- 11. Insert  data into orders
INSERT INTO orders (customer_id, order_date, total_amount) VALUES
(1, CURDATE() - INTERVAL 10 DAY, 120.00),
(2, CURDATE() - INTERVAL 40 DAY, 90.00),
(1, CURDATE() - INTERVAL 5 DAY, 180.00),
(3, CURDATE(), 200.00);

-- 1. Retrieve all customers who have placed an order in the last 30 days
SELECT DISTINCT c.*
FROM customers c
JOIN orders o ON c.id = o.customer_id
WHERE o.order_date >= CURDATE() - INTERVAL 30 DAY;

-- 2. Get the total amount of all orders placed by each customer
SELECT c.name, SUM(o.total_amount) AS total_spent
FROM customers c
JOIN orders o ON c.id = o.customer_id
GROUP BY c.id;

-- 3. Update the price of Product C (Nail Polish) to 45.00
UPDATE products
SET price = 45.00
WHERE name = 'Nail Polish';

-- 4. Add a new column 'discount' to the products table
ALTER TABLE products
ADD COLUMN discount DECIMAL(5,2) DEFAULT 0.00;

-- 5. Retrieve the top 3 products with the highest price
SELECT *
FROM products
ORDER BY price DESC
LIMIT 3;

-- 6. Get the names of customers who have ordered Product A
-- First, normalize: create order_items table and insert sample data
CREATE TABLE order_items (
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT,
    product_id INT,
    quantity INT,
    FOREIGN KEY (order_id) REFERENCES orders(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);

-- Sample order_items data (make sure product_id 1 = Eyeliner)
INSERT INTO order_items (order_id, product_id, quantity) VALUES
(1, 1, 2), -- Eyeliner
(1, 2, 1),
(3, 3, 3),
(4, 1, 1),
(4, 4, 1);

-- Now run the query to get names of customers who ordered 'Eyeliner'
SELECT DISTINCT c.name
FROM customers c
JOIN orders o ON c.id = o.customer_id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id
WHERE p.name = 'Eyeliner';

-- 7. Join orders and customers to get customer name and order date
SELECT c.name, o.order_date
FROM orders o
JOIN customers c ON o.customer_id = c.id;

-- 8. Retrieve orders with total amount greater than 150.00
SELECT * FROM orders
WHERE total_amount > 150.00;

-- 9. (Already done above) Normalize orders using order_items

-- 10. Retrieve the average total of all orders
SELECT AVG(total_amount) AS avg_order_value
FROM orders;

