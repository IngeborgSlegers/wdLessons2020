# js2020

*New curriculum repository for JS / web dev courses*


## Format

This repository is structured into folders by badge. For Gold, which is the majority of the content at the moment, the structure is by week and then by day.



## Challenges

This repository contains links to the [jsChallenges](https://github.com/ElevenfiftyAcademy/jsChallenges) repo where challenges are used in the classroom. When adding a possible challenge to the curriculum, add it to that challenges repo and then link to it from here.



## Style guide

*in progress*

### General format

Files are [(GitHub-flavored) markdown](https://github.github.com/gfm/) with blocks of code. To turn on syntax highlighting in clients that support it (GitHub, VS Code), write the language ('html' or 'js' probably) immediately after the opening three backticks that enclose a code block:

```js
let x = undefined;
```

is written as

````
```js
let x = undefined;
```
````

### Lesson writing

When writing/transcribing/adapting a lesson, a good format is to have a header sentence like this followed by bullet points.
- This is easier to read than several sentences in a row
- A single salient point can be on its own line
- But there is still a grouping of ideas

You can add end punctuation after bullets or not, your call. But after regular sentences? Always! Right‽



### Code

Comments in the code should be reserved for notating *either* the value of expressions *or* what is printed to the console.
- The students will not get the distinction at first, but usually context should distinguish anyway. When it doesn't, you can definitely be explicit about this.
- When notating what prints to the console, don't use quotes. If returning a string, though, definitely do.
- I use at least two spaces between the end of the code line and the comment, but this is not specificationworthy.
- Feel free to line up comments in the same block using space.

```js
function hello (x) {
  console.log(`Hello ${x}!`);  // Hello [x]!
  return "I said hello";       // a lined-up comment
}

hello("Vasquez");  // returns "I said hello"
```

The most important takeaway: do **not** use comments for elucidation or explanation.
This is what the surrounding text is for.

Unfortunately, although some clients support automatic line numbers in code blocks, GitHub doesn't.

As for JavaScript style, for the time being I'm using [the Standard rule set](https://standardjs.com/rules-en.html).



### Content placeholders

Things that need to be added later, like links to challenges and assignment lists, should be ***bolded and italicized*** using three asterisks on either side `***like so***`.
(Technically the Markdown spec allows for raw HTML, and I tried to use a `<style>` tag to make these stand out more, but GitHub ignores these and doesn't render them. This could open up a discussion about if there's a better content authoring platform out there that does render these and supports some kind of annotations, but I don't know if we would want to move away from GitHub, right?)


