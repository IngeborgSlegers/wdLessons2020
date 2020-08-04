# Pie Client Walkthrough

## Creating the React app

Now we want to create a frontend app using React for our Pie API.

Navigate to the `pie-project` folder and run the following command:

```
npx create-react-app pie-client
```

What is this command?
- `npx` is like `npm` except it does not install the package, it just executes the package. (Don't worry about this distinction too much.)
- `create-react-app` is the name of the package we want to execute, which will build out a basic React app for us. It sets up a lot of defaults and basic files that we would have to create manually.
- `pie-client` is the name of our project. `npx` will create a folder with this name and put our project in it.

So now we should have the following folder structure:

```
pie-project
    server
    pie-client
```

Go ahead into the `pie-client` folder and let's have a look around.


## Setup

### File structure

Open `pie-client` in VS Code and you should see a few folders and files.

```
pie-client
    node_modules
    public
    src
        App.css
        App.js
        App.test.js
        index.css
        index.js
        logo.svg
        serviceWorker.js
    package-lock.json
    package.json
    README.md
```

All of our app's code goes in the `src` folder, with `App.js` being the primary place where we will be building our app.
From here, the app goes through `index.js` into `index.html` in the public folder (don't worry about how this works yet).


### Starting our development server

- To make sure we have everything installed correctly, open the `pie-client` directory in the terminal and type `npm start`.
- You should see some things happen in the terminal, and then a browser window pops open with a spinning React logo. Great!
- When we make changes to the app and save the files, the development server will automatically reload.


### Clearing out the default files

That's great, but we don't want an app that tells us how to get to the React docs, we want our own code.

Go into `App.css` and delete everything.

Next, go into `index.css`, which right now sets font information, and delete the `font-family` properties. This should leave the only property setting the body margin to 0, which is OK.

Finally, go into `App.js`
- delete the logo from the imports (line 2),
- delete most everything from the `App` function except the `div`,
- and insert an `h1` tag so that it looks like this:

```js
function App() {
  return (
    <div className="App">
      <h1>Pie Client</h1>
    </div>
  );
}
```

Once we have all these files saved, as long as the development server is still running, we can go back to the browser and see just our `h1` tag saying "Pie Client". Pretty cool.


## React basics

### JSX

In the last section, we saw what looks like HTML written in our JavaScript.
- If you've been working through the modules you might have seen this as well.
- This is called JSX, the enhanced HTML that React provides us.
- JSX allows us to use JavaScript values directly in our HTML using curly braces.
- In order to use it in a file, we have to `import React from 'react'` at the top of the file.

A few other small notes about JSX: everything you return in a component has to be in *one* HTML element. What that means is that we can't do this:
```js
function App() {
  return (
    <div className="App">
      <h1>Pie Client</h1>
    </div>
    <div>
      <h1>Another div</h1>
    </div>
  );
}
```
The reason is that we are returning *two* `div` tags on the same level. If you ever see React complaining about "Adjacent JSX elements must be wrapped in an enclosing tag", or having to return one element from a component, this is what it's talking about.

Also, we use a `className` attribute for classes as opposed to just `class`. (`id` is still `id`.) This is because, in JS, `class` is a special keyword and the JS parser (the program that reads our code) would complain if it saw the word `class` out of place.

Finally, regular HTML comments don't work in JSX. To write a comment, we need to write it in JavaScript, which means curly braces and then `/* */` for a comment, like this: `{/* here is a comment */}`.


### Functional components

When you write a React app, you write *components*. The main component where our app is contained is in `App.js`, but you can (and should!) write other components to separate out pieces of your application. For example, you might have a component for the login part of your page and another for signing up, a component for the sidebar, and a component for the navbar. These in turn can be broken down into their own smaller components.

We write component names in *Pascal case*, with a capital letter beginning each word, including the first, and no spaces between words. So `App`, `Navbar`, `InfoContainer`, and `LeftSideToolbar` are all valid class names.

The traditional way to write React components is with *classes* (not to be confused with classes in HTML).
This is an ES6 feature that is built on top of the prototype system to provide easier object oriented programming in JS. Nowadays, we *usually* write components in a different way, as functions.

Take a look at that App component again.
We could have written it as an arrow function, and indeed most of the components we write will be arrows.

```js
const App = () => {
  return (
    <div className="App">
      <h1>Pie Client</h1>
    </div>
  );
};
```

The important thing to realize is that whether it uses the `function` keyword or it is an arrow function, *it is just a function*!
- In the return of the function, we write the JSX we want that component to render.
  - We need to wrap the JSX in parentheses if it takes multiple lines.
- Like with any function, it can (and we will see that it will) take arguments.
- Like with any function, we can (and will) do other things before the return.


## A new component

### `Navbar.js`

Let's add our own component to the `App` component to create a navbar.

Addd a new file called `Navbar.js` in our `src` folder, at the same level as `App.js`. Add the following to this file:

```js
const Navbar = () => {
  return (
    <nav className="navbar">
      navbar goes here
    </nav>
  );
};
```

Now we want to actually use our navbar, so let's go back to `App.js` and make it look like the following:

```js
import React from 'react';
import './App.css';

import Navbar from './Navbar.js';  // new line

function App() {
  return (
    <div className="App">
      <Navbar />                  {/* new line */}
      <h1>Pie Client</h1>
    </div>
  );
}

export default App;
```

If we save our files and check the browser, we'll see something's broken (actually, we missed two steps... any guesses as to what they are?).


### Fixing the errors

It's good to note that the React error messages are usually pretty helpful.
Make sure to read them and try your best to figure out what it's saying when you run into a problem, you might be able to fix it without help!

In this case, it tells us that "'React' must be in scope when using JSX".
Ah, right, we forgot to import React! Let's add that line at the top of `Navbar.js`:

```js
import React from 'react';
```

Now we can check the browser again, and it tells us that we're trying to *import* `Navbar` but we aren't explicitly *exporting* anything from `Navbar.js`. We can fix that by adding a line to the *end* of `Navbar.js`:

```js
export default Navbar;
```

After we save one more time, we should see "navbar goes here" above a "Pie Client" heading.



Having seen this and also through the modules you should have a basic understanding of how we define components in their own files, export and import them into other files, and use them in JSX as tags.










### `Navbar.css`

Why don't we want all our CSS in one file?
- Easier to manage
- Separation of concerns
- It's common convention to have specialized files
- It helps other people reading the code know where the relevant rules are

So, let's create a CSS file specifically for our navbar called `Navbar.css`.
Then import it to the top of `Navbar.js` with `import 'Navbar.css';`.

Take half an hour or so to style the Navbar. Add a few placeholders for links in the JSX of the `Navbar` component so you have an idea of what you're styling.




## Footer

Let's make a quick footer component. Instead of an arrow function, we'll use the `function` keyword, just to make it clear that we can write a component in any way we write a regular function.

```js
import React from 'react';

function Footer () {
  return (
    <footer>
      footer goes here
    </footer>
  );
}

export default Footer;
```

Don't worry about styling it right now, if you have time later you can worry about it.

Lastly, don't forget to import `Footer` into the `App.js` file and use it in your JSX return, at the same level as `<Navbar />`


## Authentication

Before we go any further, move `Footer.js` and `Navbar.js` into a new folder called `layout` in a folder called `components`, and create some new files according to the structure below:

```
src
    components
        layout
            AuthForm.js
            Footer.js
            Navbar.js
        auth
            Login.js
            Signup.js
    App.js
```


Be sure to update the imports to `Footer` and `Navbar` in `App.js`.


### `AuthForm`

Let's write a simple authorization form.

```js
import React from 'react';

const AuthForm = (props) => {
  return (
    <div>
      <form>
        <h1>Sign Up</h1>
        <div className="input-group">
          <label htmlFor="email">Email</label>
          <input 
            type="text" 
            id="emailField" 
            name="email" 
            className="input-field" 
            onChange={ props.changeInputs }
          />
        </div>
        <div className="input-group">
          <label htmlFor="password">Password</label>
          <input 
            type="text"
            id="passwordField"
            name="password"
            className="input-field"
            onChange={ props.changeInputs }
          />
        </div>
        <input type="button" value="Submit"/>
      </form>
    </div>
  )
}

export default AuthForm;
```

By now you should be getting used to the basics of writing a component:
- import React at the top
- write the component as a function with a return
- `export default` the component at the bottom
- import and use that component in another component

To that last point, let's go into `App.js` and add the line to import `AuthForm` at the top with our imports:

```js
import AuthForm from './components/layout/AuthForm';
```
And now let's use it in the return:
```js
  return (
    <div className="App">
      <Navbar />
      <h1>Pie Client</h1>
      <AuthForm />
      <Footer />
    </div>
  );
```



### `SignUp` and `LogIn`

The problem with our auth form is that is only does one type of thing: signing up.
We also need to be able to log in if we are a returning user.

So here's what we're going to do.
`AuthForm` will be our containing element for both `Signup` and `Login`, and these components will handle signing up and logging in, respectively.


In `Signup.js`, write this:

```js
import React from 'react';

const Signup = (props) => {
  return (
    <div id="signupdiv">
    </div>
  );
};
```

In `Login.js`, write this:

```js
import React from 'react';

const Login = (props) => {
  return (
    <div id="logindiv">
    </div>
  );
};
```

Now change `AuthForm.js` to look like this:

```js
import React, { useState } from 'react';

const AuthForm = (props) => {
  [login, setLogin] = useState(false);

  return (
    <div id="authdiv">
      {login ? <Login /> : <Signup />}
      <div>
        {login ? <p>Need an account?</p> : <p>Already have an account</p>}
        <button onClick={() => setLogin(!login)}>{login ? "Sign Up" : "Log In" }</button>
      </div>
    </div>
  );
};
```