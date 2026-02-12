## Unit 5
Learning about Request/Response Models, HTTP verbs, Express.js and REST APIs and how to make them, sending API requests, and testing APIs.

## Request/Response Model and HTTP Verbs
- This data is obtained through the use of a Representational State Transfer Application Programming Interface (REST API). A REST API is a set of rules and protocols that allow different software applications to communicate and interact with each other.
- The app will send a GET request to a separate application that contains current weather data. The mobile application interacts with the weather data using an API.
- The weather data API sends back a response as a JSON object. The weather app then can display this data to us.
- Hypertext Transfer Protocol (HTTP) is the protocol that is used to transfer information on the web. Whenever you want to get a website on your web browser, you send an HTTP GET request to get the information from a server. REST APIs use the same HTTP technology, but instead of sending back web pages, they send back data
- GET: Retrieving data from a server
    POST: Sending data to a server
    PUT: Modifying data on the server
    DELETE: Deleting data from the server
- In the weather app example, the data being returned in step 2 by the API is an example of JavaScript Object Notation or JSON.
- JSON, along with eXtensible Markup Language (XML), are two commonly used data interchange formats in the field of programming and web development. These data formats are designed to store and transport data. Additionally, JSON and XML are intended to be both human-readable and machine-readable.
- This is known as the request-response model. The request-response model forms the foundation for communication between different components, systems, and services.
- Both the request and response are sent using HTTP.
- Method: The operation the client wishes to perform like GET, POST, PUT, or DELETE.
    Path: The path to the resource that will be retrieved or updated.
    Version: Version of the HTTP protocol
    Host Header: Name of the host server
    Other Headers (optional): Extra information for the web server.
    Body (optional): Data that is sent to the server
- There are many different HTTP methods that can be used on an API endpoint, but there are four basic operations, known as CRUD operations, that a REST API allows
- CREATE → POST: Add new data to the server Example: POST /api/books (create a new book)
    READ → GET: Retrieve data from the server Example: GET /api/books (get all books) or GET /api/books/1 (get book with ID 1)
    UPDATE → PUT: Modify existing data on the server Example: PUT /api/books/1 (update book with ID 1)
    DELETE → DELETE: Remove data from the server Example: DELETE /api/books/1 (delete book with ID 1)
- The HTTP response is made up of the following parts:
    Version: Version of the HTTP protocol
    Status Code: With textual description of the status
    Header (optional): Name of the host server
    Body (optional): Data being sent back
- HTTP status codes are used to indicate whether or not an HTTP request was successfully completed.
- 200 OK: Your request was successful
    201 Created: You successfully created something new
    400 Bad Request: Something was wrong with your request, like missing information or incorrect formatting.
    401 Unauthorized: You need to be logged in to access this resource.
    403 Forbidden: You are not allowed to access this resource (even if you are logged in).
    404 Not Found: The server could not find your requested resource.

## Express.js and REST APIs
- This is where REST APIs and Express.js come into play. Express allows you to build server applications that can handle HTTP requests and send back data in a predictable, standardized way.
- Representational State Transfer, or as it’s more commonly called REST, is an architectural style that provides guidelines for creating web services. Think of REST as a set of "best practices" that make APIs predictable and easy to use.
- Stateless: Each request contains all the information needed to process it. The server doesn't remember previous requests.
    Uniform Interface: URLs and HTTP methods are used consistently across the API.
    Resource-Based: Everything is treated as a "resource" that can be accessed via a URL.
    HTTP Methods: Use standard HTTP methods (GET, POST, PUT, DELETE) for different operations.
- A RESTful movie API might look like:
    GET /movies - Get all movies
    GET /movies/1 - Get movie with an ID of 1
    POST /movies - Create a new movie
    PUT movies/1 - Update movie with an ID of 1
    DELETE movies/1 - Delete movie with an ID of 1
- The URLs are predictable and the HTTP methods tell you exactly what operation will happen. This consistency makes REST APIs easy to understand and use.
- Express.js is a web framework for Node.js that makes it simple to build web servers and APIs. While Node.js gives you the ability to run JavaScript on a server, Express.js provides the tools to handle HTTP requests, routing, and responses efficiently.
- Express.js handles the complex parts of web server development, like:
    Parsing incoming requests
    Routing requests to the right handlers
    Sending properly formatted responses
    Managing middleware for common tasks
- To create a basic server in Express, we need to:
    Import Express
    Create an Express application
    Define the port where the application will be hosted
    Define the middleware that will parse JSON requests
    Listen for the connection on the defined path
- While we have created our server, we haven’t defined how to handle requests like a GET request to the / endpoint. We can define how to handle a user requesting information from the server using a GET request for a specified endpoint.
- To create the /movies endpoint, we again can use the .get() method. Since this request is sending back all the information in our list of movies, we can simply send a response as json using the .json() method. 
- The .find() method can be used to search our list of movies for the movie that matches the requested ID. we can then return this movie or a 404 error if no movie is found
- This server follows REST principles with predictable URLs and appropriate HTTP methods. You are now ready to allow users to undertake the remaining CRUD actions to your API using POST, PUT, and DELETE requests.

## Sending API Requests with Postman
- Your browser can only send GET requests when you type in a URL. You need a tool that can send any type of HTTP request with custom headers, request bodies, and parameters.
- Postman is like a Swiss Army knife for API development because it allows you to send any type of HTTP request, inspect responses, save request collections, and even automate testing. Professional developers use tools like Postman daily to build, test, and debug APIs.
- Postman allows you to:
    Send any HTTP method (GET, POST, PUT, DELETE, etc.)
    Add custom headers and authentication
    Include request bodies with JSON, form data, or other formats
    Save and organize requests for repeated testing
    View detailed response information including status codes and headers
- APIs also need to handle creating, updating, and deleting data through POST, PUT, and DELETE requests. These methods require sending data in the request body, which Postman handles excellently.

## Making a REST API with Express.js
- In order to create a new resource, a POST request must be sent with the movie day in the request body. Using the .post() method in Express, we can configure the POST /movies endpoint.
- The above code does the following:
    It extracts the relevant information from the body of the request.
    It creates a new object from the data that was sent and generates a new ID based on the current size of the array.
    It then pushes the object to the movies array.
    Finally it returns a status code of 201 (Created) and the newly created movie as a response.
-  For example, a user may want to alter the genre of a movie. In order to do this, they need to send a PUT request. Using the .put() method in Express, we can configure the PUT /movies/id endpoint.
- The above code does the following:
    It extracts the id from endpoint using rq.params.id
    It extracts the movie information stored in the body of the request
    Using the ID in the request, it uses .findIndex to find the value in the index in the array that maps to the provided ID.
    If the method returns -1, then it indicates that the ID doesn’t exist so a 404 error is sent
    If the method returns a valid index, then the object is updated and the updated movie object is returned to the user.
- Users also want to be able to remove resources that are no longer relevant. This can be accomplished through the use of a DELETE request that specifies the item that should be removed. Using the .delete() method in Express, we can configure the DELETE /movies/id endpoint.
- The above code does the following:
    It extracts the id from endpoint using rq.params.id
    Similar to the PUT request, it uses the uses .findIndex to find the value in the index in the array that maps to the provided ID. If the method returns -1, then it indicates that the ID doesn’t exist so a 404 error is sent
    If the method returns a valid index, then it deletes the movie from the array using the .splice() method. It saves the deleted item in a variable so that it can be sent back in the response body.
    It then returns a response as JSON with a message and the movie that was deleted.
- Notice that your API follows RESTful patterns
    Resource-based URLs: /movies and /movies/:id
    HTTP Methods indicate action: GET (read), POST (create), PUT (update), DELETE (delete)
    Consistent responses: JSON format with appropriate status codes
    Stateless: Each request contains all needed information
- Create	POST	app.post()
  Read	    GET	    app.get()
  Update	PUT	    app.put()
  Delete	DELETE	app.delete()

## Testing APIs
- Supertest is a library specifically designed for testing HTTP endpoints. It integrates with Jest and allows you to send requests to your Express server and verify the responses without starting the server manually.
- Once installed, you can then begin setting up your testing file. Your server.js file needs two modifications to work with Supertest:
    1: The app needs to be exported
    2: You need to conditionally call the server so that app.listen() is not called when testing.
- The testing file will require that you import your Express app that you have built and supertest package
- You’ll notice that besides needing to require supertest, the setup is the same as any other Jest testing file. Supertest is utilized within the traditional Jest structure (describe, test, etc.) and is utilized simply to send HTTP requests. All tests that utilize supertest methods should be asynchronous as you will need to await the response of the HTTP request.
- Supertest will automatically handle starting and stopping your Express server for each test, making your tests isolated and reliable.
- The expect(response.status).toBe(200) extracts the status code from the response and verifies that it matches the expected status code of 200. The expect(response.body).toHaveLenth(4) checks that it is returning all the movies that are initialized when the server starts (i.e. the hard-coded movies array).
- The toHaveProperty() matcher checks that the response object has specific properties and optionally specific values. This is simply verifying that it is sending back a movie in the response.
- This will send the movie data in the body of the POST request and then check that the status and body in the response conform to our expectations.
- This is implemented nearly identically to our POST request test except we send a .put() request and check for a different status code. Additionally, we are only checking the updated value as that is the main functionality we need to verify for any PUT request.
- DELETE requests typically don't need request body data, so these tests tend to look fairly similar to how we test GET requests. We can use the .delete() method to verify the functionality of our DELETE /movies/id endpoint.