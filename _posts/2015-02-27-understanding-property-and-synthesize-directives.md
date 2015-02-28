---
layout: post
title: Understanding @property & @synthesize directives
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

### self vs underscore

* ``self.propertyName`` or ``object.propertyName`` calls setter/getter
* ``_propertyName`` is for direct access

If you override setter of getter method you should use underscore to accessing
or updating the value. If you use self notation you would end up in a recursive
loop.

  ```objective-c
  - (void)setName:(NSString *)newName {
  	NSLog(@"Changing name from %@ to %@", _name, newName);
  	self.name = newName; // This will result in infinite loop
  }
  ```

### Gotchas

* ``@synthesize`` creates instance variables local to the class. It is not
  visible to subclasses. Consider the following example:

	```objective-c
	//Automobile.h
	@interface Automobile : NSObject
	@property (strong) NSString *name;
	@end

	//Automobile.m
	@implementation Automobile
	@synthesize name;
	- (instancetype) initWithName:(NSString *)aName {
		if (self = [super init]) {
			self.name = aName;
		}
	  return self;
	}
	@end
    ```
	```objective-c
	//FourWheeler.h
    @interface FourWheeler : Automobile
    @end

    //FourWheeleer.m
    @implementation FourWheeler
    - (void)accessingName {
        //  Note: The following gives a Semantic Issue
        NSLog(@">> name is an ivar and is not visible to subclass :: %@", name);
        
        //  Note: The following line works as we are accessing the property of
        //  parent class.
        NSLog(@">> self.name is a prop and is visible to subclass :: %@", self.name);
    }
    @end
	```
* property is a public legitimate access to the private instance variable
  (via getter/setter) it automatically synthesizes. If you have synthesized a
  property in both base and sub class, accessing it using an instance variable
  in base class method, when called from object of subclass would have null
  value. Consider the following example:

	```objective-c
	//Automobile.h
	@interface Automobile : NSObject
	@property (strong) NSString *name;
	- (NSString *)decorateName;
	@end

	//Automobile.m
	@implementation Automobile
	@synthesize name;
	- (instancetype) initWithName:(NSString *)aName {
		if (self = [super init]) {
			self.name = aName;
		}
	  return self;
	}
	- (NSString *)decorateName {
    return [NSString stringWithFormat:@">>%@<<", name];
	}
	@end
    ```
	```objective-c
	//FourWheeler.h
    @interface FourWheeler : Automobile
    @end

    //FourWheeleer.m
    @implementation FourWheeler
    - (void)printDecoratedName {
        NSLog(@">>via Automobile#decorateName :: %@", self.decorateName);
    }
    @end
	```
	```objective-c
	//TwoWheeler.h
    @interface TwoWheeler : Automobile
    @end

    //TwoWheeleer.m
    @implementation TwoWheeler
    @sythesize name; //This synthesizes a private instance variable name
    - (void)printDecoratedName {
        NSLog(@">>via Automobile#decorateName :: %@", self.decorateName);
        // This would print ">>via Automobile#decorateName :: >>(null)<<"
    }
    @end
	```

I have played around a bit, you can checkout and try other cases:
[https://github.com/singhgarima/Synthesize]()