---
title: EverythingAboutMysql
tags: database
date: 2023-05-25 16:18:49
---
> This blog will record the thing I meetting when I learn mysql.
![library](databaseschema.jpg)

## MySQL Data Types

The proper definition of a table’s fields is critical to the overall optimization of your database. You should only utilise the type and size of the field that you truly require. For example, if you know you’ll only use two characters, don’t make a field 10 characters large. After the sort of data, you’ll be storing in those fields, these fields (or columns) are referred to as data types.
MySQL makes use of a variety of data types that are divided into three groups.

- Numeric
- Date and Time
- String Types.
<!--more-->
## DDL

DDL or Data Definition Language consists of those commands which include defining the structure of database schema and database table. It mainly involves the structuring of database schema and the table.

The commands included in DDL are: 

- Create – Used to create the table schema
- Drop – Drop the database from the memory
- Alter – Alter the structure of the table schema
- Truncate – Delete all the data from the database schema
- Comment – These statements are meant only for understanding the schema structure. They do not contribute to the actual database structure
- Rename – Used to rename the database table

## DML

DML or Data Manipulation Language consists of those commands which include manipulating the data of the database table. It involves creating and manipulating the data of the database table.

The commands included in DML are

- Select: Used to extract the information from the database table  
- Insert: Used to insert the data into the database table
- Update: Update the existing data in the database table where a particular condition is defined.
- Delete: Delete the data from the database table
  
## DCL

DCL or Data Control Language consists of those commands to deal with the rights, permissions, or other controls of the database system. It involves specific rights to the specific users to access the database.

The commands included in DCL are

- Grant : Used to give permissions to a specific user to access the database with read or write permissions.

- Revoke : Used to remove the permissions from a specific user to access the database.

## TCL

TCL or Transaction Control Language consists of those commands that deal with the transactions of the database.These include collection of statements between specific client and server.

The commands included in TCL are:

- Commit : Used to store the previous data.
- Rollback : Used to restore the previous deleted data or tables.
- Savepoint : Used to create a point in the database also known as checkpoint. It is used to rollback the transaction.