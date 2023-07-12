---
title: CoreData
date: 2023-07-12 09:24:50
tags: 
---
> “Core Data is a framework that you use to manage the model layer objects in your application” — Apple

## what is core data?

Core Data is a framework that lets you express what your model objects are and how they are related to one another. It then takes control of the lifetimes of these objects, making sure the relationships are kept up to date.
This collection of model objects is often called an object graph, as the objects can be thought of as nodes and the relationships as vertices in a mathematical graph.**Often you will have Core Data save your object graph to a SQLite database**
<!--more-->
Developers who are used to other SQL technologies might expect to treat Core Data like an object-relational mapping system,but this mindset will lead to confusion. Unlike an ORM, Core Data takes complete control of the storage, which just happens to be a relational database. You do not have to describe things like the database schema and foreign keys – Core Data does that. You just tell Core Data what needs storing and let it work out how to store it. Core Data gives you the ability to fetch and store data in a relational database without having to know the details of the underlying storage mechanism.

A relational database has something called a table. A table represents a type: You can have a table of people. Each table has a number of columns to hold pieces of information about the type.Every row in the table represents an example of the type.

This organization translates well to Swift. Every table is like a Swift type. Every column is one of the type’s properties. Every row is an instance of that type. Thus, Core Data’s job is to move data to and from these two representations.

Core Data uses different terminology to describe these ideas: A table/type is called an entity, and the columns/properties are called attributes. A Core Data model file is the description of every entity along with its attributes in your application.

## The Core Data Stack

There are 4 main components : persistent container, managed object model, managed object context, and store coordinator.

### The Persistent container

The persistent container is the higher level abstraction class that encapsulates the most important components of the core data stack.

### The Managed Object Model

 This is a programmatic representation of the .xcdatamodeld file. 

### The Managed Object Context

The business layer of your application interacts with the Core Data Stack through the object contex

### The Persistent Store Coordinator

This acts as the bridge between the core data stack and the store(database). When an object context makes a request to fetch all the items in the database, the persistent coordinator is the one that loads them up from the SQLite store and passes them back to the object context.

see more information on apple offical **[docment](https://developer.apple.com/documentation/coredata/creating_a_core_data_model)** about core data.

------

## what is ORM?

Object Relational Mapping (ORM) is a technique used in creating a "bridge" between object-oriented programs and, in most cases, relational databases.

using SQL

```SQL
"SELECT id, name, email, country, phone_number FROM users WHERE id = 20"
```

using ORM

```swift
users.GetById(20)
```

## what is sqlite?

SQLite is a software library that implements a self-contained, serverless, zero-configuration, transactional SQL database engine

### Install SQLite on Windows

- Go to SQLite download page, and download precompiled binaries from Windows section

- Download sqlite-shell-win32-*.zip and sqlite-dll-win32-*.zip zipped files

- Create a folder C:\>sqlite and unzip above two zipped files in this folder, which will give you sqlite3.def, sqlite3.dll and sqlite3.exe files

- Add C:\>sqlite in your PATH environment variable