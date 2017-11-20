---
layout: post
title: JavaScript Class Instantiation Patterns
---

An overview of the 4 Instantiation Patterns - Functional, Functional Shared, Prototypal & Psudoclassical.

```javascript
const HelloWorld = (name) => {
  console.log(`Hello ${name}! Welcome to Kendrick\'s World!`);
}

```

Functional

This is perhaps the simplest to understand:

```javascript

// Constructor:
const Car = (obj, location, speed) => {
  obj.location = 0;
  obj.speed = speed;
  obj.move = function(){ obj.location += 1*speed; };
  obj.honk = function(){ console.log('Beep Beep'); };
  return obj;
};

// Instantiation:
const mazda = Car({}, 0, 3);
const ferrari = Car({}, 0, 10);

```
Functional classes are very easy to understand, as they take an object as an input, assign properties and methods to it, and then return that object.

The problem with functional classes is each instance of the Class will have it's version of every method, which takes up more space in memory each time a new instance of that Class is created. This almost makes it difficult to change the methods for all instances in the Class.

Functional-shared

This seeks to relieve some of the drawbacks of the functional style, by storing all of the Class methods in one location:

```javascript

// Constructor:
const Car = (obj, location, speed) => {
  obj.location = 0;
  obj.speed = speed;
  obj.move = carMethods.move;
  obj.honk = carMethods.move;
  return obj;
};

const carMethods = {};
carMethods.move = function(){ this.location += 1*this.speed; }
carMethods.honk = function(){ console.log('Beep Beep'); };

// Instantiation:
const mazda = Car({}, 0, 3);
const ferrari = Car({}, 0, 10);

```

The functional-shared method is an improvement over the functional method, as the methods *move* and *honk* will only be created once in memory.

The Prototypal Style improves upon this concept by assigning Class methods to the Class constructor's prototype, instead of creating the *carMethods* object:

```javascript

// Constructor:
const Car = (location, speed) => {
  const obj = Object.create(Car.prototype);
  obj.location = location;
  obj.speed = speed;
  return obj;
};

Car.prototype.move = function(){ this.location += 1*this.speed; }
Car.prototype.honk = function(){ console.log('Beep Beep'); };

// Instantiation:
const mazda = Car({}, 0, 3);
const ferrari = Car({}, 0, 10);

```
[*Object.create()*]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create
The [*Object.create()*] function creates a new object and sets the first given argument as the prototype object for that new object. Thus, all instances of Car will fallback to the Car.prototype object when looking up methods. This is similar to, although more efficient, than the functional shared style.

[syntatic sugar]: https://en.wikipedia.org/wiki/Syntactic_sugar
Finally, there is the Pseudoclassical Style of Class instantiation. This style is functionally identical to the prototypal style, but includes [syntatic sugar] that allows for cleaner, more concise code:

```javascript

// Constructor:
const Car = (location, speed) => {
  this.location = location;
  this.speed = speed;
};

Car.prototype.move = function(){ this.location += 1*this.speed; }
Car.prototype.honk = function(){ console.log('Beep Beep'); };

// Instantiation:
const mazda = new Car({}, 0, 3);
const ferrari = new Car({}, 0, 10);

```
