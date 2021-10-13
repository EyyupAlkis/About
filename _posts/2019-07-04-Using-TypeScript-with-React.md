---
layout: post
title: "Using TypeScript with React"
categories:
  - "Languages"
  - "Web Development"
  - "React"
  - "Typescript"
  - "React Native"
lang: en
disqus: true
---

This post is based on a talk I gave at the [*React JS & React Native Bonn Meetup*](https://www.meetup.com/React-JS-React-Native-Bonn-Meetup/events/262193621/).
It aims to answer the following questions:

- What's TypeScript?
- How can I use it with React?
- Why should I (not) use it?

As this was my first public talk ever that was not at school, I'm relieved it went well - thanks to the people who attended the talk! 😁
Preparing and giving presentations is a thing I really enjoy, so I guess I'll try to do it more in the future.

<!--more-->

> I'm building a task queueing solution for Next.js! Check out [Quirrel](https://quirrel.dev?ref=nextnestarticle), I think you'll like it ☺️

<iframe
  src="https://docs.google.com/presentation/d/e/2PACX-1vQrVersE-zkWGbe7H03mvGDJnoXD9Hxp2XlEWbVjKphJvB8RNuUcLs-Oc5_XP-Fi9QD4Zkz1JrYcFuP/embed?start=false&loop=false&delayms=3000"
  frameborder="0"
  width="480"
  height="389"
  allowfullscreen="true"
  mozallowfullscreen="true"
  webkitallowfullscreen="true"
></iframe>

# What's TypeScript?

On [typescriptlang.org](https://typescriptlang.org), TypeScript (TS in short) is described by the following:

> TypeScript is a typed superset of JavaScript that compiles to plain JavaScript.
> ~ typescriptlang.org

So what does this mean?
The first thing that's obvious from this statement is that TypeScript is a *typed* language that's somehow related to JavaScript.
This is the main difference to JavaScript, which is a dynamically-typed language.
Secondly, TypeScript can be compiled to plain JavaScript, which can then be executed on targets like the Browser or Node.js.
Finally, being a *superset* of JavaScript means that TypeScript will only add its features on top of JS while still maintaining strict compatibility with the underlying JS specification.

Let's have a look at an example.
Since TS is a superset of JS, the following is valid code of both languages:[^thirty-one]

```js
function add(a, b) {
  return a + b;
}

const favNumber = add(31, 11);
```

[^thirty-one]: There are three interesting numbers here: Obiously, `favNumber` is *42*. But also *31*, which (as I learned from [@hendrikEbbers](https://twitter.com/hendrikEbbers) last week) is a magic number in the Java `hashCode` implementation. *11*, just like *31*, is a prime - but it's also the only prime that consists uniform digits.

As we already learned, the special thing about TypeScript is its types.
Adding types to the above code yields the following, which is now valid TypeScript, but not valid JavaScript:

```ts
function add(a: number, b: number): number {
  return a + b;
}

const favNumber: number = add(31, 11);
```

By adding these annotations, the TypeScript compiler can check your code to find any bugs in it.
For example, it could find mismatched parameter types or the usage of variables outside of the current scope.

Compiling TypeScript is done using the `tsc` CLI, which will type-check the code and - if all checks were succesful - will emit the code in plain JavaScript.
The emitted code will look a lot like your source code, just without type annotations.

TypeScript also has support for basic type-inference, which can simplify code by allowing you to omit the return type of functions and in most cases also the type of variable assignments:

```ts
function add(a: number, b: number) {
  return a + b;
}

const favNumber = add(31, 11);
```

(It can be hard to spot the difference: There's no return type in line 1 and no variable type in line 5.)

Let's have a look at the kind of types that are available in TS.
There are the primitive `boolean`, `string`, `number`, `Symbol`, `null`, `undefined` and `BigInt` types (yes, `BigInt` seems to be a primitive type in modern JS 😅); there's the `void` type to denote that a function does not return anything; there's the `Function` type and there is the `Array<T>` type which is more commonly denoted as `string[]` or `number[]`.
Also, there are environment-specific types like `ReactElement`, `HTMLInputElement` or `Express.App` which will not be available in all projects, but are specific to your deploy targets and dependencies.

The most interesting thing about TS is that you can define your own types.
Let's have a look at some ways how you can use that to model your domain.
There is the `interface` keyword which you can use to declare the shape of your objects:

```ts
interface User {
  firstName: string;
  lastName: string;
  age: number;
  state: UserState;
}
```

You can declare `enums`:

```ts
enum UserState {
  ACTIVE,
  INACTIVE,
  INVITED,
}
```

TypeScript even supports inheritance in its type definitions.

```ts
interface FunkyUser extends User {
  isDancing: boolean;
}
```

There are also more advanced types like the union type, which can be used to replace Enumerations in a more JavaScript-like way:

```ts
type UserState =
  "active" |
  "inactive" |
  "invited";

const correctState: UserState = "active"; // ✅
const incorrectState: UserState = "loggedin"; // ❌ TypeError
```

When type-checking, TypeScript does not check any prototype chains nor other means of inheritance, it only checks the type signatures of properties and functions your objects feature:

```ts
const bugs: User = {
  firstName: "Bugs",
  lastName: "Bunny",
  age: "too old", // TypeError: expected `number` but got `string`
  state: UserState.ACTIVE,
}
```

I don't want to keep from you that you obviously can also use classes in TypeScript, but *beware*: Later on in this post, I will explain why I don't think you should use them.
Anyway, here's an example:

```ts
class JavaLover implements User {
  private firstName: string;
  private lastName: string;
  private age: number;
  private state: UserState;
  
  getOpinion() {
    return [ "!!JAVA!1!" ];
  }
}
```

Now that we've seen some basic TypeScript code and have a general understanding of what it does, let's find out how to use it with React.

# How can I use it with React?

Since Babel 7 comes with built-in support for TypeScript, integrating it into your build is really simple.
I still recommend you to consult the documentation of your build tool.
Most of them include a well-written How-To-Guide concerning TypeScript setup.

Once you set up your build, you can start to use TypeScript in your components.
Here's a simple React-Native component that receives a TodoItem and a callback in its props and displays a single Todo.

```tsx
import * as React from "react";
import { View, Text, CheckBox } from "react-native";

interface TodoItem {
  id: string;
  name: string;
  isCompleted: boolean;
}

interface TodoListItemProps {
  item: TodoItem;
  onComplete: (id: string) => void;
}

function TodoListItem(props: TodoListItemProps) {
  const { item, onComplete } = props;

  return (
    <View>
      
      <Text>
        {item.name}
      </Text>

      <CheckBox
        isChecked={item.isCompleted}
        onClick={state => {
          onComplete(item.id);
        }}
      />
      
    </View>
  );
}
```

Since React is plain JavaScript at heart, it can be typed just like plain JavaScript.
You just define the schema of your component's props using the `interface` declaration (see `interface TodoListItemProps`) and annotate the type of the component's `props` param using the just-defined `TodoListItemProps` type.
You don't need to specify the return type (it's `JSX.Element`), since it can be inferred from the type of the component's return expression.

Even though JSX is not part of the JavaScript spec, TypeScript is able to type-check JSX.
This allows it to validate the props you pass into components.

You can also make use of TypeScript in conjunction with React's `class` API:

```tsx
import * as React from "react";

interface TimerState {
  count: number;
}

class Timer extends React.Component<{}, TimerState> {
  
  state: TimerState = {
    count: 0
  }

  timerId: number | undefined = undefined;

  componentDidMount() {
    this.timerId = setInterval(
      () => {
        this.setState(
          state => ({
            count: state.count + 1
          })
        );
      },
      1000
    );
  }

  componentWillUnmount() {
    if (this.timerId) {
      clearInterval(this.timerId);
    }
  }

  render() {
    return (
      <p>Count: {this.state.count}</p>
    )
  }
}
```

When typing a class, you pass your type parameter into the `React.Component` you're "extending".
The props of the component, which are empty in the example above (`{}`, the empty object), come first.
The second generic type parameter is your component's state, which simply contains a numeric count in this case.
You can also see that this uses an instance field called `timerId`, which is initialized as `undefined`.
This is why its type is `number | undefined`, which means "either `number` or `undefined`".

As you see, using TypeScript with React is very straight-forward and requires no ceremony.
It's basically a more powerful replacement for `prop-types`, since it supports more advanced types and can type normal JS code, too.
Also, TypeScript is validated at compile-time while `prop-types` does its validations during development.

If you want to play around with some code yourself, you can have a look at the example that I used in my live demo.
It is located in the `todo`-Folder of this repo: [skn0tt/react-and-typescript-talk](https://github.com/Skn0tt/react-and-typescript-talk).


# Why should I (not) use TypeScript?

By now, I assume you have a general understanding of what TypeScript is and what it can do.
Let me go on by elaborating on (what I assume to be) false assumptions about TypeScript.

## What is TypeScript *not*?

TypeScript is *not* a new way to write JavaScript - all it does is extending it by the ability to annotate your types.
First and foremost, TypeScript is a type-checker.
There seems to be a tendency to neglect this fact, as I recently read some statements that went like: 

> "TypeScript is great, finally our Java devs can also work on the frontend."

While this may seem to make sense, this attitude can be harmful.
Sure: Since there are types and classes and interfaces and all that good stuff your Java developers are used to, TypeScript can seem quite inviting to them.
However, transferring your Java (or any other heavily OOP-influenced language for that matter) developers over to write TypeScript while not learning about the deep-rooted differences between Java and JavaScript can lead to big problems down the road.
Remember: Whether your JavaScript code was compiled from TypeScript or not does **not** change anything about its runtime behaviour.
Once it is executed, there's not type information left - it's plain JavaScript, in all its glory and (especially) with all its painpoints.
JavaScript uses prototype-based inheritance, is dynamically typed and has type coercion, has functional influences and many more characteristics that could not be further away from Java.
You should keep that in mind when transferring your Java devs over to TypeScript.

Another myth is that "TypeScript will eliminate all your type errors".
While this is true for self-owned code, your (not-typescripty) dependencies may come with flawed type declarations or no type declarations at all.
This is a trade-off of TypeScript which enables easy interaction with JavaScript code, but sacrifices the virtue of true typesafeness.

## How can TypeScript still help me?

Adding types to your project is a great way to document its structure.
It gives you a way to notate the shape of the data you're passing around.
It's also machine-readable, which means it can be enforced by a machine!
This allows for great editor support (try VS Code, it's great!), easier refactorings and enhanced static security checks.

TypeScript not only enables you to document your code's structure, it forces you to.
This can also cause you to *stop* - and reflect about the code you're writing and wether you can create clearer structure, which will increas the quality of your code.

At least for me, having types in my code makes it easier to understand since I don't need to dig around in the project to find out the shape of my data.
It removes a lot of cognitive complexity.

# Summary

Adopting TypeScript in your projects will lead to better quality code.
By adding type information, it promotes structure and allows for advanced static analysis.
This will make it easier to read your code and also augment your workflows.

Integrating TypeScript into React projects works almost frictionless - especially, if you follow the [Static Typing Guide](https://github.com/piotrwitek/react-redux-typescript-guide) by [@piotrekwitek](https://twitter.com/piotrekwitek).

Do you have any experience with TypeScript or other languages that compile to JavaScript? If yes, what did you like/dislike about it? I'd love to hear from you in the comments or over at Twitter ([@skn0tt](https://twitter.com/skn0tt)).<br>
If you decided on getting into TypeScript: Have fun! It will be a great time. Just try to always keep in mind that all the code you're writing will be JavaScript at some point.