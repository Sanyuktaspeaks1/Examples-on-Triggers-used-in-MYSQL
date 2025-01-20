# Examples-on-Triggers-used-in-MYSQL
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

## Step 5: Test the Trigger
To test the trigger, insert a new book into the books table:
```diff
INSERT INTO books (book_title, author)
VALUES ('The Great Gatsby', 'F. Scott Fitzgerald');
```
