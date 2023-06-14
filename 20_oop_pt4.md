# OOP in Python, part 3
## Operator overloading basics
* Just as regular methods can be overwritten (== overloaded) in children classes, so can be magic, or dunder ones
* Magic methods are methods that correspond to particular operators and not meant to be called directly, such as `__add__` that corresponds to `+`
* Magic methods are identified by double underscores surrounding their names — and it's a good idea to not use double underscores in user methods to not overload something by accident
## Magic methods
### Instance creation and initialization
* First magic method that we are familiar with is `__init__`, which is called on class instance creation `i = Class()`
* This init method is often called a constructor and usually describes logic that sets values for each instance
* Interesting thing to note: `__init__` takes an instance as a first argument, which shouldn't be possible since it's supposed to create it
* Turns out that `__init__` is actually called second in instance creation — and the name is a bit of giveaway: it does not creates *new* instance, but initializes it with some values
* What it actually called at instance creation is dunder method `__new__`, which takes class and all init arguments as arguments
* It's rarely used compared to `__init__` and we wouldn't focus much on it
* What is probably used even more rare, is `__del__` method, which is called not when instance is deleted with `del i`, but rather when it's being garbage collected
### Comparison
* This set of methods helps to define relations between instances of the same class: methods like equal, not equal, more, less etc
* Defining equality comparison automatically defines non-equality, but according to several source it's not the case with the rest and they either should be all defined, or equality and one of the ordering operators and use `functools.total_ordering()` class decorator
* Comparison operators are used in ordering of the objects in sorting
* Let's define all of the rich comparison operators for custom class (all of them take two instances as arguments):
```python
class RichComp:
    def __init__(self, a: int) -> None:
        self.a = a
        
    def __eq__(self, other) -> bool:
        return self.a == other.a
    
    def __lt__(self, other) -> bool:
        return self.a < other.a
    
    def __gt__(self, other) -> bool:
        return self.a > other.a
    
    def __le__(self, other) -> bool:
        return self.a <= other.a
    
    def __ge__(self, other) -> bool:
        return self.a >= other.a
```

### Representation

* These methods are used to represent instance in different contexts: string, representation or length

* String representation is implemented with two methods: `__str__` and `__repr__`

* `__str__` is used by `print()` function and `str()` conversion, while `__repr__` is used in interactive representation (e.g. `>>>instance` in repl will show result of `__repr__`), by `repr()` function, in nested output and as fallback if `__str__` is not defined

  ```python
  class Repr:
      def __str__(self) -> str:
          return 'this is string representation of the class'
  
  print(Repr())
  print([Repr()])
  
  class Repr:
      def __repr__(self) -> str:
          return 'this is representation of the class'
      
  print(Repr())
  print([Repr()])
  ```

* `__bool__` defines Boolean representation of the instance, `__len__` it's lenght and is used to determine truthfullness of the object if no `__bool__` is present. If none are defined, object is considered to be true

* `__index__`, despite the name, is used to represent object as a number

### Indexing, slicing and iteration

* To enable indexing and slicing of the object, `__getitem__` is used

* It takes index or *slice object* as second argument and have to return either single value for indexing or iterable for slicing

* It also should handle typechecking for index argument
  ```python
  class IndexableClass:
      def __init__(self, upper_boundary: int) -> None:
          self.ub = upper_boundary
          self.data = list(range(upper_boundary+1))
          
      def __getitem__(self, idx: int|slice) -> int|list:
          if not (isinstance(idx, slice) or isinstance(idx, int)):
              raise TypeError(f'Index of type {type(idx)} is not supported')
          if isinstance(idx, slice):
              return self.data[idx.start:idx.stop:idx.step]
          else:
              return self.data[idx]
  ```

* `__setitem__` is used in indexed assignment

* `__getitem__` is used as fallback for iteration protocol: iteration context will index values from object one by one until `IndexError` is encountered

* But proper way to implement iteration protocol is by implementing `__iter__` method that returns an object that implements  `__next__` method and throws `StopIteration` when appropriate, be it the same object that returns self, or separate object. `__iter__` is given priority over `__getitem__`:
  ```python
  class Iter:
      def __init__(self, i: int) -> None:
          self.i = list(range(i))
          self.current = -1
          self.stop = i - 1
          
      def __iter__(self):
          return self
      
      def __next__(self) -> int:
          if self.current == self.stop:
              raise StopIteration
          self.current += 1
          return self.i[self.current]
  ```

* Self approach has one con: it can be only iterated over once since instance stores state of iteration inside:
  ```python
  it = Iter(5)
  for i in it: print(i)
  for i in it: print(i)
  ```

* To prevent this, iteration object should be implemented separately:
  ```python
  class IterIterationObject:
      def __init__(self, i: int) -> None:
          self.i = list(range(i))
          self.current = -1
          self.stop = i - 1
      
      def __next__(self) -> int:
          if self.current == self.stop:
              raise StopIteration
          self.current += 1
          return self.i[self.current]
      
  class Iter:
      def __init__(self, i: int) -> None:
          self.i = i
  
      def __iter__(self):
          return IterIterationObject(self.i)
      
  it = Iter(5)
  for i in it: print(i)
  for i in it: print(i)
  ```

* Method `__contains__` defines membership check, but if it's not defined, it has not one, but two fallbacks: `__iter__` and `__getitem__`: if no `__contains__` is defined, membership test will go over all elements and compare it to required value

### Getting and setting attributes

* Once in while you might need to have a complete control over how class attributes are accessed, set or even deleted

* And of course Python lets you to have that kind of control, and as it is with the most things, in a multitude of ways of varying complexity[^1]

* Let's use this completely made up task as an example: 
  * `Person` class that has an attribute `name`
  * `name` should be specified at instance creation
  * `name` attribute should always return reversed and capitalized version of what was specified at creation
  * if `name` was removed, it should be dispalyed as `John Doe`

* First way to approach this would be using getter and setter methods, just like any other language

  * This has it's own drawbacks, some of which are philosophical, but some are pretty concrete:

    * This is considered to be non-pythonic way of doing things, since Python allows direct access to any attributes
    * One getter-setter pair (and deleter in our case) is required for every attribute

  * But the pros are also there and pretty important:

    * Methods like these clearly show that there's some processing involved with the attributes, and user should not expect instant reaction
    * They are named and explicit about all the processing for other developers
    * Can be easily inhereted

  * Solution
    ```python
    class PersonGS:
        def __init__(self, name: str) -> None:
            self.set_name(name)
            
        def get_name(self) -> str:
            return self.name.capitalize()
        
        def set_name(self, name: str) -> None:
            self.name = name[::-1]
            
        def del_name(self) -> None:
            self.name = 'john doe'
    ```

* More Pythonic way, and connected the most with current topic would be overloading magic methods that are responsible for getting and setting attributes: `__getattr__`, `__setattr__` and `__getattribute__`

  * `__getattr__` intercepts access to the attributes that are not present in the instance or class dicts

    ```python
    class GetAttrClass:
        def __init__(self) -> None:
            self.a = 1
            
        def __getattr__(self, name: str) -> str:
            return 'this attribute does not exist'
    ```

  * `__setattr__` intercepts all attribute assignments, including ones inside itself

    ```python
    class BadlyDesignedClass:
        def __init__(self) -> None:
            self.a = 1
            
        def __setattr__(self, name: str, val: any) -> None:
            print(f'Setting attribute {name}={val} using __setattr__')
            self.name = val
    
    # this causes stack overflow due to infinite recursion since setattr tries to call itself over and over
    ```

  * solution to this problem is using `__dict__` assignment or `object` one
    ```python
    class OKDesignedClass:
        def __init__(self) -> None:
            self.a = 1
            
        def __setattr__(self, name: str, val: any) -> None:
            print(f'Setting attribute {name}={val} using __setattr__')
            self.__dict__[name] = val
            # or
            object.__setattr__(self, name, val)
    ```
    
  * Those methods are applicable to a dot notation access, but will not be triggered by using `__dict__` access

  * Before moving to `__getattribute__`, two notes should be made about `__getattr__`: 

    * methods lookup is also handled by these methods
    * it will be skipped in lookup chain on implicit or `__getattribute__`

    ```python
    class A:
        def __getattr__(self, name: any) -> type:
            return int
        
    a = A()
    a[1]
    >>> TypeError: 'A' object is not subscriptable
    # a[1] == a.__getitem__(1) --> a.__getattr__(__getitem__)(1) -- doesn't work
    a.__getattribute__(1)
    >>> AttributeError: 'A' object has no attribute '1'
    a.__getitem__(1)
    >>> 1
    # same call, but made explicitely, works
    ```

  * thus you need to implement every magic method if you want it to work

    * This note is here because it wasn't a case in Python 2, but this is no longer relevant 

  * Most dangerous method of them is `__getattribute__` which instercepts all of attribute access calls, and hands it off to `__getattr__` only if the attribute can't be found in the namespaces[^2]

  * Dangerous part of method is that it is called on every attribute access, including dictionary ones (since first step in that is accessing dictionary, which is an attribute), so `__dict__` trick from `__setattr__` wouldn't work

  * Only way around it, which is also recommended by docs[^91], is using `object.__getattribute__(self, attr)` — this method is special and skips instance lookup completely and it would work just fine

    * using `object` methods both here and in `__setattr__` will also ensure that your class will work *slots* — remember that for future

  * Solution using overloading:
    ```python
    class PersonOL:
        def __init__(self, name: str) -> None:
            self.name = name
            
        def __getattr__(self, attr: str) -> None:
            print(f'what even is {attr}?')
            
        def __setattr__(self, attr: str, val: any) -> None:
            if attr == 'name':
                val = val[::-1]
            self.__dict__[attr] = val
            
        def __getattribute__(self, attr: str) -> any:
            val = object.__getattribute__(self, attr)
            if attr == 'name':
                val = val.capitalize()
            return val
        
        def __delattr__(self, attr: str) -> None:
            if attr == 'name':
                self.name = 'john doe'
            else:
                del(self.__dict__[attr])
    ```

  * Solution is pretty pythonic and natural to use, but will be more and more elaborate as number of attributes grows

  * More and more complex will also slow down attributes access, which is looked down upon

  * It also complicates inheritance since one needs to redefine entire magic method to add new logic to it

* Properties are another pythonic way to define custom getter and setters using decorators
  ```python
  class PersonProp:
      def __init__(self, name: str) -> None:
          self._name = name
          
      @property
      def name(self) -> str:
          return self._name.capitalize()
      
      @name.setter
      def name(self, name: str) -> None:
          self._name = name[::-1]
          
      @name.deleter
      def name(self) -> None:
          self._name = 'John Doe'
  ```

  * Function named same as desired attribute is decorated with `@property` to define getter, then `@name.setter` and `@name.deleter` can be used to define according methods

  * This allows us to move custom logic for different attributes to separate functions to make it look cleaner

  * Main drawbacks are amount of such functions — one to three need to be defined for each attribute even if they are the same; and the fact that all of these functions has to be redefined in children classes if any of them is redefined: they all parts of the same *descriptor* object and redefining one redefines entire object:
    ```python
    class PropChild(PersonProp):
        @property
        def name(self) -> str:
            return self._name.upper()
       
    pc = PropChild('james')
    pc.name
    >>> JAMES
    pc.name = 'jane'
    >>> AttributeError: can't set attribute 'name'
    ```

* Descriptor is the object, on top of which properties are built and allows to define custom logic for getters etc for class attributes[^3]

  * Desriptor object is defined by desriptor protocol, which includes three main methods: `__get__`, `__set__` and `__delete__` and is considered implemented if any or all of them are implemented

  * This, though, will only work if such object is used as class variable

  * Important note: descriptor that only implements `__get__` method is non-data descriptor, and if it implements anything else — it becomes data descriptor. This is important for data lookup chain

  * Solution using descriptors:
    ```python
    class PersonDescriptor:
        def __get__(self, obj, objtype=None) -> any:
            return obj._name.capitalize()
        
        def __set__(self, obj, value: str) -> None:
            obj._name = value[::-1]  # _name is used to avoid infinite recursion
            
        def __delete__(self, obj) -> None:
            obj._name = 'john doe'
            
    class Person:
        name = PersonDescriptor()
        
        def __init__(self, name: str) -> None:
            self.name = name
    ```

  * This solution will allow us to access attribute in pythonic way, while also reusing same custom logic for multiple attributes and having less headache with inheritance

  * There's also cool method `__set_name__` that allows one to set name for attribute and reference it later[^4]

  * Data descriptors take precedence over instance dict in lookup chain, non-data don't

  * Lookup chain for attribute search looks like this: data descriptors -> instance dict -> non-data descriptors -> class dict -> getattr -> parent[^5]


### And others

* There are many more magic methods: math, rightside math, conversions, bitwise and unary operators — total about of half a hundred[^6]

[^1]: https://realpython.com/python-getter-setter
[^2]: https://docs.python.org/3/reference/datamodel.html#special-method-lookup
[^3]: https://realpython.com/python-descriptors/
[^4]: https://docs.python.org/3/howto/descriptor.html#customized-names
[^5]: https://docs.python.org/3/howto/descriptor.html#invocation-from-an-instance
[^6]: [Magic methods](https://rszalski.github.io/magicmethods/)
