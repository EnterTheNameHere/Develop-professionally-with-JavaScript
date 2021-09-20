# Develop professionally with JavaScript

---

## Chapter 1 : Introduction

## Chapter 2 : Functions and functional aspects

## Chapter 3 : OOP in JavaScript

## Chapter 4 : ECMA 2015 and newer version

## Chapter 5 : The development process

## Chapter 6 : Testing JavaScript applications

## Chapter 7 : Advanced concepts in the object-oriented programming

## Chapter 8 : The design patterns of the Gang of Four

## Chapter 9 : Architectural patterns and concepts of modern JavaScript Web Frameworks

## Chapter 10 : Messaging

## Chapter 11 : Continuous Integration

---

## Chapter 1 : Introduction

Basic JavaScript syntax

## Chapter 2 : Functions and functional aspects

### Features of JavaScript functions

Functions are first-class objects in JavaScript. That means that they can be passed as arguments inside other arguments, they can be assigned to variables, returned from other functions etc. All functions have a name property that represents the name of the function, a length property that represents the number of defined arguments that it has and a prototype property that we'll talk about in chapter 3 when in comes to OOP in JavaScript.

In functions we can use, as well as we can in objects, the keyword ```this```. The keyword ```this``` is based on the context of the function. Example:

```JavaScript
const person = {
    firstName : "Max",
    getFirstName : function(){
        return this.firstName();
    }
}

console.log(person.getFirstName()); // Output : Max
```

In this case ```this``` is the object ```person```. ```this``` is always about the context. If we are not inside any object and use ```this``` on a function, ```this``` points out to the window object:

```JavaScript
getFirstName : function(){
    return this.firstName();
}

console.log(getFirstName()); // undefined
```

Here, we get 'undefined' since we don't have any property named firstName inside the object ```this``` where ```this``` is pointing to.

Functions also build a 'Function-Level-Scope' meaning that we have a defined visibility scope inside functions. Example:

```JavaScript
function example(x){
    if(x){
        var y = 4711;
    }
    for(var i = 0 ; i < 4711; i++){
        // Code
    }

    console.log(y); // 4711
    console.log(i); // 4711
}

example(true);
```

In our case we still get the values because they were defined inside the function level scope. This only works with ```var```, not with ```let```.

In many cases it's better to define all the variables at the beginning of the function. It's a good thing to define all of your variables at the start of the function since you can be sure that you don't repeat yourself. **Declaring all the variables at the start of a function, before using them, is called Variable Hoisting.**

```JavaScript
function example(x){
    var y, i;

    console.log(y);
    console.log(i);

    if(x){
        var y = 4711;
    }
    for(var i = 0 ; i < 4711; i++){
        // Code
    }
}
```

If you choose not to declare the visibility/behavior of your variable by not writing ```let```, ```var``` nor ```const``` before it, it will be seen as a global variable. This is a very bad practice because it can cause name conflicts and other bugs that are very hard to fix.

In JavaScript there is no such thing as proper function overloading. A 'best practice' when it comes to function overloading is setting up a 'config' variable that changes the output of the function. Example:

```JavaScript
function add(x, y, config){
    const result = x + y;
    if(config && config.log){
        console.log(result);
    }

    return result;
}

add(2, 2);
add(2, 2, {log : true});
```

### Standard Methods for JavaScript Functions

In JavaScript, as we said before the keyword ```this``` represents the context that a function is bound to.
All JavaScript functions have some standard functions. This functions are:

- bind
- call
- apply

#### bind

With bind, as well as with the other standard methods, we can change the context of the keyword ```this``` and get that function in return. Example:

```JavaScript
const button = {
    handler : null,
    onClick : function(handler){
        // handler is a callback-handler
        this.handler = handler;
    },
    click : function(){
        this.handler();
    }
}

const handler = {
    log : function(){
        console.log("Clicked on the button.");
    },
    handle : function(){
        this.log();
    }
}

// button.onClick(handler.handle); Wrong, since this points to button, not to handler
button.onClick(handler.handle.bind(handler));
button.click();
```

In this example you can see that if we choose to use ```button.onClick(handler.handle)``` we will get an error because we use ```this.log()``` inside the ```handler.handle``` function, but we use the ```handler.handle``` function inside another context. The context is not ```handler```, the context is ```button``` and since ```button``` doesn't have a function called ```log``` we will get an error. However, if we use the ```bind``` keyword, we can change the reference of ```this``` inside the when we pass ```handler.handle``` function inside the ```button.onClick``` function and we can change it to be ```handler``` since we have the function ```handler.log``` there, so we won't get an error and everything will work the way we want it to.

If we want to pass arguments with ```bind``` we can do it by inserting them as rest arguments:

```JavaScript
function.bind(thisArg)
function.bind(thisArg, arg1)
function.bind(thisArg, arg1, arg2)
function.bind(thisArg, arg1, ... , argN)
```

Bind doens't execute the function, it just returns a new function back with the given context. 

#### call

Call, just like bind, changes the context, the function is bound to. That means that, as well as bind, it changes the meaning of the ```this``` keyword. Unlike ```bind```, call executes the function directly without giving you back a new function with a changed context. Example:

Let's say that you want to have a function that prints all the names it is given ( using the ```arguments``` property that every function has ).

```JavaScript
function printNames(){
    console.log(arguments);

    arguments.forEach((argument) => {
        console.log(argument);
    });
}
```

This will result into an error since the keyword ```arguments``` doesn't fully inherit all the methods of a list.
We can, however, take the ```forEach``` method from the ```Array.prototype``` and apply it to our list of arguments. **This is also known as Method Borrowing**.

```JavaScript
function printNames(){
    console.log(arguments);

    Array.prototype.forEach.call(arguments, function(argument){
        console.log(argument);
    });
}

printNames("a", "b");
```

#### apply

Apply is almost the same as call. The only difference between apply and call is that we have to give the list of arguments inside an array, not as rest parameters.

Example:

```JavaScript
function printNames(){
    Array.prototype.forEach.apply(arguments, [(argument) => {
        console.log(argument);
    }]);
}

printNames("a", "b");
```

#### using standard methods in variadic functions

Variadic functions are functions where the total numbers of parameters is unknown ( where we work with rest parameters ).

An example for a variadic function is ```Math.max()```:

```JavaScript
console.log(Math.max(1, 2)); // 2
console.log(Math.max(1, 2, 3)); // 3
console.log(Math.max(1, 2, 3, 4)); // 4
console.log(Math.max(1, 2, 3, 4, 5)); // 5
```

If we would have a list of numbers that we want to apply this function to, we can't just give the list as an argument. We can however, use apply, for example ( or add numbers as rest argument ):

```JavaScript
const numbers = [1, 2, 3, 4, 5];
// console.log(Math.max(numbers)); // WRONG
console.log(Math.max(...numbers)); // 5
console.log(Math.max.apply(null, numbers)); // 5
```

**It is considered a *best practice* to set the ```this``` argument to be ```null``` if it's not needed.**