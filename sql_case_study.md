> # SQL CASE STUDY 
                   BY DATA IN MOTION
                   
*Below is the SCHEMA FOR TABLES FROM TINY SHOP SALE CASE STUDY*
**There are total 4 tables in the schema which are customers, products, orders & order_items.**

**Schema (PostgreSQL v15)**

    CREATE TABLE customers (
        customer_id integer PRIMARY KEY,
        first_name varchar(100),
        last_name varchar(100),
        email varchar(100)
    );
    
    CREATE TABLE products (
        product_id integer PRIMARY KEY,
        product_name varchar(100),
        price decimal
    );
    
    CREATE TABLE orders (
        order_id integer PRIMARY KEY,
        customer_id integer,
        order_date date
    );
    
    CREATE TABLE order_items (
        order_id integer,
        product_id integer,
        quantity integer
    );
    
    INSERT INTO customers (customer_id, first_name, last_name, email) VALUES
    (1, 'John', 'Doe', 'johndoe@email.com'),
    (2, 'Jane', 'Smith', 'janesmith@email.com'),
    (3, 'Bob', 'Johnson', 'bobjohnson@email.com'),
    (4, 'Alice', 'Brown', 'alicebrown@email.com'),
    (5, 'Charlie', 'Davis', 'charliedavis@email.com'),
    (6, 'Eva', 'Fisher', 'evafisher@email.com'),
    (7, 'George', 'Harris', 'georgeharris@email.com'),
    (8, 'Ivy', 'Jones', 'ivyjones@email.com'),
    (9, 'Kevin', 'Miller', 'kevinmiller@email.com'),
    (10, 'Lily', 'Nelson', 'lilynelson@email.com'),
    (11, 'Oliver', 'Patterson', 'oliverpatterson@email.com'),
    (12, 'Quinn', 'Roberts', 'quinnroberts@email.com'),
    (13, 'Sophia', 'Thomas', 'sophiathomas@email.com');
    
    INSERT INTO products (product_id, product_name, price) VALUES
    (1, 'Product A', 10.00),
    (2, 'Product B', 15.00),
    (3, 'Product C', 20.00),
    (4, 'Product D', 25.00),
    (5, 'Product E', 30.00),
    (6, 'Product F', 35.00),
    (7, 'Product G', 40.00),
    (8, 'Product H', 45.00),
    (9, 'Product I', 50.00),
    (10, 'Product J', 55.00),
    (11, 'Product K', 60.00),
    (12, 'Product L', 65.00),
    (13, 'Product M', 70.00);
    
    INSERT INTO orders (order_id, customer_id, order_date) VALUES
    (1, 1, '2023-05-01'),
    (2, 2, '2023-05-02'),
    (3, 3, '2023-05-03'),
    (4, 1, '2023-05-04'),
    (5, 2, '2023-05-05'),
    (6, 3, '2023-05-06'),
    (7, 4, '2023-05-07'),
    (8, 5, '2023-05-08'),
    (9, 6, '2023-05-09'),
    (10, 7, '2023-05-10'),
    (11, 8, '2023-05-11'),
    (12, 9, '2023-05-12'),
    (13, 10, '2023-05-13'),
    (14, 11, '2023-05-14'),
    (15, 12, '2023-05-15'),
    (16, 13, '2023-05-16');
    
    INSERT INTO order_items (order_id, product_id, quantity) VALUES
    (1, 1, 2),
    (1, 2, 1),
    (2, 2, 1),
    (2, 3, 3),
    (3, 1, 1),
    (3, 3, 2),
    (4, 2, 4),
    (4, 3, 1),
    (5, 1, 1),
    (5, 3, 2),
    (6, 2, 3),
    (6, 1, 1),
    (7, 4, 1),
    (7, 5, 2),
    (8, 6, 3),
    (8, 7, 1),
    (9, 8, 2),
    (9, 9, 1),
    (10, 10, 3),
    (10, 11, 2),
    (11, 12, 1),
    (11, 13, 3),
    (12, 4, 2),
    (12, 5, 1),
    (13, 6, 3),
    (13, 7, 2),
    (14, 8, 1),
    (14, 9, 2),
    (15, 10, 3),
    (15, 11, 1),
    (16, 12, 2),
    (16, 13, 3);
    

---
### QUESTIONS:



**Query #1**

--1) Which product has the highest price? Only return a single row.

    SELECT product_name, price FROM products
    WHERE price IN (SELECT MAX(price) FROM products);

| product_name | price |
| ------------ | ----- |
| Product M    | 70.00 |

---
**Query #2**
--2) Which customer has made the most orders?

    with cte as 
    (SELECT a.customer_id , concat(first_name, ' ' , last_name) name, 
    count(order_id) as max_order , dense_rank() over(order by count(order_id) DESC) as rank_ 
    FROM customers as a
    INNER JOIN orders as b
    on a.customer_id = b.customer_id
    GROUP BY 1)
    
    SELECT customer_id , name , max_order from cte
    WHERE rank_ = 1;

| customer_id | name        | max_order |
| ----------- | ----------- | --------- |
| 2           | Jane Smith  | 2         |
| 3           | Bob Johnson | 2         |
| 1           | John Doe    | 2         |

---
**Query #3**

--3) What’s the total revenue per product?

    SELECT a.product_name , sum(a.price * b.quantity) as Total_revenue
    FROM products as a
    inner join order_items as b
    on a.product_id = b.product_id
    GROUP BY a.product_name 
    ORDER BY Total_revenue DESC;

| product_name | total_revenue |
| ------------ | ------------- |
| Product M    | 420.00        |
| Product J    | 330.00        |
| Product F    | 210.00        |
| Product L    | 195.00        |
| Product K    | 180.00        |
| Product C    | 160.00        |
| Product I    | 150.00        |
| Product B    | 135.00        |
| Product H    | 135.00        |
| Product G    | 120.00        |
| Product E    | 90.00         |
| Product D    | 75.00         |
| Product A    | 50.00         |

---
**Query #4**

--4) Find the day with the highest revenue.

    SELECT a.order_date , sum(price * quantity) as Total_revenue
    FROM orders as a
    join order_items as b on a.order_id = b.order_id
    join products as c on b.product_id = c.product_id
    GROUP BY 1
    ORDER BY Total_Revenue DESC;

| order_date               | total_revenue |
| ------------------------ | ------------- |
| 2023-05-16T00:00:00.000Z | 340.00        |
| 2023-05-10T00:00:00.000Z | 285.00        |
| 2023-05-11T00:00:00.000Z | 275.00        |
| 2023-05-15T00:00:00.000Z | 225.00        |
| 2023-05-13T00:00:00.000Z | 185.00        |
| 2023-05-08T00:00:00.000Z | 145.00        |
| 2023-05-14T00:00:00.000Z | 145.00        |
| 2023-05-09T00:00:00.000Z | 140.00        |
| 2023-05-07T00:00:00.000Z | 85.00         |
| 2023-05-04T00:00:00.000Z | 80.00         |
| 2023-05-12T00:00:00.000Z | 80.00         |
| 2023-05-02T00:00:00.000Z | 75.00         |
| 2023-05-06T00:00:00.000Z | 55.00         |
| 2023-05-03T00:00:00.000Z | 50.00         |
| 2023-05-05T00:00:00.000Z | 50.00         |
| 2023-05-01T00:00:00.000Z | 35.00         |

---
**Query #5**

--5) Find the first order (by date) for each customer.

    SELECT concat(first_name , ' ' , last_name), min(order_date) as first_order
    FROM customers as a join orders as b on a.customer_id = b.customer_id 
    GROUP BY 1;

| concat           | first_order              |
| ---------------- | ------------------------ |
| Kevin Miller     | 2023-05-12T00:00:00.000Z |
| Charlie Davis    | 2023-05-08T00:00:00.000Z |
| Bob Johnson      | 2023-05-03T00:00:00.000Z |
| Quinn Roberts    | 2023-05-15T00:00:00.000Z |
| Oliver Patterson | 2023-05-14T00:00:00.000Z |
| Sophia Thomas    | 2023-05-16T00:00:00.000Z |
| George Harris    | 2023-05-10T00:00:00.000Z |
| Lily Nelson      | 2023-05-13T00:00:00.000Z |
| John Doe         | 2023-05-01T00:00:00.000Z |
| Eva Fisher       | 2023-05-09T00:00:00.000Z |
| Ivy Jones        | 2023-05-11T00:00:00.000Z |
| Jane Smith       | 2023-05-02T00:00:00.000Z |
| Alice Brown      | 2023-05-07T00:00:00.000Z |

---
**Query #6**

--6) Find the top 3 customers who have ordered the most distinct products

    SELECT a.customer_id , concat(first_name, ' ', last_name) ,
    COUNT(DISTINCT product_id) as dist_product_count
    FROM customers as a join orders as b 
    on a.customer_id = b.customer_id
    join order_items as c
    on b.order_id = c.order_id
    GROUP BY 1,2
    ORDER BY dist_product_count DESC
    Limit 3;

| customer_id | concat      | dist_product_count |
| ----------- | ----------- | ------------------ |
| 2           | Jane Smith  | 3                  |
| 3           | Bob Johnson | 3                  |
| 1           | John Doe    | 3                  |

---
**Query #7**

--7) Which product has been bought the least in terms of quantity?

    SELECT product_name , Sum(quantity) as Quantity
    FROM products as p join order_items as o
    on p.product_id = o.product_id
    GROUP BY 1
    ORDER BY 2
    LIMIT 3;

| product_name | quantity |
| ------------ | -------- |
| Product E    | 3        |
| Product H    | 3        |
| Product G    | 3        |

---
**Query #8**

--8) What is the median order total?

    WITH CTE AS (
    SELECT a.order_id , sum(price * quantity) AS Revenue
    FROM orders as a join order_items as b on a.order_id = b.order_id
    join products as c on b.product_id = c.product_id
    GROUP BY 1)
    
    SELECT PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY Revenue) as MEDIAN FROM CTE;

| median |
| ------ |
| 112.5  |

---
**Query #9**
--9) For each order, determine if it was ‘Expensive’ (total over 300), ‘Affordable’ (total over 100), or ‘Cheap’.

    WITH CTE AS (
    SELECT a.order_id , sum(price * quantity) AS Revenue
    FROM orders as a join order_items as b on a.order_id = b.order_id
    join products as c on b.product_id = c.product_id
    GROUP BY 1)
    
    SELECT order_id,
    CASE 
    WHEN Revenue > 300 then 'Expensive' 
    WHEN Revenue > 100 then 'Affordable'
    Else 'Cheap' END AS ORDER_Bracket
    FROM CTE
    ORDER BY 1;

| order_id | order_bracket |
| -------- | ------------- |
| 1        | Cheap         |
| 2        | Cheap         |
| 3        | Cheap         |
| 4        | Cheap         |
| 5        | Cheap         |
| 6        | Cheap         |
| 7        | Cheap         |
| 8        | Affordable    |
| 9        | Affordable    |
| 10       | Affordable    |
| 11       | Affordable    |
| 12       | Cheap         |
| 13       | Affordable    |
| 14       | Affordable    |
| 15       | Affordable    |
| 16       | Expensive     |

---
**Query #10**
--10) Find customers who have ordered the product with the highest price.

    SELECT a.customer_id, concat(first_name, ' ',last_name)
    FROM customers as a left join orders as b on a.customer_id = b.customer_id 
    left join order_items as c on b.order_id = c.order_id
    left join products as d on c.product_id = d.product_id
    WHERE price = (select max(price) from products);

| customer_id | concat        |
| ----------- | ------------- |
| 8           | Ivy Jones     |
| 13          | Sophia Thomas |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/5NT4w4rBa1cvFayg2CxUjr/4)


