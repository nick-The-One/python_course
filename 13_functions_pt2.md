# Functions, pt. 2

## Getting back on track

* Last time I mentioned that we'll get back on track with in-depth look at functions, notably by mentioning «how functions are registered» — and to be honest, I have no idea what I meant by that.
* That's why w'll start with function-is-an-object concept and scopes instead

## Function is an object

* As it is with most things in Python, functions are objects

* But not OOP kind of objects, but rather first class objects — it being treated just like any other defiined object (variable, class, instance etc)

* This makes it possible:

  *  to assign functions to variables
    ```python
    def f() -> None:
      print('a')
    f()
    a = f
    a()
    ```

  * to keep them in containers
    ```python
    def f() -> None:
      print('f')
    
    def g() -> None:
      print('g')
    
    d = {'f': f, 'g': g}
    d['f']()
    ```

  * pass functions as arguments to functions:
    ```python
    from typing import Callable
    
    def f(a: int) -> int:
      return a * 2
    
    def g(a: int, b: Callable[[int], int]) -> int:
      return b(a)
    
    g(2, f)
    ```

  * and give them attributes:

    ```python
    def f() -> None:
      pass
    
    f.x = 1
    f.x
    ```

  * Accessing them from inside the function is higly non-trivial and only works with predefined attribute name (x in this case):

    ```python
    def foo():
      import sys
      frm = sys._getframe()  # get current executions frame — we'll talk about it way later
      return frm.f_globals[frm.f_code.co_name].x  # get globals from frame, determine current function name, get it's scope and retrieve the attribute
    
    foo.x=777
    foo()
    ```

## Scopes

* Scope (also can be called namespace, but that is disputed by some — namespace is a dictionary that has everything in scope) is a collection of defined (registered) names, that available unambiguously by that name 

  * let's take a look at what's currently in out global scope — scope that is most used until you dive deep into functions and classes

  ```python
  dir()
  a = 1
  b = 2
  c = 3
  dir()
  ```

* Scope of an object is assigned at the moment of definition: assigning variable, defining function etc.

* There are four main scopes in Python: built-in, global, local and enclosing
  ![legb](/Users/noni1001/Projects/PLC/lectures/img/legb.png)

* Built-ins is the «biggest» scope (not by amount of objects in it, but by accessibility — everything has access to it)

  * It's created with start of Python interpreter every time script is executed/interactive session started

  * It contains all the things you consider «Python» — e.g.: `int`, `list` etc
    ```python
    __builtins__.__dict__
    ```

* Global is the most used scope — this is where all variables and functions and classes, defined in script file/iPython session exist

  * Despite being called global, this scope is limited to a single file/session

  * What is currently in that scope can be checked with `dir()`

  * Accessible to both reading and writing by just names of the objects, no addressing scope is necessary
    ```python
    a = 1
    print(a)
    ```

  * …unless you're trying to *write* to it from `local` scope — that will require `global` keyword, as we'll see

* Local scope is the scope bound to function, and contains every object defined in that function. Objects are accessible only inside the function

  * Names inside local scope can shadow global names — global ones will not be overwritten or impacted in any way
    ```python
    a = 1
    print(a)
    def f() -> None:
      a = 2
      print(a)
    print(a)
    ```

  * New local scope is created with each *call* to function
    ```python
    def f() -> None:
      a = 1
      print(a)
      a += 5
      print(a)
    f()  # out is 1, 6
    f()  # out is still 1, 6 despite a being modifed in previous call
    ```

    

* There's still one scope left, but before explaining it, we need to know one thing: how does interpreter decides which name to use in case of same names in different scopes?

  * This is determined by mechanism called «name resolution» — which means exactly that: finding object with given name

  * In Python lookup rule is pretty simple and goes by acronym LEGB — it stands for Local-Enclosing-Global-Built-in

  * When some object is requested by name, at first it would be searched for in **L**ocal scope — inside the function it was requested from

  * If not found there — in **E**nclosing scope

    * Enclosing scope is a scope of a function, that encloses original function; easier to explained with example:
      ```python
      def enclosing_function() -> None:
        enclosing_scope_var = 1
        def enclosed_function() -> None:
          local_scope_var = enclosing_scope_var + 1
      ```

    * Enclosing scopes can be nested

  * If still not found in that, **G**lobal scope is next in line

  * And last resort is **B**uilt-ins scope
    ```python
    global_var = 1
    def enclosing_func() -> None:
      enclosing_var = 2
      def internal_func() -> None:
        local_var = 3
        print(local_var)  # local scope
        print(enclosing_var)  # enclosing scope
        print(global_var)  # global scope
        print(int('1'))  # built-ins scope
    ```

  * Scope lookup is one-way only: only current and above current one scopes are accessible for lookup, but not other way around: global scope can't access function's variables. Search is lazy and stops after first found object

  * Normally objects from other scopes are read-only: function can use value from global scope, but not change it
    ```python
    a = 1
    def func() -> None:
      print(a)  # reads from global scope
      a = 2  # creates new object in local scope
      print(a)
    print(a)  # global object is not affected
    ```

  * But as expected there's a way around it:

    * For writing to global from inside the function keyword `global` should be used:
      ```python
      a = 1
      def func() -> None:
        print(a)  # reads from global scope
        global a
        a = 2  # modifies global object
        print(a)
      print(a)  # global object is affected
      ```

    * For writing to enclosing function keyword `nonlocal` should be used:
      ```python
      def func_enc() -> None:
        a = 1
        def func() -> None:
          print(a)  # reads from enclosing scope
          nonlocal a
          a = 2  # modifies enclosing object
        	print(a)
      	print(a)  # global object is affected
      ```

    * Built-ins are different. You can't write to that scope normally — but as with many things in Python, that is not enforces in any way:
      ```python
      __builtins__.inc = lambda x: x +1
      dir()  # global does not have inc still
      inc(1)
      ```

    * …but you really shouldn't do this — built-ins in particular, but in general avoid modifying global/non-local objects from other scopes — this will lead to confusion and debug more difficult than neccessary. Sometimes it might be unavoidable, that's why we have options; but if possible — don't use it

  * Be careful with picking names for objects, because you can shoot yourself in the foot:
    ```python
    l1 = list((1, 2, 3))  # cool and good
    list = [2, 3, 4]  # mistake, but not error
    l2 = list((3, 4, 5))  # error! Lists couldn't be defined using list anymore
    ```

  * This works because for line 3 lookup starts at global scope, and it already has `list` object

  * Original `list()` is still accessible:
    ```python
    __builtins__.list()
    ```

  * …which, of course, could be broken too:

    ```python
    __builtins__.list = 1
    __builtins__.list()
    ```

  * Now only interpreter restart will fix things (no, not even `importlib.reload(__builtins__)`[^1])

* Bit more on enclosing scope: it's mostly useful for keeping function state for enclos**ed** function, or some params for it

  * Good example of that would be closure: a function with bound variables, defined elsewhere; for simplicity — it's a function that returns a function; this is possible in Python thanks to **E** part of scope lookup
  * Example: we need a function(s) that doubles, triples and quadruples given value

  ```python
  from typing import Callable
  from functools import partial
  
  # heads-on
  def doubler(a: int) -> int:
    return a * 2
  
  def tripler(a: int) -> int:
    return a * 3
  
  def quadrupler(a: int) -> int:
    return a * 4
  
  # closures
  def mul_factory(a: int) -> Callable[[int], int]:
    def mul(b: int) -> int
    	return a * b
    return mul
  
  doubler = mul_factory(2)
  tripler = mul_factory(3)
  quadrupler = mul_factory(4)
  
  # partial solution
  def mul(a: int, b: int) -> int:
    return a * b
  
  doubler = partial(mul, b = 2)
  tripler = partial(mul, b = 3)
  quadrupler = partial(mul, b = 4)
  ```

* There are a few additional scopes, but in general they obide by the same LEGB rule, replacing **L** layer:

  * Comprehension scope: variable inside comprehesion is confined to it
    ```python
    x = 1
    l = [x for x in range('abcd')]  # last value of x is 'd'
    print(x)  # still 1
    ```

  * Class scope — this is pretty similar to function, but with it's own caveats, that are best left for later:
    ```python
    a = 1
    class Test:
      a = 2
    print(a)  # still 1
    t = Test()
    print(a)  # still 1
    ```

  * Exception scope is a bit different: it actually overwrites and deletes variable from enclosing scope after catching an exception[^2]

    ```python
    e = 1
    try:
      1 / 0
    except ZeroDivisionError as e:
      print(e)
    print(e)  # not defined error
    ```



[^1]: [Reload caveats](https://docs.python.org/3/library/importlib.html#importlib.reload)
[^2]: [Exceptions variables overwrite](https://docs.python.org/3/reference/compound_stmts.html#except-clause)