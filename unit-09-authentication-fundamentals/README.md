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
- Presenting your ID is authentication: This verifies you are who you say you are
  The key card represents your session: Proof that you've already been authenticated
  Room access is authorization: The key card only works for YOUR room, not other rooms
- Applications authenticate when our username and password match the stored values, then provide us with a session or token that allows us to repeatedly access the API without having to login again.
- Authentication is the process of verifying the identity of a user or system attempting to access an application. This prevents unauthorized access and protects sensitive resources.
- When someone tries to access the library system they go through the following login flow:
    They provide their email and password
    The system checks if these credentials match a registered user
    If they match, the person is authenticated because we know who they are
    If they don't match, access is denied
- Authorization determines what actions or resources a user or system can access after successful authentication. This ensures that authenticated entities can only perform actions within their defined boundaries.
-  For example, an authorized library patron should be able to view books, check out books for themselves, and view their own checkout history. However, they should NOT be able to add books to the catalog or view other patron’s information. By contrast, a library staff has authorization to add new books and view all patrons.
- Authentication and authorization work together to create secure applications:
    Authentication without authorization is like having a hotel key card that opens every room . You know who someone is, but they have too much access.
    Authorization without authentication is like having room numbers written on paper. You know what someone should access, but you don't know if they're actually allowed to.
    Both together create the hotel system we're familiar with and you are able to verify identity first, then grant appropriate access.
- Authentication Endpoints:
    POST /api/login → "Prove who you are with email/password"
    POST /api/logout → "End your authenticated session"
- Authorization Endpoints:
    GET /api/profile → "You can only see YOUR profile" (patron authorization)
    GET /api/users → "Only admins can see all users" (admin authorization)
    POST /api/books → "Only staff can add books" (staff authorization)
- Understanding the distinction between "Who are you?" (authentication) and "What can you do?" (authorization) is fundamental to building secure applications.

## Password Hashing and Security
- An email with an associated password is a very common way to authenticate that users can gain access. Obviously, this information needs to be saved in a database.
- The above stable would be a security disaster for a number of reasons:
    Database breaches: If someone gains access to our database, they can see everyone's passwords
    Insider threats: Anyone with database access (developers, administrators) can see user passwords
    Password reuse: Many users use the same password across multiple sites, so a breach affects more than just our library
- Securely storing information is of paramount importance whenever you need to store sensitive information like user passwords for authenticated systems.
- Password hashing is the process of converting a plain text password into a fixed-length string of characters that appears random. This process is one-way. This means you can turn a password into a hash, but you cannot turn a hash back into the original password.
- The hash looks like random characters, but it's actually a mathematical representation of the original password.
- When a user wishes to register a password, they use the following process:
    User registers: They provide a password like "mypassword123"
    System hashes: The password is converted to a hash using a hashing algorithm
    Store hash: Only the hash is saved to the database, never the original password
    Discard original: The original password is never stored anywhere
- Once they are registered, they then can use the following login process:
    User logs in: They enter their password "mypassword123"
    System hashes input: The entered password is hashed using the same algorithm
    Hashes are compared: The new hash is compared to the stored hash
    Grant access: If the hashes match, the user is authenticated!
- The system never needs to "decrypt" or "reverse" the stored hash. It just hashes the login attempt and compares the results.
- This library uses the bcrypt algorithm, which is specifically designed for password security.
- The first is bcyrpt.hash(password, salt), which converts a plain password to a secure hash. You provide the plaintext password and the number of rounds of salting. Salt is a randomly generated string that is added to a password before hashing so that even if two users have the same password they won’t be the same prior to hashing.
- The hashed version can now be stored in the database and you NEVER the original password. Additionally, you don’t return the password to the user.
- Once the user is registered, we can then use the bcyrpt.compare(password, hash) method to determine if the provided user password matches the hashed password that we have stored in our database. This function returns a boolean value verifying whether the two passwords match.
- Thus, we never need to "decrypt" the stored hash. Rather, we can simply compare the plaintext version of a password provided by the user and the hashed version.
- We then can create a POST api/register endpoint where the user provides their name, email, and password in the body of the request. When the request is sent we need to do four things:
    Check if the user already exists and prevent registration if they do
    Hash the password
    Create a new user with their name, email, and HASHED password
    Return a response to the user indicating success, but never giving them information about the password.
- Once a user has been created, they need to be able to login using the POST api/login endpoint. The user will send their email and password in the body of the request and then the endpoint should do the following:
    Check if the user exists and return a message if they do not
    Compare the provided password with the hashed password in the database
    If the password is correct, return a message informing them that they have logged in. If the password is incorrect, return a failed login message.

## Session Management and Middleware
- When a user logs into your system, how does the server remember that they're authenticated as they browse different pages or make API calls? They’ve provided a password, but now they need a “key” to get access to systems they are allowed to access. Enter session management. Without session management, users would need to send their username and password with every single request, which is both insecure and impractical.
- Session management solves this by creating a temporary "session" after successful login. 
- In web applications, sessions work by:
    Server creates a unique session ID after successful login
    Session ID is stored in a cookie on the user's browser
    User's browser automatically sends this cookie with subsequent requests
    Server validates the session ID to confirm the user is authenticated
- Express-session is a middleware package that handles session management in Express applications. It automatically:
    Creates unique session IDs
    Stores session data (like user information)
    Manages session cookies
    Handles session expiration
- The session command has a few important configurations:
    secret: This is a string used to cryptographically sign the session ID cookie, which prevents the user from tampering with it.
    resave: Prevents the session from being saved back to the session store if it is modified during the request.
    saveUninitialzied: Prevents empty sessions from being created
    Cookie: Set to false since we are using an HTTP server for development. Will need to be true in production once we use HTTPS. We also define the length of the session in milliseconds (24 hours in the above example).
- We need to create a session when a user successfully logs in. Our current POST api/login endpoint successfully validates credentials, but doesn't create a session. We can update the endpoint to create a session with the necessary session values. 
- Now that the session has been created, we need to use middleware to check if a user is logged in before allowing access to protected routes. Thus, we can define a middleware function to perform this check
- This function does a few important things:
    It checks if the session exists and if the userID is stored in the session. This is verifying that the user has been authenticated (i.e. they are logged in).
    If they are authenticated, we attach their information to req.user so route handlers can access it.
    If they are not authenticated, we return an error message informing them that they have not been authenticated to access this endpoint.
- We can now protect any necessary routes that require a login by simply adding the authentication middleware as a parameter to the route handler.
- The middleware will run every time someone attempts to access this endpoint and will only return the information if they are logged in.
- Our users also need to be able to end a session. We can define a POST api/logout endpoint that will end a session. To end a session, we use the req.session.destroy() method.
- If you start your server and try to visit the GET api/books endpoint without logging in, you’ll get an authentication error.
- You must now first login with a username and password that is in the database via the POST api/login endpoint.
- Once logged in, you then can go to the GET api/books endpoint, you’ll now see all the books!
- You can also logout via the POST api/logout endpoint. If you try to get the books after logging out, you again will be denied access.
- Because these endpoints require authentication, you will not be able to visit them in your browser even if you logged in via Postman. You will only be able to access these endpoints via Postman.
- Session management and middleware are fundamental concepts that make your library system secure and user-friendly. Users can log in once and access protected resources without re-entering credentials for every request.