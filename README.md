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
 CREATE TABLE branch(branch_id varchar(10) PRIMARY KEY,
				   manager_id varchar(10),
                   branch_address varchar(50),
                   contact_no varchar(10));

CREATE TABLE employees(emp_id varchar(10 ) PRIMARY KEY,
	                   emp_name varchar(25),
                       position varchar(20),
                       salary int,
                       branch_id varchar(15));
					
CREATE TABLE books(isbn varchar(20) PRIMARY KEY,
	               book_title varchar(70),
                   category	varchar(15),
                   rental_price float,
                   status varchar(10),
                   author varchar(20),
                   publisher varchar(35));


CREATE TABLE members(member_id varchar(20) PRIMARY KEY,
	                 member_name varchar(30),
                     member_address varchar(75),
                     reg_date date);
                     

CREATE TABLE issued_status(issued_id varchar(10) PRIMARY KEY,
						issued_member_id varchar(10),
                        issued_book_name varchar(75),
                        issued_date date,
                        issued_book_isbn varchar(25),
                        issued_emp_id varchar(10));
                        
CREATE TABLE return_status(return_id varchar(10),
                          issued_id	varchar(10),
                          return_book_name varchar(75),
                          return_date date ,
                          return_book_isbn varchar(20));
```
 
