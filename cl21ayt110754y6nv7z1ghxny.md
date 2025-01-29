---
title: "Error Handling In NodeTS"
seoTitle: "Error Handling In NodeTs"
seoDescription: "What Is Error Handling
when you deploy your application to production, you need to make sure that your application is well tested for all possible scenarios"
datePublished: Sat Apr 16 2022 03:31:36 GMT+0000 (Coordinated Universal Time)
cuid: cl21ayt110754y6nv7z1ghxny
slug: error-handling-in-nodejs
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1649224181633/tdLHm7n1c.png
tags: express, nodejs, error-handling, typescript, mern

---

In your development process, you need to make debugging as easy as possible, you definitely don't want to see an `uncaughtException` that could crash your application. 

# What Is Error Handling
when you deploy your application to production, you need to make sure that your application is well tested for all possible scenarios. but you might heard before that handling errors in the world of **NodeJS**, could be a pain in the a**, I won't lie, it is already not that easy, however, it's not that hard too

all you need to do is prepare your application to properly handle your errors, and this is what we're gonna do in this article.

# Error Object

as [documentation](https://nodejs.dev/learn/error-handling-in-nodejs/#error-objects) says: An error object is an object that is either an instance of the Error object or extends the Error class, provided in the Error core module.

here are some examples:
```typescript
// throw an instance of the Error object
throw new Error('An error occurred');
```
or, with a custom error class:
```typescript
class CustomErrorHandler extends Error {
// ...
}

throw new CustomErrorHandler('Custom Error ...');
``` 

# How to handle errors in our application
you can simply use `try/catch` blocks, any raised exception within `try` block, is handled in the corresponding `catch` block.

```typescript
try {
// Some logic
} catch (error) {
// Handle Error
 throw new Error('An error occurred');
}
```

if you're using `express`, you can easily catch and process raised errors by creating a custom error handler as follows: `file: index.ts`

```typescript
import express, { NextFunction, Request, Response } from 'express';
import cors from 'cors';

const app = express();
app.use(cors());

app.post('/write-test', (req: Request, res: Response) => {
    try {
        fs.writeFileSync("programming.txt", undefinedvarible); // this code should raise an exception
    } catch (error) {
        // Handle Error
        throw new Error('An error occurred');
    }
});

// Last middleware
app.use((err:  Error, req: Request, res: Response, next: NextFunction) => {
 return res.status(400).send('An error occurred');
})

app.listen(5000, () => console.log('Up & Running'));
``` 

> note: your custom error handler should be the last middleware in your application.

But here's the thing, now you should add `try/catch` blocks in every action method, so you can catch the errors with the error handler that we just created.
this is horrible right? we should find another way that could save us a lot of time during our development process.

# Use higher-order function (Curring)

a higher-order function is a function that accepts an array as a parameter and/or returns a function.

> you can read more about higher-order functions in this [article](https://www.freecodecamp.org/news/a-quick-intro-to-higher-order-functions-in-javascript-1a014f89c6b/)

now, we can create one that returns a promise to resolve all our action methods and catch any possible errors.

here's an example:

```typescript
const req = (fn: any) => (req: Request, res: Response, next: NextFunction) => {
    return Promise.resolve(fn(req, res, next)).catch(next);
}
```

as you can see, this is a middleware that takes a single parameter `fn`, (which is our action method) and returns a promise.

now we can use this middleware to wrap all our methods:

```typescript
import express, { NextFunction, Request, Response } from 'express';
import cors from 'cors';
import fs from 'fs';

const app = express();
app.use(cors());

// here's our middleware that will check all our methods for us
const req = (fn: any) => (req: Request, res: Response, next: NextFunction) => {
    return Promise.resolve(fn(req, res, next)).catch(next);
}

/**
 * as you can see here, we're calling our middleware and pass 
 * our action method as a parameter
 */
app.post('write-test', req((req: Request, res: Response) => {
    fs.writeFileSync("programming.txt", undefinedvarible); // this code should raise an exception
}));

// Last middleware
app.use((err:  Error, req: Request, res: Response, next: NextFunction) => {
     return res.status(400).send('An error occurred');
})

app.listen(5000, () => console.log('Up & Running'));
``` 

here's the result:

![Screenshot from 2022-04-17 01-18-46.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1650151204150/T-tffWpGh.png)

now our code is much cleaner, and we no longer need to worry about handling errors, this `req` middleware is going to handle everything for us.

but we still have a little problem here!. as you can see in our result, the result will always be the same. and we can't return the same message for each exception, this doesn't tell us a lot about the exception reason or what we have to change.
as an example let's create `404` error for not found routes: `File: NotfoundError.ts`

```typescript
class NotFoundError extends Error  {
	statusCode = 404; // Not found
    message: string;

	constructor(message: string = 'Not Found') {
        super();
        this.message = message;
	}
}
export default NotFoundError;
```

now we can create a route with wildcard `*` at the end of our routes to catch all not found routes:

```typescript
app.use('*', (req: Request, res: Response, next: NextFunction) => {
    throw new NotFoundError();
});
```

as you can see, now we're throwing our custom `NotFoundError` error, this will give us much more flexibility. but now let's edit our error handler, in order to send the particular message & Status error code  for our exception

```typescript
// Error Handler
app.use((err:  NotFoundError, req: Request, res: Response, next: NextFunction) => {
    return res.status(error.statusCode).send(err.message);
});
```

***result: ***
![Screenshot from 2022-04-17 01-35-30.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1650152180929/uBqy4eBrb.png)

the response here is much cleaner and tells us what's going on, we also can specify the error code for each kind of error.

as you might notice in our error handler, we still have a little issue that we can't send any other error types, because we define the type of our error to be `NotFoundError` only, in order to solve this we need to create an abstract class that all of our error classes should inherit from.

```typescript
abstract class BaseError extends Error {
	abstract statusCode: number;
	
	constructor(public message: string = "An error occurred") {
		super();
	}
}

class NotFoundError extends BaseError {
	statusCode = 404; // Not found
    message: string;

	constructor(message: string = 'Not Found') {
        super(message);
        this.message = message;
	}
} 
```

we should also modify our error handler middleware as follows:

```typescript
// Error Handler
app.use((err:  Error, req: Request, res: Response, next: NextFunction) => {

    /**
     * Here we're checking if the raised error is one of our custom errors,
     * so we can send the particular message & statusCode
     */
    if (err instanceof BaseError) {
        return res.status(error.statusCode).send(error.message);
    }
    
    // Otherwise, we respond with generic response message.
    return res.status(500).send('Something went wrong');
});
```

# Conclusion
handling your errors in a smarter way can save you from repeating yourself, and you'll also find yourself more comfortable by using this approach. writing `try/catch` block in every method was a hassle for me, so I think this way is a time saver.

in the end, I hope this article was useful for you, and you got something out of it.
if you have any questions, feel free to write them in the comments section so we can discuss them furthermore.
you can also reach me on my Twitter account: [@thefeqy](https://twitter.com/thefeqy)