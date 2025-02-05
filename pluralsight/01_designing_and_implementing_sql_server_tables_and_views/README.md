# Designing and Implementing SQL Server Tables and Views

Introduction to SQL Server Tables and Views.

## 1. Introducing Tables and Views

Some definitions can be found [here](definitions.md) :-).

>Relational databases are made up of tables and views. Tables are used to store data. Views are used to retrieve data. Views are also used to restrict access to data. Views can be used to hide sensitive data from users. Views can also be used to combine data from multiple tables. Tables are related to each other through relationships.

E.F. Codd, the father of relational databases. He is credited with inventing the relational database model.

Some terminology:

- relation == table,
- tuple == row,
- attribute == column,
- RDBMS == relational database management system.

### _Bob's Shoes_

Problem description (for which we will create a database):

_Bob has a factory that produces shoes, he needs a database to store information about his shoes, customers, orders, etc._

We need to store:

- customer information,
- order information,
- shoe information.

## 2. Designing and Implementing Tables

To create a database use [01_create_database.sql](sql/01_create_database.sql).

To create sample tables use [02_create_tables.sql](sql/02_create_tables.sql).

To insert a sample data use [03_insert_data.sql](sql/03_insert_data.sql).

To create sample views use [04_create_views.sql](sql/04_create_views.sql).

### Creating Identifiers

Regular identifiers:

- starts with: letter, underscore, at sign (@) (!use :-)), number sign (#) (!use :-)),
- then: letters, digits, @, $, #, \_,
- not a reserved word,
- not a special character,
- identifier delimited with square brackets ([]) can break all the rules above,
- no longer than 128 characters.

Valid identifiers:

`orders`, `product_name`, `[Order Details]`, ...

### Using Naming Conventions

Do not use @ or # (used for objects in tempdb) as the first character.

Use consistent style for naming objects, for example:

- Pascal Case: `OrderDetails`,
- Underscore separated: `order_details`,
- don't use delimited identifiers: `[Order Details]`.

### Using Character Data Types

There are two types of character data types:

- fixed length: char, nchar, binary,
- variable length: varchar, nvarchar, varbinary.

`char(n)` - fixed length non-unicode characters, n is the length of the string (1 <= n <= 8000).

`nchar(n)` - fixed length unicode characters, n is the length of the string (1 <= n <= 4000).

`varchar(n)` - variable length non-unicode characters, n is the maximum length of the string (1 <= n <= 8000).

`nvarchar(n)` - variable length unicode characters, n is the maximum length of the string (1 <= n <= 4000).

`binary(n)` - fixed length binary data, n is the length of the string (1 <= n <= 8000).

`varbinary(n)` - variable length binary data, n is the maximum length of the string (1 <= n <= 4000).

`varchar(max)` - variable length non-unicode characters, maximum length of the string is 2GB.

`nvarchar(max)` - variable length unicode characters, maximum length of the string is 1GB.

`binary(max)` - variable length binary data, maximum length of the string is 2GB.

`varbinary(max)` - variable length binary data, maximum length of the string is 1GB.

### Using Integer and Decimal Data Types

There are two types of integer data types:

- integer: tinyint, smallint, int, bigint,
- float: real, float,
- decimal: decimal, numeric, smallmoney, money.

`tinyint` - 1 byte, 0 to 255.

`smallint` - 2 bytes, -32,768 to 32,767.

`int` - 4 bytes, -2,147,483,648 to 2,147,483,647.

`bigint` - 8 bytes, -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807.

`real` - 4 bytes, -3.40E + 38 to -1.18E - 38, 0, and 1.18E - 38 to 3.40E + 38.

`float` - 8 bytes, -1.79E + 308 to -2.23E - 308, 0, and 2.23E - 308 to 1.79E + 308.

`decimal(p, s)` - 5 to 17 bytes, -10^38 + 1 to 10^38 - 1, 0, and 10^38 - 1 to 10^38 - 1, p is the precision (1 <= p <= 38), s is the scale (0 <= s <= p).

`numeric(p, s)` - 5 to 17 bytes, -10^38 + 1 to 10^38 - 1, 0, and 10^38 - 1 to 10^38 - 1, p is the precision (1 <= p <= 38), s is the scale (0 <= s <= p).

`smallmoney` - 4 bytes, -214,748.3648 to 214,748.3647.

`money` - 8 bytes, -922,337,203,685,477.5808 to 922,337,203,685,477.5807.

### Using Date and Time Types

There are six types of date and time data types:

- date: date,
- time: time,
- date and time: datetime, datetime2, smalldatetime,
- time interval: datetimeoffset.

`date` - 3 bytes, year, month, day (range: 0001-01-01 to 9999-12-31).

`time` - 5 bytes, hour, minute, second, millisecond (range: 00:00:00.000 to 23:59:59.999).

`datetime` - 8 bytes, year, month, day, hour, minute, second, millisecond (range: 1753-01-01 00:00:00 to 9999-12-31 23:59:59).

`datetime2` - 6 to 8 bytes, year, month, day, hour, minute, second, millisecond (range: 0001-01-01 00:00:00.000 to 9999-12-31 23:59:59.999).

`smalldatetime` - 4 bytes, year, month, day, hour, minute (1900-01-01 00:00:00 2079-06-06 23:59:59).

`datetimeoffset` - 10 bytes, year, month, day, hour, minute, second, millisecond, offset (range like datetime2 + timezone info).

### Other Data Types

There are other data types:

- `uniqueidentifier` - 16 bytes, globally unique identifier (GUID),
- `rowversion` - 8 bytes, timestamp.
- `bit` - 1 bit, 0 or 1.

Comprehensive list of data types can be found [here](https://learn.microsoft.com/en-us/sql/t-sql/data-types/data-types-transact-sql?view=sql-server-ver15).

### Using Collations

>A collation specifies the rules for comparing characters in a character set. Collations are used to sort and compare data, they also define case and accent sensitivity and code page for non-unicode data (no n-char/varchar).

All character data has some collation, whether it is specified or not. If not specified, the default collation for the database is used. Collation can be specified at the instance level (during setup), database level (ALTER DATABASE), or column level (CREATE TABLE) it is also possible to specify collation at the expression level (SELECT).

More info [here](https://learn.microsoft.com/en-us/sql/relational-databases/collations/collation-and-unicode-support?view=sql-server-ver15).

## 3. Improving Table Design Through Normalization

>Normalization is a process of organizing data in a database to minimize redundancy and dependency of data.

There are three normal forms (at least these are the most popular ones):

- zero normal form (0NF) - not really a normal form, it is a state of a table before normalization,
- first normal form (1NF) - each column contains atomic values, no repeating groups, table has a primary key,
- second normal form (2NF) - table is in 1NF and all non-key columns are fully dependent on the primary key,
- third normal form (3NF) - table is in 2NF and all non-key columns are not transitively dependent on the primary key.

More info about database normalization can be found [here](https://learn.microsoft.com/en-us/office/troubleshoot/access/database-normalization-description) and [here](https://www.guru99.com/database-normalization.html).

## 4. Ensuring Data Integrity with Constraints

>To ensure the data integrity in the database we might have to create a constraint. A constraint is a rule that is enforced on the data in a table.

There are different types of constraints:

- `NOT NULL` - column cannot contain NULL values,
- `PRIMARY KEY` - column or set of columns that uniquely identifies a row in a table,
- `UNIQUE` - column or set of columns that must contain unique values,
- `FOREIGN KEY` - column or set of columns that must match the values in a column or set of columns in another table,
- `CHECK` - column or set of columns that must satisfy a specified condition,
- `DEFAULT` - column would have a default value if no other value is specified.

>NULL is a special marker, that means: absent, unavailable, inapplicable. Introduction of the NULL value leads us to the so-called "three-value logic": true, false, unknown. NULL is not equal to anything, including NULL. NULL is not equal to 0, empty string, or any other value. NULL is not equal to itself. Be explicit about NULL values while creating your tables and always use: `NULL` or `NOT NULL` in your columns.

Primary key in SQL Server ensures uniqueness, to achieve this a backing index is created (clustered by default - not always the best choice). Sometimes we might use unique constraint together with the primary key (unique constraint would allow a null value - but, which is a bug IMHO, only single one). PK constraints and unique constraints can be referenced by the foreign key.

For foreign key we might use cascading update and cascading delete, for these we might define what should happen to the child rows when the parent row is updated or deleted. There are three options:

- `NO ACTION` - no action is taken,
- `CASCADE` - child rows are updated or deleted,
- `SET NULL` - child rows are updated or deleted, but the foreign key columns are set to NULL,
- `SET DEFAULT` - child rows are updated or deleted, but the foreign key columns are set to the default value.

For foreign keys backing index is not created automatically, but it is recommended to create one.

To change a constraint you must drop the constraint and then create a new constraint.

You can define a constraint inside `CREATE TABLE` statement or after the table is created using `ALTER TABLE ... ADD CONSTRAINT` statement.

More info about constraints can be found [here](https://learn.microsoft.com/en-us/sql/relational-databases/tables/unique-constraints-and-check-constraints?view=sql-server-ver15).

## 5. Designing View to Meet Business Requirements

>A view is a virtual table based on the result-set of an SQL statement. A view contains rows and columns, just like a real table. The fields in a view are fields from one or more real tables in the database.

Views are used to:

- hide data (David Parnas, 1972 - information hiding) & customize results,
- combine data from multiple tables,
- restrict access to data,
- simplify queries & encapsulate queries,
- provide backward compatibility.

>Updateable views are views that can be used to update data in the database. Updateable views are created using `CREATE VIEW` statement with `WITH SCHEMABINDING` option. Any modifications, including update, insert or delete statements must reference columns from only one base table.

If we are using `WITH SCHEMABINDING` option base tables cannot be dropped or altered and we must use two-part names to reference base tables, last all referenced objects must be in the same database.

More info about views can be found [here](https://learn.microsoft.com/en-us/sql/relational-databases/views/views?view=sql-server-ver15).

## 6. Implementing Indexed Views

>An indexed view is a view that has an index on it. Indexed views are used to improve performance of queries that use the view.

Indexed view has many requirements, some of them:

- first index must be a unique clustered index,
- certain SET options must be specified,
- a view must be deterministic,
- must be created with `SCHEMABINDING` option,
- restricted T-SQL elements that can't be used inside the index.

More info about indexed views can be found [here](https://learn.microsoft.com/en-us/sql/relational-databases/views/create-indexed-views?view=sql-server-ver15).

## 7. Implementing Partitioned Views

>Use case for the partitioned views is when we have a large table and we want to create a view that would be partitioned by a column. Partitioned views are used to improve performance of queries that use the view. For example let's say we have a table storing an order data, over time that table would grow significantly, at the same time we don't want to just delete the old data. What we can do instead is to create some history tables and from time move the data from the main table to the history ones. Provided that we want to create some queries that would use the data from the main table and the history ones, we can create a partitioned view that would be partitioned by the date column.

General syntax to create a partitioned view is like this:

```sql
CREATE VIEW [schema_name].[view_name]
AS
    SELECT [column_name], [column_name], ...
    FROM [schema_name].[table_name1]
    WHERE [column_name] = [value]

    UNION ALL

    SELECT [column_name], [column_name], ...
    FROM [schema_name].[table_name2]
    WHERE [column_name] = [value]

    UNION ALL
    ...
```

Requirements for the partitioned views are:

- all columns should be included in the view,
- columns in same position should have the same data type,
- at least one column should have `CHECK` constraint:
  - it is called partitioning column,
- a column can appear only once in the select statement.

More info abut that can be found [here](https://www.brentozar.com/archive/2016/09/partitioned-views-guide/).

## Summary

Now you know how to design a database to meet business requirements. You know how to create tables, views, and indexed views. You know how to create constraints and how to use them to ensure data integrity. You know how to create partitioned views.
