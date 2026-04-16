# Library-Management-System
## Project Overview

**Project Title**: Library Management System

**Level**: Intermediate

**Database**: library_db

This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations, and executing advanced SQL queries. The goal is to showcase skills in database design, manipulation, and querying.

![image alt](https://github.com/kanishka-sehrawat/Library-Management-System/blob/9caa837dc50b43e230f2aca94fc9567e50c4734c/library.jpg)

## Objectives

**1.Set up the Library Management System Database:** Create and populate the database with tables for branches, employees, members, books, issued status, and return status.

**2.CRUD Operations:** Perform Create, Read, Update, and Delete operations on the data.

**3.CTAS (Create Table As Select):** Utilize CTAS to create new tables based on query results.

**4.Advanced SQL Queries:** Develop complex queries to analyze and retrieve specific data.

# Project Structure 

### 1. Database Setup:
![image alt](https://github.com/kanishka-sehrawat/Library-Management-System/blob/917317cb93667ae0577830272c7218caf213af9f/library_erd.png)

-**Database Creation**: Created a database named library_db.

-**Table Creation**: Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.
 
 ```sql
CREATE DATABASE library_db;

-- Create table "branch"--
CREATE TABLE branch(branch_id varchar(10) PRIMARY KEY,
				   manager_id varchar(10),
                   branch_address varchar(50),
                   contact_no varchar(10));

-- create table "employees" --
CREATE TABLE employees(emp_id varchar(10 ) PRIMARY KEY,
	                   emp_name varchar(25),
                       position varchar(20),
                       salary int,
                       branch_id varchar(15));
-- create table "books"	--				
CREATE TABLE books(isbn varchar(20) PRIMARY KEY,
	               book_title varchar(70),
                   category	varchar(15),
                   rental_price float,
                   status varchar(10),
                   author varchar(20),
                   publisher varchar(35));

-- create table "members" --
CREATE TABLE members(member_id varchar(20) PRIMARY KEY,
	                 member_name varchar(30),
                     member_address varchar(75),
                     reg_date date);
                     
-- create table "issued_status" --
CREATE TABLE issued_status(issued_id varchar(10) PRIMARY KEY,
						issued_member_id varchar(10),
                        issued_book_name varchar(75),
                        issued_date date,
                        issued_book_isbn varchar(25),
                        issued_emp_id varchar(10));

-- create table "return_status" --                        
CREATE TABLE return_status(return_id varchar(10),
                          issued_id	varchar(10),
                          return_book_name varchar(75),
                          return_date date ,
                          return_book_isbn varchar(20));
```

## CRUD Operations:

- **Create**: Inserted sample records into the books table.
- **Read**: Retrieved and displayed data from various tables.
- **Update**: Updated records in the employees table.
- **Delete**: Removed records from the members table as needed.

  ** TASK 1.Create a new  book records**
  --"978-1-60129-456-2','To Kill a Mockingbird','Classic',6.00,'yes','Harper Lee','J.B.Lippincott & Co.')"
```sql
INSERT INTO books(isbn,book_title,category,rental_price,status,author,publisher)
values('978-1-60129-456-2','To Kill a Mockingbird','Classic',
6.00,'yes','Harper Lee','J.B.Lippincott & Co.');
```

**TASK 2.Update an existing members address**
```sql
UPDATE members
SET member_address='125 Main St'
WHERE member_id='C101';
```

**TASK 3. Delete a record from the issued status table**
-- objective :- Delete the record with issued_id='IS107' from the issued_status table.
```sql
SET SQL_SAFE_UPDATES=0;
DELETE FROM issued_status WHERE issued_id='IS120';
SET SQL_SAFE_UPDATES=1;
```

**TASK 4.Retrieve all books issued by specific employee**
-- OBJECTIVE:- Select  all books issued by the employee with emp_id='E101'
```sql
SELECT issued_book_name as book from issued_status where issued_emp_id='E101';
```

 **TASK 5. List members who have issued more than one book**
 -- OBJECTIVE:-GROUP BY to find members who have issued more than one book
 ```sql
SELECT count(issued_member_id) as number_of_time_issued,issued_member_id
from issued_status status group by issued_member_id 
HAVING count(issued_member_id)>1 order by count(issued_member_id);
```

### 3. CTAS (Create Table As Select)
**TASK 6. Create summary tables: Used CTAS to generate new tables based on query results - each book and total book_issued_count.**
```sql
CREATE TABLE book_counts
AS 
SELECT b.isbn,b.book_title ,count(ist.issued_id) as no_issued
from books as b JOIN issued_status as ist 
ON b.isbn=ist.issued_book_isbn group by b.isbn,b.book_title;
```

### 4.Data Analysis and Findings
**TASK 7. Retrieve all books in a specific category**
```sql
SELECT * FROM books WHERE category='Classic';
```
**TASK 8. Find total rental income by category**
```sql
SELECT category,SUM(b.rental_price)as  rental_income,COUNT(*) AS count
FROM BOOKS AS  b join issued_status as ist
on b.isbn=ist.issued_book_isbn group by category;
```

**TASK 9.List the members who have registered in the last 180 days.**
```sql
SELECT * FROM members WHERE reg_date >= CURRENT_DATE- INTERVAL 180 day ;
```

**TASK 10.List employees with their branch manager's name and their branch details**
```sql
SELECT e1.*,e1.emp_name as manager, b.branch_id from employees as e1 JOIN employees as e2 ON
e1.branch_id=e2.branch_id JOIN branch as b on b.manager_id=e1.emp_id;

**TASK 11.Create a table of books with rental price above a certain threshold 7 usd**
```sql
CREATE TABLE book_price_greater_than_7
AS
SELECT * FROM books WHERE rental_price>7;
```

**TASK 12.Retrieve the list of book not yet returned **
```sql
SELECT ist.*, rs.return_id FROM issued_status as ist LEFT JOIN return_status as rs
ON ist.issued_id=rs.issued_id
WHERE rs.return_id is NULL;
```

## Advance SQL Operations

### TASK 13- Identify the members with overdue books
**Write a query to identify members who have overdue books (assume a 30 day return -period).
Display the member's_id,member's name ,book title,issue date , and days overdue.**
```sql
SELECT ist.issued_member_id,mb.member_name,ist.issued_book_name as book_name,ist.issued_date,
DATEDIFF(CURRENT_DATE,ist.issued_date) as overdue_days
FROM issued_status as ist JOIN members as mb ON ist.issued_member_id=mb.member_id
LEFT JOIN return_status as rts on rts.issued_id=ist.issued_id WHERE rts.return_date IS NULL
AND DATEDIFF(CURRENT_DATE,ist.issued_date)>30 order by ist.issued_member_id;
```

### - TASK 14- Update booook status on return.
**Write a query to update the status of books in the books table to "yes" when they are returned
(based on enteries in the return_status table).**
```sql
-- STORE PROCEDURE 
DELIMITER $$
CREATE PROCEDURE add_return_records
(IN p_return_id VARCHAR(10),
IN p_issued_id VARCHAR(10),
IN p_book_quality VARCHAR(20))
-- IN MEANS values are coming into procedure 
-- P means parameter(input)
BEGIN
DECLARE v_isbn VARCHAR(50);
DECLARE v_book_name VARCHAR(80);
-- v means variable internal usage


-- 1.Insert into return record
INSERT INTO return_status(return_id,issued_id,return_date,book_quality)
VALUES(p_return_id,p_issued_id,curdate(),p_book_quality);

-- 2.Get book details 
SELECT issued_book_isbn,issued_book_name
INTO v_isbn,v_book_name
FROM issued_status
WHERE issued_id=p_issued_id;

-- 3.Update book status
UPDATE books
SET status ='yes'
WHERE isbn=v_isbn;

-- 4.Show message 
SELECT CONCAT('Thank you for returning the book :',v_book_name) as message;

END $$

CALL add_return_records('RS125','IS130','good');
```

### TASK 15. Branch Performance Report 
**Create a query that generates a performance report for each branch , showing the number of books issued, the number of books
returned,and the total revenue generated from book rentals.**
```sql
CREATE TABLE  branch_reports
AS
SELECT branch_id,count(ist.issued_id) as no_of_book_issued,count(rst.return_id) as no_of_books_return,sum(b.rental_price) as revenue
FROM issued_status as ist JOIN employees as emp ON ist.issued_emp_id=emp.emp_id LEFT JOIN return_status as rst
ON rst.issued_id=ist.issued_id LEFT  JOIN books  as b ON ist.issued_book_isbn=b.isbn group by branch_id;
```


### TASK 16. CTAS: Create a table of Active members 
**Use the Create tables as (CTAS) statement as a new table active_members containing members who have issued at least one book in the last 6 months.**
```sql
CREATE TABLE active_members
AS
SELECT * FROM members
WHERE member_id IN(
SELECT DISTINCT(issued_member_id)
FROM issued_status WHERE issued_date>=curdate()-interval 6 MONTH);
```

### TASK 17.Find the employees with the most book issues processed 
**Write a query to find the top 3 employees who have processed the most book issues.Display the employee name,number of books processed,and their branch.**
```sql
SELECT e.emp_name,COUNT(issued_book_name) as no_of_book_issued,e.branch_id FROM employees as e JOIN 
issued_status as ist ON e.emp_id=ist.issued_emp_id GROUP BY e.emp_id ORDER BY COUNT(issued_book_name) desc LIMIT 3;
```

### TASK 18. Identify members issuing High-risk books.
**Write a query to identify members who have issued books more than twice with the status
'damaged' in the books table.Display the member name,book title,and the number of times they've issued damaged books.**
```sql
SELECT m.member_name,ist.issued_book_name ,COUNT(*) as no_of_times_damage_books FROM members as m JOIN issued_status as ist
 ON m.member_id =ist.issued_member_id JOIN return_status as rts ON 
ist.issued_id=rts.issued_id WHERE book_quality = 'Damaged' GROUP BY m.member_id,ist.issued_book_name ;
```

### -- TASK 19.Stored procedure objectives:
**Create a stored procedure to manage the status of the books in the library system.Description :Write a stored 
procedure that updated the status of the library based on its issuance.The procedure should function as follows:The stored procedure should take the book_id as an input parameter.The procedure should first check if the book is available (status='yes').If the book is available it should be issued and the status in the book table should be updated to 'no'.If the book is not available(status='no'),the procedure should return an error message indicating that the book is currently not available.

```sql
-- STORED PROCEDURE
DELIMITER $$ 
CREATE PROCEDURE issue_book(
IN p_issued_id VARCHAR(10), IN p_issued_member_id VARCHAR(30),IN p_issued_book_isbn VARCHAR(20),IN p_issued_emp_id VARCHAR(10))

BEGIN 
DECLARE v_status VARCHAR(10);

-- check if the book is available 
SELECT status into v_status from books WHERE isbn=p_issued_book_isbn;

IF v_status='yes' THEN
-- insert into issued_status table
INSERT INTO issued_status(issued_id,issued_member_id,issued_date,issued_book_isbn,issued_emp_id) VALUES 
(p_issued_id,p_issued_member_id,curdate(),p_issued_book_isbn,p_issued_emp_id);

-- update book status 
UPDATE books
SET status='no'
WHERE isbn=p_issued_book_isbn;
 
SELECT 'Book issued successfully' AS message;
ELSE 
SELECT 'Book is not available' AS message;

END IF;

END $$
 

CALL issue_book('IS106','C106','978-0-330-25864-8','E104');
```

### TASK 20: Create Table As Select (CTAS) Objective: Create a CTAS (Create Table As Select) query to identify overdue books and calculate fines
**Description: Write a CTAS query to create a new table that lists each member and the books they have issued but not returned within 30 days. The table should include: The number of overdue books. The total fines, with each day's fine calculated at $0.50. 
The number of books issued by each member. The resulting table should show: Member ID Number of overdue books Total fines**
```sql
CREATE TABLE overdue_fine AS
SELECT mb.member_id , mb.member_name,COUNT(issued_date) as no_of_overdue, SUM(datediff(current_date,issued_date)*0.50) as total_fine
FROM issued_status as ist LEFT JOIN return_status as rst ON ist.issued_id = rst.issued_id  JOIN members as mb ON ist.issued_member_id=mb.member_id 
WHERE datediff(current_date,issued_date)>=30 AND rst.return_id IS NULL  GROUP BY mb.member_id,mb.member_name
```

### Reports:
**Database Schema:** Detailed table structures and relationships.
**Data Analysis:** Insights into book categories, employee salaries, member registration trends, and issued books.
**Summary Reports:** Aggregated data on high-demand books and employee performance.

### Conclusion
This project demonstrates the application of SQL skills in creating and managing a library management system. It includes database setup, data manipulation, and advanced querying, providing a solid foundation for data management and analysis.
