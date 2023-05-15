# OOP in Python, part 2

## Class methods typing

* To type `self` in methods, future annotations can be used:
  ```python
  from __future__ import annotations
  
  class A:
      def __init__(self: A, a: int) -> None:
          self.a = a
  ```

* This was planned to be included into Python 3.10 as default, but was postponed

* It postpones evaluation of annotations, so undefined yet types will be represented by strings

## Inheritance

* Inheritance is a crucial mechanism of OOP, one of the staples

* Inheritance is an «is a» relationship, where derived (child) class IS A specialized version of the base (parent) class

* It allows child class to «inherit» methods and attributes from parent class without explicitely copying code from one class to another — which would defeat the purpose of OOP for the most parts

* Instead all of the attributes and methods are available for the child classes from the parent implicitely

* To specify from which class to inherent, in the class creation specify all parents classes in the brackets after class name (function arguments style):
  ```python
  class Parent:
      a = 'parent'
      def method_a(self) -> None:
          print('Parent method called')
          
  class Child(Parent):
      b = 'child'
      def method_b(self) -> None:
          print('Child method called')
  ```

* For child the class all of the methods and attributes are available: both `a` and `method_a`, as well as its own

* All of the methods (including init) and attributes are inherited and will be available, unless overwritten

* All of the classes, except for Exception classes, inherit from `object` class implicitely. Exception classes should be inherited (explicetely) from `BaseException`

* Inheriting from `object` allows to omit writing some methods, such as `__init__`, `__str__` etc

* You can inherit from several classes at once, but that poses another problem: init will be inherited only from one, so you need to consider that, as well as attribute names clashing
  ```python
  class ParentA:
      a = 'parent A'
      def __init__(self, b, c) -> None:
          print(f'b: {b} c: {c}')
          
      def methodA(self, a) -> None:
          print(f'a: {a}')
          
      def methodC(self, c) -> None:
          print(f'c from A: {c}')
          
  class ParentB:
      b = 'parent A'
      def __init__(self, d, e) -> None:
          print(f'd: {d} e: {e}')
          
      def methodB(self, b) -> None:
          print(f'b: {b}')
          
      def methodC(self, c) -> None:
          print(f'c from B: {c}')
          
  class ChildAB(ParentA, ParentB):
      pass  #init from ParentA, a, b, methodA & methodB and one version of the methodC are available 
  ```

* To specify which method to use in case child overwrites it, `super()` can be used:
  ```python
  class ChildAB(ParentA, ParentB):
      def methodC(self, c):
          super().methodC(c)        
  ```

* To specify which method to use in case of several parents implementing it, call them from class directly:
  ```python
  class ChildAB(ParentA, ParentB):
      def methodC(self, c):
          ParentB.methodC(self, c)
  ```

* Attributes and methods in child classes overwrite same attributes/methods in parent classes for that scope; and attributes and methods, redefined in instances (for some reason) would overwrite them in classes

* Mechanism that contols this is called MRO, method resolution order, and works like this:

  * MRO is lazy, so first encountered result would be returned and search halted

  * MRO is triggered everyh time attribute/method is looked up — be it `Class.method()`, `instance.method()` or even `self.method()` in the class

  * MRO needs to be triggered with explicit starting point — either instance or class. Calling the parent method inside the child class as is will result in exception:
    ```python
    class ChildAB(ParentA, ParentB):
        def methodC(self, c):
    		methodC(self, c)
    >>> NameError: name 'methodC' is not defined
    ```

  * Lookup order looks like this: instance — instance class — parent class. In case of several parent classes, they are traversed left to right in order they were specified; and up until `object` 

  * This helps to avoid the Deathly Diamon of Death (really) problem: if several super (== parent) classes have same method defined, how to choose which one to use — the order is set by user

* There are several ways to inherit according to Lutz:

  * Straight inheritance — super methods are used as is:
    ```python
    class SuperDevice:
        def push_button(self) -> None:
            print('Button pushed, nothing happened')
            
    class UselessDevice(Super):
        pass
    ```

  * Overwriting — child overwrites super method:
    ```python
    class DeskLamp(SuperDevice):
        def push_button(self,) -> None:
            print('Light state switched')
    ```

  * Extending — extends super method by calling it directly + adding new code:
    ```python
    class MonitoringDevice(Super):
        def push_button(self,) -> None:
            print('Monitor lights up')
            super().push_button()
            print('Monitor shows nothing')
    ```

  * Composition — not technically inheritance, but can be used when it doesn't make sense to inherit from the class (is a relationship), but to incorporate something from another class:
    ```python
    class Car:
        def __init__(self) -> None:
    		self.ac = SuperDevice()
        def turn_ac(self) -> None:
            self.ac.push_button()
    ```

  * Most interesting one is delegation, when super class relies on child to implement something:
    ```python
    class SuperDevice:
        def push_button(self) -> None:
            print('Nothing happened')
            
        def work(self) -> None:
            self.action()
            
    class PaperShredder(SuperDevice):
        def action(self) -> None:
            print('It shreds')
    ```

    * In this case `work` method of the `SuperDevice` can't be called from instance of `SuperDevice` because `action` is not implemented there, and it relies on child to implement and call it

* To prevent calling unimplemented methods in super classes, abstract superclasses are used

* Abstract superclass is a class that implements interface, but not the logic, and requires classes down the line to implement logic

* There are several ways to implement abstract superclasses:

  * Ask nicely:
    ```python
    class SuperDevice:
        def push_button(self) -> None:
            print('Nothing happened')
            
        def work(self) -> None:
            self.action()
            
        def action(self) -> None:
            print('action is not implemented')
    ```

  * Force with `assert`:
    ```python
    class SuperDevice:
        def push_button(self) -> None:
            print('Nothing happened')
            
        def work(self) -> None:
            self.action()
            
        def action(self) -> None:
            assert False, 'action is not implemented'
    ```

  * Force with Exception:
    ```python
    class SuperDevice:
        def push_button(self) -> None:
            print('Nothing happened')
            
        def work(self) -> None:
            self.action()
            
        def action(self) -> None:
            raise NotImplementedError('action is not implemented')
    ```

  * Force with AbstractSuperclass
    ```python
    from abc import ABCMeta, abstractmethod
    class SuperDevice(metaclass=ABCMeta):
        def push_button(self) -> None:
            print('Nothing happened')
            
        def work(self) -> None:
            self.action()
            
        @abstractmethod
        def action(self) -> None:
            pass
    ```

    * This not only prevents calling the `action` or `work` methods, but creating the `SuperDevice` class instances alltogether  since it's now an abstract class
    * This relies on pretty advanced topic of metaclasses, but in short metaclass is controling the way class is created the same way class controls instances creation


## Name mangling interlude

* Consider the following **ask every step**:

  * Parent class has a method that runs on init
  * Child class doesn't overwrite init and expected to have same method called on its init
  * Child class has a method named the same as on-init parent method
  * Parent class must still be able to use its on-init method

* You remember name mangling — way to «privatise» attributes in classes? This is a bit counter-Pythonic, but might be useful still

  ```python
  # child inits incorrectly, parent method accessible
  # init should post 'proper x' in both cases
  class Test:
  	def __init__(self, a):
  		self.post(a)
     
  	def post(self, x):
  		print(f'proper {x}')
    
  class TestChild(Test):
  	def post(self, x):
  		print(f'child {x}')
  
  # child inits correctly, parent method inaccessible
  class TestFixed:
  	def __init__(self, a):
  		self.__post(a)
     
  	def __post(self, x):
  		print(f'proper {x}')
     
    
  class TestChildFixed(TestFixed):
  	def post(self, x):
  		print(f'child {x}')
  
  # fully solved
  class TestFixedFixed:
  	def __init__(self, a):
  		self.__post(a)
     
  	def __post(self, x):
  		print(f'proper {x}')
     
  	post = __post
    
  class TestChildFixedFixed(TestFixedFixed):
  	def post(self, x):
  		print(f'child {x}')
  ```

* This is the only valid use for mangling I found/could come up with; except for straight up hiding attributes [^3]

* Good news: you might as well forget about mangling completely now and everything would still be fine in your life.