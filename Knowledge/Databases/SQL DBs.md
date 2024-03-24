# What does SQL mean?
---
SQL stands for Structured Query Language. It's a way to form questions and statements and send them to the database to create, read, update and delete records.

When talking about SQL, we think of SQL databases. SQL databases are **relational databases**. Many DBMS (database management systems) belong in this list:
- [[PostgreSQL]] *(most popular with web developers)*
- Microsoft SQL Server
- MySQL *(open source)*
- MariaDB *(open source fork of MySQL and much faster)*
- H2
- Oracle Database

Here are some of the pros hey are the best at representing data with relationships.

>[!tip]+ Fact
> Relational databases are great at representing data with relationships (for example many to many relationships) whereas MongoDB is not. It's expensive to perform JOINs between two collections.

# Guarantees
---
SQL databases provide [[ACID]] guarantees.