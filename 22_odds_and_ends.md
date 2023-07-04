# Odds and ends

## Exceptions

* Exception is a type of error that can happens when syntactically correct code execution results in an error

* Exception, if not handled, interrupts execution of the code — and more often then not terminates execution alltogether

* There are lot of types of exception that can occur naturally, so to say, for example:

  * `AttributeError` that can be raised if code tries to access object's attribute that does not exist

    ```python
    class A:
        def __init__(self) -> None:
            self.a = 1
            self.b = 2
            
    a = A()
    a.c
    >>> AttributeError: type object 'A' has no attribute 'c'
    ```

  * `IndexError` — dreaded ArrayOutOfBounds error

    ```python
    a = [1, 2, 3]
    a[3]
    >>> IndexError: list index out of range
    ```

  * `KeyError` that happens when trying to get value from mapping with nonexistent key

    ```python
    a = {'a': 1, 'b': 2}
    a['c']
    >>> KeyError: 'c'
    ```

  * And many, many more[^1]

* Although exception, being an error, sounds menacing, it's not always a bad thing — they can be used for control flow purposes

* One of the most prominent examples is iteration protocol: it raises `StopIteration` exception to signal that iterator is exhausted, but since it is handled by context it does not result in execution termination

* To handle exception — if we anticipate them, or want to make our script more resilient, we are given tools to do so

* Most important is `try - except` combo, that *tries* to execute code in `try` block and then catches (optionally specified) exception and handles it in a way, defined in `except` block:
  ```python
  try:
      something_shady()
  except:
      salvage()
  ```

* Important to note that after catching exception conrol will **not** be returned to the `try` block:
  ```python
  try:
      a = 1
      something_shady(a)  # exception raised here
      print('this line will never be executed if something_shady raises an exception')
  except:
      print('this line will be executed if something_shady raises an exception')
      
  print('this is a line that will be executed after exception handling')
  ```

* By default `except` will catch each and every type of exception, which is in general a bad idea — you want to have specialized handling procedures for different exceptions, and be notified (albeit by crash) about new types of errors

* To define which type of exception to handle by particular `except` block construction `except ExceptionType` can be use

* Several `exception` blocks can be used in conjunction with single `try` to handle different errors — raised exception will be matched with their types one by one until either match found or no more options left — in that case exception will be raised to the level above `try` block and terminate execution

* And several exception types can be used in a single block to handle in the same way multiple errors:
  ```python
  try:
      something_shady()
  except SomeException:
      handle_se()
  except (SomeOtherException, AnotherException):
      handle_soe()
  ```

* Next keyword, that can be used in `try` construction is `else` — here it denotes a block that will be executed if no exception was raised:
  ```python
  try:
      nothing_shady()  # will be executed without exception
  except:
      handle()  # will not be executed
  else:
      handle_ok()  # will be executed after try block
  ```

* last keyword is fittingly `finally` which will be executed regardless of exception status — handy for cleanups of any sort:
  ```python
  try:
      nothing_shady()  # will be executed without exception
  except:
      handle()  # will not be executed
  else:
      handle_ok()  # will be executed after try block
  finally:
      handle_final()  # will be executed regardless 
  ```
  
* Exception in Python 3 is a class, and one can define own exception classes by inheriting from `Exception` or one of its children — not diving deep into details for now, this is pretty niche feature, but it's nice to define specialized exceptions for your domain, even without special logic just to give more precise errors

* Since exceptions are classes, raised exception creates and instance of exception object that can be caught with `as` contruction:
  ```python
  try:
      something_shady()
  except Exception as E:
      print(E)  # will print particular error that was raised
  ```

* Important: variable that contains exception object will be removed after end of `except` block, but it also will overwrite any variable with the same name from enclosing namespace:
  ```python
  E = 'a'
  try:
      something_shady()
  except Exception as E:
      print(E) 
  E
  >>> NameError: name 'E' is not defined
  ```

* One can raise exception at will using `raise` statement:
  ```python
  def something_shady() -> None:
      raise Exception
  ```

* `raise` should be followed by exception type, except when used in `except` block — in that case it just re-raises last caught exception

  ```python
  try:
      something_shady()  # raises Exception
  except Exception:
      raise  # raises Exception again
  
  >>> Exception
  ```

* Any exception type accepts string argument that describes error:
  ```python
  raise Exception
  >>> Exception
  raise Exception('something bad happened')
  >>> Exception: something bad happened
  ```

* There's also `assert` statement that will raise `AssertionError` is some condition is not met and is usually used in tests:
  ```python
  a = 1
  b = 2
  assert a == b
  >>> AssertionError
  
  # roughly equivalent to:
  if a != b:
      raise AssertionError
  ```

## Decorators

* Decorator is a way to run custom code right after function or class definition — code, that will alter or enhance the way defined object behaves

* This is done by binding function/class name to a decorator code — so every time function is called or instance is created, not original, but rather custom object is called and original object is passed as an argument

* Decorator itself is and higher-order function — we met them before: it's a function that takes as argument and returns a function instead of regular values

* And being a higher-order function, decorator can modify function/class behaviour without modifying its code

* Sicne decorator has to return a function and it can't return directly received function (that would be pointless), wrapper function is expected to be defined inside the decorator and it will be returned from it

* Best way to understand it is to write one:
  ```python
  from typing import Callable
  
  def decorator_func(f: Callable[..., Any]) -> Callable[..., Any]:
      def wrapper() -> None:
          print("I'm the function now")  # guess the last name of the captain
          f()
          print("It's over")
      return wrapper
  
  
  def func() -> None:
      print('OG function')   
  ```

* To decorate the function it needs to be passed to the decorator function:
  ```python
  func()
  >>>OG function
  
  func = decorator_func(func)
  func()
  >>>I'm the function now
  >>>OG function
  >>>It's over
  ```

* Of course there's syntax sugar available for decorators by using `@decorator` statement before decorated function definition — and this is one thing that makes it even more powerful by making it highly noticable in the code, and this way somewhat improving readability over reassigning names as in example above:
  ```python
  @decorator_func
  def func() -> None:
      print('OG function')  
  ```

* Now if we try to decorate function that receives any arguments with this decorator, nothing particularly good will happen:
  ```python
  @decorator_func
  def func_b(i: int) -> None:
      print(f'OG function: {i}')
      
  func_b(12)
  >>>TypeError: decorator_func.<locals>.wrapper() takes 0 positional arguments but 1 was given
  ```

* This happens because decorated `func_b` is not actually `func_b`, but rather `wrapper`, which does not take any arguments

* This is an easy fix:
  ```python
  def decorator_func(f: Callable[..., Any]) -> Callable[..., Any]:
      def wrapper(*args, **kwargs) -> None:
          print("I'm the function now")  # guess the last name of the captain
          f(*args, **kwargs)
          print("It's over")
      return wrapper
  
  @decorator_func
  def func_b(i: int) -> None:
      print(f'OG function: {i}')
      
  func_b(12)
  >>>I'm the function now
  >>>OG function: 12
  >>>It's over
  ```

* Returning values from decorated function is also has to be handled explicitely:
  ```python
  def decorator_func(f: Callable[..., Any]) -> Callable[..., Any]:
      def wrapper(*args, **kwargs) -> None:
          print("I'm the function now")  # guess the last name of the captain
          ret = f(*args, **kwargs)
          print("It's over")
          return ret
      return wrapper
  
  @decorator_func
  def func_c(i: int) -> int:
      return i + 22
  
  func_c(12)
  >>>I'm the function now
  >>>It's over
  >>>34
  ```

* Note that all of the wrapper logic is executed before return, since no code after it will be executed in wrapper function

* Wrapper can also can be a class with overloaded `__call__` method since decorator will be called on every decorated function call
  ```python
  class DecoratorClass:
      def __init__(self, func: Callable[..., Any]) -> None:
          self.func = func
          
      def __call__(self, *args, **kwargs):
          print('wrapped func call')
          return self.func(*args, **kwargs)
      
  @DecoratorClass
  def func_c(i: int) -> int:
      return i + 22
  ```

* Obviously one can decorate class methods just like any other function

* But it is also possible to decorate entire class — in this case decorator will be called on every new instance creation instead of function calls, and class (not instance!) will be passed to wrapper

* Best example would be implemetation of singleton pattern — ability to create only one instance of the class, and with every subsequent creation original instance will be returned
  ```python
  def singleton(cls):
      def wrapper(*args, **kwargs):
          if not wrapper.instance:
              wrapper.instance = cls(*args, **kwargs)
          return wrapper.instance
      wrapper.instance = None  # new attribute assigned to the wrapper function after it was defined
      return wrapper
  
  @singleton
  class TestClass:
      pass
  
  t1 = TestClass()
  t2 = TestClass()
  
  id(t1) == id(t2)
  >>>True
  ```

* Decoration will overwrite function/class metadata:
  ```python
  def func_c(i: int) -> int:
      return i + 22
  
  func_c.__name__
  >>>'func_c'
  
  @decorator_func
  def func_c(i: int) -> int:
      return i + 22
  
  func_c.__name__
  >>>'wrapper'
  ```

* To fix that for whatever reason ironically another decorator can be used:
  ```python
  from functools import wraps
  
  def decorator_func(f: Callable[..., Any]) -> Callable[..., Any]:
      @wraps(f)
      def wrapper(*args, **kwargs) -> None:
          print("I'm the function now")  # guess the last name of the captain
          ret = f(*args, **kwargs)
          print("It's over")
          return ret
      return wrapper
  
  @decorator_func
  def func_c(i: int) -> int:
      return i + 22
  
  func_c.__name__
  >>>'func_c'
  ```

* Several decorators can be used at once — they will call each other nested from bottom to top:
  ```python
  @dec_a
  @dec_b
  @dec_c
  def func_d() -> None:
      pass
  
  # same as
  dec_a(dec_b(dec_c(func_d)))
  ```

* Decorator can accept arguments itself, in that case it will be called and decorated object will be passed to the returned object — thus we need another layer in the decorator code to handle that:
  ```python
  def decorator_func(s: str) -> Callable[..., Any]:
      def outer_wrapper(f: Callable[..., Any]) -> Callable[..., Any]:
          def wrapper(*args, **kwargs) -> None:
              print(f"I'm the function now, custom text: {s}")  # guess the last name of the captain
              ret = f(*args, **kwargs)
              print("It's over")
              return ret
          return wrapper
      return outer_wrapper
  
  @decorator_func('heyo')
  def func_c(i: int) -> int:
      return i + 22
  
  func_c(12)
  >>>I'm the function now, custom text: heyo
  >>>It's over
  >>>34
  ```

* [More on decorators](https://realpython.com/primer-on-python-decorators)

## Unit tests basics

* Unit test — tests minimal pieces of code independetly

* Why unit tests? — instant feedback

* What makes a good unit test?

  * Simple
  * Readable
  * Deterministic
  * Fast to run (or force it!)
  * Unit only
  * Should run locally, mock EVERYTHING — you only need you're testing logic, not connections: input -> processing -> output

* Simples way to test something is to use `assert`:
  ```python
  def inc_func(a: int) -> int:
      return a + 1
  
  assert inc_func(2) == 3
  ```

* But more proper way would be using built-in unit-test solution, that gives you ability to get more granular information on which tests passed and which are failed – `unittest`

* To write tests using `unittest` one need to write a class, inherited from `unittest.TestCase` and define in it methods with names that start with `test` — they will be run on `unittest` test run

* There are special methods that will be called in different points in time — before and after tests or even class creation, such as `setup` and `teardown`[^2]

* All assertion in such test cases are done using special methods, and not regular assertion — they still can be used, but special methods have more informative output, especially with ability to pass messages to it:
  ```python
  import unittest
  
  def inc_func(a: int) -> int:
      return a + 1
  
  class TestInc(unittest.TestCase):
      def test_inc_method(self) -> None:
          self.assertEqual(inc_func(2), 4)
  
      def test_inc_assert(self) -> None:
          assert inc_func(2) == 4
  
          
  >>>FAIL: test_inc_method (utest.TestInc)
  ----------------------------------------------------------------------
  Traceback (most recent call last):
    File "/Users/noni1001/Projects/PLC/test_demo/tests/utest.py", line 8, in test_inc_method
      self.assertEqual(inc_func(2), 4)
  AssertionError: 3 != 4
  
  
  >>>FAIL: test_inc_assert (utest.TestInc)
  ----------------------------------------------------------------------
  Traceback (most recent call last):
    File "/Users/noni1001/Projects/PLC/test_demo/tests/utest.py", line 11, in test_inc_assert
      assert inc_func(3) == 3
  AssertionError
  ```

* There are a pretty sizeable number of available assertion methods[^3] for all kinds of testing, including expected exception

* To run tests via `unittest` following command may be used:
  ```python
  python -m unittest -v path/to/test_file
  ```

* To emulate expensive/remote/unpredictable calls, mocking can be used — replacing these calls with objects with predetermined returns or logic to catch calls to it[^4]

* In ML team we are mostly using `pytest` instead, which works slightly different — it does not require base class, or any class for that matter: each test case should be single function with name starting with `test_`; regular `assert` is used

* It also provides tools for expected exceptions, fails, skips, parametrization of test cases with fixtures (predetermined params), mocking and even can run `unittest` tests with slightly prettier output



[^1]: [Exception types](https://docs.python.org/3/library/exceptions.html#Exception)

[^2]: [Additional test methods](https://docs.python.org/3/library/unittest.html#unittest.TestCase.setUp)
[^3]: [Assertion methods](https://docs.python.org/3/library/unittest.html#unittest.TestCase.debug)
[^4]: [Mock library](https://realpython.com/python-mock-library/)
