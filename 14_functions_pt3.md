# Functions, pt. 3

## Frames

* As we know, everything that happens in a function stays in the functions — there are some exceptions, of course; as always Python gives us tools to shot ourselves in the foot — but in general function state is contained
* This is achieved by using **Call Frames** — context for each function *call*, containing it's local variables and function state
* Call frame is what provides us with local scope for function — it's stored in the frame
* Frames are created every time the function is called, and destroyed after function ends
* If function calls another function before exiting, new frame is created and put onto LIFO call stack
* After new frame is created, code execution «jumps» to it, executes it entirely if possible, deletes the frame and then returns to caller
* Since the stack is LIFO, last called function will be [executed first](https://web.archive.org/web/20220809063346/https://sites.cs.ucsb.edu/~pconrad/cs8/topics.beta/theStack/02/)
* You can inspect frame objects (and they are objects like everything else) using [Limited C API](https://docs.python.org/3/c-api/frame.html) and get interesing things there: code, locals or even code line
* This goes very deep into metaprogramming so we will leave it there[^1][^2]

## Arguments

* Functions are good, but not so useful without ability to pass some data to them as input data and receive output

* Returning data is simple with `return`, to which there are three points:

  * `return` will end function execution and pass whatever values come after it to the caller

  * No `return` means function will be returning `None` on exit

  * Multiple values returns are possible with coma: they will be packed into one tuple and returned that way
    ```python
    def multiple_returns(a: int, b: int) -> Tuple[Int]:
      sum = a + b
      mul = a * b
    ```

    

* Mechanism for accepting data into function is arguments

* Arguments become part of the local scope with the same name as they're given in function definition, by assigning it's value to a local variable

  ```python
  # arg is an argument
  def func(arg: str) -> None:
    print(arg)
  ```

* What kind of input data can be send via arguments? Pretty much anything you want

  * depending on modules you're using this might not be completely true: pyspark will refuse to work if you try to iterate over dataframe using outside function
  * this does not apply to standard Python or functions you're writing, unless you explicitely code them this way

* But ability to pass any data comes at a price of — you guessed it — hard-to-find bugs

* This comes down to a single point: immutable data is passed by value, but mutable — by reference

* Thus changing any mutable data inside the function will change it outside, despite being bound by local scope, which is independent thanks to frames

  * Problem is only variable name is in local scope, but it still points to value in memory that is shared between it and global
    ```python
    def _plot_rules_breakdown_chart(data: DataFrame, ax: Axes) -> None:
        rules_to_display = sorted(st.session_state['sm_rules_rule_ids'])
        if not rules_to_display:
            return None
    
        data = data[data['supra_final'] != 'NOT_A_RECEIPT']
    
        ref_data = data.copy()  # make a deep copy of mutable data and operate on it
        ref_data = ref_data[ref_data['supra_final'] == st.session_state['sm_rules_category']]\
            .groupby(['supra_final', 'email_sent_date'], as_index=False)['count'].sum()
    	# funtion body continues
    ```

* If your data is not mutable or you're not going to change it — function call will not cause any problems even if varibale names are *shadowed* — are the same in global in local scopes thanks to context isolation

* Now to how Python actually handles arguments
  ```python
  # Remove this!
  def func_a(a, b=1, *c, **d):
      for var, val in locals().items():
          if val:
              print(f'{var}={val}')
  
  func_a(1)
  func_a(1, 2)
  func_a(1, 2, 3)
  func_a(1, 2, 3, 4)
  func_a(1, 2, 3, 4, x=5)
  func_a(1, 2, 3, 4, x=5, y=6)
  func_a(a=1)
  func_a(a=1, b=2)
  ```

* You can't see body of the function yet, but what you can see is that despite being passed almost in a same way they are handled differently for some reason

* That's because as we already can expect there are several (around five, depending on how you count) ways to pass and receive an argument — this is what we'll call *argument matching*

* We'll look at them now from both caller and function persective — since we will be both wrtiting and using functions

* First and most widely used way is **positional matching** — when you don't specify the names of arguments in the call, but rather rely on their position in the call:
  ```python
  def func_arg_1(a: int, b: int, c: int) -> None:
    print(f'{a=} {b=} {c=}')
    
  func_arg_1(1, 2, 3)
  ```

  * In the *call* we just pass three values and hope that function figure out the way to assign them to respective locals — and it does
  * But from function side we still have to use names to be able to use them — and if caller pass right amount of values, they'll be mapped to defined names
  * If count of passed arguments differs from defined, exception will be raised
  * As with the most things in Python, arguments will be matched left to right

* Positional matching can be error-prone, especially if we rely on black-box function — what if arguments changed order in signature?
  ```python
  def pow_old(base: int, power: int) -> int:
    return base**power
  
  def pow_new(power: int, base: int) -> int:
    return base**power
  
  pow_old(2, 8)
  pow_new(2, 8)
  ```

* To prevent this from happening exists **keyword** or **name matching** — where caller explicitely states names of arguments to assign
  ```python
  pow_old(base=2, power=8)
  pow_new(base=2, power=8)
  ```

  * This does not require any additional changes to the function definition

  * This is recommended way of calling functions to make more readable and maintainable code

  * Order of arguments is arbitrary as long as all required arguments are passed
    ```python
    pow_old(power=8, base=2)
    pow_new(power=8, base=2)
    ```

* If you think that you know better than your user what would be a good value of argument, there's a way to define **default value** — such value that would be used if none passed by caller
  ```python
  def pow_default(base: int, power: int = 2) -> int:
  	return base**power
  
  pow_default(2)
  pow_default(2, 8)
  ```

  * Default values need to be set in function definition

  * Arguments with default values should come after arguments without
    ```python
    def default_test(a: int = 1, b: int) -> None:  # fails!
      pass
    ```

  * Using mutable value as default can be dangerous since default value will be reused on each call, which can lead to unexpected results:
    ```python
    def bad_default(a: list[int]=[]) -> None:
      a.append(0)
      print(a)
      
    bad_default([1])
    bad_default()
    bad_default()
    bad_default()
    ```

  * Use `None` instead and assign empty list if still `None` inside the function

* But what if the sittuation is opposite: we have no idea what might be passed to the function, but need to accept all?

* Python provides us with two ways to achieve that: **positional** and **keyword collecting arguments** that can be specified in definition:

  * Positional collector is denoted by a single star before the name and will collect everything passed to it in a tuple:
    ```python
    def func_collector(a: int, *b: int) -> None:
      print(f'{a=} {b=}')
      
    func_collector(1, 2, 3)
    ```

  * Positional collector should be last positional argument — every argument after it becomes keyword-only and positional-dependent in call:
    ```python
    def func_collector(*b: int, a: int) -> None:
      print(f'{a=} {b=}')
      
    func_collector(1, 2, 3)  # fails
    func_collector(a=1, 2, 3)  # fails
    func_collector(1, 2, a=3)
    ```

  * This gives us a way to force caller to use keyword arguments, but we also have a bit of blurry signature — it will accept any number of arguments without a protest

  * But there's a way to force keyword-only arguments with a fixed number of arguments: all arguments after a single star without a name become kw-only
    ```python
    def kw_only(*, a: int, b: int) -> None:
      pass
    
    kw_only(1, 2)  # fails
    kw_only(1, a=1, b=2)  # fails
    kw_only(a=1, b=2)  
    ```

  * Keyword collector works by collecting all unspecified keyword arguments and denoted by two stars:
    ```python
    def func_kw_collector(a: int, **b: int) -> None:
      print(f'{a=} {b=}')
      
    func_kw_collector(1, x=2, y=3)
    ```

  * KW collector should be always last in definition

  * Good way to pass params to underlying functions if you don't want to define each and every one of them

* On caller side of things we have a way to use combined arguments also with packed arguments:

  * Single star for collection of positional arguments that will be unpacked by function to corresponding positions:
    ```python
    def pos_unpacking(a: int, b: int, c: int) -> int:
      return a + b + c
    
    args = [1, 2, 3]
    pos_unpacking(*args)
    ```

  * double star for dictionary of keyword-value pairs for kw-arguments:
    ```python
    def kw_unpacking(*, a: int, b: int, c: int) -> int:
      return a + b + c
    
    kwargs = {'a': 1, 'b': 2, 'c': 3}
    kw_unpacking(**kwargs)
    ```

* Reference table of all possible argument options:
  <img src="/Users/noni1001/Projects/PLC/lectures/img/argument_matching.png" alt="argument_matching" style="zoom:50%;" />

* Steps that Python takes to assign all arguments:

  * Assign nonkeyword arguments by position
  * Assign keyword arguments by matching names
  * Assign extra nonkeyword arguments to *name tuple
  * Assign extra keyword arguments to **name dictionary
  * Assign default values to unassigned arguments in header



[^1]: [Call frames and tail call optimization](https://towardsdatascience.com/python-stack-frames-and-tail-call-optimization-4d0ea55b0542)
[^2]: [A bit more on call frames](https://www.cs.toronto.edu/~david/course-notes/csc110-111/05-memory-model/05-python-memory-model-2.html)