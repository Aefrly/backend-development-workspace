# Unit 3
Learning about Backend Development and working with Node.js and Jest.

## Backend Development
- Backend development is the invisible foundation that powers every modern web application
- It is the server-side programming that handles everything users don't see 
- Manages data storage and retrieval, business logic, security and authentication, communication, and file processing
- Can think of an application like a restaurant. The frontend is the dining room where customers sit and order. The backend is the kitchen, storage, accounting, and management systems that make the restaurant actually function. 
- We can't just use frontend for everything because browsers are not allowed to access files on your computer, connect directly to databases, send emails, make payments, or process large amounts of data efficiently
- Backend systems run on servers with full access to system resources, databases, and external services. They can handle complex processing, manage security, and serve multiple users simultaneously without the security restrictions that limit browser-based code.
- Popular backend languages include Python, JavaScript/Node.js, and Java
- Learning Node.js as your first backend language is strategic because it allows you to become a full-stack developer using one language across both frontend and backend. This makes you more versatile and valuable in the job market while building on the JavaScript foundation you already have.

## Node.js
- Node.js is a runtime environment that allows JavaScript to run outside of web browsers built on Chrome's V8 JavaScript engine
- Allows JavaScript to access and manipulate files on on your computer, connect to databases and external services, build web servers and APIs, process large amounts of data, and create command-line tools and desktop applications
- New to us will be accessing file systems and creating web servers
- The purpose of Node.js is to build server applications, have full file system access, connect to any database, create servers and APIs, but it does require separate installation 
- You'll use Node.js to process files, analyze data, and eventually build REST APIs.

## Programming with Node.js
- node filename.js - Run any JavaScript file
- cd foldername - Navigate to different folders
- ls (Mac/Linux) or dir (Windows) - See files in current folder
- pwd (Mac/Linux) or cd (Windows) - See current folder location
- All core JavaScript functionality (variables, functions, loops. etc.) are written exactly the same in Node as they are in browser based JavaScript
- Node.js uses modules to allow you to organize, manage, and separate functionality within an application. 

## File Operations with Node.js
- Node includes a built-in module called "fs" (file system) that gives you the power to read and write files on your computer
- This is something browser JavaScript can't do for security reasons. 
- The readFileSync() method returns a single string that contains all of your content. However, it is often easier to analyze the information as an array so the .split(‘\n’) method can be invoked to split the string into an array every time a line break is encountered. 
- Once the file data has been read, it is processed in the exact same way that we would process any other data in a JavaScript program
- Read File     fs.readFileSync(path, ‘utf8’)   Gets entire file content as text
- Split by Lines    content.split(‘/n’)	   Creates array with one line per element
- Split by Words	content.split(‘ ’)	Creates array with one word per element
- Covert to a Number	parseFloat(text)	Turns text like "42.5" into number 42.5
- Remove Whitespace     text.trim()     Removes spaces from beginning and end

## Testing with Jest
- Jest is a JavaScript testing framework that is utilized for unit testing web development projects.
- Each test follows the same pattern: A test() declaration describing what you are testing
Setting up your test data (e.g. “Hello world test”)
Calling the function you want to test.
Checking that result is what you expected using the expect() and toBe() methods.
- When you have multiple related tests, group them with describe()
- Good tests check multiple scenarios
- By considering edge cases and crafting unit tests for these cases, you are ensuring that your tests have full coverage of the inputs that your function will need to be able to handle
- test('description', () => {}) Creates a single test
- describe('group name', () => {}) Groups related tests
- expect(result).toBe(expected) Checks if values are exactly equal
- npm test Runs all tests in your project