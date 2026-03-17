## Unit 9
Learning to use foreign keys and table relationships, authentication versus authorization, password hashing and security, and session management and middleware.

## Foreign Keys and Table Relationships
- This is the fundamental problem that relational databases solve. The word "relational" doesn't mean "database with tables". Rather, it specifically refers to the relationships between tables that allow data to connect and work together.
- There are a number of issues with this approach:
    Data duplication
    Wasted space
    Update nightmares
    Incomplete data
- Luckily, relational databases offer a simple and elegant solutions: create tables for each specific entity.
- For our library database, this means creating three distinct tables that each handle specific components of the data:
    Books table
    Users table
    Checkouts table
- This eliminates duplication, reduces errors, and allows us to store books without checkouts and users without checkouts.
- A foreign key is a field in one table that references the primary key of another table. Think of it as a "connection point" between two tables.
- In this example, userId in the Checkouts table is a foreign key that references the id field in the Users table and the bookId in the Checkouts table is a foreign key that references the id field in the Books table
- This allows us to track which users have checked out which books and ensure that we only need to update the checkouts table with the required ids and then we can use this to point towards additional information stored in other tables.
- Before we can associate tables, we need to create the tables.
- To associate tables with one another, we need to tell Sequelize how these tables are related. This can be done in database/setup.js below the model definition with the following commands.
- The first series of commands establishes the relationship between the User and Checkout models.
- One user can have many checkouts, so the hasMany() command is used. The method is passed the foreign key of userId as this is the value in the Checkout table that points back to the user. A checkout can have only one single user, so the belongsTo() command is used with the same foreign key being provided as an argument.
- One book can be checked out many times , so the hasMany() command is again used. The method is passed the foreign key of bookId as this is the value in the Checkout table that points back to the user. A single checkout record has only one book, so the belongsTo() command is again used.
- The foreign key constraints will ensure that:
    Checkouts can only be created with valid userId values that exist in the Users table
    Checkouts can only be created with valid bookId values that exist in the Books table
    If you try to create a checkout with an invalid foreign key, the database will reject it
- We now can (1) store data in separate models and (2) query data across models because of the foreign key association that we created.

## Authentication vs. Authorization
- 

## Password Hashing and Security
- 

## Session Management and Middleware
- 
