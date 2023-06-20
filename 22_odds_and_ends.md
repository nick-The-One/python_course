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



[^1]: [Exception types](https://docs.python.org/3/library/exceptions.html#Exception)

