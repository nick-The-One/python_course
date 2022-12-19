# Functions Intro

## What are functions

* So far we were learning how to do things, not how to do them efficiently – learning how to walk before learning to run
* While I tried to give what in my perspective the best way to do things, things so far were really basic and you will be using them anyway, regardless of how quality you want your code to be
* But that's to be expected – in the beggining I said that teaching you how to code is not up to me; but teaching you proper Python is
* And proper Python includes not only variables and loops, but also today's topic: functions
* First let's see what the function is: 
  * in math terms function is relation between input set X and output set Y, f(x) = y, which means that that for every input value (x small) we get corresponding output value (y small); good pure math examples would be cos(x); real life is planet movement: it's position (y) is changing with time (x)
  * programming, being math derivative, develops on this concept: it's a block of code, that optionally accepts an argument or several — x; and optionally returns a new value — y; cos(x) fits right in; and if we had an API for moving the planed we might write a function for previous example with ease
  * For function user it might as well be a black box — the only exposed parts of it are name, signature (how many and which kind of arguments does it take) and return if any
  * there's entire programming paradigm built on it: functional programming; I wish I could tell you more, because it (seems) awsome, but I can't — I don't know it and might be too dumb to understand in a first place
* But main purpose of the function is not just add more math tie-ins in your code, but two things:
  * code reuse — if you plan on repeating same action over and over in different places of your code (if in the same, just use a loop), functions are your best choice: write once, use many times
  * code decomposition — split your code into logically independent parts with functions: load, process, write, test etc. This improves readability and maintanability of the code in contrast with just entirety of logic dumped to a single scope
* To sum it all up: functions is a block of code that will be executed with a single call with optional arguments and returns, that is used to improve code reuse and decomposition

## Functions in Python

* As it is our last meet this year, I don't want to go into depth too much, so today we just go over the basics of functions

* Structure of a function somewhat replicating the structure of the code in general: we have arguments, that act as input data; body of the function, which is logic; and return which esentially is output

* Let's start by defining the function — i. e. registering it in the namespace so it can be called later: for this we have control word `def`, followed by name of the function (same restrictions apply as for variable names), parenthesis with a list of arguments and colon:
  ```python
  def function(arg1: int, arg2: str, arg3: List) -> bool:
    pass
  ```

  * `pass` used here just because Python does not allow empty function bodies

  * type after colon after each argument name is optional, but I strongly encourage you to use it anyway to help yourself and others

  * arrow and type notes return type

  * while Python is dynamically typed language and much respected Lutz would advocate you for writing functions in a way that it would accept any type of argument, I would say against it. What this function counts and how it works?
    ```python
    def count(val, char):
      cnt = 0
      for x in val:
        if x == char:
          cnt += 1
      return cnt
    
    s = 'o mice! come forth'
    l = s.split()
    count(s)  # returns 3
    count(l)  # returns 1
    ```

  * one of the most important things in code is expected outcome and function above does not fall under that rule

  * sure not every function must be idempotent (same input produces same output), but it has to be predictable:
    ```python
    def count(val: str, char: str) -> int:  # now 3 is expected result
      cnt = 0
      for x in val:
        if x == char:
          cnt += 1
      return cnt
    ```

  * reminder: Python will not enforce any of typing hints, but linter will; and IDE will remind you to use proper type

* Body of the function is a regular block of code — indented, as usual — with no restrictions I could think of: you can assign variables, use control flow and loops, or call or even define functions

  * Function body is it's own scope (visibility) — variables, created inside the function, are accesible only from inside the function

  * Arguments are creating function-scope variables named same as the arguments
    ```python
    def function(a: str) -> None:
      print(f'{a=}')  # ask what it is
      
    function('termina')
    ```

  * Arguments are optional
    ```python
    def function() -> int:
      return 1
    ```

  * Variables are passed by reference, which means you should be careful with mutable datatypes

    ```python
    def mutable(l: List[int]) -> None:
      l.append(999)
      
    l = [1, 2, 3]
    mutable(l)
    print(l)
    ```

  * Variables, defined outside the function (globals) are freely accesible from inside the function, but it's always better to pass all variables explicitly to make clear what function uses; and it's heavily discouraged to modify global variable in function — so heavily that it requires special statement that I will not tell you (yet)

    ```python
    a = 1
    def function() -> None:
      a = 2
    print(a)
    ```

  * `return` marks end of the function and returns values passed with it back to caller — call example a bit later

    ``` python
    def function() -> int:
      return 1
    ```

  * you can return several values at once, they'll be received as tuple
    ```python
    def function() -> Tuple[int, int, int]:
      return 1, 2, 3
    ```

  * or you can skip return all at once — this will still return something, `None` in this case
    ```python
    def function() -> None:
      pass
    ```

  * no code after `return` will be executed — function exits on `return` statement, like on `break` in loops

* To call a function, use its name + parenthesis with list of arguments:
  ```python
  def function(a: int) -> int:
    return a
  
  b = function(1)
  ```

  * if you assign function call to a variable, you'll get returned value in it

  * **ask** if you assign function to a variable, you can call it by variable name:

    ```python
    def function() -> int:
      return 1
    
    function()
    a = function
    a()
    ```

  * that's because `def` creates, as expected in Python, an *object* — function object in this case, and function object, like any other, can be assigned to variable

* That's all of function basics laid out, more on them after new year, all the juicy bits – how functions are registered, scopes, what is a function frame and other.

## Homework

* Every project has three people attached to it: author, manager and developer

* Author just brings the idea

* Manager writes full task requirement based on idea

* Developer writes code based on requirement

* You need to write a small script that, given a list of tuples `project:author` and list of names outputs list of dictionaries like this:
  ```json
  {
    'project': 'name',
    'author': 'name',
    'manager': 'name',
    'developer': 'name'
  }
  ```

* Rules: 

  * autor can't manage or develop their own project
  * manager can't develop their project
  * each person has to manage exactly one project
  * each person has to develop exactly one project
  * if somebody brings ideas for more than one project they should be used after everyone else's ideas are assigned — i.e. first we distribute one idea from person, and after that if we still have more developers without a project, we use more ideas from same person