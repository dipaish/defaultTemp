# Introduction to Database: 
### What is a database? 

- An ***organized  collection*** of related data (structured information) 
    -  Stored in **logical and structured** manner
    - Typically stored electronically in a computer system
- Databases are everywhere 
    - points related to course assignments are recorded in the database
    - if you do enough work on the course to achieve the learning objectives related to the course, your grade will be recorded in the database
    - Application utilizes one or more databases
- Databases can be **local** (located on the same machine)
- Databases can be **external** (located on a separate server)
- Information is retrieved from the database and presented to the end-user via an application  
![database storing & retrieving information](assets/images/db.png)
### What is Database Management System (DBMS) ?
- A database management system is an application through which a user can create and maintain databases.
    > Example: **MySQL**, Microsoft Access, Microsoft SQL Server, FileMaker Pro, Oracle Database, and dBASE

- DBMS provides multiple functions for managing databases & its data. These functions can be classified as below: 
    - **Data definition**: Organization of the data (creating, modifying & removing)
     - **Update** – Insertion, modification, and deletion of the actual data.
     - **Retrieval** – Getting data for presentation or further processing by an application.
     - **Administration** – Managing database users, databases, enforcing data security, monitoring performance, maintaining data integrity, database backups
- **MySQL is a popular, open-source DBMS**

### Benefits of a Database System
- Centralized data management.
- Data duplication is reduced or managed.
- Data inconsistency can be avoided.
- Multiple users can access data, even at the same time.
- Standardization is easier.
- Data access rights can be secured.
- Data is more reliable and accurate.
- Different operating needs can be considered when building the system.
- Easier deployment of new applications.
- Provides mechanisms for data backup and recovery.


### What is a Relational Database ?
- A relational database is based on the ***relational data model.***
- A relational database stores data in the form of rows and columns that forms an ***entity(table).***
- It allows to store data across multiple table. Tables are related to each other ***(relationship)***

- To interact with databases, it uses ***Structured Query Language (SQL)*** 
 - SQL is used to store, modify and maintain the data.
- It is the most common type of `DBMS`

**Example of an entity/table**
![relational data model](assets/images/relationalModel.png)

# Database Design: 
### What is Data Modeling? 
> It is a process to define and analyze data requirements to support a certain busines sprocess or to serve a purpose. 
#### Tools for Data modeling
You may use any of the following options:
- A piece of paper and a pen to model your database. 
- **Lucidchart:** https://www.lucidchart.com/ The free option is enough for us to design our databases. (***I am using Lucidchart***)
- Draw.io https://app.diagrams.net/

***Before creating an actual database, you produce three different types of data models:***

1. ***Conceptual Data Model***
     - It is a highl level model.
    - Think about the data requirements: what data your system contains? Take into account your stakeholders: application users & scope of your application 
    - You are basically explaining the concept based on your data requirements & the relationship between the objects
    -  The end result of the conceputal data model will be names such as `student`, `course`, `teacher`
    - The relationship between the objects are simply shown by drawing a line between the objects.
     - This model is done for wider audience that is it should be easy to understand for anyone looking the diagram. 
        - **Hint: Represent data as a normal user will see it in the real world.** 
        ##### Example of a Conceptual Data Model: A teacher will teach a course(s) and student can participate in a course(s).
    ![Example of a Conceptual Data Model](assets/images/cmodel.png)

2. ***Logical Data Model***
    - The logical data model defines the structure of the data.
    - Each object is defined in details that is you inform about each of your object. In the above case, you need to define information for `student`, `course` & `teacher`
    - You need to explain data types such as number or string for each of your data attribute.
    - It also shows the relationships between objects. 
    ##### Example of a Logic Data Model: Each object is defined in details. 
     ![Example of a Logical Data Model](assets/images/logicModel.png)

3. ***Physical Data Model***
    - It is describing a database specific implementation of the data model. 
    - It is used to creata a database and required tables.
    - It includes the name of the table & column names with [Data Types](https://dev.mysql.com/doc/refman/8.0/en/data-types.html). 
    - Primary keys, foreign keys, views etc are also defined. 
    
    ##### Example of a Physical Data Model: Each object is defined in details. 
     ![Example of a Physical Data Model](assets/images/pmodel.png)

> Note: We did not specificy primary or foreign key. Once we learn them, we will start to use them.

### PRIMARY KEY

- The relational data model can not contain duplicate values. The duplicate values create ambiguities in retrieval, therefore  **primary key (PK)** is used to uniquely identify each record in a database table. 

- **Common Rules for Primary Key**
    - Must contain unique values and Not NULL
    - Most tables have a primary key
    - A table has only one primary key
    - Primary key column’s type is often an integer type (INT or BIGINT)
    - Primary key column often has AUTO_INCREMENT attribute that generates a unique sequence for the key automatically. 

- Sometimes you have an obvious choice for selecting a primary key such as the registration number of a car, other times it can be difficult. 
- One of the common solution for primary key is to allocate a number and use autoincrement (studentID, playerID, staffID)
- It is possible to have a primary key that is made up of more that one attribute which is called a **composite primary key**

#### Now lets update our Data Model by including primary key

##### Example of a Physical Data Model: Each object is defined in details. 
![Example of a Physical Data Model](assets/images/pkmodel.png)

### COMPOSITE KEY

- It is made up of more than one attribute

![Composite Key](assets/images/ckey.png)
- In the above table, none of the attributes can be guaranteed to be unique 
    - A student however is enrolled for a course therefore the combination of Course and StudentId will be unique. 
    - Combining these two attributes will form a **composite primary key**
- To avoid the **composite key**, you may create a new attribute enrollmentID as an integer & make it autoincrement. However, this will create an extra (unnecessary column), therefore a more effective choice is a **composite key.**

### FOREIGN KEY

### ENTITY RELATIONSHIP DIAGRAM
- It is a top-down technique for visualizing and understanding the entities and the relationships between them. 
- An **entity–relationship diagram (or ER diagram)** describes 
    - the entities
    - logical structure of the entities (attributes) and 
    - relationships between the entities
- Each **entity** in an ER diagram is represented by a rectangle. 
- **Relationship** is an association between two o rmore entities.
 `Example: the relation between the course and student table is that a student can enroll to one or many coourses.` ***It is shown by a line and can be one of the three types***
    - One-to-one relationship (It is not very common type of relationship)
    - **One-to-many relationship** (The most common type of relationship )
    - Many-to-many relationship (It is not ideal in the database design)

#### One-to-one relationship

- As the name suggests, **One-to-one** refers to the association between two entities(tables) A & B in which one element from A is linked to one element of B. 
- In the **relational model**, it means that one row in a table is linked with only one row in another table and vice versa.
![One-to-one relationship](assets/images/onetoone.png)
> Examples:

    - Your address has only 1 postal code. 
    - Each course has only 1 capital city.


#### One-to-many relationship
- **One-to-many** refers to the association between two entities(tables) A & B in which one element from A is linked to multiple elements of B.
- In the **relational model**, it means that one row in a table is linked with many rows in another table and vice versa.
- It is the most commmon type of relationship. 
![One-to-many relationship](assets/images/om.png)
> Examples:

    - A city has multiple Zip codes
    - A teacher can teach many courses. 


#### Many-to-many relationship

- **Many-to-many** refers to the association between  entities(tables)  in which many elements from one entity are linked to many elements in another entity.
- In the **relational model**, it means that multiple rows in a table are linked with multiple rows in another table and vice versa.
![many-to-many relationship](assets/images/mtm.png)    



#### How to handle Many-to-many relationship in database design?
- Lets think about the **student** and **course** tables, ***a student can enroll in many courses and a course has many students***. 
     - The relationship in this case is many to many relationship ***which is not an ideal situation in database design as it becomes difficult to manage many to many relationship using those two tables***. 
     - To store data effectively, we can think of a adjoining table that relates both of those two tables by establishing one to many relationship. 
     - ***In practice, the many-to-many relationship is implemented as two one-to-many relationships, with the introduction of the adjoining (junction) table.***
        - One student can have one to many enrollments. 
        - A course can have one to many enrollments.

![many-to-many relationship](assets/images/new.png)
---

##  Database Design Process

**Step 1:Requirement Analysis - Define the purpose of the Database**
- ***You can think of questions such as***
    - Why do you need a database?
    - What data you will be storing? 
    - What kind of entities you might need?

**End result: Conceptual Model**

**Step 2: Define the attributes of the entities that you discovered from Step 1** 
- ***You may do the following activities***
    - List column names (attributes)
    - Specify data types such as number, text etc. 
    - Specify **Primary Key** for each **entity**. 

**End result: Logical Data Model**

**Step 3: Define relationships among tables**

- ***You may do the following activities***
    - Specify data types such as VARCHAR, INT, BOOLEAN.
    - Specify Foreign Keys
    - Define what kind of relationship exist between tables. 

**End result: ER Diagram / Database Model**

---

#### Example 1: A simple Database for a library
> Now let's put all pieces what we have learnt so far and create a database design for a library.
- Library has several books and items to loan. 
- Think of the entities: `book` 
### Hello world
- create tables with draw.io
- understand relationship and establish relationship between tables
- Decide primary and foreign keys
- Specify datatypes


### References: 
- https://en.wikipedia.org/wiki/Database Read on 27.09.2022
- https://en.wikipedia.org/wiki/Data_modeling Read on 27.09.2022

