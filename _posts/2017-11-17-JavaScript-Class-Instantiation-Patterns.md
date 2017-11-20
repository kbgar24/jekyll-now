---
layout: post
title: JavaScript Class Instantiation Patterns
---

An overview and comparison of the four JavaScript Class Instantiation patterns.

# Functional
```javascript
// Constructor
const Car = (obj, location, speed) => {
  obj.location = 0;
  obj.speed = speed;
  obj.move = function(){ obj.location += 1*speed; };
  obj.honk = function(){ console.log('Beep Beep'); };
  return obj;
};

// Instantiation
const mazda = Car({}, 0, 3);
```
Functional classes are very easy to understand, as they take an object as an input, assign properties and methods to it, and then return that object.

The potential disadvantage of functional classes is that each instance of the Class will have it's own copy of every method, which will take up more space in memory each time a new instance of that Class is created. This also makes it more cumbersome to change Class methods in the future, as all current instances would have to be manually updated to reflect the changed methods.

The primary advantage of the functional style is that it does not use the **this** keyword, which can be a source of confusion and misunderstanding for developers. This style is ideal for objects that either do not have methods, or that will not be instantiated many times.


# Functional-Shared
```javascript
// Constructor
const Car = (obj, location, speed) => {
  obj.location = 0;
  obj.speed = speed;
  obj.move = carMethods.move;
  obj.honk = carMethods.move;
  return obj;
};

// Methods
const carMethods = {};
carMethods.move = function(){ this.location += 1*this.speed; }
carMethods.honk = function(){ console.log('Beep Beep'); };

// Instantiation
const mazda = Car({}, 0, 3);
```

The functional-shared style seeks to relieve one drawback of the functional style - by storing all of the Class methods in one location. The methods *move* and *honk* will be created only once in memory. Nevertheless, the functional-shared style does share one drawback with its functional cousin: whenever methods on *carMethods* are added, changed, or removed, already instantiated objects of the *Car* class will not be automatically updated.


# Prototypal
```javascript
// Constructor
const Car = (location, speed) => {
  const obj = Object.create(Car.prototype);
  obj.location = location;
  obj.speed = speed;
  return obj;
};

// Methods
Car.prototype.move = function(){ this.location += 1*this.speed; }
Car.prototype.honk = function(){ console.log('Beep Beep'); };

// Instantiation
const mazda = Car({}, 0, 3);
```

The prototypal style improves upon this concept by assigning Class methods directly to the Class constructor's prototype, instead of creating the *carMethods* object.

The [*Object.create()*] function creates a new object, setting the first given argument as the prototype object for that new object. Thus, all instances of *Car* will fallback to the *Car.prototype* object when looking up methods. The use of fallback is much more efficient than the solutions employed in the functional and functional-shared styles, as any changes to the methods on the *Car.prototype* object will be immediately accessible to all already-instantiated instances of the *Car* class.


# Psuedoclassical
```javascript
// Constructor
const Car = (location, speed) => {
  this.location = location;
  this.speed = speed;
};

//Methods
Car.prototype.move = function(){ this.location += 1*this.speed; }
Car.prototype.honk = function(){ console.log('Beep Beep'); };

// Instantiation
const mazda = new Car({}, 0, 3);
```

Finally, there is the pseudoclassical style of Class instantiation. This style is functionally identical to the prototypal style, but includes [syntactic sugar] that allows for cleaner, more concise code. As you can see, the constructor for this style is much more concise than in previously reviewed styles. Under the hood, the compiler interprets the **new** keyword and implicitly creates an object via *Object.create()* and then implicitly returns that object.

# Final Thoughts

I recommend using the functional instantiation pattern for code clarity and avoiding unnecessary usage of the **this** keyword. However, if many, many instances of an object are needed, and they share the same methods, than using the pseudoclassical is most appropriate.

[*Object.create()*]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create
[syntactic sugar]: https://en.wikipedia.org/wiki/Syntactic_sugar
