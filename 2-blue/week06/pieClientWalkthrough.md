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

When you write a React app, you write *components*. 

The traditional way to write React components is with *classes* (not to be confused with classes in HTML).
This is an ES6 feature that is built on top of the prototype system to provide easier object oriented programming in JS.
