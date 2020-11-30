---
layout: post
title:  "JavaScript Symbols"
date:   2020-11-30 03:15:00 +0530
categories: javascript es6
---

# Symbols

## Introduction

Hello everyone! Welcome back to another tutorial in my ES6 series. In today's video, we will look at JavaScript Symbols.

Symbol is a new JavaScript primitive data type introduced with ES6. In this video, we will go through how to create and use symbols and also what problems we can solve with them.

```
- undefined
- Boolean
- Number
- String
- Bigint
- Symbol
```

It's worth noting that symbols aren't exclusive to JavaScript. Symbols are supported by languages like Lisp, Prolog, Ruby and Smalltalk as well.

## Creating Symbols

We can create a symbol by calling the global factory function `Symbol()`. This is in contrast to the other primitive types that have a literal syntax. (TODO: find literal syntax, add examples)

```
console.log(Symbol()); // Symbol()
```

Symbol() is not a consturctor. If we try to use it as one, it will throw an error.

```
new Symbol(); // TypeError: Symbol is not a constructor
```

## Description argument

The `Symbol()` function accepts an optional description argument.

```
const sym1 = Symbol("speed");
const sym2 = Symbol("direction");

console.log(sym1.toString()); // speed
console.log(sym2.toString()); // direction
```

This description property can be accessed by calling `toString` on the symbol. Actually, we can remove it here since `console.log()` calls `toString()` implicitly.

```
console.log(sym1);
console.log(sym2);
```

The description property can make debugging easier. However, it does not affect the uniqueness of the symbol in any way.

```
const sym1 = Symbol("speed");
const sym2 = Symbol("speed");

console.log(sym1 === sym2); // false
```
As you can see, the symbols are still unique eventhough the description is the same.


## typeof

We can use the typeof operator to check whether a variable is a symbol.

```
const sym1 = Symbol("speed");
console.log(typeof sym1);
```

## Symbols as object keys

It is possible to use symbols as object keys and this is prbobably where you will use them the most.

Let's assume we have the following object to represent a todo in a todo app.

```
const todo = {
    id: '001',
    description: 'deploy services',
}

console.log(todo);
```
What if we do the following somewhere in our code.

```
...

todo.id = '002';
console.log(todo);
```

This overwrites `id` in the todo object. This is a simple example, but there are instances in real world applications where we need to ensure that certain object properties are uncollidable to avoid avoid scenarios like this.

Let's try this again this with a Symbol.

```
const id = Symbol('id');

const todo = {
    id: '001',
    description: 'deploy services',
}

todo.id = '002';
console.log(todo); // { id: '002', description: 'deploy services' }
```

As you can see, that didn't change anything. This is because the key `id` on the todo object is still treated as a string key. We need to wrap it in sqaure brackets to indicate that it is a Symbol.

```
const id = Symbol('id');

const todo = {
    [id]: '001',
    description: 'deploy services',
}

todo.id = '002';
console.log(todo); // { id: '002', [Symbol(id)]: '001', description: 'deploy services' }
```

Now, although `todo.id` still adds a string key `id` to the object, it also has a Symbol `id` key that was not overwritten. We can use the same notation with square brackets to access the the Symbol key.

```
...
console.log(todo.id); // 002
console.log(todo[id]); // 001
```

## Symbols are ignored by most object inspection features

It is worth noting that some JavaScript features that we commonly use with objects simply ignore symbols.

### for...in statement

Let's iterate over the properties of out todo object with a for...in statement.

```
const id = Symbol('id');

const todo = {
    [id]: '001',
    description: 'deploy services',
    date: '01/12/2020'
}

for (const prop in todo) {
    console.log(`${prop}: ${todo[prop]}`);
}

// output
description: deploy services
date: 01/12/2020
```
As you can see, `for...in` ignored the `id` Symbol.

### Object.keys()

Similarly Symbols are ignore by `Object.keys()`.

```
const id = Symbol('id');

const todo = {
    [id]: '001',
    description: 'deploy services',
    date: '01/12/2020'
}

console.log(Object.keys(todo)); // ['description', 'date']
```

### Object.getOwnPropertyNames()

`Object.getOwnPropertyNames()` also ignores Symbols.

```
const id = Symbol('id');

const todo = {
    [id]: '001',
    description: 'deploy services',
    date: '01/12/2020'
}

console.log(Object.getOwnPropertyNames(todo)); // ['description', 'date']
```

### JSON.stringify()

`JSON.stringify()` also leaves out the Symbols.

```
const id = Symbol('id');
const priority = Symbol('priority');

const todo = {
    [id]: '001',
    [priority]: 'high',
    description: 'deploy services',
    date: '01/12/2020'
}

console.log(JSON.stringify(todo)); // {"description":"deploy services","date":"01/12/2020"}
```

### Object.getOwnPropertySymbols()

However, symbols aren't completely invisible. We can get a list of all Symbol properties of an object with `Object.getOwnPropertySymbols()`.

```
const id = Symbol('id');
const priority = Symbol('priority');

const todo = {
    [id]: '001',
    [priority]: 'high',
    description: 'deploy services',
    date: '01/12/2020'
}

console.log(Object.getOwnPropertySymbols(todo)); // [ Symbol(id), Symbol(priority) ]
```

## Global Symbol Registry

Although the `Symbol()` function always returns a unique Symbol, it is possible to use something known as the Global Symbol Registry to create global Symbols shared across the codebase. This can be done by using `Symbol.for()`.

```
const sym1 = Symbol.for('speed');
const sym2 = Symbol.for('speed');

console.log(sym1 === sym2); // true
```

In this instance, `sym1` and `sym2` refer to the exact same Symbol. What `Symbol.for` basically does is, it searches the Symbol registry across the current run-time and creates a new Symbol if it doesn't already exist. In our example, `const sym1 = Symbol.for('speed');` creates a new Symbol since it doesn't alreay exist and `const sym2 = Symbol.for('speed');` simply returns the Symbol that was already created with the same description.


## Real World Use Cases

Speaking of use cases, it is unlikely that you'll come across too many use cases for Symbols in your everyday code although Symbols are used by JavaScript itself internally and also by certain libraries. With that said, you can certainly use Symbols as unique keys in objects to prevent name collisions and also to add meta data to objects that won't be visible to JavaScript features that we commonly use with objects.
