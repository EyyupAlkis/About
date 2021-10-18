---
layout: post
title: SuperJSON - JSON on steroids
categories:
  - Web Development
  - Javascript
  - Typescript
  - Algorithms
  - Data Structures
  - Projects
  - React
  - Blitz.js
lang: en
---

[SuperJSON](https://github.com/blitz-js/superjson) is a high-fidelity replacement to `JSON.stringify`.
It supports data types like `Date`, `RegExp`, `Map`, `Set` etc., preserves referential equality and supports circular references.
This blog post aims to compare it to alternatives and explain how it works.

<!--more-->

> I'm building a task queueing solution for Next.js! Check out [Quirrel](https://quirrel.dev?ref=nextnestarticle), I think you'll like it ☺️

### What's the Problem?

JSON is the defacto standard data interchange format for web applications.
It's human-readable, has broad tooling support and its choice on data types are compatible with most platforms.

As usage of JavaScript across backend and frontend became more popular, one of its value propositions have been let down by JSON: It doesn't support data types like `Date`, `RegExp`, `Map` or `Set`, forcing developers to write cumbersome adapter logic to work around these limitations.

This problem became especially apparent while working on [Blitz.js](https://blitzjs.com).
Blitz.js is a fullstack React framework that (amongst other things) generates your applications' API layer at compile time.
It thus allows developers to import backend functions straight from the frontend, completely abstracting away network calls.
Here's the problem: If we used `JSON.stringify`, our developers would be surprised because it destroyed their nice data types!
That's where SuperJSON comes into play: It's able to serialize any JavaScript value and thus saves our developers from quite some headache.

## Other ways to solve this

SuperJSON is not the first contender, other approaches that solve this problem have existed.

[devalue](https://github.com/Rich-Harris/devalue) by Rich Harris is an algorithm that encodes value into JavaScript code that creates it - it turns `{ foo: "bar", self: this }` into `(function(a){a.foo="bar";a.self=a;return a}({}))`, which can then be evaluated to get back the original value.
While it's incredibly efficient[^benchmark], there isn't any tooling support for it, and it also can't be used to send data to the server -
what if a malicious client sent `while (true) { /* do nothing */ }`?

[^benchmark]: see [this benchmark](https://github.com/Rich-Harris/superjson-and-devalue) for comparison

A similar approach is taken by Yahoo's [Serialize JavaScript](https://github.com/yahoo/serialize-javascript), but it suffers from the same problems.

## Goals of SuperJSON

Contrary the approaches mentioned above, SuperJSON aims to ...

- ... support any JavaScript value
- ... be safe from remote code execution
- ... be JSON-compatible, so existing tooling can be leveraged
- ... support circular references
- ... preserve referential equalities
- ... be human-readable, so it's easy to debug
- ... be easy-to-understand, even if you don't know it's SuperJSON
  
## How does SuperJSON solve it?

There are two parts to what we're trying to achieve. We need to ...

1. ... transform any JavaScript value into a JSON-compatible one
2. ... be able to reverse it!

### Transforming into a JSON-compatible value

Transforming is quite easy, actually:
For already compatible ones, it's trivial: `42` becomes `42`, `"lol"` becomes `"lol"`, `true` becomes `true` and `false` becomes `false`.

It's a bit harder for values where there is no JSON counterpart: `Set { "foo", "bar" }` becomes `["foo", "bar"]`, `Map { 1 => 11, 2 => 22 }` becomes `[ [1, 11], [2, 22] ]` and `NaN` becomes `"NaN"`.

So given this value ...

```js
{
    a: new Set([ /a-Z/g ]),
    b: new Map([
        [ 1, NaN ],
        [ 2, NaN ]
    ])
}
```

... we'll create this json:

```json
{
    "a": ["/a-Z/g"],
    "b": [
        [1, "NaN"],
        [2, "NaN"]
    ]
}
```

Easy, right? This can done recursively and fits into a couple of lines of code.

But we won't be able to reverse this, right? Unless ... we just take notes:

```json
{
    "a": "set",
    "a.0": "RegExp",
    "b": "map",
    "b.0.1": "number",
    "b.1.1": "number"
}
```

This contains notes on any non-trivial transformations.[^notes-confession]

[^notes-confession]: In reality, we're encoding these in a tree structure to save space. But that's a topic for another post 😄

We can then safely send both our JSON-ified value and the notes over the network.
During deserialization, we then apply the notes to recreate the original value:

```js
value["b"][1][1] = Number(value["b"][1][1])
value["b"][0][1] = Number(value["b"][0][1])
value["b"] = new Map(value["b"])
...
```

Not so hard, right? Now that we can preserve types, let's see how to preserve referential equalitites.

### Preserving Referential Equalities

Imagine the object that's created by the following code:

```js
const a = { id: "a" }
const b = { id: "b" }

{ options: [a, b], selected: a }
```

As stated in the [Goals](#goals-of-superjson), we want to preserve the fact that `options[0] === selected`.
This can be done by not only taking notes on types to recreate, but also on referential equality.
For the value above, SuperJSON takes the following notes:

```json
{
    "selected": ["options.0"]
}
```

We can then use these notes during deserialization like so:

```js
value["options"]["0"] = value["selected"]
```

This feature can also be used to preserve circular references.

## Go use it!

SuperJSON is currently in beta, it should be ready for you to use in your own projects:

```
npm install --save superjson
yarn add superjson
```

```js
import SuperJSON from "superjson"

const string = SuperJSON.stringify(yourValue)
const profit = SuperJSON.parse(string)
```

We're currently testing support for preserving `class` and `Symbol` instances, we'll also work on making SuperJSON faster and smaller.

I'd love to hear what you think of it! Tweet at me: [@skn0tt](https://twitter.com/skn0tt) or write a comment below.
