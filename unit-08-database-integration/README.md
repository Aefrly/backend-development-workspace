## Unit 8
Learning to use ORMs and Sequelize with models and CRUD operations.

## Intro to ORMs and Sequelize
- In the previous unit, you learned how to persist data using raw SQL queries. This works and gives you full control, but has significant drawbacks:
    Complex syntax: Raw SQL queries are hard to read and maintain
    Error-prone: Easy to make typos or syntax errors in SQL strings
    No type safety: JavaScript can't catch database errors until runtime
    Repetitive code: The same patterns repeated for every query
    Security risks: Manual parameter binding to prevent SQL injection
- Object-Relational Mapping or ORMs solve these problems by providing a more intuitive, JavaScript-friendly approach. They provide a layer between your JavaScript code and your database.
- This line of code queries the database just like the raw SQL query, but the ORM provides several key benefits:
    Database abstraction: Write JavaScript instead of SQL
    Type safety: Catch errors before they reach the database
    Automatic migrations: Handle database schema changes safely
    Query optimization: ORMs optimize queries for performance
    Security: Built-in protection against SQL injection attacks
- For this course, we'll use Sequelize because it strikes the perfect balance for learning database concepts. It provides a gentle introduction to ORM patterns while still allowing you to see the underlying SQL queries being generated. Sequelize also integrates seamlessly with Express.js applications and has extensive documentation to support your learning journey. However, the focus here shouldn’t be on “mastering” all the syntax associated with Sequelize. Instead, your goal should be to understand how an ORM is structured and is integrated into a backend application as your future job may very well use a different ORM based on your company’s business needs.
- In order to utilize Sequelize, we need to install the sequelize package along with the dotenv package that we learned how to use in Unit 4.
- Before we can add and query values from a database, we need to create the database! In order to create the database, you should go to the database/setup.js file and first import the necessary packages.
- In addition to importing the dotenv package, this also provides access to two properties from the sequelize module:
    Sequelize: This is the main class used to connect to the database and interact with it.
    DataTypes: This gives you access to various data types (STRING, INTEGER, DATE, etc.) that you will use to define the structure of columns in your model.
- You can call the variable where the instance is stored whatever you want, but db is a useful name since this is the variable you’ll use to interact with the database going forward. Note, many of Sequelize’s documentation will call this variable sequelize. There are two mandatory options that are included and one optional:
    dialect: This specifies the database that Sequelize will connect to and indicates that it is a SQLite database.
    storage: This defines the location of the SQLite database file. We use process.env to access the environment variable name of the database. If this fails, then the default path database/library.db of is used.
    logging: This enables logging of SQL queries to the console. We will use this to illustrate what is happening when we run various actions with Sequlize.
- Once the new instance of the Sequelize class is created, we can test the connection and create the database with the following asynchronous function.
- This function, setupDatabase, is asynchronous because numerous Sequelize methods return promises that we need to await for once the promise is resolved. This function does three distinct steps:
    await db.authenticate(): This attempts to establish a connection to the database.
    await db.sync({ force: true }): This syncs all defined models with the database. The { force: true } parameter forces Sequelize to drop and recreate the tables, which is typically used in development.
    await db.close(): This final command will close the database gracefully.
- If successful, you’ll now have a database filed called library.db! The database file now exists and is ready to store data permanently.

## Sequelize Models
- In your previous unit, you created a table by using a raw SQL statement. Once the table was defined, you then had to write INSERT statements to add data. This approach works, but it's error-prone and difficult to maintain.
- Models solve this problem. Instead of writing raw SQL commands, you can define your database structure using JavaScript objects that Sequelize converts into proper SQL tables. This gives you type safety, validation, and a much cleaner way to interact with your database.
- A model in Sequelize represents a table in your database. Think of it as a JavaScript blueprint that defines:
    What columns your table has
    What data types each column stores
    What rules and constraints apply to your data
    How to interact with that table using JavaScript methods
- Models bridge the gap between your JavaScript code and SQL tables. Instead of writing raw SQL commands, you can use intuitive JavaScript methods to create, read, update, and delete data.
- To make tables using the Sequelize ORM, we use the .define() method. The Sequelize model provides the same functionality but with better type safety, validation, and JavaScript integration.
- Now we'll use the DataTypes property to define the structure of your model fields.
- At the top of seed.js, import the Book model and database connection that you defined in setup.js.
- Below the provided seed data in sampleBooks array, we can define an asynchronous function that will seed the database with the sample data.
- You should see confirmation that your Book model was created and synchronized with the database. Since logging is still on, you will also see the raw SQL commands that are being run by the ORM.
- You now have a properly defined Sequelize model that handles data validation, type checking, and provides convenient methods for database operations.

## Sequelize CRUD Operations
- Sequelize has basic CRUD operations that can be used inside your Express.js routes. Instead of writing raw SQL queries in your API endpoints, you'll use the Book model methods to interact with your database.
- As you have done numerous times before, we need to start by importing the necessary packages and modules into server.js and initializing an Express application.
- Next, we want to confirm that we can connect to the database when the server starts, so we can define an asynchronous function that verifies that the connection was successful. This will allow us to catch connection errors before we try to query the database.
- This endpoint is nearly identical to the logic for GET /api/books, except it uses the Sequelize .findByPk() method. This method finds and returns a book based on its primary key (the id in this case).
- This route looks similar to POST routes you have written before, except that it uses the Sequelize .create() method to insert a new book into the database. This method creates the new book and then returns an object representing the data that was inserted into the database, which we can then return to the user along with a status code of 201. Additionally, this route now sends a status code of 500 if there is an error and the book cannot be created in the database.
- The .update() method returns an array where the first element is the number of updated rows. We use this to verify that a row has been updated. If no rows were updated, then a 404 error is returned.
- This endpoint uses the Sequelize .destroy() method to delete a value in the database. The .destroy() method accepts one object which contains a where clause containing the id of the item that you wish to delete. The destroy method returns the number of deleted rows, which you can then use to send a status code indicating if the book was or was not removed from the database.
- You now have a fully functional REST API that uses Sequelize CRUD operations to query your database using the model that you created.