# Loops

## Loops intro

* After getting the hang of variables and basics of control flow, the one thing that is missing to write proper, full-fledged programs are loops
* Loop is a sequence of instructions that is repeated until certain condition is met — simple as that
* Usually consist of two parts: header with condition of the loop, and body with the code that needs to be executed repeatedly
* Loops are mainstay of programming — almost every peace of software you run has something called the main loop
* Main loop is just an infinite in most cases loop that waits for inputs or other events and processes them
* One can write a program without any loops — and you will, a lot: scripts, pipelines, fire-and-forget types of utils
* But for most cases there's no way around learning looping tech, so let's start

## While loop

* There are two main types of loops that I know of, and one of them can be split in two:

  * for loops have a set number of iteration, specified in header
  * while — do loops, or cycles with pre-condition, i.e. condition is checked *before* cycle execution…
  * …and do — while loops, or post-condition cycles, where body is executed before condition check (thus making sure that body would be executed at least once regardless)

* Python features only two of them — for loop and while loop, no pre-condition loops for you out of the box, although you can make do with while and break statement — we'll see it later

* Let's start with while loop, since it's a bit simpler:
  ```python
  while condition:
    body
  ```

* Idea is simple: every iteration condition is checked, and if it is can be evaluated to True, body will be executed and next cycle begin — again from checking the condition

* Condition in this case could be called «passive» in a way — it does not change on itself, you need to do it expicitly do it in the body:
  ```python
  i = 10
  while i > 0:
    print(i)
    i -= 1
  ```

* Entire body should be a block of code (nested however deep you want it); end of the body would be indicated by returning to the ehader indentation level:

  ```python
  some code
  while condition: # header
    body
    body
    	still body
      	body again
      still body
    body
    body
  out of the loop
  ```

* `while` loops could be used to create infinite loops with condition that will always be evaluated to truth:

  ```python
  from time import sleep
  while True:
    sleep(1)
    print('hi!')
  print('bye!')
  ```

  There's nothing that can change the fact that `True` is True in the body of the loop, so it'll go on forever until stopped by keyboard interruption — thus `print('bye!')` will never be executed

* There's a number of special control statements, specific for loops:

  * `break` will, as it says, break out of the loop and continue execution of the code regardless of condition — and is a way to programmatically end infinite loops:

    ```python
    from time import sleep
    while True:
      sleep(1)
      print('hi!')
      break
    print('bye!')
    ```

    Using `break` statement one can emulate post-condition loop:
    ```python
    while True:
      body
      if end_condition:
        break
    ```

  * `continue` will jump to the next iteration without executing the rest of the body

    ```python
    from time import sleep
    while True:
      sleep(1)
      print('hi!')
      continue
      print('hello!')  # this line will never be executed
    ```

    This is often used with if-else controls to determine if the loop should be executed in full or cut short

  * `pass` you already know about — it does nothing; useful if you for whatever reason need to waste some time or just writing a stub — the loop (or function, or branching for that matter) body can't be empty, so `pass` needs to be used

  * `else` in conjunction with loops works in a different way compared to `if-else`:  `else` after the loop will be executed *only* if loop exits normally, after exhausting the condition; it will *not* be executed if loop exits via `break`:

    ```python
    i = 10
    while i:
      if i < 0:  # couldn't be reached
        print('not ok')
        break
      print(i)
      i -= 1
    else:  # will be executed
      print('ok')
      
    i = 10
    while i:
      if i < 5:  # will be executed
        print('not ok')
        break
      print(i)
      i -= 1
    else:  # will not be executed
      print('ok')
    ```

    

## For loop

* Next (and last) type of loop in Python is `for` loop

* Unlike `while` it does not operate on condition, but rather an iterable, going over it's members one by one

* Not only that, but the current value of the iterable member is stored and can be used in the body of the loop

* `for` loop will continue to iterate until there's nothing left in iterator object

* Any sequential object you know about (and some you don't yet) will work with `for` loop:

  * List
    ```python
    iter_list = [1, 2, 3]
    for i in iter_list:
      print(i)
    ```

  * Tuple
    ```python
    iter_tuple = (1, 2, 3)
    for i in iter_tuple:
      print(i)
    ```

  * Set
    ```python
    iter_set = {1, 2, 3}
    for i in iter_set:
      print(i)
    ```

  * Dict has three separate iterators (next time I'll tell you what exactly iterator is):

    * for keys:
      ```python
      iter_dict = {1: 'one', 2: 'two', 3: 'three'}
      for k in iter_dict.keys():
        print(k)
      ```

    * for values:
      ```python
      iter_dict = {1: 'one', 2: 'two', 3: 'three'}
      for v in iter_dict.values():
        print(v)
      ```

    * for key-value pairs — this iterator returns tuple, so you want to use multiple assignment with it:
      ```python
      iter_dict = {1: 'one', 2: 'two', 3: 'three'}
      for k,v in iter_dict.items():
        print(f'{k=}, {v=})
      ```

      Although multiple assignment is not required if you don't want to:

      ```python
      iter_dict = {1: 'one', 2: 'two', 3: 'three'}
      for kv in iter_dict.items():
        print(kv)
      ```

    * And lastly, if you don't specify the iterator you want, you'll get **ask** keys iterator:
      ```python
      iter_dict = {1: 'one', 2: 'two', 3: 'three'}
      for i in iter_dict:
        print(i)
      ```

  * Even for strings, if you want to:
    ```python
    iter_str = 'Quick brown fox jumps over the lazy dog'
    for char in iter_str:
      print(char)
    ```

  * But the one of the most common for loop, that you might remember from any other language is going over numbers from n to m with the step of k, e.g.
    ```c
    int i;
    for (i = 1; i < 11; ++i)
      {
        printf("%d ", i);
      }
    ```

    Python has it's own solution for this: `range` — immutable sequence type, that returns numbers from n to m with the step of k:
    ```python
    for i in range(1, 10, 2):
      print(i)
    ```

    You can ignore start (defaults to zero) and step (defaults to one), but end is mandatory

  * `zip` and `enumerate` can help you iterate over iterables in a special ways: either by combining several of them into one and returning tuples of values; or adding index into tuple with value from iterable

  * You can write your own iterable class or generator to iterate over, but that's for later

* `break`, `continue`, `pass` and `else` all work with `for` the same way they do with `while`

* One very important quote from Lutz:

  > As a general rule, you should *resist the temptation to count things in Python*—its iteration tools automate much of the work you do to loop over collections in lower-level languages like C.

Don't
```python
iter_list = [1, 2, 3]
for i in len(iter_list):
  print(iter_list[i])
```

Just
```python
iter_list = [1, 2, 3]
for i in iter_list:
  print(i)
```

I highly recommend checking out part `Loop Coding Techniques` from chapter 13 of his book if you want to know more.