# Payment---SQL
 Use SQL queries to extract and analyze data from a database.
-- Task 4: SQL for Data Analysis Project
-- =====================================
-- 1. Create the 'payment' table
CREATE TABLE payment (
    payment_id INTEGER PRIMARY KEY,
    customer_id INTEGER,
    staff_id INTEGER,
    rental_id INTEGER,
    amount NUMERIC(5,2),
    payment_date TIMESTAMP);

-- 2. Load CSV data into 'payment' table
-- NOTE: This will only work if the PostgreSQL server has permission to read the file.

COPY payment
FROM 'C:/Users/selsha/Downloads/Tableau Dataset/Dataset/payment.csv'
DELIMITER ','
CSV HEADER;

-- 3. SQL Queries as per Task 4 Guide

-- a) SELECT, WHERE, ORDER BY, GROUP BY
-- Top 10 highest payment transactions
SELECT payment_id, customer_id, amount, payment_date
FROM payment
ORDER BY amount DESC
LIMIT 10;


-- Total amount paid by each customer


SELECT customer_id, SUM(amount) AS total_spent
FROM payment
GROUP BY customer_id
ORDER BY total_spent DESC; 



-- b) JOINS (example, if customer table exists)
SELECT p1.payment_id AS payment_1,
       p1.customer_id AS customer_1,
       p2.payment_id AS payment_2,
       p2.customer_id AS customer_2,
       p1.payment_date
FROM payment p1
JOIN payment p2 
  ON DATE(p1.payment_date) = DATE(p2.payment_date)
 AND p1.payment_id < p2.payment_id
ORDER BY p1.payment_date;



-- c) Subqueries
SELECT customer_id, SUM(amount) AS total_paid
FROM payment
GROUP BY customer_id
HAVING SUM(amount) > (
    SELECT AVG(total_amount)
    FROM (
        SELECT SUM(amount) AS total_amount
        FROM payment
        GROUP BY customer_id) sub);



-- d) Aggregate Functions
SELECT DATE(payment_date) AS pay_date,
       SUM(amount) AS total_revenue,
       COUNT(payment_id) AS transaction_count
FROM payment
GROUP BY pay_date
ORDER BY pay_date;



-- e) Create Views
CREATE OR REPLACE VIEW customer_total_spend AS
SELECT customer_id, SUM(amount) AS total_spent
FROM payment
GROUP BY customer_id;



-- f) Optimize with Index
CREATE INDEX idx_payment_date ON payment(payment_date);

