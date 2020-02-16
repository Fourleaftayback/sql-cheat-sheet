# SQL-cheat-sheet

####Basics of SQL 

* A cheat sheet created from this video Taken from youTube: [https://www.youtube.com/watch?v=7S_tz1z_5bA&t=55s](https://www.youtube.com/watch?v=7S_tz1z_5bA&t=55s) 

### SELECT
* To iniciate a statement you need to choose the Table you are wirking with. 
* The USE will do this.
* The SELECT astrek will choose all in the table. 
* The others are helpers and the -- is comments 

```sql
USE sql_store;

SELECT * 
FROM customers
-- WHERE customer_id = 1
ORDER BY first_name
```

 * You can SELECT certain columns from the Table
 * You can assign mathmatical operator 
 * The AS clause you to assign the column header name when you output.
 
```sql
SELECT 
 last_name, 
 first_name, 
 points, 
 points * 10 + 100 AS 'discount factor'
FROM customers
```

* The DISTINCT keys word will only grab one of the value from the table 

```sql
SELECT DISTINCT state
FROM customers
```

### WHERE

* allows you to filter the data based on the conditions
* They are > >= < <= = != <> The last two are not equals

```sql
SELECT * 
FROM customers
WHERE points > 3000
```
* Date 

```sql
SELECT * 
FROM customers
WHERE birth_date > '1990-01-01'
```

* You can use ranges
```sql
SELECT * 
FROM orders
WHERE order_date >= '2018-01-01' && order_date <= '2018-12-31'
```

### **AND** operator 
```sql
SELECT * 
FROM customers
WHERE birth_date > '1990-01-01' AND points > 1000
```

The options are **AND** , **OR** , **NOT**

* You can combine multiple operators 

```sql
SELECT * 
FROM customers
WHERE NOT birth_date > '1990-01-01' AND points > 1000
```

```sql
SELECT * FROM order_items
WHERE order_id = 6 AND (unit_price * quantity) > 30
```

### **IN** operator or **NOT IN**

```sql
SELECT * 
FROM customers
WHERE state NOT IN ('VA', 'FL', 'GA')
```

### **BETWEEN**

```sql
SELECT * 
FROM customers
WHERE points BETWEEN 1000 AND 3000
```

### **LIKE** to match a certain pattern

```sql
SELECT * 
FROM customers
WHERE last_name LIKE 'b%'
```
* This will match all last names that start with a 'b' and this is case insensitive 
* The percent sign can be placed any where in the string
* This will get any string that has a b

```sql
SELECT * 
FROM customers
WHERE last_name LIKE '%b%'
```

* The undescores will represent any 

```sql
SELECT * 
FROM customers
WHERE last_name LIKE 'b____y'
```

* This will search for any addresses that has trail or avenue

```sql
SELECT * 
FROM customers
WHERE address LIKE '%trail%' OR address LIKE '%avenue%'
```

```sql
SELECT * 
FROM customers
WHERE phone NOT LIKE '%9'
```

### **REGEXP**

* search for field at the end of a string
```sql
SELECT * 
FROM customers
WHERE last_name REGEXP 'field$'
```

* using the pipe

```sql
SELECT * 
FROM customers
WHERE last_name REGEXP 'field$|mac|rose'
```

```sql
SELECT * 
FROM customers
WHERE last_name REGEXP '[a-h]e'
```

* **IS NULL** and **IS NOT NULL**
* This will select a specific customer that has a null the phone

```sql
SELECT * 
FROM customers
WHERE phone IS NULL
```

### **ORDER BY** clause

```sql
SELECT * 
FROM customers
ORDER BY first_name DESC
```

* This will first sort the states descending then with the state the names 

```sql
SELECT * 
FROM customers
ORDER BY state DESC, first_name ASC
```

* You can sort by the alias names as well

```sql
SELECT first_name, last_name, 10 AS points
FROM customers
ORDER BY points, first_name
```

* In this example we get declare the totol price at the top, then you can use it to sort

```sql
SELECT * , quantity * unit_price AS total_Price
FROM order_items
WHERE order_id = 2
ORDER BY total_price DESC
```

### Limit 

* This will limit the amount of data that is being returned
* The limit clause should always be at the end.

```sql
SELECT * 
FROM customers
LIMIT 3
```

* You can select the amount not only at the beggining but in the middle. This can be useful for pagination.
* This will skip first 6 recodrs and ge the next 3

```sql
SELECT * 
FROM customers
LIMIT 6, 3
```

### INNER JOIN

* This allow you to combine columns from different tables 
* You can use the JOIN short cut instead for most 

```sql
SELECT order_id, sql_store.orders.customer_id, first_name, last_name
-- here since you are asking for a different id from another table you have to specify it
FROM orders
JOIN customers ON orders.customer_id = customers.customer_id
-- Then you choosing the first table and declaring what two relantions are for the ids
```

* You can use the **Alias** make your query shorter 
* Instead of spelling out the full table in each statement you set the allias

```sql
SELECT order_id, O.customer_id, first_name, last_name
FROM orders O
-- here you set the alias then you can use it in the whole query 
JOIN customers C
ON O.customer_id = C.customer_id
```

```sql
SELECT order_id, oi.product_id, quantity, oi.unit_price
FROM order_items oi
JOIN products p ON oi.product_id = p.product_id
```

**Joining across databases**
* To do this all you need to do is prefix the database that you are not currently on.

```sql
SELECT * 
FROM order_items oi
JOIN sql_inventory.products p 
  ON oi.product_id = p.product_id
```

**Self JOIN**

* In this case you can join a table within the same table 

```sql
SELECT e.employee_id, e.first_name, m.first_name AS manager
FROM employees e 
JOIN employees m
  ON e.reports_to = m.employee_id
-- then were taking the reports to and combining it with the managers info  
```

* **Joining multiple tables** 

* You can chain the joins together and in the last line the status is equal to the status_id

```sql
USE sql_store;

SELECT o.order_id, o.order_date, c.first_name, c.last_name, os.name
FROM orders o 
JOIN customers c
  ON o.customer_id = c.customer_id
JOIN order_statuses os
  ON o.status = os.order_status_id
```

* ** Compound Join Conditions**
* learn this better 

```sql
SELECT * 
FROM order_items oi
JOIN order_item_notes oin
  ON oi.order_id = oin.order_id
  AND oi.product_id = oin.product_id;
```

### Outer JOIN

* With a inner join you only get results that match the condition you are joinin on. There can be instances when you want the record of all other data that does not. This is when the outer join can be handy. 
* There are **LEFT** and **RIGHT** joins. 
* When you use the LEFT join all data from the left table is returned.

```sql
SELECT c.customer_id, c.first_name, o.order_id
FROM customers c
LEFT JOIN orders o --this is the left join
 ON c.customer_id = o.customer_id
 ORDER BY c.customer_id;
```
```sql
SELECT p.product_id, p.name, oi.quantity
FROM products p
LEFT JOIN order_items oi
  ON p.product_id = oi.product_id
```

**Outer JOIN with multiple tables**


```sql
SELECT c.customer_id, c.first_name, o.order_id, sh.name AS shipper
FROM customers c
LEFT JOIN orders o
 ON c.customer_id = o.customer_id
LEFT JOIN shippers sh 
 ON o.shipper_id = sh.shipper_id
 ORDER BY c.customer_id;
```
* Another example
```sql
Select 
  o.order_date, 
  o.order_id, 
  c.first_name AS customer, 
  s.shipper_id, 
  os.name AS status
FROM orders o
  JOIN customers c 
   ON o.customer_id = c.customer_id 
  LEFT JOIN shippers s 
   ON o.shipper_id = s.shipper_id
  JOIN order_statuses os
   ON o.status = os.order_status_id
```

** Self Outer Joins**
* This is useful when you need to combined data within the table and the some of the data does not have a refernece to ON

```sql
SELECT 
  e.employee_id,
  e.first_name,
  m.first_name AS manager
FROM employees e
LEFT JOIN employees m
  ON e.reports_to = m.employee_id  
```  

### USING clause
* You can use this if the prop is the same the as you are joining from 

```sql
Select 
  o.order_date, 
  o.order_id, 
  c.first_name AS customer, 
  s.shipper_id, 
  os.name AS status
FROM orders o
  JOIN customers c 
  -- ON o.customer_id = c.customer_id 
  USING (customer_id)
```  

```sql
SELECT p.date, c.name, p.amount, pm.name
FROM payments p 
  JOIN clients c 
  USING (client_id)
  JOIN payment_methods pm
  ON pm.payment_method_id = p.payment_method;
```

**natural joins**
* This will allow SQL to auto join 
* It is not recommended since you can get unexpected results

```sql
SELECT o.order_id, c.first_name
FROM orders o
NATURAL JOIN customer c;
```
**Cross joins** 
* Allows you to join everything from one table to another.

```sql
SELECT c.first_name AS customer, p.name AS product
FROM customers c, orders o 
-- CROSS JOIN products p 
ORDER BY c.first_name
```

###UNION
* This will allow you to combine rows from different tables
* you can use in one table or combine from other tables as well

```sql
SELECT 
  order_id,
  order_date,
  'Active' AS status
FROM orders
WHERE order_date >= '2019-01-01'
UNION
SELECT 
  order_id,
  order_date,
  'Archived' AS status
FROM orders
WHERE order_date < '2019-01-01'
```
* using muliple tables with UNION 
* make sure the columns are the same lengh or you get an error
```sql
SELECT 
  first_name
  FROM customers
  UNION
  SELECT name
  FROM shippers
```

* In this example you take a few conditional and set the type based on how many points they have 

```sql
SELECT 
  customer_id, first_name, points, 'Bronze' AS type
 FROM customers
 WHERE points < 2000
 UNION
 SELECT 
  customer_id, first_name, points, 'Siler' AS type
 FROM customers
 WHERE points BETWEEN 2000 and 3000
 UNION
 SELECT 
  customer_id, first_name, points, 'Gold' AS type
 FROM customers
 WHERE points > 3000
 ORDER BY first_name
 ```
 ###Column Attributes
* You can see the attributes in the tools areas for the tables, which provides info on schema

![](screen-shot-2020-02-16-at-10-08-43-am.png)

### INSERT
* inserting a single row into a table 
* You should check the table schema to see what type of data and what is required bf doing so 

```sql
INSERT INTO customers (first_name, last_name, birth_date, address, city, state)
VALUES (
 "john", 
 'smith', 
 '1990-01-01',
 '83 pine st',
 'lones',
 'CA'
 )
 ```
 
 * Inserting multiple rows
 
 ```sql
INSERT INTO shippers(name)
VALUES ('shipper1'),
	   ('shipper2'),
       ('shipper3')
```       

```sql
INSERT INTO products(name, quantity_in_stock, unit_price)
VALUES ('item1', 7, 4.78),
	   ('item3', 74, 1.99),
       ('item5', 17, 9.99)
```

### Inserting Hierarchical rows
* In this case we will be inserting but some will be based on other tables. 
* for example if a non valid id is use then the insert is not valid 
* Were inserting the first data then using id from that insert to insert another table based off this. 
```sql
INSERT INTO orders (customer_id, order_date, status)
VALUES(1, '2019-01-02', 1);

-- SELECT LAST_INSERT_ID(); this is the function 

INSERT INTO order_items
VALUES (LAST_INSERT_ID(), 1, 1, 2.95),
       (LAST_INSERT_ID(), 2, 1, 3.95)
```

### creating a copy of a Table 

* Keep in mind that when using this to create a copy, it will not copy the settings from the previous table.
* Like the auto increment...

```sql
CREATE TABLE orders_archived AS
SELECT * FROM orders
```

* if you truncate the table it will clear all the records
* Using the sub-query moethod

```sql
INSERT INTO orders_archived
SELECT * 
FROM orders
WHERE order_date < '2019-01-01'
```
* after clearing the table, You can insert from one table to another. 
* You can create the table directly by getting data from tables and placing it in another

```sql
CREATE TABLE invoices_archived AS
SELECT 
  i.invoice_id,
  i.number,
  c.name AS client,
  i.invoice_total,
  i.payment_total,
  i.invoice_date,
  i.payment_date,
  i.due_date
FROM invoices i 
JOIN clients c
USING (client_id)
WHERE payment_date IS NOT NULl
```

### UPDATE
* **updating a single row**

```sql
UPDATE invoices
SET payment_total = 10, payment_date = '2019-03-01'
WHERE invoice_id = 1;
```

* resetting 

```sql
UPDATE invoices
SET payment_total = DEFAULT, payment_date = null
WHERE invoice_id = 1;
```

* using a expression as the value

```sql
UPDATE invoices
SET payment_total = invoice_total * 0.5 ,
 payment_date = due_date
WHERE invoice_id = 3;
```

### updating multiple rows

* In the mySQL work bench this is not allowed. But this will work in other settings and if you disable it in preferences

```sql
UPDATE invoices
SET payment_total = invoice_total * 0.5 ,
 payment_date = due_date
WHERE client_id = 3
```

```sql
UPDATE customers
SET points = points + 50
WHERE  birth_date < '1990-01-01'
```

* **Using subqueries in updates**
* In this case you are passing in a query as the condition in ()
* Before doign this type of queries make sure to run the select so you do not update the wrong ones


```sql
UPDATE invoices
SET 
  payment_total = invoice_total * 0.5,
  payment_date = due_date
WHERE client_id IN
           (SELECT client_id
            FROM clients
            WHERE state IN ('CA', 'NY')) 
```

```sql
UPDATE invoices
SET 
  payment_total = invoice_total * 0.5,
  payment_date = due_date
WHERE payment_date IS NULL
```

* updating comments where customer has over 3000 points

```sql
UPDATE orders
SET comments = "Gold Customers"
WHERE customer_id IN 
             (SELECT customer_id
              FROM customers 
              WHERE points >= 3000)
```

### DELETE

* make sure you give it a condition or you will delete the whole table 

```sql
DELETE FROM invoices
WHERE invoices_id = 1
```

* You can place sub queries as well

```sql
DELETE FROM invoices
WHERE client_id = (
                 SELECT * 
                 FROM clients 
				WHERE name = 'Myworks')
```




