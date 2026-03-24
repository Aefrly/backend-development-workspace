# Unit 10
Learning about JSON web tokens or JWTs and implementing them into our authentication and role-based authorization. 

## JSON Web Tokens (JWTs)
- After a user logged in, two things happened:
    The server created a unique session ID and stored their user information (e.g. id, name, email, etc.) in the server's memory.
    The browser received this session ID in a cookie, and with every subsequent request, the server looked up the session data to verify who the user was.
- This worked great for traditional web apps, but creates problems when you need to:
    Scale across multiple servers -Each server has its own session storage
    Build mobile apps - Cookies don't work reliably
    Create microservices - Each service needs access to the same session store
- This approach works well for traditional web applications, however modern backend applications typically utilize a different tool: JSON Web Tokens (JWTs).
- JWTs implement stateless authentication. Instead of storing user information on the server, all user data is contained in the token itself. 
- Through use of stateless authentication, this means that any server can validate any token without looking anything up. Mobile apps can store and send tokens easily. Microservices can validate tokens independently.
- Most modern applications are built as APIs that serve multiple client types (web, mobile, desktop). JWTs work seamlessly with any client, while sessions are designed specifically for web browsers. This is why JWT authentication has become the industry standard for REST APIs, single-page applications, and microservices architectures.
- A JSON Web Token is a compact, URL-safe string that contains encoded user information. Unlike session IDs (which are just random strings), JWTs actually contain the user data needed for authentication. They're self-contained, meaning everything you need to know about the user is right there in the token.
- Notice it has three parts separated by dots (.). These dots separate the three components of a JWT: Header, Payload, and Signature:
    Header: Specifies the signing algorithm and confirms this is a JWT token.
    Payload: Contains the actual user information like user ID, name, and issued timestamp.
    Signature: Created using the header, payload, and a secret key. This prevents tampering because only the server with the secret key can create valid signatures. If you change any of the values in Secret you’ll see that the signature is no longer valid.
- Notice how the middle part of the token changes, but it will still be decoded so long as you have a valid secret key. Additionally, you can change the secret key and you’ll notice how the end of the token changes.
- The shift from sessions to JWTs represents a move from stateful to stateless authentication. With sessions, you were building a system that worked for one server serving web pages. With JWTs, you're building a system that can scale globally and work with any type of client application.
- Understanding JWT authentication is essential because it's what you'll encounter in most modern development roles. While sessions are simpler to understand initially, JWTs provide the flexibility and scalability that modern applications require

## Implementing JWT Authentication
- JWT authentication works differently from session authentication. Instead of storing user information on the server, all user data is embedded directly into a token that gets sent to the client. When the client makes requests, they send this token back, and the server verifies it contains valid, unmodified information.
- The process involves three main components:
    Token Generation: Created during login using jwt.sign() method.
    Token Verification: Done using middleware as part of the user request using jwt.verify() method.
    Token Usage: Grants access to protected routes.
- JWT tokens are signed with a secret key that only your server knows. This secret prevents others from creating fake tokens.
- In production, the secret should be a long, random string, but we can use a simple string like the one above when in development. The expiration time determines how long tokens remain valid before users need to log in again.
- We now need to update the POST /api/login route to return a JWT to the user when they successfully login and create middleware that will determine if a user is able to access a route with a provided JWT.
- In session-based authentication, the server stores user information and gives the client a session ID. This approach stored user information in req.session, which lived in server memory. With JWT authentication, instead of storing user data on the server, we embed it directly into a signed token.
- To use JWTs, we need to
    Import the jsonwebtoken package at the top of the file.
    Remove the express session authentication and replace it with JWT that uses the .sign() method to create the JWT token containing user data
- The jwt.sign() function takes three parameters:
    The user data to embed (payload)
    The secret key for signing
    Options like expiration time.
- The resulting token contains all the user information needed for authentication. When you send valid login information to the POST /api/login endpoint, you’ll now see that a token has been returned to you.
- Session middleware checks if a session exists and looks up user data. JWT middleware works differently because it:
    Extracts the token from the request
    Verifies that the token is valid using the jwt.verify() method.
    Decodes the user information.
- The middleware expects tokens to be sent in the Authorization header with the format Bearer <token>. The jwt.verify() function checks the token's signature and expiration, then returns the decoded user data.
- In the login flow, we were sent a token that can be sent in order to gain access to an endpoint. Unlike sessions that use cookies automatically, JWT tokens must be explicitly sent by the client. You can send this in Postman by:
    Clicking on the Headers tab.
    Naming a Key of Authorization
    Typing the value of Bearer <token>. The word bearer is required.
    Checking of the checkbox to indicate you want it included in the parameters.
- The fundamental difference between sessions and JSON Web Tokens is where user information lives. Sessions store data on the server and give clients a reference (session ID). JWTs store data in the token itself, which clients hold and send back.
- Because of this structure, this makes JWTs stateless due to the fact that the server doesn't need to remember anything about logged-in users. Every request contains all the information needed for authentication.

## Role-Based Authorization
- Without role-based authorization, you'd need separate login systems for each user type, or you'd have to build complex permission checks into every single endpoint. Role-based authorization solves this by creating a hierarchy of permissions that scales naturally as your application grows.
- We need to add roles to our blog database so that users are assigned a role when they are created. First, you need to add a role field to your User model. This field will store each user's permission level in database/setup.js
- The role field uses the isIn validation method to ensure only valid roles can be stored. The default value of 'reader' gives new users the lowest permission level.
- Once the model has been reconfigured, we can update the database/setup.js file to assign roles to the sample users.
- Now that you have configured your database to have roles, you need to include the role in the JSON Web Token that you created as well.
- Now when users log in, their role information is embedded in the JWT token and will be available in the decoded token data.
- Now that roles have been defined, we need to create middleware functions that check for specific roles. These work in addition to your existing authentication middleware. For this example, we’ll create two middleware functions:
    requireAuthor: Grants access to an individual that has either a role of author or editor.
    requireEditor: Grants access to an individual only if they have a role of editor.
- These middleware functions create a hierarchy where editors can do everything authors can do, and authors can do everything readers can do. An error message is returned only if they have the proper role in the header of their request.
- These middleware functions can then be invoked to protect the routes that should only be accessible to authors and/or editors.
- For actions where users should only affect their own content, combine role checks with ownership verification. This allows authors to edit their own posts while letting editors edit any post.
- Being authors cannot edit everyone, but instead can only edit posts that have been authored by themselves. 
- You can now test these endpoints by:
    Logging in as a user and obtaining a token in the response
    Testing routes that you can and cannot access based on the user's role (e.g. the user reader@example.com should be denied access to the GET /api/users endpoint)
    Verifying that the error responses map to the correct issue.
- Role-based authorization is essential for any application with multiple user types because it ensures users can only access features appropriate to their permission level while maintaining security and proper access control.