# Pie API Walkthrough


### Folder Structure

In your blue badge folder, create a folder called `pie-project`.
We will end up having two folders in here, one for the client (next week) and one for the server.
Just create the `server` folder now.

```
pie-project
    server
```

## Intro to NPM

NPM is the most popular package manager for Node.js.
- A *package manager* in this sense is in effect a project manager
  - It will install your *dependencies* for you
  - These are packages that you import in your code to help you build your app
  - Your dependencies might have dependencies themselves
- By default, it installs packages *locally*, in the same folder that you build your project
  - This helps with making sure that you have a consistent set of versions for each package you need
- NPM also supports other features that will come in handy when we build an app, like scripts
- NPM is not the only package manager for JS, but we use NPM because it comes packaged with Node and is well-supported.


## First steps

### `package.json`

Head into the `server` folder if you're not there already and run `npm init`.
- This will guide you through creating a `package.json` configuration file
- For the first prompt, enter `pie-api` for the name
- Then go ahead and hit enter (i.e. accept the rest of the defaults) until it asks you to write the information to a file
- Then hit enter one more time to write the configuration settings to a file

Now open up Visual Studio Code and open the `server` folder.
You should see a `package.json` with the contents that we wrote just a minute ago.
Let's open that file.

To `"scripts"`, let's add a start and a dev script.
- This will allow us to run arbitrary commands by typing `npm start` and `npm run dev`
- Add a `"start"` script with the value of `"node index.js"`
- Add a `"dev"` script with the value of `"nodemon"`

Your `package.json` file should now look like this:

```json
{
  "name": "pie-api",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node index.js",
    "dev": "nodemon"
  },
  "author": "",
  "license": "ISC"
}
```


### nodemon

We've added a command called `nodemon` to our dev script, but what is it?
- Nodemon is a tool that lets us run code while we're developing
- It will automatically restart our code when it detects we've made changes
- We will install nodemon as a *dev dependency*, meaning it is not needed to run our library as a user but is needed while we are developing it

Run `npm i --save-dev nodemon`. (The "i" is short for "install".)
You'll notice a few new lines in `package.json`.


### Express

We're going to be using a server called Express.

Let's install it with `npm i express`.




## Starting our server

Create an `index.js` file at the root of our server.

The first thing we have to do is bring in express and make a new server.

```js
const express = require('express');
const app = express();
```

A note about `require`:
- `require` is a built-in function that imports another JS library or file.
- When we `require('express')`, we are saying, go find the module called 'express', and take the export of that file and give it to us.
- Then we bind that exported value in the variable `express` in our file so we can use it.
- This will make more sense once we start exporting things ourselves.

What about `express`? We can see it is a function that returns a new server, which we are storing in the variable `app`.

At the end of the file, after the first two lines, add the following:

```js
app.listen(4000, () => console.log('App is listening on 4000'));
```

This line actually starts the server. The second argument to `.listen` is a *callback function* that will run after the server starts listening; here, it just prints a message to the console.


### dotenv

We don't want to push information like port numbers to GitHub, because they will make our app easier to attack. We want to keep the port hidden.
So we are going to use another package that will allow us to keep sensitive information in another file that we won't push to GitHub.

First, let's get that package. Run `npm i dotenv`. This will install the `dotenv` package and add it to `package.json`.

After this finishes, create a new file called `.env` containing just the following:

```
PORT=4000
```

Now add this as the very first line of `index.js`:

```js
require('dotenv').config();
```

Instead of binding the return of `require` to a variable and then calling `.config` on that, we are doing both in one step.

Now change the final line of `index.js` to read like this:

```js
app.listen(process.env.PORT, () => console.log(`App is listening on ${process.env.PORT}`));
```

Notice how we're using our `PORT` variable, by digging into the `process.env` object of Node.

One last thing: we want git to ignore our `.env` file since it contains sensitive information. To do this, create a file called `.gitignore` (don't forget the leading period), and put this in it:

```
node_modules/
*.env
```

Now git will ignore everything in the `node_modules` directory as well as any file ending in `.env`.



## Building out our server

*Editor's note: I am skipping delivering a static page.*

### Our first endpoint

Let's start building endpoints!

In `index.js`, before the call to `.listen` but after the imports and configuration, add the following line:

```js
app.get('/pies', (req, res) => res.send('Pie is stellar'));
```

If you haven't already started running the app, go to the terminal and in the `server` folder, type `nodemon` (we could also use the `npm run dev` script). This will start the app and restart it on changes.

Now open Postman and create a GET request to `http://localhost:4000/pies`. When you send it, you should receive a response with `Pie is stellar` in the body!



### Our first controller


- We don't want to keep all our endpoints in the main `index.js` file.
- Having different *controllers* will help us separate out different parts of our app.
- For instance, we will end up having a controller for users and one for pies

To see this in action, let's create a new folder called `controllers`, and in this folder add a file called `piecontroller.js`.

In `piecontroller.js`, add:

```js
const express = require('express');
const router = express.Router();


module.exports = router;
```

The `router` object is what we export from this file and plug into the main express app in `index.js`.
You'll see another import/export system when we get to React, but for now:
- we *export* something by assigning module.exports to it, and
- we *import* something from another file by using the `require` function.


To make our controller do something, we need to create an endpoint.
Instead of writing a new one, let's just cut the `app.get` line from our `index.js` file and paste it here, after we create the router but before we export it.
Remember to change `app` to `router`:
```js
router.get('/pies', (req, res) => res.send('Pie is stellar'));
```

Finally, we need to *use* our controller in `index.js`.
Remember, we import using `require`, so add this to the top of `index.js` with the other imports:
```js
const pieController = require('./controllers/piecontroller');
```

Then, to use the controller, add this line before `app.listen`.
```js
app.use('/pies', pieController);
```




## Postgres and sequelize


### Creating a new database

Open a new pgAdmin window and expand Servers, then Postgres in the left hand tree until you see Databases.
Then right-click on that and create a new database.
We're going to call it `pieApi`. Enter the name in and press Save.


### Adding packages

Now we need to add some more packages to our server so we can connect to Postgres.
In the folder where we are building our server, run these two commands:
```sh
npm install sequelize
npm install pg
```

The first command installs *sequelize*.
Sequelize is an *object relational mapper*, which is a fancy way of saying it connects to Postgres and converts back and forth between SQL data and JavaScript data.
The second command installs the *pg* package, which sequelize needs to use Postgres specifically (there are a lot of kinds of SQL out there).


### `db.js`

We need to create the connection to our database in JavaScript.
- At the root level of your server folder, add a new file called `db.js`.
- Add the following code (be sure to use the right capitalization):
```js
const Sequelize = require('sequelize');
const sequelize = new Sequelize('pieApi', 'postgres', process.env.PASS, {
  host: 'localhost',
  dialect: 'postgres'
});

sequelize.authenticate()
  .then(() => console.log('connected to postgres'))
  .catch(err => console.log(err));
```

Before we do anything else, let's add the `PASS` variable to our `.env` file.
Use the same format as you did for the `PORT` variable.

Lastly, remember in order to use the `sequelize` object in another file, we need to export it.
So, add the following line to the end of `db.js`:
```js
module.exports = sequelize;
```

And to use it, in `index.js`, add the following lines:
```js
const sequelize = require('./db');  // put this with the imports

sequelize.sync();                   // put this after imports and before the app.use for the controller
```



## Models

We have connected to our Postgres database, but we have not specified what our data will look like.

Create a `models` folder and a file in it called `pie.js`.

In `pie.js`, write the following code:

```js
module.exports = (sequelize, DataTypes) => {
  const Pie = sequelize.define('pie', {
    nameOfPie: DataTypes.STRING,
    baseOfPie: DataTypes.STRING,
    crust: DataTypes.STRING,
    timeToBake: DataTypes.INTEGER,
    servings: DataTypes.INTEGER,
    rating: DataTypes.INTEGER
  });
  return Pie;
};
```

Explanation:
- Notice we are setting `module.exports` directly without binding something to a variable and setting `module.exports` equal to *that*. This is perfectly acceptable.
- What we are exporting is a function that takes two arguments: our sequelize connection object and an object that contains representations of the SQL data types.
- In the function, we create `Pie` using `sequelize.define`. This is a bit complicated but essentially `Pie` is an object that `sequelize` uses to know what table we want to interact with and what its data structure is.
- The first parameter we pass to `sequelize.define` is a string that will be the name of our table.
- For each column we want in our table, we have to say what its name is and what data type it is. These are the keys and values of the object.
- Lastly, we `return` the `Pie` object we have created.


It should come as no surprise that now we have to `require` this and use it somewhere.
If we think about it, we will need this in the pie controller, because that's where we will be interacting with this Postgres table.
Under the `const router` line, we can write the following code:
```js
const Pie = require('../db').import('../models/pie');
```

If it helps understand what's going on, you can also write the same thing as
```js
const sequelize = require('../db');
const Pie = sequelize.import('../models/pie');
```

The first version saves us a line of code because we don't bother binding the exported `sequelize` object to a variable.
We can do this because we only need to use it once in this file and don't need it after that use.







## Using JSON

One thing we might not think to tell our server is how to parse incoming data.

Add the following line to `index.js`, right after `sequelize.sync()`:
```js
app.use(express.json());
```

This tells the app to expect data in JSON format, which we will be sending from our front end.





## GET and POST

We've already written a GET request that just returns a string.
But now:
- we want to use GET requests to retrieve data from our SQL database, and
- we want to use POST requests to create data in that database.




In `piecontroller.js`, below the existing endpoint, add this:
```js
router.get('/', (req, res) => {
  Pie.findAll()
    .then(pie => res.status(200).json(pie))
    .catch(err => res.status(500).json({ error: err }));
});
```

Explanation:
- The call to `.findAll` goes into the Pie table in the database and comes back with all the rows...
- but right now there aren't any: if we hit this endpoint in Postman (and you can try to be sure) we'll get an empty array back.
- That `.findAll` method returns a promise containing the result of the findall, which we send back using the `.json` method.
- Before we use `.json`, we set the status on the return object `res` using `.status`.
- If something goes wrong with the `.findAll` we fall down to the `.catch`, where we set the status to 500, meaning error, and send back the error message as JSON so we can debug.


To put pies *in* our database, we need to add a POST. We can do this as follows:

```js
router.post('/', (req, res) => {
  if (!req.errors) {
    const pieFromRequest = {
      nameOfPie: req.body.nameOfPie,
      baseOfPie: req.body.baseOfPie,
      crust: req.body.crust,
      timeToBake: req.body.timeToBake,
      servings: req.body.servings,
      rating: req.body.rating
    };

    Pie.create(pieFromRequest)
      .then(pie => res.status(200).json(pie))
      .catch(err => res.json(req.errors));

  } else {
    res.status(500).json(req.errors);
  }
});
```

There's a lot to unpack here so let's take it piece by piece.
- The whole thing is wrapped in a call to `router.post`, so we're doing a POST request.
- The first thing we do is check for errors. If there are errors, we skip down to the `else`.
  - If we hit that else, we set the response status to 500 and send back the errors as JSON.
- If we don't have any errors, we continue and construct `pieFromRequest`.
  - This assembles an object from the information contained in the body of the request. We'll see what that looks like in a moment.
- After we create the pie object, we pass it to `Pie.create`. This actually creates a row in our table from an object we give it, in this case that pie object.
- This returns a promise, so we use `.then` to catch what's inside the return value of that promise and send it back with a status of 200.
- We also have a `.catch` just in case something goes wrong with putting data into the table.

Note that we build the `pieFromRequest` object to match the specification of our table. If we have other data in our object, we will run into problems.

Now we can hit this endpoint and create a pie in our database!
Go to Postman and, in the body of a POST request to the `/pies` endpoint, type out:

```json
{
  "nameOfPie":"peach",
  "baseOfPie":"fruit",
  "crust": "graham",
  "timeToBake": 50,
  "servings": 8,
  "rating": 5
}
```
Make sure to mark this as JSON.

When you send this request, you should get back the same object that you send, which has been added to the database.
*Now* send the GET request from earlier; you should get back an array with one pie in it, the one we added.

Add a few more pies using this POST request before continuing.


## Challenges

### Challenge 1: Broken code

See if you can fix the multiple errors in this code:

```js
router.get('/name', (req, res) => {
  Pie.findone({ where: { nameOfPie: req.params.nameOfPie }})
    .then(pie => res.status(200).json(pie))
    .catch(err => res.status(500).json({ error: err}));
});

router.put('/:id', (req, res) => {
  if (!req.errors) {
    pie.update(req.body, { where: { id: req.body.id }})
      .then(pie => res.status(200).json(pie))
      .catch(err => res.json(req.errors));
  } else {
    res.status(500).json(req.errors);
  }
});
```

Here's the correct code:

```js
router.get('/:name', (req, res) => {
  Pie.findOne({ where: { nameOfPie: req.params.name }})
    .then(pie => res.status(200).json(pie))
    .catch(err => res.status(500).json({ error: err}))
})

router.put('/:id', (req, res) => {
  if (!req.errors) {
    Pie.update(req.body, { where: { id: req.params.id }})
      .then(pie => res.status(200).json(pie))
      .catch(err => res.json(req.errors));
  } else {
    res.status(500).json(req.errors);
  }
});
```

### Challenge 2: DELETE

Add delete functionality to the pie controller.
- This means building out an endpoint for DELETE requests.
- You'll need to use params, not the request body.
- You'll also need to look up the method used by sequelize to delete rows from a table.


An answer:

```js
router.delete('/:id', (req, res) => {
  if (!req.errors) {
    Pie.destroy({ where: { id: req.params.id }})
      .then(pie => res.status(200).json(pie))
      .catch(err => res.json(req.errors));
  } else {
    res.status(500).json(req.errors);
  }
});
```



## Authentication

We are now going to add users and authentication to our pie app.

To keep track of users, we're going to use a separate table, which means a new model.
So, in a new file in the `models` folder called `user.js`, write the following.

```js
module.exports = (sequelize, DataTypes) => {
  const User = sequelize.define('user', {
    firstName: {
      type: DataTypes.STRING,
      allowNull: false
    },
    lastName: {
      type: DataTypes.STRING,
      allowNull: false 
    },
    email: {
      type: DataTypes.STRING,
      allowNull: false,
      unique: true 
    },
    password: {
      type: DataTypes.STRING,
      allowNull: false 
    }
  });

  return User;
};
```

We're using a slightly different format of specifying the here, but it should make sense.
- Instead of just providing a data type for each key in the object defining our columns, we're providing an object that specifies the data type as well as additional options.
- We use these options to specify things like whether we want anything to be allowed to be `null` or whether a value has to be unique (so, here, the same email can only be used once to sign up).
- At the end of the function we export, we return the defined model.


Now, like we did with pies, let's make a file in `controllers` called `usercontroller.js`.
We start it out like this:

```js
const router = require('express').Router();
const User = require('../db').import('../models/user');

module.exports = router;
```

### Signing up

Our first endpoint will allow us to sign up by providing a first name, last name, email, and password.
Between the imports and the exports, add this:
```js
router.post('/signup', (req, res) => {
  User.create({
    firstName: req.body.firstName,
    lastName: req.body.lastName,
    email: req.body.email,
    password: bcrypt.hashSync(req.body.password, 10)
  })
    .then((user) => {
        let token = jwt.sign({ id: user.id }, process.env.JWT_SECRET, { expiresIn: 60 * 60 * 24 });

        res.json({
          user: user,
          message: 'user created',
          sessionToken: token
        });
      },
      err => res.send(500, err)
    );
});
```

Before we look at this code, let's make sure we have jsonwebtoken and bcryptjs installed so we can import them.
In the main folder of our app, run `npm i jsonwebtoken bcryptjs`.

Now we import these folders. Under the first two lines, add
```js
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');
```

So `bcrypt` and `jwt` are defined now, and your editor shouldn't be complaining about that anymore.

Let's analyze that endpoint now.
- It's a POST, because we're creating data, so we use `router.post`.
- Our endpoint will be at the `/signup` endpoint in this controller (see below for the full endpoint).
- The first thing we do is create the user using `User.create`. We pass this an object with the four pieces of data that we receive from the client: first and last names, email, and password.
  - Notice that the password is *hashed*, meaning we encrypt it so it's not stored in plain text.
- After we create the user, in the `.then` we sign a token including the id of the user, which we will need later. The second argument is our JWT_SECRET, and the last argument is an object with an `expiresIn` property, which here we set to one day.
- We then create an object to send back as JSON, containing the user object, a short message saying the user was created, and then the token we signed. It is up to the client to take this token and store it so that they can access our application.



### Signing in

Signing in is similar to signing up, but we will be comparing the password instead of creating a new hash.

Under the sign up endpoint, create another POST endpoint as follows:
```js
router.post('/signin', (req, res) => {
  User.findOne({ where: { email: req.body.email }})
    .then(user => {
      if (user) {
        bcrypt.compare(req.body.password, user.password, (err, matches) => {
          if (matches) {
            let token = jwt.sign({ id: user.id }, process.env.JWT_SECRET, { expiresIn: 60 * 60 * 24 })
            res.json({
              user: user,
              message: 'successfully authenticated',
              sessionToken: token 
            });
          } else {
            res.status(502).send({ error: 'bad gateway' });
          }
        })
      } else {
        res.status(500).send({ error: 'failed to authenticate' });
      }
    }, err => res.status(501).send({ error: 'failed to process'})
    )
});
```

- The user will be signing in with the email (essentially their username) and their password.
- Instead of creating a user, we use the `.findOne` method to find one where the email matches. Remember the email will be unique because that's how we created the model.
- In the `.then`, we check if there is a user found, and if not we jump down to the second else and send back a 500 error.
- If there is a user found, we compare the password supplied to the password stored. We provide a callback function to take care of the rest of the logic because this is an asynchronous function (don't worry too much about this).
- In the callback, we check if the passwords matched, and if so, we sign a new token the same way as in the signup and send it back in a JSON object.
- If the passwords don't match, we send back a 502.

Feel free to ask any questions about how this works.





### Hooking up the auth endpoints


Finally, in `index.js`, we need to connect this controller.
Import it at the top:
```js
const user = require('./controllers/usercontroller');
```
And then next to the `app.use` for the pie controller, add
```js
app.use('/auth', user);
```

Now we can sign up by hitting `/auth/signup` and sign in by hitting `/auth/signin`.



## Headers

Before we actually hit those endpoidnts, though, we need to take care of one other thing.
To prevent CORS errors, we need to add a file that contains certain header specifications for our response.
Create a folder called `middleware` and, inside, create a file `headers.js`.

In it, write the following. Pay attention to the spelling! 

```js
module.exports = (req, res, next) => {
  res.header('access-control-allow-origin', '*');
  res.header('access-control-allow-methods', 'GET, POST, PUT, DELETE');
  res.header('access-control-allow-headers', 'Origin, X-Requested-With, Content-Type, Accept, Authorization');

  next();
};
```

Now go to `index.js` and let's use it. Add the following line right after `app.use(express.json());`.
```js
app.use(require('./middleware/headers'));
```




## Validating the session



In the `middleware` folder, create a new file called `validateSession.js`.
In this file, write the following (I know it's a lot, just bear with me):

```js
const jwt = require('jsonwebtoken');
const User = require('../db').import('../models/user');

const validateSession = (req, res, next) => {
  const token = req.headers.authorization;
  jwt.verify(token, process.env.JWT_SECRET, (err, decodedToken) => {
    if (!err && decodedToken) {
      User.findOne({ where: { id: decodedToken.id }})
        .then(user => {
          if (!user) throw 'err';
          req.user = user;
          return next();
        })
        .catch(err => next(err));
    } else {
      req.errors = err;
      return next();
    }
  });
};

module.exports = validateSession;
```

Let's go over what's happening here:
- We import the `jsonwebtoken` package and the `User` model.
- The export for this file is a function that takes three parameters: the request, the response, and a `next` function that we call when we want to continue on to the next part of the endpoint.
- In the `validateSession` function, we first get the provided token out of the Authorization header.
- Then we use the `jwt.verify` function to check that the token was made with the JWT_SECRET from our .env file. This function is asynchronous, but instead of using `.then`, we give it a callback function that it executes when it finishes. This callback takes any errors and the decoded token if there is one (`decodedToken` will be `undefined` otherwise).
- In this callback, we check if there are no errors. If there are errors, we skip down to the `else`, note the errors, and continue to the endpoint. Remember that in the endpoints we check if there are any errors, and if there are, we send back an error status code and the error message.
- If there are no errors and the token exists, we find the user using `User.findOne` and pass in the decoded token's id (which, remember, we included in the token when we signed it in `usercontroller.js`).
- In the `.then`, if there is no user found we throw an error.
- But, if there *is* a user found, **we set that user object to be a property of the request object** so we can access it later. This is important.
- Finally, we call `next`.


Now we need to use this function.
So let's go to `piecontroller.js` and import `validateSession` at the top:
```js
const validateSession = require('../middleware/validateSession');
```
and then *add it as a parameter* to all the endpoints *before the arrow function*.
So, for example, the first line of the POST request now looks like this:
```js
router.post('/', validateSession, (req, res) => {
```