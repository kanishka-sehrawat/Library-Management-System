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
  INSERT INTO books(isbn,book_title,category,rental_price,status,author,publisher) values('978-1-60129-456-2','To Kill a Mockingbird','Classic',
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
SELECT count(issued_member_id) as number_of_time_issued,issued_member_id from issued_status status group by issued_member_id 
HAVING count(issued_member_id)>1 order by count(issued_member_id);
```

### 3. CTAS (Create Table As Select)
**TASK 6. Create summary tables: Used CTAS to generate new tables based on query results - each book and total book_issued_count.
```sql
CREATE TABLE book_counts
AS 
SELECT b.isbn,b.book_title ,count(ist.issued_id) as no_issued
from books as b JOIN issued_status as ist 
ON b.isbn=ist.issued_book_isbn group by b.isbn,b.book_title;
```

**TASK 7. Retrieve all books in a specific category**
SELECT * FROM books WHERE category='Classic';

