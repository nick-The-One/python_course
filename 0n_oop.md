# OOP in Python Intro

## The big picture

* OOP vs Functional vs Procedural **Ask**

  * Procedural
    * derived from Imperative paradigm — you describe steps to achieve goal; unlike declarative, which describes goal, but not the logic **Ask for examples** like SQL
    * based on changing program state, reusing code modules (procedures in this case)
  * Functional — math
    * avoids state and mutability
    * treats functions as their result
    * composing functions
    * no fuctions side effects — functions call do not change shared states
    * declarative — you have little to no control of the flow
    * I'm not smart enough to understand it, let alone explain; but [this](https://stackoverflow.com/questions/23277/what-is-the-difference-between-procedural-programming-and-functional-programming) got me really curious and I might give it a try.

  * OOP — way of writing code, that relies on describing objects that include both data and procedures
    * derived from procedural
    * Why would *you* use OOP? **Ask**. I don't know, and frankly, my dear, I don't give a damn.

## OOP Features

**Ask about four pillars**

* Encapsulating and abstraction

  * Everything related to the class is in the class, this is encapsulation. It's inner workings are hidden from end-user — you free to change them however you want if interface is unchanged — this is abstraction. This might not be right, because I've checked about dozen sources and got dozen different answers.

    * Good example of both would be a phone:
      * pressing a button is an *abstract* action that has nothing to do with the principles on which phone work, but provides us with simple and understandable interface
      * the phone itself, as a device, *encapsulates* inside ton of complex circuitry than makes abovemention abstraction possible

  * Private and public attributes and methods

    * Public methods are exposed outside and make an interface

    * Private methods providing «inner workings»

    * **Ask** How do we define private members in Python? Well, we can't  # TODO: move this to a latter lecture

    * Gentlemen's agreement `_var`

    * Or can we? Name mangling

      ```python
      class A:
        def __init__(self):
          __x = 1
          y = 2
        
        def __func_private(self, a):
          return self.__x + 1
        
        def func(self, a):
          return self.__func_private(a)
        
      ia = A()
      ia.func(2)
      >>> 3
      ia.y
      >>> 2
      ia.__x
      >>> AttributeError: 'A' object has no attribute '__x'
      ia.__func_private(2)
      >>> AttributeError: 'A' object has no attribute '__func_private'
      ia._A__x
      >>> 1
      ia._A__func_private(2)
      >>> 3
      ```

    * Why does one need private methods and attributes anyway? **Ask** They might modify state in a way we do not want if used not internally.

    * To sum it all up — everything in Python is public if you know where to look

* Inheritance — simpler concept out of four. Child classes inherit their attributes and methods from parent classes

  * Direct inheritance — self explanatory. Child is made in an image of parent (container -> lunchbox)
  * Composition — class does not directly inherit other one, but rather uses it as part of itself (car -> van <- container)
  * Delegation — I really don't know[^1]

* Polymorphism — different objects have same interface

  * method overriding/overloading in child classes
    * Animal — voice; cat — meow

## OOP in Python

* Is Python object-oriented? **ask** Well, it is. But it isn't strictly OOP language  — since it was concieved as scripting language, you can use it for production level tasks without writing a class once. Furthermore, if you check [wiki article](https://en.wikipedia.org/wiki/Comparison_of_programming_paradigms) on programming paradigmes, you'll find python as example to almost every one.
* The only one strictly OOP language seems to be SmallTalk, everything is an object — oh, wait… But it's not the same kind of object. Let's see what Guido van Rossum has to say about it:

> One of my goals for Python was to make it so that all objects were "first class." By this, I meant that I wanted all objects that could be named in the language (e.g., integers, strings, functions, classes, modules, methods, etc.) to have equal status. That is, they can be assigned to variables, placed in lists, stored in dictionaries, passed as arguments, and so forth.[^2]

* This would be a nice cutoff point, but let's write simplest python class just to see how it goes

```python
class SimpleClass:
  def __init__(self, a, b):
    self.a = a
    self._b = b
    self.__c = 1
    
  def _internal_func_a(self, x):
    return self.a + x
  
  def __internal_func_b(self, x):
    return self._b - x
  
  def func(self, a, b):
    return self._internal_func_a(a) * self.__internal_func_b(b)
```

* `__init__` is class constructor — defines the way new instances will be created

* various `func` functions are class methods **ask** what's the difference between them? (public, «private», mangled)
* `self` refers to the instance of the class
* `a, b, c` are attributes



[^1]: https://erikscode.space/index.php/2020/08/01/delegate-and-decorate-in-python-part-1-the-delegation-pattern/
[^2]: http://python-history.blogspot.com/2009/02/first-class-everything.html

Mono — Hymn To The Immortal Wind; 3:20 for 780 words + head hurts