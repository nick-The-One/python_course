# Functions, pt. 5

## Lambdas

* First — just a definition, to get a subject to talk about

* Lambda in Python is an anonymous function

* Just like in that America's song — just a function with no name; remember this thought — we will be checking it's truthfulness later with `dis` 

* Idea of a lambda itself comes from lambda calculus and have absolutely nothing to say about it, aside from the fact that lambda theory was opposing Turing's one on computing

* What does this mean for us?

* Like most overly mathematical things in programming, this means that we're venturing into functional programming territory  — pure functions, no states, no side effects

* And, of course, obsurity of things that are happening; but with some perseverance we might just succeed

*  Lambda functions in it's essence is just a function, but without a name

* This somewhat limits it's uses; but implementation details open other possiblities for it

* Let's finally take a look at it in full glory:
  ```python
  def func_a(x: int) -> int:
    return x + 1
  
  lambda x: x + 1
  ```

* What happens here is that function is created that accepts single int argument `x` and returns sum of `x` and 1

* As you can see, syntax for defining lambdas quite differs from regular functions:

  * keyword `lambda`
  * no name, obviously
  * argument isn't enclosed in brackets
  * no `return` statement

* But most important thing now is how can one call newly created lambda?

* In the case it's simple — you can't

* To use lambda you need to either call it immediately:
  ```python
  (lambda x: x + 1)(2)
  ```

  * In this case lambda is enclosed in brackets to show that it is a single expression and then called like regular function

* …use it somewhere:

  ```python
  list(map(lambda x: x + 1, range(5)))
  ```

* …or give it a name:
  ```python
  func_a_l = lambda x: x + 1
  func_a_l(2)
  ```

* `map` example will become clear later, for now you'll have to just accept it for what it is

* Last example is blatant violation of PEP-8[^1] and should not be used — at this point just define regular function

* And speaking of regular functions: as I mentioned before, lambdas *are* regular functions at it's core, but unlike `def` statement they return result instead of function object, while being function objects themselves:
  ```python
  import dis
  func_a_l = lambda x: x + 1
  dis.dis(func_a_l)
  def func_a(x: int) -> int:
    return x + 1
  dis.dis(func_a)
  ```

* Looks pretty much identical, which is to be expected

* One difference — again, as expected — comes down to a name of function:
  ```python
  def func_a(x: int) -> int:
    return x + 1
  func_a
  # should return <function __main__.func_a(x: int) -> int>
  func_a_l = lambda x: x + 1
  func_a_l
  # should return <function __main__.<lambda>(x)>
  func_b_l = lambda x: x + 2
  func_b_l
  # should return <function __main__.<lambda>(x)>
  ```

* Regular functions have names attached to them, while all lambdas will be just that, lambdas

* This might pose a problem while debugging: you might get a stacktrace, that simply states that error happened in lambda function with incorrect value of `x` — and now there's all of named lambdas to sift through in search of a culprit

* Now to what lambda can, or to be more precise, can't do

  * Lambda can accept several arguments — just separate them by comma:
    ```python
    (lambda x, y: x + y)(1, 2)
    ```

  * body of lambda is a single expression — can't use `for` for example inside of it

    * this is done to discourage complex logic inside

  * but it can be still multiline:
    ```python
    (lambda x:
    x % 2 and 'odd' or 'even')(3)
    ```

    * **ask** remember bitwise operators? This is what happens here: remainder of division by 2 can be either 0 (even, evaluates to False) or 1 (odd, evaluates to True), then it evaluated with `and` against string `odd`  (which always True) or due to way `or` expression evaluated returns `even`
    * Don't write code like this unless it's for code golf

  * And even nested:
    ```python
    (lambda x: (lambda y: x + y))(3)(4)  # no
    ```

    * internal lambda is returned by external one, that why two calls

  * No statements are allowed: no `pass`, `assert`, `raise` or `return`

    * This is again by design[^2] — lambdas in Python are meant to be shorthands for lazily defining function, not add any functionality to language — there's no situation that can be solved with lambda and not regular function

  * No typing allowed — lambdas are not meant to be reused after all
    ```python
    lambda x: int: x -> int
    # throws an exception
    ```

  * Since lambda is not a statement, but an expression that returns result, it can be defined in places where regular function can't be *defined* (but not used!):
    ```python
    3 + (lambda x: x + 1)(2)
    3 + (def func_b(x: int) -> int: return x + 1)(2)
    ```

* Now to the most important question: why use them in first place?

* They are really great at two things: as key functions or with functional tools

  * Key functions are functions that are used to determinte the way values are compared in `min` or `max`; or sorted:
    ```python
    list_a = ['1', '2', '3', '10', '12', '100', '101']
    sorted(list_a)
    sorted(list_a, key=lambda x: int(x))  # converts values to int before applying sorting; does not cahnge the list
    max(list_a)  # 3
    max(list_a, key=lambda x: int(x))  # 101
    ```

  * Functions that are applied as part of `map` or `filter` or other functional tools:
    ```python
    list_a = ['1', '2', '3', '10', '12', '100', '101']
    
    def func_b(a: str) -> str:
      return str(int(a) + 2)
    
    list_b = []
    for i in list_a:
      list_b.append(func_b(i))
      
    # lambda way
    list_b = list(map(lambda x: str(int(x) + 1), list_a))
    
    # comprehension way
    list_b = [func_b(i) for i in list_a]
    
    # ask — this just creates list of function objects since functions are not called
    list_b = [lambda x: str(int(x) + 1) for i in list_a]
    
    # this creates list we expect
    [(lambda x: str(int(x) + 1))(i) for i in list_a]
    ```

  * This is a rabbit hole that goes deep into nightmare land: [read at own discretion](https://stackoverflow.com/a/34021333/7415427)

  * [Test on lambdas](https://realpython.com/quizzes/python-lambda/)

## Functools

* and since I mentioned functional tools as one of the uses for lambdas, let's quickly glance over ones that I find most useful

* First one is a `map`, built-in that *maps* given function over every member of iterable — essentially a generator that returns result of a function for every value in an iterable:
  ```python
  ts = 'he wore a hat and brought home the bacon'
  def upper_but_bad(s: str) -> str:
  	return s[0].upper()
  # map returns an iterable, so we need to do something with it to make visible
  ''.join(map(upper_but_bad, ts))
  ```

  * note that we do not call the function, but rather pass the function object

  * there's no reason to use lambda in previous example, but we still can:
    ```python
    ts = 'he wore a hat and brought home the bacon'
    # map returns an iterable, so we need to do something with it to make visible
    ''.join(map(lambda x: x.upper(), ts))
    ```

    * We still pass a function object to map, but have to call `upper` inside lambda to get any use out of it

  * More appropriate example would be dump (no wrap-around) Caesar's cypher:
    ```python
    ts = 'he wore a hat and brought home the bacon'
    ''.join(map(lambda x: chr(ord(x) + 1), ts))
    ```

* Next is `filter` — does exactly what is says on the tin: filters elements of an iterable based on return (True/False) of a function:
  ```python
  ts = 'he wore a hat and brought home the bacon'
  vowels = 'aeiou'
  ''.join(filter(lambda x: x not in vowels, ts))
  ```

* The rest are contained in `functools` module — and I highly recommend looking through documentation for it

* Two I would say I've seen or used myself most often are `reduce` and `partial`

* `reduce` applies some function to every value in an iterator *and* result of previous application (accumulator):

  ```python
  # factorial
  from functools import reduce
  
  reduce(lambda x, y: x * y, range(1, 7))
  ```

  * unlike `map` or `filter` it returns a single value
  * also accepts `initializer` argument to fill in accumulator before first iteration

* `partial` is my hands down most used functool: it allows to add default argument values to already defined function:

  ```python
  from functools import partial
  
  def sum_func(a: int, b: int) -> int:
    return a + b
  
  sum_func_2 = partial(sum_func, b=2)  # pass function object and all args you wish add defaults to
  sum_func_2(1)
  ```

[^1]: [PEP-8 on binding lambdas](https://peps.python.org/pep-0008/#programming-recommendations)
[^2]: [No statements in lambdas](https://docs.python.org/3/faq/design.html#why-can-t-lambda-expressions-contain-statements)