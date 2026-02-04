## Unit 4
Working with asynchronous JavaScript and reading different file types.

## Asynchronous JavaScript
- Synchronous - all tasks must be completed before we move to the next set of tasks
- Asynchronous - not sure which tasks will be completed first but can both be in transit simultaneously
- JavaScript is at its core a synchronous (single threaded) language. However, JavaScript can mimic asynchronous execution in certain situations using the event loop and task queue.
- If JavaScript were purely synchronous, your entire site would freeze until the data was returned. 
- Using asynchronous patterns like async/await or .then(), you can allow your code to continue running while waiting for the data and handle it as soon as it’s available.
- The call stack is a dynamic data structure in the runtime of JavaScript that organizes the execution of your code. It operates on a Last-In-First-Out principle.
- There are 4 main components interacting with one another:
    Call Stack: Executes synchronous code
    Web APIs: Handle timers, fetch, etc.
    Task Queue: Stores completed async callbacks
    Event Loop: Moves tasks from the queue to the stack
- Promises are JavaScript's way of handling asynchronous operations more cleanly. A promise represents a value that may not be available yet but will be resolved at some point in the future.
- While this is very useful, you can only use await inside a function that has been marked as async.

## Reading & Testing JSON Files
- In web development, you worked with JSON data that was already loaded in memory. In backend development, you need to read JSON files from your computer's file system, which requires asynchronous operations and proper error handling.
- Node.js provides the fs (file system) module which allows you to perform file operations like reading and writing to a file. This module is built into Node, so does not require additional installation. Suppose the example JSON file is saved in your directory as music-data.json, we can use the readFile() method from fs to read the data that is stored in this file.
- Since we imported the promise based version of this method, the recommended approach for modern applications, we can read the file using an asynchronous function.
- Reading the file gives you a string of JSON text. To use it as data in our JavaScript program, you can convert it using the JSON.parse() method.
- Now that you have converted the data to a JavaScript object, you can work with using dot notation and loops. We can also generalize the function to work for any provided file path.
- The beforeAll() method runs once before your tests start. It creates a file called test-music.json with some fake data (testData) that your readMusicFile function will read. Think of it as setting up the test environment so your function has something to work with.
- Once your tests are complete, you want to delete the file to ensure that your project folder remains clean. The afterAll() method runs after all your tests have finished.
- Since the function we are testing is asynchronous, the tests must also be asynchronous.
- The important thing to highlight about these tests is that you are testing the test file that you created NOT the actual music file. This ensures that you are testing program functionality and not one particular file, which ensures that you are constructing tests for the widest possible range of outputs and not simply tailored to one file.

## Reading & Testing CSV Files
- CSV = Comma-Separated Values
- Unlike JSON, which is already structured for JavaScript, CSV files need to be parsed and converted into usable data structures.
- While Node has built-in file reading capabilities with the fs module, it doesn't have native CSV parsing. The csv-parser package is a popular, lightweight library that converts CSV data into JavaScript objects.
- npm install csv-parser
- Since csv-parser uses event-based processing (not async/await), we wrap it in a Promise so that we can use it with async/await syntax.
- The fs.createReadStream(filepath) command creates a stream that reads the file piece by piece rather than loading the entire file into memory at once. This is more efficient for large CSV files.
- The .pipe() method connects the file stream to the csv-parser. The csv-parser converts each line of CSV text into a JavaScript object.
- The .on(‘data’) line listens for the ‘data’ event that is emitted every time csv-parser finishes reading a row. We then push each row to the array.
- The .on(‘end’) line listens for the ‘end’ event that is emitted when the file is finished being processed. Once the file has been read, we can resolve the Promise and return the array of data.
- The .on(‘error’) line handles any potential errors that may occur during processing and rejects the Promise in the event of an error.
- Notice that all values come back as strings, even numbers. You'll need to convert them when doing calculations. For example, if you iterate through the returned data, you will need to call parseInt() or parseFloat() to convert these values to ints or floats respectively.
- As with processing JSON files, you can use try/catch blocks to ensure that your application handles these problems gracefully
- Similar to the JSON tests that we wrote in the previous resource, these tests must also be asynchronous because they are calling an asynchronous function

## Environment Variables 
- Environment variables solve a critical problem in software development: how do you keep sensitive configuration data separate from your source code?
- This means that anyone could now access your database and make API calls on your behalf. You should NEVER hardcode sensitive data into your program because even small pieces of sensitive information can lead to catastrophic damage like what happened to Uber.
- Environment variables let you store this sensitive information outside your code
- In this example, the database password and api key are stored in a special file called .env that, if configured correctly, will not be committed to GitHub
- Environment variables are stored in a file called .env (there is no text before the . in this file) that typically lives in your root directory. This file contains key-value pairs in a simple format
- There is no space around the = sign and no quotes are needed around values. It is best practice to use uppercase with underscores for variable names and each variable needs to be on its own line.
- Node.js can read environment variables natively, but the dotenv package makes it much easier to manage them during development. 
- npm install dotenv
- The require('dotenv').config() line reads your .env file and makes all variables available through process.env. This must be the first line in your file, before any other imports or code
- To ensure that this file is never committed to GitHub, you can add a .gitignore file that lists the .env file as a file that git will ignore when tracking files in your repository. By using environment variables properly, you protect your applications from security breaches and make them more flexible and maintainable.