---
layout: post
title: Understanding @synthesize closely
date: 2015-02-27 17:18:00.000000000 +08:00
tags:
- objectiveC
---
I am new to Objective-C and still understanding some of the basics. While
trying to debug an issue at work with a colleague, I had to take a deeper dive
in how synthesize really work. After reading through apple docs and other blogs
here are few of my understandings.

### Basics

* An object's property declared using ``@property`` in ``@interface``
  synthesizes getter & setter methods for other objects to access it.
* ``@property NSString *propertyName`` defaults to synthesizing the
  getter/setter for instance variable ``_propertyName``. Getter as
  ``object.propertyName`` and setter as ``[object setPropertyName]
* ``@synthesize`` directive (declared in ``@implementation``) can be used to
  customize instance variable names.
  e.g. ``@synthesize propertyName = fooInstanceVariable``. Default
  ``@synthesize propertyName`` has same instance name as the property's name.

### Gotchas

* ``@synthesize`` creates instance variables local to the class. It is not
  visible to subclasses.