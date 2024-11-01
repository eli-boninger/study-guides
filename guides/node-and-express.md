# Node & Express interview prep

## Node

Node is server-side JS runtime environment, running the Google Chrome V8 JavaScript engine outside of the browser.

Node is single-threaded. Node libraries are generally, in line with this, written using non-blocking paradigms. When Node.js performs an I/O operation, like reading from the network, accessing a database or the filesystem, instead of blocking the thread and wasting CPU cycles waiting, Node.js will resume the operations when the response comes back. Calling any synchronous functions will block not just the current request but every other request to the application.

A very basic node server:

```
const http = require('http')

const hostname = '127.0.0.1'
const port = 3000

// createServer makes the server and returns it
// req provides request details, res does the same for the response, to return data to the caller
const server = http.createServer((req, res) => {
  res.statusCode = 200
  res.setHeader('Content-Type', 'text/plain')
  res.end('Hello World\n')
})

// listen on the specified port and hostname, and log a statement when the server is up and running
server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`)
})
```

The above code uses the node `http` module.

### Differences between node and the browser

In the browser, most programmatic interactions are with the DOM, the web platform API, cookies, or other things of that nature. In Node,
there is no `document` or `window` or browser-provided functionality.
Node instead has its own modules that provide things such as file system access.

In Node, you can control the environment, unlike building code that must work in a variety of browsers and browser versions. This means there is no need for transpilation or use of old ECMAScript functionality - your JS used is limited only by the version of Node you are on.

### Some core features

#### V8

V8 parses and executes the JS for both Google Chrome, and Node. A browser's JS engine is independent of the browser itself.

V8 internally compiles JS with just-in-time (JIT) compilation.

#### npm

The standard package manager for Node. Though it began as a package manager only for node, it now is used for frontend JS as well.

#### Dev and prod environments

Node always assumes a dev environment by default. Setting the `NODE_ENV` environment variable to `"production"` will change this behavior.

The production environment generally ensures that logging is kept to a minimum, essential level and that more caching takes place to optimize performance.

Node can conditionally execute code based on the environment in which it is running:

```
if (process.env.NODE_ENV === 'development') {
  // ...
}

if (process.env.NODE_ENV === 'production') {
  // ...
}

if (['production', 'staging'].includes(process.env.NODE_ENV)) {
  // ...
}
```

#### The Node event loop

Simplified order of operations:

```
   ┌───────────────────────────┐
┌─>│           timers          │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │
│  └─────────────┬─────────────┘      ┌───────────────┐
│  ┌─────────────┴─────────────┐      │   incoming:   │
│  │           poll            │<─────┤  connections, │
│  └─────────────┬─────────────┘      │   data, etc.  │
│  ┌─────────────┴─────────────┐      └───────────────┘
│  │           check           │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │
   └───────────────────────────┘
```

## Express

### The basics

Express is an unopinionated (and the most popular) web framework hosted in the Node runtime. Using express, you can:

- Write handlers for requests with different HTTP verbs at different routes
- Integrate with "view" rendering engines
- Add middleware to the request pipeline

A very basic Express server:

```
const express = require("express");
const app = express();
const port = 3000;

// .get can be replaced with any HTTP verb (post, put, etc...)
app.get("/", function (req, res) {
  res.send("Hello World!");
  // other methods here are available such as res.json() and res.sendFile()
});

app.listen(port, function () {
  console.log(`Example app listening on port ${port}!`);
});
```

#### Express generator

Express generator is a CLI tool to quickly create an Express application skeleton.
For example, `npx express-generator --view=pug myapp` creates an app name "myapp" with the view engine set to Pug.

### Express routing

A route definition uses the following structure:

```
app.METHOD(PATH, HANDLER)
```

in which app is an express instance, METHOD is an HTTP verb, PATH is the uri path, and HANDLER is the function execute when the route is matched.

The handler can be a single callback, but it could also be an array of callbacks (in order) or some combination of the two, as long as those callbacks at the beginning of the chain receive call the `next` argument.

The `app.all()` method will match any HTTP request and is generally used for loading middleware for all verbs at a particular path:

```
app.all("/secret", function (req, res, next) {
  console.log("Accessing the secret section…");
  next(); // pass control to the next handler
});
```

For routes that all share a common prefix (eg. `/wiki`), a separate route module can be created, to reduce clutter and encourage modularization.

```
// wiki.js - Wiki route module

const express = require("express");
const router = express.Router();

// Home page route
router.get("/", function (req, res) {
  res.send("Wiki home page");
});

// About page route
router.get("/about", function (req, res) {
  res.send("About this wiki");
});

module.exports = router;
```

And then to use that module in the main server file:

```
const wiki = require("./wiki.js");
// …
app.use("/wiki", wiki);
```

Similarly, if I have one route upon which I'd like to define callbacks for several HTTP verbs, I can set that up as follows:

```
app.route('/book')
  .get((req, res) => {
    res.send('Get a random book')
  })
  .post((req, res) => {
    res.send('Add a book')
  })
  .put((req, res) => {
    res.send('Update the book')
  })
```

#### Route parameters

We can define route parameters in route like so:

```
app.get('/users/:userId/books/:bookId, (req, res) => {
    // req.params.bookId and req.params.userId can now be accessed here.
})
```

#### Response methods

In addition to `res.send()` which can send a response of various types, the following response methods are available:

- `res.download()` - prompt the download of a file
- `res.end()` - end the response process
- `res.json()` - send a JSON response
- `res.jsonp()` - send a JSON response with JSONP support
- `res.redirect()` - redirect a request
- `res.render()` - render a view template
- `res.sendFile()` - send a file an octet stream
- `res.sendStatus()` - set the response status code and send its string representation as the response body

### Serving static files

An example call to serve static image, CSS files, and JS files in a directory named `public` is as follows:

```
app.use(express.static('public'))

// can now load fields in the public directory with urls such as below:
// http://localhost:3000/images/kitten.jpg
// http://localhost:3000/css/style.css
// http://localhost:3000/js/app.js
// http://localhost:3000/images/bg.png
// http://localhost:3000/hello.html
```

Multiple directories can be served by calling the middleware multiple times.

To create a virtual path prefix for static files, specify a mount path as the first argument:

```
app.use('/static', express.static('public'))
// can now load fields in the public directory with urls such as below:
// http://localhost:3000/static/images/kitten.jpg
// http://localhost:3000/static/css/style.css
// http://localhost:3000/static/js/app.js
// http://localhost:3000/static/images/bg.png
// http://localhost:3000/static/hello.html
```

The path provided to the `express.static` function is relative to the directory from which your `node` process is launched. Therefore it safer to use the absolute path of the directory:

```
const path = require('path')
app.use('/static', express.static(path.join(__dirname, 'public')))
```

### Middleware

Express middleware can:

- Execute any code
- Make changes to the request and response objects
- End the request-response cycle
- Call the next middleware in the stack

While route functions typically end the request-response cycle, middleware typically perform an operation on the request or response and then call the next function in the stack.

Setting up a middleware can be a simple as call `app.use` according to the middleware's specs:

```
const express = require("express");
const logger = require("morgan");
const app = express();
app.use(logger("dev"));
// …
```

Middleware and routing functions are called in the order in which they are declared. Middleware shoudl almost always be configured prior to routes.

Middleware functions, as opposed to route handler functions, receive a third argument, `next`, which they are expected to call if they do not complete the request cycle internally.

An example of a custom middleware function:

```
const express = require("express");
const app = express();

// An example middleware function
const a_middleware_function = function (req, res, next) {
  // Perform some operations
  next(); // Call next() so Express will call the next middleware function in the chain.
};

// Function added with use() for all routes and verbs
app.use(a_middleware_function);

// Function added with use() for a specific route
app.use("/someroute", a_middleware_function);

// A middleware function added for a specific HTTP verb and route
app.get("/", a_middleware_function);
```

Middleware can mutate the request or response objects:

```
const requestTime = function (req, res, next) {
  req.requestTime = Date.now()
  next()
}
```

There are five types of middleware.

#### Application-level middleware

This is the type of most the above examples configured via `app.use()` or `app.METHOD()`. The follow is an application-level middleware sub-stack that handles GET requests to the `/user/:id` path.

```
app.get('/user/:id', (req, res, next) => {
  console.log('ID:', req.params.id)
  next()
}, (req, res, next) => {
  res.send('User Info')
})

// handler for the /user/:id path, which prints the user ID
app.get('/user/:id', (req, res, next) => {
  res.send(req.params.id)
})
```

To short-circuit a sub-stack, call `next('route')` (only available in middleware functions loaded with `app.METHOD()` or `router.METHOD()`).

#### Router-level middleware

Works the same as application-level middleware but is bound to an instance of `express.Router()`. Similar to the above short-circuiting method, call `next('router')` to pass controll back out of the router instance.

#### Error-handling middleware

Define the same, but takes four arguments instead of three (`err`, `req`, `res`, `next`). More on error handling later.

#### Built-in middleware

Express has three built-in middleware functions:

- `express.static` - see above section on serving static files
- `express.json` - parses incoming requests with JSON payloads
- `express.urlencoded` - parses incoming requests with URL-encoded payloads.

#### Third-party middleware

Some middleware you got via your package manager. Under the hood, it is the same.

### Error handling

#### Catching errors

Errors occurring in synchronous code inside route handlers and middleware will be caught automatically. If an errors is received asynchronously, in a callback `err` object or otherwise, it must be passed to the `next()` function (`next(err)`) in order to be processed by Express:

```
app.get('/', (req, res, next) => {
  setTimeout(() => {
    try {
      throw new Error('BROKEN')
    } catch (err) {
      next(err)
    }
  }, 100)
})
```

Promises work well here to avoid the try/catch overhead.

As of Express 5, route handlers or middleware that return a Promise will call `next(value)` automatically when they reject or throw an error.

```
app.get('/user/:id', async (req, res, next) => {
  const user = await getUserById(req.params.id)
  res.send(user)
})
```

If the `getUserById` call throws an error or rejects, `next` will automatically be called either with the thrown error or reject value. Anything passed to `next` other than very specific keyword strings will be considered an error.

Express ships with a default error handler which adds the following to the response object:

- the `statusCode` is set from `err.status` or `err.statusCode`. If that is out of range or unavailable, a 500 is used
- the `statusMessage` is set according to the code
- the body will be either the HTML of the `statusMessage` (in prod) or `err.stack` in other envs.
- headers specified in `err.headers` will be added.

#### Writing your own error handlers

Error-handling functions are very similar to middleware but they take four arguments instead of three: (`err`, `req`, `res`, `next`).

```
app.use((err, req, res, next) => {
  console.error(err.stack)
  res.status(500).send('Something broke!')
})
```

Any number of error handlers is allowed in the request-response pipeline. If not calling `next(err)` in an error handler, it is the handler's responsibilty to write and end the response.
