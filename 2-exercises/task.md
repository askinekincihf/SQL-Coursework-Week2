# E-Commerce Database

In this homework, you are going to work with an ecommerce database. In this database, you have `products` that `consumers` can buy from different `suppliers`. Customers can create an `order` and several products can be added in one order.

## Submission

Below you will find a set of tasks for you to complete to set up a database for an e-commerce app.

When you have finished all of the questions - open a pull request with your answers to the `Databases-Homework` repository.

## Setup

To prepare your environment for this homework, open a terminal and create a new database called `cyf_ecommerce`:

```sql
createdb cyf_ecommerce
```

Import the file [`cyf_ecommerce.sql`](./cyf_ecommerce.sql) in your newly created database:

```sql
psql -d cyf_ecommerce -f cyf_ecommerce.sql
```

Open the file `cyf_ecommerce.sql` in VSCode and examine the SQL code. Take a piece of paper and draw the database with the different relationships between tables (as defined by the REFERENCES keyword in the CREATE TABLE commands). Identify the foreign keys and make sure you understand the full database schema.

## Task

Once you understand the database that you are going to work with, solve the following challenge by writing SQL queries using everything you learned about SQL:

To submit this homework write the correct commands for each question here:

```sql

1. Retrieve all the customers names and addresses who live in the United States

SELECT name, address
FROM customers
WHERE country = 'United States';

2. Retrieve all the customers in ascending name sequence

SELECT *
FROM customers
ORDER BY name ASC;

3. Retrieve all the products whose name contains the word `socks`

SELECT *
FROM products
WHERE product_name ~* 'socks';

4. Retrieve all the products which cost more than 100 showing product id, name, unit price and supplier id.

SELECT p.id, p.product_name, p_a.unit_price, sup.supplier_name, sup.id as supplier_id
FROM products as p
INNER JOIN product_availability as p_a ON p_a.prod_id = p.id
INNER JOIN suppliers as sup ON sup.id = p_a.supp_id
WHERE p_a.unit_price > 100;

5. Retrieve the 5 most expensive products

SELECT p.product_name, p_a.unit_price
FROM products as p
INNER JOIN product_availability as p_a ON p_a.prod_id = p.id
ORDER BY p_a.unit_price DESC
LIMIT 5;

6. Retrieve all the products with their corresponding suppliers. The result should only contain the columns `product_name`, `unit_price` and `supplier_name`

SELECT p.product_name, p_a.unit_price, sup.supplier_name
FROM products as p
INNER JOIN product_availability as p_a ON p.id = p_a.prod_id
INNER JOIN suppliers as sup ON sup.id = p_a.supp_id
ORDER BY p.product_name, p_a.unit_price DESC;

7. Retrieve all the products sold by suppliers based in the United Kingdom. The result should only contain the columns `product_name` and `supplier_name`.

SELECT p.product_name, sup.supplier_name
FROM products as p
INNER JOIN product_availability as p_a ON p.id = p_a.prod_id
INNER JOIN suppliers as sup ON sup.id = p_a.supp_id
WHERE sup.country = 'United Kingdom';

8. Retrieve all orders, including order items, from customer ID `1`. Include order id, reference, date and total cost (calculated as quantity \* unit price).

SELECT o.id, o.order_reference, o.order_date, SUM(o_i.quantity * p_a.unit_price)as total_cost
FROM orders as o
INNER JOIN order_items as o_i ON o.id = o_i.order_id
INNER JOIN product_availability as p_a ON o_i.product_id = p_a.prod_id
WHERE o.customer_id = 1
GROUP BY o.id
ORDER BY o.id;

9. Retrieve all orders, including order items, from customer named `Hope Crosby`

SELECT * FROM orders as o
INNER JOIN order_items as o_i ON o.id = o_i.order_id
INNER JOIN customers as c ON c.id = o.customer_id
WHERE c.name = 'Hope Crosby';

10. Retrieve all the products in the order `ORD006`. The result should only contain the columns `product_name`, `unit_price` and `quantity`.

SELECT p.product_name, p.id, p_a.unit_price, o_i.quantity
FROM order_items as o_i
INNER JOIN products as p ON o_i.product_id = p.id
INNER JOIN orders as o ON o.id = o_i.order_id
INNER JOIN product_availability as p_a ON o_i.product_id = p_a.prod_id AND o_i.supplier_id = p_a.supp_id
WHERE o.order_reference = 'ORD006';

11. Retrieve all the products with their supplier for all orders of all customers. The result should only contain the columns `name` (from customer), `order_reference`, `order_date`, `product_name`, `supplier_name` and `quantity`.

SELECT c.name, o.order_reference, o.order_date, p.product_name, sup.supplier_name, o_i.quantity
FROM customers as c
INNER JOIN orders as o ON c.id = o.customer_id
INNER JOIN order_items as o_i ON o.id = o_i.order_id
INNER JOIN products as p ON p.id = o_i.product_id
INNER JOIN suppliers as sup ON sup.id = o_i.supplier_id;

12. Retrieve the names of all customers who bought a product from a supplier based in China.

SELECT DISTINCT c.name
FROM customers as c
INNER JOIN orders as o ON c.id = o.customer_id
INNER JOIN order_items as o_i ON o.id = o_i.order_id
INNER JOIN suppliers as sup ON sup.id = o_i.supplier_id
WHERE sup.country = 'China';

13. List all orders giving customer name, order reference, order date and order total amount (quantity \* unit price) in descending order of total.

SELECT c.name, o.order_reference, o.order_date, SUM(o_i.quantity * p_a.unit_price) as order_total_amount
FROM customers as c
INNER JOIN orders as o ON c.id = o.customer_id
INNER JOIN order_items as o_i ON o.id = o_i.order_id
INNER JOIN product_availability as p_a ON p_a.prod_id = o_i.product_id AND p_a.supp_id = o_i.supplier_id
GROUP BY c.name, o.order_reference, o.order_date
ORDER BY order_total_amount DESC;

```
