## Unit 6
Learning about Middleware, request logging, input validation, and making documentation with Postman.

## Middleware
- Middleware functions are the "layers" of processing that happen between receiving a request and sending a response. 
- Think of middleware like a security checkpoint at an airport. Every passenger (request) goes through the same sequence of checks (middleware functions) before reaching their destination (route handler)
- Middleware is a function that sits between the incoming request and the outgoing response. It has access to:
    The request object (req): Contains information about the incoming request
    The response object (res): Allows you to send a response back
    The next function: Passes control to the next middleware in line
- Every middleware function can do one of three things:
    Process the request and pass it to the next middleware
    Send a response and end the request-response cycle
    Pass an error to error handling middleware
- The app.use(express.json()) is middleware that processes the incoming request and parses JSON data from the request body before your route handle receives it
- Imagine a task management API where someone wants to create a new task. The request might flow through:
    Logging middleware: Records that a request was made
    Validation middleware: Checks if the task data is valid
    Route handler: Actually creates the task
    Response: Sends back the created task
- If any middleware finds a problem (like invalid data), it can stop the process and send an error response immediately.
- Middleware functions execute in the order they're added to the application.
- In order to properly implement middleware, this means you need to:
    Parse JSON bodies first
    Log the request (now you can see the parsed data)
    Validate the data (now you can check the parsed data)
    Handle the route (now you know the data is valid)
- If you try to validate data before parsing the JSON, the validation will fail because the data hasn't been parsed yet. If you try to log the request before parsing, you won't be able to see the request body content. Thus, one of the most common errors you will encounter when building and using middleware is ensuring the execution order is done correctly.
- Application-level middleware	Applied to all routes	Logging every request that comes to your task management API
    Router-level middleware	Applied to specific routes	Only validating data for routes that create or update tasks
    Built-in middleware	Express provides some middleware	Parsing JSON request bodies, serving static files
    Custom middleware	Middleware you write yourself	Checking if a user is authorized to access their tasks
- It allows you to create a consistent, organized request processing pipeline that handles logging, validation, authentication, and error handling efficiently across your entire API.

## Request Logging
- Request logging middleware solves this problem by automatically capturing information about every request that comes to your API. Instead of manually adding logging code to each route, you write the logging logic once and apply it to all routes.
- With proper request logging middleware, you get automatic visibility into all API activity. Request logging ensures that you can get a full picture of the data that is flowing into and out of your application as well as efficiently troubleshoot errors when bugs arise.
- We need to implement middleware to ensure that the API:
    Logs who made the request,
    Checks if the data they sent is valid
    Handles any errors that occur.
- We will start by creating a middleware function called requestLogger that will log every request that is made and, when applicable, the information that was sent in the body of the request. 
- Every middleware must call next() to pass control to the next middleware. If you forget this, your request will hang and never reach the route handler.
- Remember, this will need to be created above where you call this middleware
- This function looks different than the ones you have written in the past because it uses an ES6 arrow function. This is the more modern way to write the function.
- Once the requestLogger function has been created, you need to call app.use() in order to add the middleware to your application. You can add this directly after app.use(express.json()).
- The logging middleware comes after express.json() because we want to log the parsed request body, not the raw data.
- As you can see, your todo API automatically logs every request, giving you visibility into how your API is being used. This logging will be invaluable for debugging issues and understanding user behavior
- When someone makes a request to your API, here's what happens:
    The request arrives at your Express server
    express.json() middleware parses JSON from request body
    requestLogger middleware logs the request information
    Route handler processes the request and sends response
- The middleware executes in this order because that's how you defined it with app.use(). Middleware runs in the order you add it to your application. Logging comes after JSON parsing so we can see the parsed request body.

## Input Validation
- Input validation middleware is a vital security practice and solves this problem by checking incoming data before it reaches your route handlers. Instead of writing validation logic in every route, you create middleware that ensures only valid, clean data gets processed and returns an error message to the user if the data doesn’t conform to the required validation rules.
- Without validation, users can send:
    Empty or missing required fields
    Wrong data types (strings instead of numbers)
    Malicious data that could break your system
    Unexpected fields that don't belong
- Validation middleware acts as a gatekeeper, ensuring only valid data reaches your server.
- When validation fails, your API needs to communicate what went wrong using proper HTTP status codes.
- 200 OK	Successful GET requests	Successfully retrieved todos
    201 Created	Successful POST requests	Successfully created new todo
    400 Bad Request	Validation errors, malformed data	Missing required field, invalid data type
    404 Not Found	Resource doesn't exist	Todo with ID 999 not found
    500 Internal Server Error	Server problems	Database connection failed
- One lightweight and popular package is express-validator. We are going to implement input validation using the express-validator package. These are checks that will run whenever a PUT or POST request is executed as these are the two methods for this API that require the application to interact with data that has been sent by the user.
- Express Validator works by creating an array of validation rules that check each field in your request body. Each rule specifies what field to validate, what the requirement is, and what error message to show if validation fails. 
- You can add as many validation rules to the array as necessary. For example, you may also want to validate that the priority is either “high”, “medium”, or “low”. This can be done by adding a second validation check to the array.
- For our Todo API, we want to implement the following validation rules:
    task: Must be at least 3 characters (prevents empty tasks)
    description: Must be at least 10 characters (ensures meaningful descriptions)
    priority: Must be exactly "low", "medium", or "high" (prevents invalid priorities)
    dueDate: Must be a valid ISO date format (YYYY-MM-DD)
    tags: Must be an array with at least one tag (prevents empty tag arrays)
    completed: Optional field, but if provided must be boolean
- In order to use the validation rules that we defined in the previous step, we need to create a function that checks if any errors are found and return an error message with a 400 status code.
- You’ll notice that we also set optional values if they were not provided. 
- Since PUT and POST routes are the endpoints that interact with information that is sent in the request body, we need to update each of these routes to use the validation that we constructed.
- The parameters for each route are updated to accept the validation array that was created and calls the error handling middleware. No other updates are needed for the routes and each route uses the exact same validation method. Thus, we have ensured that our code is DRY (don’t repeat yourself) and have effectively validated all of the information that a user will be sending the body of the request.
- We can then use Postman to test that the PUT and POST routes send proper error messages when the data does not conform to the defined rules. Your todo API now rejects invalid data and provides helpful error messages, making it both secure and user-friendly.
- When someone makes a POST or PUT request, here's the execution order:
    express.json(): Parses JSON request body
    requestLogger: Logs the request
    todoValidation: Validates the data using express-validator rules
    handleValidationErrors: Checks validation results and handles errors
    Route handler: Creates the todo only if the validation passes
- If validation fails at step 4, the middleware sends a 400 response and stops execution and the route handler never runs. This ensures that malicious or improperly formatted data never reaches the route handler and an error is thrown before it can be utilized by the system, which helps to promote a secure API.

## Documentation with Postman
- Well organized and consistent documentation is vital for any API that you create. Writing the code for an API, like your Todo API, is only half the battle. Without documentation, other developers (including your future self!) will struggle to use it effectively.
- Postman is also a powerful documentation tool used by professional development teams. With just a few clicks, you can build documentation as you test your API endpoints. Additionally, it will automatically convert your documentation to numerous languages so that developers can use your API regardless of the language that they are coding in.
- You can document your endpoints, by sending requests in Postman.
- The most common error is forgetting to save your requests. You will see an orange dot on the tab if it hasn’t been saved. Your documentation will not update until you save!
- Often for changes to be picked up by the documentation, you need to close the tab and reopen it.