# Technical Writing Assignment

For guidance on setting up and submitting this assignment, refer to the Marcy lab School Docs How-To guide for [Working with Short Response and Coding Assignments](https://marcylabschool.gitbook.io/marcy-lab-school-docs/fullstack-curriculum/how-tos/working-with-assignments#how-to-work-on-assignments).

## Prompt 1

Imagine you are teaching a friend about OOP. They mainly want to understand what is Encapsulation. Write a brief lesson on Encapsulation that includes the following:

* What is encapsulation?
* What major goal does this help to achieve in software engineering?
* Give an example (in code) of encapsulation.
* An explanation of how the code example demonstrates encapsulation

### Response 1

## Prompt 2

The following `friendsManager` object is an example of an interface that is **NOT** consistent and predictable:

```js
const friendsManager = {
 friends: [],
 addFriend(newFriend) {
   if (typeof newFriend !== 'string') return;
   this.friends.push(newFriend);
 }
}


friendsManager.addFriend('daniel');
friendsManager.addFriend(true);
friendsManager.friends.push('emmaneul');
friendsManager.friends.push(42);
```

Explain how the code is not consistent or predictable, then provide an example in code that uses closure to make it more consistent and predictable.

### Response 2

## Prompt 3

With OOP in JavaScript, it's possible to use factory functions to achieve encapsulation and re-use them to make objects that look alike. However, factory functions have drawbacks and we often use classes instead. 

How would you explain to a budding developer what the drawbacks of using factory functions are and why it is better to use classes instead?

### Response 3
If we were to consider purely the functionality and capability that you could achieve with utlizing exclusively either factory functions or classes, there is practically no difference (save for losing the ability to track prototype chains and identify instances with `instanceof`). In fact it's not a good dichotomy in the first place---these two approaches are closely related but not identical design patterns. Surprisingly, it is the best hygiene to use both for various reasons! But for the sake of this question, we can, for now, think of classes as syntactic sugar (which is NOT demeaning to say!) that helps with maintainability and readability.

We turn to factory functions and classes when we want to establish a clear blueprint for a custom object. A factory function defines this pattern with no additional specifications besides the general shape of the resulting object. Consider the below:

```JavaScript
const personFactory = (name, age) => {
  return {
    name,
    age,
    greeting() {
      console.log(`Hello there! I'm ${this.name} and I'm ${this.age} years old.`);
    }
  }
}

// demo
const bob = personFactory('Bob', 29);
bob.name; // 'Bob'
bob.age; // 29
bob.greeting(); // logs --> "Hello there! I'm Bob and I'm 29 years old."
```

This is pretty reasonable, and in small codebases, this is a valid way to introduce structure without being too uptight about it. Although when we define custom objects in this way, we can't leverage `instanceof` in order to determine a custom type.

```JavaScript
bob instanceof Person; // false
```

What this implies is the need to have a comprehensive way to check each specific property of objects in order to determine that they're a person created by `personFactory`. Classes are used to skip over this inconvenience while promoting better code hygiene and readability.

```JavaScript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  greeting() {
    console.log(`Hello there! I'm ${this.name} and I'm ${this.age} years old.`);
  }
}

// demo
const bob = new Person('Bob', 29);
bob.name; // 'Bob'
bob.age; // 29
bob.greeting(); // logs --> "Hello there! I'm Bob and I'm 29 years old."

bob instanceof Person; // true!!
```

So in the context of classes, where do factory functions fit in? One neat way to utilize them is for when you need generics of a specific class! This can range from creating completely randomized instances, to slightly different variations of a class that is not distinct enough to create an entire new class for.

```JavaScript
const createRandomPerson = () => {
  return new Person(
    randomNameList[Math.random() * randomNameList.length], // a random name from a predetermined list of names
    Math.random() * 80 // a random age
  )
}

// demo
const random = createRandomPerson();
random.name; // some generated name
random.age; // some generated age
random.greeting(); // logs --> "Hello there! I'm {name} and I'm {age} years old."

random instanceof Person; // true !!
```

## Prompt 4

Do some research on the history of when / how classes were introduced into JavaScript and share your findings. Your response should include:

* What version of JavaScript were classes introduced in and when did it come out?
* Why were classes introduced into JavaScript?


### Response 4

## Prompt 5

OOP can still be achieved in JavaScript without using the `class` keyword and instead using the "Constructor Functions" and the "Prototype Chain" (look them up!)

```js
function Person(name, age) {
 this.name = name;
 this.age = age;
}


Person.prototype.greet = function () {
 return `Hi, I'm ${this.name}, and I'm ${this.age} years old.`;
};


const alice = new Person('Alice', 30);
console.log(alice.greet());
```

Provide one point that advocates for the use of this syntax and then provide a counter-argument for the use of classes instead.

### Response 5

Besides the fact that writing classes in this specific syntax demonstrates an understanding of how prototype chains work, a practical application would probably be the ability to dynamically modify a class' behavior during runtime. However that violates a lot of expectations when using an object-oriented approach, specifically encapsulation, because there is no longer a simple way to understand an outline of a class if you had to find all the ways it could change in deployment.

```JavaScript
function Person(name, age) {
 this.name = name;
 this.age = age;
}

const alice = new Person('Alice', 30);

setTimeout(() => {
  console.log(`Greeting isn't allowed in the first 10s...`);
  Person.prototype.greet = function () {
    return `Hi, I'm ${this.name}, and I'm ${this.age} years old.`;
  };
}, '10000')

console.log(alice.greet());
```

In my opinion, using the `class` syntax is preferred even if you needed to somehow "mutate" a certain class! Instead of adding/removing properties/methods directly in the prototype, one could simply set up validation schemes from within the base class itself. This can simulate the same idea of a "changing" class at runtime, without the expense of readability and maintainability.

```JavaScript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
    this.createdAt = Date.now();
  }
  greet() {
    if (this.createdAt+10000 > Date.now()) {
      return `Greeting isn't allowed in the first 10s...`;
    }
    return `Hi, I'm ${this.name}, and I'm ${this.age} years old.`;
  }
}
const alice = new Person('Alice', 30);
console.log(alice.greet());
```
