---
layout: post
title: JavaScript Class Instantiation Patterns
---

An overview of the 4 JavaScript Class Instantiation Patterns - Functional, Functional Shared, Prototypal & Psuedoclassical.


# Functional
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

The potential problem with functional classes is that each instance of the Class will have it's own copy of every method, which takes up more space in memory each time a new instance of that Class is created. This also makes it more cumbersome to change class methods in the future, as all current instances would have to be manually updated to reflect the changed methods.

The primary advantage of the functional style is that it does not use the **this** keyword, which can be a source of confusion and misunderstanding for developers. This style is ideal for objects that either do not have methods, or that will not be instantiated many times.


# Functional-Shared
```javascript
// Constructor:
const Car = (obj, location, speed) => {
  obj.location = 0;
  obj.speed = speed;
  obj.move = carMethods.move;
  obj.honk = carMethods.move;
  return obj;
};

// Methods:
const carMethods = {};
carMethods.move = function(){ this.location += 1*this.speed; }
carMethods.honk = function(){ console.log('Beep Beep'); };

// Instantiation:
const mazda = Car({}, 0, 3);
const ferrari = Car({}, 0, 10);
```

This **Functional-shared** style seeks to relieve the main drawback of the functional style by storing all of the Class methods in one location. The methods *move* and *honk* will be created only once in memory.


# Prototypal
```javascript
// Constructor:
const Car = (location, speed) => {
  const obj = Object.create(Car.prototype);
  obj.location = location;
  obj.speed = speed;
  return obj;
};

// Methods:
Car.prototype.move = function(){ this.location += 1*this.speed; }
Car.prototype.honk = function(){ console.log('Beep Beep'); };

// Instantiation:
const mazda = Car({}, 0, 3);
```

The prototypal style further improves upon this concept by assigning Class methods directly to the Class constructor's prototype, instead of creating the *carMethods* object.

The [*Object.create()*] function creates a new object, setting the first given argument as the prototype object for that new object. Thus, all instances of Car will fallback to the Car.prototype object when looking up methods. This is similar to, although more efficient, than the functional-shared style.


## Psuedoclassical
```javascript
// Constructor:
const Car = (location, speed) => {
  this.location = location;
  this.speed = speed;
};

//Methods
Car.prototype.move = function(){ this.location += 1*this.speed; }
Car.prototype.honk = function(){ console.log('Beep Beep'); };

// Instantiation:
const mazda = new Car({}, 0, 3);
```

Finally, there is the pseudoclassical style of Class instantiation. This style is functionally identical to the prototypal style, but includes [syntatic sugar] that allows for cleaner, more concise code. As you can see, the constructor for this style is much more concise than in previously reviewed styles. Under the hood, the compiler interprets the **new** keyword and implicitly creates an object via *Object.create()* and then implicitly returns that object.


[*Object.create()*]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create
[syntatic sugar]: https://en.wikipedia.org/wiki/Syntactic_sugar
