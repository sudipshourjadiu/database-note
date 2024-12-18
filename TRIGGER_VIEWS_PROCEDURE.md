# **Triggers, Views, and Stored Procedures in SQL**

## **Triggers**
A **trigger** is a stored program that is executed automatically in response to specific events, such as `INSERT`, `UPDATE`, or `DELETE`, occurring in a table. Triggers can be used to enforce business rules, validate data, or log changes.

### **Types of Triggers**

1. **Before Update Trigger**
   - Executes before an `UPDATE` operation.
   - Example: Log the current values of a record before updating it.

   ```sql
   -- Table Definitions
   CREATE TABLE customer (
       acc_no INT PRIMARY KEY,
       cust_name VARCHAR(20),
       avail_balance DECIMAL
   );

   CREATE TABLE mini_statement (
       acc_no INT,
       avail_balance DECIMAL,
       FOREIGN KEY (acc_no) REFERENCES customer(acc_no) ON DELETE CASCADE
   );

   -- Trigger
   DELIMITER //
   CREATE TRIGGER before_update_customer
   BEFORE UPDATE ON customer
   FOR EACH ROW
   BEGIN
       INSERT INTO mini_statement (acc_no, avail_balance)
       VALUES (OLD.acc_no, OLD.avail_balance);
   END //
   DELIMITER ;

   -- Test Case
   INSERT INTO customer VALUES (1000, 'Fanny', 7000);
   UPDATE customer SET avail_balance = avail_balance + 3000 WHERE acc_no = 1000;
   SELECT * FROM mini_statement;
   ```

   **Output:**
   ```
   +--------+---------------+
   | acc_no | avail_balance |
   +--------+---------------+
   | 1000   |         7000  |
   +--------+---------------+
   ```

2. **After Update Trigger**
   - Executes after an `UPDATE` operation.
   - Example: Log new values into another table after an update.

   ```sql
   -- Table Definition
   CREATE TABLE micro_statement (
       acc_no INT,
       avail_balance DECIMAL,
       FOREIGN KEY (acc_no) REFERENCES customer(acc_no) ON DELETE CASCADE
   );

   -- Trigger
   DELIMITER //
   CREATE TRIGGER after_update_customer
   AFTER UPDATE ON customer
   FOR EACH ROW
   BEGIN
       INSERT INTO micro_statement (acc_no, avail_balance)
       VALUES (NEW.acc_no, NEW.avail_balance);
   END //
   DELIMITER ;

   -- Test Case
   INSERT INTO customer VALUES (1002, 'Janitor', 4500);
   UPDATE customer SET avail_balance = avail_balance + 1500 WHERE acc_no = 1002;
   SELECT * FROM micro_statement;
   ```

   **Output:**
   ```
   +--------+---------------+
   | acc_no | avail_balance |
   +--------+---------------+
   | 1002   |         6000  |
   +--------+---------------+
   ```

3. **Before Insert Trigger**
   - Executes before an `INSERT` operation.
   - Example: Automatically set default values before inserting a record.

   ```sql
   -- Table Definition
   CREATE TABLE contacts (
       contact_id INT AUTO_INCREMENT PRIMARY KEY,
       last_name VARCHAR(30),
       first_name VARCHAR(25),
       birthday DATE,
       created_date DATE,
       created_by VARCHAR(30)
   );

   -- Trigger
   DELIMITER //
   CREATE TRIGGER before_insert_contact
   BEFORE INSERT ON contacts
   FOR EACH ROW
   BEGIN
       SET NEW.created_date = SYSDATE();
       SET NEW.created_by = USER();
   END //
   DELIMITER ;

   -- Test Case
   INSERT INTO contacts (last_name, first_name, birthday) 
   VALUES ('Newton', 'Enigma', '1999-08-19');
   SELECT * FROM contacts;
   ```

   **Output:**
   ```
   +------------+-----------+------------+------------+--------------+----------------+
   | contact_id | last_name | first_name | birthday   | created_date | created_by     |
   +------------+-----------+------------+------------+--------------+----------------+
   |          1 | Newton    | Enigma     | 1999-08-19 | 2024-11-27   | root@localhost |
   +------------+-----------+------------+------------+--------------+----------------+
   ```

   
 4. **DELETE Trigger**
   - A **DELETE trigger** is executed automatically when a row is deleted from a table. It can be used to log deleted records or maintain referential integrity.
   - Log the deleted record into an audit table before the deletion.

```sql
-- Table Definitions
CREATE TABLE contacts (
    contact_id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(25),
    last_name VARCHAR(30)
);

CREATE TABLE contacts_audit (
    contact_id INT,
    deleted_date DATE,
    deleted_by VARCHAR(30)
);

-- Trigger Definition
DELIMITER //
CREATE TRIGGER before_delete_contact
BEFORE DELETE ON contacts
FOR EACH ROW
BEGIN
    INSERT INTO contacts_audit (contact_id, deleted_date, deleted_by)
    VALUES (OLD.contact_id, SYSDATE(), USER());
END //
DELIMITER ;

-- Test Case
INSERT INTO contacts (first_name, last_name) VALUES ('John', 'Doe');
DELETE FROM contacts WHERE last_name = 'Doe';
SELECT * FROM contacts_audit;
```

---

## **Views**

A **view** is a virtual table created based on the result of an SQL query. Views simplify data retrieval and enhance security by restricting access to specific data.

### **Creating a View**
```sql
CREATE VIEW Bangladeshi_Customers AS
SELECT CustomerName, ContactName
FROM CustomerInfo
WHERE Country = 'Bangladesh';

SELECT * FROM Bangladeshi_Customers;
```

### **Updating a View**
Views can be modified using the `CREATE OR REPLACE VIEW` statement:
```sql
CREATE OR REPLACE VIEW Bangladeshi_Customers AS
SELECT CustomerName, ContactName, City
FROM CustomerInfo
WHERE Country = 'Bangladesh';
```

### **Dropping a View**
```sql
DROP VIEW Bangladeshi_Customers;
```

---

## **Stored Procedures**

A **stored procedure** is a set of SQL statements that can be stored and executed as a single command.

### **Types of Procedures**

1. **No Parameters**
   - Example: Display all books.
   ```sql
   DELIMITER //
   CREATE PROCEDURE display_books()
   BEGIN
       SELECT * FROM book;
   END //
   DELIMITER ;

   CALL display_books();
   ```

2. **With IN Parameter**
   - Example: Update the price of a book based on ISBN.
   ```sql
   DELIMITER //
   CREATE PROCEDURE update_price(IN book_isbn INT, IN new_price DECIMAL)
   BEGIN
       UPDATE book SET price = new_price WHERE ISBN = book_isbn;
   END //
   DELIMITER ;

   CALL update_price(001, 600);
   ```

3. **With OUT Parameter**
   - Example: Retrieve the highest book price.
   ```sql
   DELIMITER //
   CREATE PROCEDURE get_max_price(OUT highest_price DECIMAL)
   BEGIN
       SELECT MAX(price) INTO highest_price FROM book;
   END //
   DELIMITER ;

   CALL get_max_price(@max_price);
   SELECT @max_price;
   ```

4. **With INOUT Parameter**
   - Example: Count authors by gender.
   ```sql
   DELIMITER //
   CREATE PROCEDURE count_authors_by_gender(INOUT count INT, IN gender_type VARCHAR(10))
   BEGIN
       SELECT COUNT(*) INTO count FROM author WHERE gender = gender_type;
   END //
   DELIMITER ;

   CALL count_authors_by_gender(@male_count, 'Male');
   SELECT @male_count;
   ```

### **Dropping a Procedure**
```sql
DROP PROCEDURE IF EXISTS display_books;
```
