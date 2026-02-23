## Unit 7 
Learning about architecture and creating and querying relational databases.

## Three-Tiered Architecture
- The webpage can be broken down into three tiers:
    1: Presentation Tier: Any page you see as you navigate is an example of the presentation tier. If you are logged in, this tier is customized based on the actions taken by the application tier.
    2: Application Tier: There are many different examples of the application tier, but let's consider one example: The Cart.
        When a user adds a product to their shopping cart, the application tier is responsible for:
        Processing the request and updating the user's shopping cart information to be displayed by the presentation tier.
        Interacting with the data tier to store the shopping cart information so that the information is saved even if the user navigates away from the page.
    3: Data Tier: There are many different examples on the webpage, but consider one example: Order History.
        Amazon keeps a record of all the orders placed by its users. The data tier is responsible for storing this information and making it accessible to the application layer, so users can view their order history and track their shipments.
- Applications are often broken down into three different tiers, each with their own purpose and functionality:
    Presentation Tier: Delivers information to the users. This is most often the webpage that the user interacts with. Most often, this tier utilizes HTML, CSS, and JavaScript.
    Application Tier: This tier contains the business logic of the application. It's responsible for processing user input, interacting with the database, and returning results to the presentation tier. This layer is typically implemented using a server-side language like Node.js.
    Data Tier: In order to "remember" information about a user, data must be stored in a database. The data tier is responsible for storing and retrieving data used by the application. This layer is usually implemented using a database management system like MySQL, MongoDB, PostgreSQL, etc.
- Throughout the remainder of this course, you will build APIs that demonstrate three-teried architecture:
    Presentation Tier: The API endpoints you've created that handle HTTP requests and responses.
    Application Tier: Your Express.js middleware and route handlers that process requests and implement business logic.
    Data Tier: The databases you'll learn to build that permanently store and retrieve information

## Creating Relational Databases
- Currently, if you want to create something similar to this functionality, you have needed to store this information in an array.
- This works for learning, but has serious problems:
    Data disappears: Every time you restart your server, all product data is lost.
    No persistence: If you add new products through your API, they vanish when the server stops.
    Hard to scale: Amazon can't hard-code millions of products in an array.
- A relational database stores information in tables with rows and columns, similar to spreadsheets. What makes it "relational" is that these tables can connect to each other through relationships.
- These tables are related because each product belongs to a category, and each review belongs to a specific product. The database can quickly find all electronics products or all reviews for a specific item because of these relationships.
- There are many reasons why companies opt to utilize relational databases, but a few of the most common include:
    Persistent Storage: Unlike arrays in your Express APIs that disappear when the server restarts, databases permanently store data. Relational databases can survive server restarts, power outages, and system updates.
    Fast Queries: Databases are optimized to quickly search through millions of records.
    Data Integrity: Databases enforce rules to keep data consistent such as ensuring that products have a valid price and preventing duplicate product IDs.
    Concurrent Access: Multiple users can access the database simultaneously. Thousands of customers can browse your website while staff members update information.
- We will be creating using SQLite to store the data for our REST API. SQLite is a lightweight relational database that stores data in a single file, which makes it perfect for learning database concepts.
- The database will be initialized by the database/setup.js file. To create the database, we need to import the sqlite3 package and create a new database file.
- This creates a database file called inventory.db in your database folder. If the file doesn't exist, SQLite creates it automatically. The use of .verbose() enables detailed error reporting to help with any debugging that we need to do as our application scales.
- Once the database file has been created, we need to define the structure of our tables. Tables define the structure of our data and are created using SQL commands. To create the products table for our inventory system, we need to use the CREATE TABLE command followed by the names of the columns and the data type that each column will store.
-  Let’s break down each command:
    db.run() - Executes a SQL command
    CREATE TABLE products - Creates a new table called "products"
    id INTEGER PRIMARY KEY AUTOINCREMENT - Creates a unique ID that automatically increases as more items are added to the table.
    TEXT - Text field for names and descriptions
    REAL - Decimal number for prices
    INTEGER - Whole number for stock quantities
- To add data to a database, we use the SQL insert command.
- Let’s break down each command:
    INSERT INTO products - Specifies which table to add data to (name, description, price, category, inStock) - Lists the columns that will have data added
    VALUES (...) - The data to insert. You can insert multiple rows of data by separating each line with a comma (similar to an array).
- In the next resource, we’ll learn how to access the data at various API endpoints, but you can add the following command to the end of another file to view all the data that has been added.
- The .all() command executes a SQL query and returns all the resulting rows that match this query. In the above example, this query is for all items in the products database, so it will result in all items being printed to your terminal.
- We have written code that seeds the database, but if you run the node database/seed.js command multiple times this will add duplicates to the database. There are ways to avoid this that are beyond the scope of this resource. However, you can always delete the database file and run the node database/seed.js command again if you have duplicates.

## Querying Relational Databases
- There are 4 CRUD (Create, Update, Read, and Delete) operations that you need to be able to perform on data
- Create    INSERT  Adds a new record to the table
    Read	SELECT	Find and retrieves a record from the table
    Update	UPDATE	Modifies an existing record
    Delete	DELETE	Removes a record
- To connect the database we created in the previous resource, we need to do the following in the server.js file:
    Import the sqlite3 package
    Connect to the database by using the .Database(PATH) command from the sqlite3 package.
    Implement our typical REST API setup (import express, set the port, etc.)
- To get data for our users we need to use the SQL SELECT statement to retrieve data from the database.
- We can add this to our GET /api/products route and instead of printing to the console, we can use res.json() to return a JSON object of the data.
- The WHERE clause can be added to the SELECT statement along with the specified id.
- The ? acts as a placeholder and then the id is passed in as the next parameter (1 in this case). In the above example the id is hardcoded, but we can also pass a variable with the id that we extract from the query parameters from the GET /api/products/:id endpoint. Once the data is retrieved, we can return a JSON object with the requested information.
- The INSERT command can be used to add new records to your database. Previously, you used this command to seed your database with some initial data. We again can use the ? placeholder to safely insert data and have a placeholder for data that we will eventually extract from the body of the request.
- However, the data we want to insert will be in the body of the request, so the POST /api/products endpoint can be updated to insert the information from the body into the row in our table.
- If you send a request to the POST /api/products endpoint using Postman, you should see the ID of the new item returned. Additionally, you can visit the endpoint for the ID that was just created and it will query the new value in your database. Even if you shutdown the server and restart it, this value will still be present because it has been persisted in your database!
- The UPDATE command allows us to run a SQL query to modify data.
- As with POST requests, we don’t want to hardcode the information that we are going to update, but rather we need to extract it from the body of the request. Thus, we can extract the data and update the record in a nearly similar way as INSERT, but instead use the UPDATE command. Additionally, we add the WHERE command to filter for the record that we are looking for.
- We can then run a request to the PUT /api/products/:id endpoint to update the value for the specified id.
- This can be done using the DELETE FROM command. Typically, we will also use a WHERE clause to specify the unique id for the item that we want to delete.
- We can use this with our DELETE /api/products/:id endpoint and instead of hardcoding the id, we can pass the value that is pulled from the query parameters.
- You now have a complete data tier that can permanently store product information and serve it through Express API endpoints. This is the same pattern that powers Amazon's product catalog, inventory systems, and countless other applications that need to store and retrieve data reliably.