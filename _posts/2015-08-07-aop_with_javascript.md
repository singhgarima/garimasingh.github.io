---
layout: post
title: AOP with JQuery
date: 2015-08-07 14:33:00.000000000 +08:00
tags:
- aop js
---
At my current project we did some aspect-oriented programming for adding
logging framework. Project we worked on is a cordova based ios application.
Most of the project is html and angular-js application which parts of it as
native. To ensure we don't have logging logic scattered across the code we used
aop approach for the adding logging feature.

I have been wanting to play around with basic aop with JQuery code. This is my
attempt at it.

For those hearing about AOP for the first time, this is a quick definition I pulled

> Aspect-Oriented Programming (AOP) complements OO programming by allowing the
> developer to dynamically modify the static OO model to create a system that can
> grow to meet new requirements. Just as objects in the real world can change
> their states during their lifecycles, an application can adopt new
> characteristics as it develops.
> We can often keep this additional code in a single location rather than
> having to scatter it across the existing model, as we would have to if we
> were using OO on its own.
>
> Overview of Aspect Oriented Programming by Graham O'Regan
[http://www.onjava.com/pub/a/onjava/2004/01/14/aop.html]()

## Key Terms

* Aspect: cross-cutting concerns in stand-alone modules
* Advice: action taken by the aspect
* Join-point: a point during the execution of a program
* Pointcut: a join-point where advice should be executed

## Code

[https://github.com/singhgarima/js-aop]()

### My Approach

* I have introduced few extension methods (`after`, `before` and `around`), by
modifying the prototype property of Function type. This modifies all functions
created using `new Function(...)` or `function (...) {...}`, and hence
introduces these extendion methods on all objects of type Function. (A really
good introduction to prototypes can be found [here](http://yehudakatz.com/2011/08/12/understanding-prototypes-in-javascript/)).

* In these extension functions, we are modifying the original definition of the
exisiting methods (after/before/around which we want to introduce our advice).
We are getting the original definition, from prototype chain on the object. In
the modified definition, we call our advice function, at the expected pointcut.