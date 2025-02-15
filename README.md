# Examples-on-Triggers-used-in-MYSQL
![image](https://github.com/user-attachments/assets/d8f2f4e3-f032-454c-9d63-e33629ab474d)


##  The goal is to create a trigger that logs an entry whenever a new book is added to the books table.
```DIFF
CREATE DATABASE librarydb;
USE librarydb;
```
## Step 2: Create the books Table
- The books table will have the following columns:

- book_id: A unique identifier for each book.
- book_title: The title of the book.
- author: The author of the book.
```diff
CREATE TABLE books (
    book_id INT AUTO_INCREMENT PRIMARY KEY,
    book_title VARCHAR(255) NOT NULL,
    author VARCHAR(255) NOT NULL
);
```
## Create the book_logs Table
- The book_logs table will store logs of when a new book is added. It will have:

- log_id: A unique identifier for each log.
- message: The log message containing information about the added book.
- created_at: The timestamp of when the log entry was created.
```diff
CREATE TABLE book_logs (
    log_id INT AUTO_INCREMENT PRIMARY KEY,
    message TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
![image](https://github.com/user-attachments/assets/16310369-666d-4160-b7b1-f44261ca1283)

## Step 4: Create the Trigger for books Table
Now, we will create a trigger that logs an entry in the book_logs table whenever a new book is inserted into the books table.
```diff
DELIMITER $$

CREATE TRIGGER after_book_insert
AFTER INSERT ON books
FOR EACH ROW
BEGIN
    INSERT INTO book_logs (message, created_at)
    VALUES (CONCAT('New book added: ', NEW.book_title, ' by ', NEW.author, ' (ID: ', NEW.book_id, ')'), NOW());
END$$

DELIMITER ;
```
![image](https://github.com/user-attachments/assets/9f2981ea-9a83-4811-a757-eff4ec37eb23)

## Step 5: Test the Trigger
To test the trigger, insert a new book into the books table:
```diff
INSERT INTO books (book_title, author)
VALUES ('The Great Gatsby', 'F. Scott Fitzgerald');
```
![image](https://github.com/user-attachments/assets/4e3d9c03-f2f8-4234-90ce-47e7acac3b45)

```diff
OLD: Used to reference the old values of a record before a DELETE or UPDATE operation.
NEW: Used to reference the new values of a record after an UPDATE operation.
CONCAT(): A function to concatenate multiple strings together.
NOW(): Returns the current timestamp when the operation (insert, update, or delete) occurs.
VALUES: Specifies the data to be inserted into a table.
```
## AFTER UPDATE Trigger:
This trigger logs a message when an existing book's details are updated (e.g., title or author)
```diff
DELIMITER $$

CREATE TRIGGER after_book_update
AFTER UPDATE ON books
FOR EACH ROW
BEGIN
    INSERT INTO book_logs (message, created_at)
    VALUES (CONCAT('Book updated: ', NEW.book_title, ' by ', NEW.author, ' (ID: ', NEW.book_id, ')'), NOW());
END$$

DELIMITER ;
```


- Now I insert a new book and author
- ![image](https://github.com/user-attachments/assets/2c368e73-ec6f-4002-a8e3-86a7f18ef700)

- Now I change dd to dddd
- ![image](https://github.com/user-attachments/assets/8de92a5c-3073-423b-8dcf-0a4f85eddd86)

- In the books_log table now it should show you the information about the new insert and update
  ![image](https://github.com/user-attachments/assets/b1009bad-307d-41db-a821-18db15c357e2)

## Now let`s create a trigger for Delete
Original
![image](https://github.com/user-attachments/assets/a0941bc9-1ec3-4079-a90a-5e1604e5fdc0)
- Delete the last row now let`s write a tigger which will give us a message that something is deleted
```diff
DELIMITER $$

CREATE TRIGGER after_book_delete
AFTER DELETE ON books
FOR EACH ROW
BEGIN
    INSERT INTO book_logs (message, created_at)
    VALUES (CONCAT('Book deleted: ', OLD.book_title, ' by ', OLD.author, ' (ID: ', OLD.book_id, ')'), NOW());
END$$

DELIMITER ;
```

![image](https://github.com/user-attachments/assets/2be23b13-48dc-486e-ad8e-780361a31525)


# :zap: :zap: When and How to write BEFORE TRIGGER
- In MySQL, BEFORE triggers are used when you want to execute some logic before an operation (like INSERT, UPDATE, or DELETE) occurs on a table. This is helpful for scenarios like:

- Validating data before it is inserted/updated.
- Automatically modifying data before it is written to the table.
- Enforcing business rules before changes are applied.
```sql
CREATE DATABASE CompanyDB;
USE CompanyDB;
CREATE TABLE Employees (
    EmployeeID INT AUTO_INCREMENT PRIMARY KEY,
    Name VARCHAR(100) NOT NULL,
    Salary DECIMAL(10, 2) NOT NULL,
    CreatedAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
:white_check_mark: Create a BEFORE INSERT Trigger
- This trigger ensures that any new employee's salary is at least $3000. If not, it automatically adjusts the salary to $3000.
```sql
DELIMITER //

CREATE TRIGGER BeforeInsertEmployee
BEFORE INSERT ON Employees
FOR EACH ROW
BEGIN
    IF NEW.Salary < 3000 THEN
        SET NEW.Salary = 3000;
    END IF;
END;

//

DELIMITER ;
```
:rotating_light: Test the Trigger
```sql
INSERT INTO Employees (Name, Salary) VALUES ('Alice', 4000);
SELECT * FROM Employees;
```
:rotating_light: Now enter a value less than 3000 and see it change to 3000
```sql
INSERT INTO Employees (Name, Salary) VALUES ('Bob', 2500);
SELECT * FROM Employees;
```
![image](https://github.com/user-attachments/assets/a671ea11-c7e1-4554-9e38-f17049e5127b)




## Example 2:
```sql
CREATE TABLE customers (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50)
);

CREATE TABLE orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT,
    drink VARCHAR(50),
    order_time DATETIME
);

CREATE TABLE order_log (
    log_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT,
    drink VARCHAR(50),
    action_time DATETIME,
    action VARCHAR(50)
);
```
## Trigger
```sql

3. Create the Log Trigger 
Add the trigger to log all orders:  
```sql
DELIMITER //

CREATE TRIGGER log_order
AFTER INSERT
ON orders
FOR EACH ROW
BEGIN
    INSERT INTO order_log (customer_id, drink, action_time, action)
    VALUES (NEW.customer_id, NEW.drink, NOW(), 'Ordered');
END;
//

DELIMITER ;
```
## To check
```diff
INSERT INTO orders (customer_id, drink, order_time)
VALUES (1, 'Mystic Mocha', NOW());
```
Now see the log_order table
```sql
SELECT * FROM order_log;
```
## Add Multiple Orders
```sql
INSERT INTO orders (customer_id, drink, order_time)
VALUES (2, 'Dragon Latte', NOW()), (3, 'Phoenix Frappuccino', NOW());

SELECT * FROM order_log;
```

## Update trigger
```sql
DELIMITER //

CREATE TRIGGER log_update_order
AFTER UPDATE
ON orders
FOR EACH ROW
BEGIN
    INSERT INTO order_log (customer_id, drink, action_time, action)
    VALUES (NEW.customer_id, NEW.drink, NOW(), 'Updated');
END;
//

DELIMITER ;
```

To test it
```sql
-- Update an order
UPDATE orders
SET drink = 'Celestial Cappuccino'
WHERE order_id = 1;

-- Verify the log
SELECT * FROM order_log;
```

## To test the Delete Trigger

```sql
DELIMITER //

CREATE TRIGGER log_delete_order
AFTER DELETE
ON orders
FOR EACH ROW
BEGIN
    INSERT INTO order_log (customer_id, drink, action_time, action)
    VALUES (OLD.customer_id, OLD.drink, NOW(), 'Deleted');
END;
//

DELIMITER ;
```
To test
```sql
-- Delete an order
DELETE FROM orders
WHERE order_id = 1;

-- Verify the log
SELECT * FROM order_log;
```
 ## :white_check_mark: Example 3 E-commerce Order Tracking
![image](https://github.com/user-attachments/assets/b21f3965-c267-4762-959f-e9e43fb8ccaa)
- Let`s create 3 tables any E-commerce might need
- :alien: Users (name and email)
- :money_with_wings: products ( product_name, price)
- :package: orders (order_id ,user_id ,product_id ,order_time)
- :heavy_minus_sign: A table which will capture order placement or order cancellelation 

```sql
-- Create Users Table
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL
);

-- Create Products Table
CREATE TABLE products (
    product_id INT AUTO_INCREMENT PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    price DECIMAL(10, 2) NOT NULL
);

-- Create Orders Table
CREATE TABLE orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    product_id INT NOT NULL,
    order_time DATETIME NOT NULL DEFAULT NOW(),
    FOREIGN KEY (user_id) REFERENCES users(user_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

-- Create Order History Table
CREATE TABLE order_history (
    history_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    product_id INT NOT NULL,
    action_time DATETIME NOT NULL,
    action VARCHAR(50) NOT NULL,
    FOREIGN KEY (user_id) REFERENCES users(user_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

```
:heavy_minus_sign: Trigger for Order Placement
```sql
DELIMITER //

CREATE TRIGGER log_order_placement
AFTER INSERT
ON orders
FOR EACH ROW
BEGIN
    INSERT INTO order_history (user_id, product_id, action_time, action)
    VALUES (NEW.user_id, NEW.product_id, NOW(), 'Order Placed');
END;
//
```
:pushpin: Testing insert
```sql
-- Add users
INSERT INTO users (username, email)
VALUES ('Alice', 'alice@example.com'),
       ('Bob', 'bob@example.com');

-- Add products
INSERT INTO products (product_name, price)
VALUES ('Laptop', 999.99),
       ('Smartphone', 699.99);
SELECT * FROM order_history;

```


:heavy_minus_sign:  Trigger for Order Updates
```sql
DELIMITER //

CREATE TRIGGER log_order_update
AFTER UPDATE
ON orders
FOR EACH ROW
BEGIN
    INSERT INTO order_history (user_id, product_id, action_time, action)
    VALUES (NEW.user_id, NEW.product_id, NOW(), 'Order Updated');
END;
//

DELIMITER ;
```
:pushpin: Testing update
```sql
-- Update an order
UPDATE orders
SET product_id = 2
WHERE order_id = 1;

-- Check order history
SELECT * FROM order_history;
```

:heavy_minus_sign: (Log Order Cancellations)
```sql
DELIMITER //

CREATE TRIGGER log_order_delete
AFTER DELETE
ON orders
FOR EACH ROW
BEGIN
    INSERT INTO order_history (user_id, product_id, action_time, action)
    VALUES (OLD.user_id, OLD.product_id, NOW(), 'Order Cancelled');
END;
//

DELIMITER ;
```
:pushpin: Testing order deletions
```sql
-- Delete an order
DELETE FROM orders
WHERE order_id = 2;

-- Check order history
SELECT * FROM order_history;
```


