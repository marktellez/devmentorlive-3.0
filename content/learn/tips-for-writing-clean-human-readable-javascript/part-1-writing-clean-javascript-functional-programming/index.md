---
title: "Writing Clean JavaScript - Part 1 - Functional Programming"
h1: "Writing Clean JavaScript - Functional Programming"
summary: "Want more readable, concise JavaScript? Here are some functional programming tips to guide you!"
tags: ["javascript", "clean code", "functional programming"]
authors:
  - "marcustellez"
showAuthorsBadges: true
date: 2023-05-23
lastmod: 2023-05-23
draft: false
series: ["Tips for Writing Clean, Human Readable JavaScript"]
series_order: 1
---
> This is the first part of a series, because otherwise it would be a 30m read. No one wants a 30m read. This isn't a Stephen King novel!

I am known for writing clean, concise, human-readable JavaScript code and I have showcased this ability extensively on YouTube.

Now I will extract the rules I live by when at my keyboard, into a simple step-by-step algorithm so that you can use it to do the same.

By the end of reading this article, you should have the rules and tools you need to write the kind of code you "Uncle Bob" can be proud of!

I will be writing my examples in JavaScript, and certainly, some of these will be JavaScript (or TypeScript) centric, but almost all of it can be applied to any high-level programming language.

## From when do the rules come?

Most of my rules-of-the-road here were culled from much more experienced programmers I have interacted with over the years. Once you get your name at the top of an organization-wide email from the likes of someone like Doug Crockford. It just isn't something you easily forget.

I will list a few of the people who most affected my coding style here, instead of trying to remember who said or wrote what:

- Douglas Crockford
- Gary Bernhardt
- Rich Hickey
- Ward Cunningham
- "Uncle Bob" Martin
- Jordan Walke
- David Heinemeier Hansson

## Prefer Functional over the Imperative

If you are writing a "game loop" and need a high framerate, then by all means, write an imperative for-loop construct. However, if you are trying to write concise, decoupled, beautiful code that is less prone to bugs; use `map`, `filter`, and `reduce`.

### Transforming Arrays

The `map` function decouples the looping logic out of the "work to be done". This has a couple of benefits, including not pushing code that spins out of control due to a missed ++ somewhere.

It also has a beneficial side effect - you don't have to remember if you are counting up or down, and you don't need to worry about if it is ++i or i++. That part of the looping mechanism is abstracted away from you.

Instead, you can focus on the "work to be done" - the part you care about writing. And the code you write will be shorter and easier to follow. You will also have quite a few fewer assignments changing values that you now have to keep track of.

`Map` is the tool to reach for when you need to transform one array into another array of the same length as the original.

**Imperative loop**
``` JavaScript
const articles = [
  { title: "This is article title 1" },
  { title: "This is article title 2" },
  { title: "This is article title 3" }
];
const transformedArticles = [];

for (let i = 0; i < articles.length; i++) {
  const article = articles[i];
  transformedArticles.push({ ...article, slug: article
    .title.toLowerCase()
    .replace(/[^\w\s]/gi, "")
    .trim()
    .replace(/\s+/g, "-") });
}
```

**Result**
```
[
  {
    title: "This is article title 1",
    slug: "this-is-article-title-1"
  },
  ...
]
```

**Functional fix**
``` JavaScript
const articles = [
  { title: "This is article title 1" },
  { title: "This is article title 2" },
  { title: "This is article title 3" }
];

articles.map(article => ({
  ...article,
  slug: slug(article.text)
}))

function slug(text) {
  return text.toLowerCase()
    .replace(/[^\w\s]/gi, "")
    .trim()
    .replace(/\s+/g, "-")
}
```

**Result**
```
[
  {
    title: "This is article title 1",
    slug: "this-is-article-title-1"
  },
  ...
]
```

### Need a subset of the original array?

`Filter` also decouples the looping logic from your function, leaving behind a simpler construct where you are only focused on returning a _predicate_, a fancy way of saying `Boolean`.

At each iteration, you simply check some value, and if that check returns true, the current value will be returned, otherwise, it will be discarded from the new array.

**Imperative loop**
``` JavaScript
const articles = [
  { title: "This is article title 1" },
  { title: "This is article title 2" },
  { title: "This is article title 3" }
];

const filteredTitles = [];

for (let i = 0; i < articles.length; i++) {
  const article = articles[i];
  const title = article.title;
  const number = Number(title.match(/\d+$/));

  if (number > 2) {
    filteredTitles.push(title);
  }
}
```

**Result**
```
[
  {
    title: "This is article title 3",
    slug: "this-is-article-title-3"
  },
  ...
]
```

**Functional fix**
``` JavaScript
const articles = [
  { title: "This is article title 1" },
  { title: "This is article title 2" },
  { title: "This is article title 3" }
];

const filteredTitles = articles
  .filter(article => Number(article.title.match(/\d+$/)) > 2)
  .map(article => article.title);
```

**Result**
```
[
  {
    title: "This is article title 3",
    slug: "this-is-article-title-3"
  },
  ...
]
```

### Reduce is generally misunderstood

`Reduce` is by far the most powerful of the "big 3". Its job is to "massage" an array into a new form. It could reduce that array down, like a filter, but it can also "build up" a new array. Under the hood, `map`, `filter`, and its two cousins `every` and `some`- are built from the `reduce` call.

Some good uses include building up a new object of keys, a new array, a string, and for summing values. And by the way, it has little to do with the "reducer" pattern.

All three of these function calls can be used to clean up and decouple (and simplify) your code, reduce bugs, and can make for much more concise code.

**Imperative loop**
``` JavaScript
const titles = [
  "This is article title 1",
  "This is article title 2",
  "This is article title 3"
];

const transformedArticles = [];

for (let i = 0; i < titles.length; i++) {
  const title = titles[i];
  const slug = createSlug(title);
  const transformedArticle = { title, slug: article.title.toLowerCase()
    .replace(/[^\w\s]/gi, "")
    .trim()
    .replace(/\s+/g, "-") }) };
  transformedArticles.push(transformedArticle);
}
```

**Result**
```
[
  {
    title: "This is article title 1",
    slug: "this-is-article-title-1"
  },
  ...
]
```

**Functional fix**
``` JavaScript
const titles = [
  "This is article title 1",
  "This is article title 2",
  "This is article title 3"
];

const transformedArticles = titles.reduce((acc, title) => (
  { title, slug: createSlug(title) }
), []);


function createSlug(title) {
  return title
    .toLowerCase()
    .replace(/[^\w\s]/gi, "")
    .trim()
    .replace(/\s+/g, "-");
}
```

**Result**
```
[
  {
    title: "This is article title 1",
    slug: "this-is-article-title-1"
  },
  ...
]
```

We can also use the same `reduce` function to build up a string, an object, or a sum of numbers!

## Always use const, occasionally let

When you are trying to follow the execution of a code block and all the variables are being changed and reassigned continuously, it makes it incredibly difficult to keep track of any one value at any specific point in time.

Const protects you from this, and it also helps in reducing the likelihood of a variable changing its type or "interface". Interface is simply the functions that be called when accessing the variable. An `Integer` has different functions available to it than you will find on an `Array` or a `String`.

This will reduce "type" errors, something that has plagued JavaScripters enough that a new subset language was invented to solve the problem, namely "TypeScript". I think that solution is a bit like hammering a nail in with a sledgehammer, but that is for a future article.

**Imperative code**
``` JavaScript
let name = "Mark"
... 7 lines of code
name = "Marcus".length

name.split("")
```

**Result**
```
Uncaught TypeError: name.split is not a function
```

**Functional fix**
``` JavaScript
const name = "Mark"
... 7 lines of code
name = "Marcus".length

name.split("")
```

**Result**
```
Uncaught TypeError: Assignment to constant variable.
```

We never have to debug why the `split` function fails, because as soon as we try to reassign the variable, we are alerted! No more trying to figure out what is this variable _now_.

## Keep Your Functions Short

Short functions are less prone to being dumping grounds for unstructured code. If you are concerned with the number of lines in your functions, you will be incentivized to extract additional behavior out instead of just "jamming it in".

This will lead to well-structured, easier-to-read, and generally, better code. And you get all of this without having to put an incredible amount of thought into the "why".

I will revisit the first example:

**Imperative loop**
``` JavaScript
const articles = [
  { title: "This is article title 1" },
  { title: "This is article title 2" },
  { title: "This is article title 3" }
];

function addSlugs(articles) {
  const articlesWithSlugs = [];

  for (let i = 0; i < articles.length; i++) {
    const article = articles[i];
    articlesWithSlugs.push({ ...article, slug: article
      .title.toLowerCase()
      .replace(/[^\w\s]/gi, "")
      .trim()
      .replace(/\s+/g, "-") });
  }

  return articlesWithSlugs
}
```

**Functional fix**
``` JavaScript
const articles = [
  { title: "This is article title 1" },
  { title: "This is article title 2" },
  { title: "This is article title 3" }
];

function slugArticles(articles) {
  return articles.map(article => ({
    ...article,
    slug: slug(article.text)
  }))
}

function slug(text) {
  return text.toLowerCase()
    .replace(/[^\w\s]/gi, "")
    .trim()
    .replace(/\s+/g, "-")
}
```

I think we can both agree that the detangling of the loop from the "work to be done" and the extraction of the `slug` function are both wins!

## Every Function should have a Return

By ensuring that every function returns a value, you will never have to debug, step by step, line by line - code, to figure out why the code "loses its value" - "somewhere". How many times have _you_ dumped `console.log` statements all over your code?

## Stop using If/Else and If/Else/Elsif

Ifs are divine. If/Else/Elsif is evil.

If you write an If/Else - do not be surprised if someone (maybe you, tomorrow) will come in behind you and add a nested If/Else inside the original `if` statement.

Then the same thing will happen again, this time, in the `else` side of the conditional. Before you know it, you have an insane cyclical complexity score, and your code has become a "spaceship" - where, if you turn your monitor on the side, it will resemble a spaceship from an old video game.

This makes code difficult to read, and it makes it possible for all kinds of logic to creep in.

Forgive me for my sin, but here is an example:

**Gross code**
``` JavaScript
function evaluateCondition(a, b, c, d, e) {
  if (a) {
    // First condition is true
    if (b) {
      // Nested condition: a and b are true
      if (c) {
        // Nested condition: a, b, and c are true
        if (d) {
          // Nested condition: a, b, c, and d are true
          if (e) {
            // All conditions are true
            console.log("All conditions are true");
          } else {
            console.log("Condition e is false");
          }
        } else {
          console.log("Condition d is false");
        }
      } else {
        console.log("Condition c is false");
      }
    } else {
      console.log("Condition b is false");
    }
  } else if (c) {
    // First condition is false, but c is true
    console.log("Condition a is false, but c is true");
  } else if (d) {
    // First and second conditions are false, but d is true
    console.log("Conditions a and c are false, but d is true");
  } else {
    // All conditions are false
    console.log("All conditions are false");
  }
}
```

Let's not make spaceships, let's write pretty code:

``` JavaScript
function evaluateCondition(a, b, c, d, e) {
  if (!a) return "All conditions are false";
  if (!b) return "Condition b is false";
  if (!c) return "Condition c is false";
  if (!d) return "Condition d is false";
  if (!e) return "Condition e is false";

  return "All conditions are true";
}
```

The rules work like this:

Use `if` for guard clauses and always return from them
Use a `ternary` when the conditional is binary (2 branches)
Use a `switch` statement for anything with complex branching

This brings me to `switch` rules!

## The switch statement

The `switch` is a great construct when used well, and it is a nest of vipers when it is not.

Never use the `break` keyword. It allows _fallthrough_ behavior and makes for easy bugs. Instead, every `case` statement should end with a return "some value". The switch can then be put into a function, which makes it more functional and less imperative.

Always include a default case, I usually end all my switches with a default case that throws an `Error` telling the caller that no case matched!

I have found this use of switch-in-a-function not only achieves functional bliss, but greatly reduces the caller's size, and reduces unwanted logic errors and bugs.

**Gross switch**
``` JavaScript
function evaluateCondition(value) {
  let retval
  switch (value) {
    case 1:
      retval = "Value is 1"
      break;
    case 2:
      retval = "Value is 2"
      break;
    case 3:
      retval = "Value is 3"
      break;
  }
  return retval
}
```

**Beautiful switch**
``` JavaScript
function evaluateCondition(value) {
  switch (value) {
    case 1:
      return "Value is 1";
    case 2:
      return "Value is 2";
    case 3:
      return "Value is 3";
    default: throw Error("No case matched!")
  }
}
```

## Summary

In the article, I have highlighted several key tips for improving the readability and maintainability of JavaScript code. These rules are influenced by experienced programmers such as Douglas Crockford, Gary Bernhardt, Rich Hickey, and others. In this part of the series, the author focuses on functional programming and how to use it effectively:

1. Prefer functional programming for cleaner code: Focus on using map, filter, and reduce rather than imperative for-loop constructs for concise, decoupled, and less buggy code.
2. Use map to transform arrays: Decouple looping logic from the work to be done, resulting in shorter and easier-to-follow code.
3. Filter for creating subset of arrays: Focus on returning a predicate (Boolean) at each iteration for cleaner and more organized code.
4. Reduce for versatility: Use the powerful reduce function on various data types such as numbers, arrays, objects, strings, to transform and massage data into new forms.
5. Utilize const and let appropriately: Improve code readability and reduce type errors by using const for unchangeable variables and let for changeable variables.
6. Keep functions short and ensure each one returns a value: This results in better-structured, easy-to-read code, and eliminates the need for excessive console.log statements.
7. Avoid if/else and if/else/elsif: Use guard clauses, ternary conditions, and switch statements to reduce complexity and make code more readable.
8. Properly use switch statements: Replace break with return, include a default case, and encapsulate switch inside a function for a more functional and less imperative approach.

By following these guidelines, programmers can write clean, concise, and human-readable JavaScript code, making it easier for themselves and others to understand and work with the code.
