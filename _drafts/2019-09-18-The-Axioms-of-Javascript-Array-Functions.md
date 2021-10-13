---
layout: post
title: "The Axioms of Javascript Array Functions"
categories:
  - "Web Development"
  - "Javascript"
  - "Typescript"
  - "Maths"
lang: en
---

What are the most basic functions that all array functions are based on and how can we construct the others from them?
We will have a look on them and re-implement them to understand their inner workings.

<!--more-->

# What's an *Axiom*?

Last Sunday, I moved to Potsdam started to study Computer Science at [HPI](https://hpi.de).

TODO: embed tweet

While the term starts at the beginning of October, I'm currently taking a preparation course in mathematics.
The second day, our lecturer introduced us to *Axioms*.
An *Axiom* is a fundamental mathematical assumption which does not need to be proven.
All definitions and proofs need to be based on a set of such *Axioms*.
When giving a definition or proof, you need to specify which *Axioms* you're assuming beforehand.

What does that have to do with JavaScript?
Nothing.
It's maths, not JavaScript - but the idea of basing something on something else that's more general, is deeply rooted in programming as well.
It's at the core of object orientation, DRY and the (TODO: Englisch für Tugend) of  generalization.

In maths, you don't just prove things by combining other proofs, you also define operations by combining other operations.
So in this post, we will try to find the axioms of JavaScript's array operations and recreate the other ones by combining the axioms.

# `reduce` as the single Axiom of Array functions

> Disclaimer: I'm sure there are a lot of different ways to recreate the array functions.
> I will show you the way I came up with spontaneously, I'd love to see your ideas on how to do it!

Although the title says *JavaScript*, I will notate the functions in *TypeScript* syntax.
I think that in this case, giving types will make the code a lot easier to understand - without types, the function signatures of array functions are pretty similar.

```ts
function reduce<S, T>(
  array: S[],
  next: (accumulator: T, currentValue: S) => T,
  initialValue: T
) {
  let accumulator = initalValue;

  for (const element of array) {
    accumulator = next(accumulator, value);
  }

  return accumulator;
}
```

```ts
function flatMap<S, T>(
  array: S[],
  transform: (v: S) => T[]
): T[] {
  return reduce<S, T[]>(
    array,
    (accumulator, currentElement) => [
      ...accumulator,
      ...transform(currentElement)
    ]
    []
  );
}
```

```ts
function map<S, T>(
  array: S[],
  transform: (v: S) => T
): T[] {
  return flatMap(
    array,
    element => [ transform(element) ]
  );
}
```

```ts
function filter<S, T>(
  array: S[],
  include: (v: S) => boolean // TODO: find name for include ("genügt")
): T[] {
  return flatMap(
    array,
    element =>
      include(element)
        ? [ element ]
        : []
  );
}
```

```ts
function some<S>(
  array: S[],
  include: (v: S) => boolean // TODO: find name for include ("genügt")
): boolean {
  return reduce(
    array,
    (accumulator, element) => {
      if (accumulator) {
        return true;
      }

      return include(element);
    },
    false
  );
}

const includes = some;
```

```ts
function every<S>(
  array: S[],
  include: (v: S) => boolean // TODO: find name for include ("genügt")
): boolean {
  return reduce(
    array,
    (accumulator, element) => {
      if (!accumulator) {
        return false;
      }

      return include(element);
    },
    true
  );
}
```

```ts
function reverse<S>(
  array: S[]
): S[] {
  return flatMap(
    array,
    (accumulator, element) => [ element, ...accumulator ],
    []
  );
}
```

```ts
function reduceRight<S, T>(
  array: S[],
  next: (accumulator: T, currentValue: S) => T,
  initialValue: T
): T[] {
  return reduce(
    reverse(array),
    next,
    initialValue
  );
}
```

```ts
function fill<S, T>(
  array: S[],
  with: T
): T[] {
  return map(
    array,
    () => with
  );
}
```

```ts
function flat<S>(
  array: S[][]
): S[] {
  return flatMap(
    array,
    v => v
  );
}
```

```ts
function indexOf<S>(
  array: S[],
  include: (v: S) => boolean // TODO
): number {
  return Number(
    reduce<S, number | string>(
      array,
      (index, element) {
        if (typeof index === "string") {
          return index;
        }

        const newIndex = index + 1;

        if (include(element)) {
          return "" + newIndex;
        } else {
          return newIndex;
        }
      },
      -1
    )
  );
}
```

```ts
function lastIndexOf<S>(
  array: S[],
  include: (v: S) => boolean // TODO
): number {
  return indexOf(
    reverse(array),
    include
  );
}
```













