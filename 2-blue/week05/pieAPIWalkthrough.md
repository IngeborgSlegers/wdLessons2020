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










