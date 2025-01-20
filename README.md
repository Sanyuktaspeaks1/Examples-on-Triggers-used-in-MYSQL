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

