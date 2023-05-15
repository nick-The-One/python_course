# OOP in Python, part 1

## Construct a simple class

* Today is a sad day — I'll be using Jupyter from now on, it's just too tedious to work with classes in console
* Let's start with simple class example and go through it line by line to see what's going on

```python
class TestClass:
    d = 'test string'
    e = []
    def __init__(self, a, b):
        self.a = a
        self.b = b
        c = a + b
   
    def method_a(self, x):
        return self.a + x
```



## Class Parts

* Class — object, denoted by the keyword `class`. It is a template, fabricator of instances — creates new instance when called

  * all code inside class is getting executed once `class` reached in execution, kind of like with module imports
  
* Instance — object, created in image of the class, has all attributes and methods of the class available to it

* Attributes are variables, bound to class namespace (class namespace is created by `class` keyword and includes everything inside class, but outside its methods — those are separate namespaces)

  * Class attributes — attributes that are declared directly in class namespace. Will be shared between instances. **ask** Usually used to store class state, constants or defaults — instances count, for example **show with different strings AND with re-creating same instance**

    * all class-level attributes are shared between instances


    ```python
    class TestC:
        instance_count = 0
        def __init__(self):
            TestC.instance_count += 1
    ```
    
    * can save you a nick of time — class attributes are only created once[^1]

  * all attributes, denoted with `self.` are instance-specific and aren't shared between instances — different namespace for each and every instance

  * **ask** changing class-level attribute in instance does not change it for rest of the instances — change is instance-level

  * …except for mutable class-level attributes **show** _use example from beginning_

  * variables in method namespaces without `self` can be accessed only inside that method

  * all attributes are stored in `__dict__`, unique to each instance and class[^2]

  * attributes can be added later with `instance.x = x` or `Class.x = x`. In latter case new attribute will be available to all instances, even already created

* Methods are the same thing as functions, but, again, bound to the namespace of the class. Accepts special `self` argument — instance object, passed implicitely — as first one to give you access to the instance namespace (which opens many doors, as we'll see later)

  * First argument to the methods is instance object, passed implicitely (or you can do it explicitely with `Class.method(instance)`), usualy denoted by `self` 

    * `self` is arbitrary word and can be replaced with anything you want, as long as you pass it as first argument to methods — but using `self` is conventional

  * Constructor — special method, that is run automatically on every new instance creation. Don't need to be in user-classes — inherited from `object` 

  * classmethods takes class object instead of instance object as implicit argument. Allows you to directly access class-level attributes and usefule for that **show**

    ```python
    class TestCLM:
        a = 1
        def __init__(self, a):
            self.a = a
            
        def get_a(self):
            return self.a
    		
        # iterate over this method: self.a, plain a, classmethod a
        @classmethod
        def get_og_a(cls):
            return cls.a
    ```

     Unlike directly accessing class-level attributes, these methods can be inherited, but beware of shared attributes

    ```python
    
    class TestCLMChild(TestCLM):
        a = 0 # without this a will be 1 still
        def hello(self):
            print('hi')

  * static methods — methods, not bound to the instance, but rather class. Can be called from class object (any method). **ask & show** Can be called from instances if decorated with `@staticmethod` or registered with `Class.func_a = staticmethod(func_a)` to avoid implicitely passing instance object. Don't have access to instance attributes, obviously.

  * LEGB does not work the way you think it should work — class attributes are not accessible from methods directly, but we know at least two ways to make it work

[^1]: [Class attributes time save](https://www.toptal.com/python/python-class-attributes-an-overly-thorough-guide)
[^2]: [Class internals](https://rushter.com/blog/python-class-internals/)
[^3]: [Private variables](https://docs.python.org/3/tutorial/classes.html#private-variables)