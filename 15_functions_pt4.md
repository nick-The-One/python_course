# Functions, pt. 4

## Generators

* Regular functions are single-run: once they reach `return` statement or end of itself, they'll return value(s) or `None` and that'll be it: frame is deleted from stack, state removed and new call will produce new frame

* But there's a way to *generate* multiple returns from a single function call with state being saved between calls: generator functions, or just generators

* For now I will use generator and generator function interchangeably, but there's also generator comprehension, so just keep that in mind for now

* Generator is a special kind of function — even defined as function with `def` — that produces results not all at once, but rather one by one over time — new result with each call

* To achieve that generator functions actually compiled into generators — objects, that supports iteration protocol methods: `__iter__` and `__next__` methods, and raises `StopIterations` when exausted

* From user point of view each `__next__` call resumes function where it left off last time and produces new result on each "return"

* But how does Python knows which functions are to be compiled into generators since generator functions are defined in the same way as regular functions?

* For that we need to use `yield` keyword instead of `return`:
  ```python
  # regular function, returns entire list at once
  def func_a(n: int) -> List[int]:
    return [x for x in range(n)]
  
  # generator function, returns each value from range in iterative fashion
  def func_b(n: int) -> Iterable[int]:
    for x in range(n):
      yield x
      
  func_a(10)
  list(func_b(10))
  fb = func_b(10)
  next(fb)
  ```

* State of the generator will be saved on the point where `yield` appears and resumed after it when next value will be accessed

* So to summarize what we know about generator functions for now:

  * they are defined like regular functions, but with `yield` instead of `return`
  * they return iterator, which returns values one by one, and not all at once
  * their state is saved at point of reaching `yield` and resumed from the same point in the next call

* `return` is still can be used in generator functions, but it will not return any value, but rather raise `StopIteration`, so you can cut generation short:

  ```python
  # this will generate all value in range:
  def gen_a(n: int) -> Iterable[int]:
    for i in range(1, n):
      yield(i)
      
  # this will stop iteration at first value that iss divisble by 3
  def gen_b(n: int) -> Iterable[int]:
    for i in range(1, n):
      yield(i)
      # execution resumes from here on the next call
      if not i%3:
        return -1  # -1 is not returned
  ```

* …or you can have multiple `yield` statements, since they are not interrupting execution, but simply pausing it:
  ```python
  def multiple_yields() -> Iterator[str]:
    yield 'string 1'
    yield 'string 2'
    yield 'string 3'
    yield 'string 4'
  ```

* Why would anyone use generators instead of conventional functions? To save memory — since generators are using lazy computation and do not hold all values in memory at once:
  ```python
  rm -f mp* || true && mprof run full_list.py && mprof plot
  # uses ~550Mb of memory
  def gen_list(n: int) -> list[int]:
      return [x for x in range(n)]
  
  if __name__ == '__main__':
      for x in gen_list(int(14e6)):
          pass
        
  # uses ~18Mb of memory
  def gen_list(n: int) -> list[int]:
      for x in range(n):
          yield x
  
  if __name__ == '__main__':
      for x in gen_list(int(14e6)):
          pass
  ```

* One thing to remember about generators — they're their own iterators. I hope you know what that means, but if not — it means that they're single-use once created, even if you will iterate over them in different contexts:
  ```python
  def inf_gen() -> Iterator[int]:
    i = 0
    while True:
      yield i
      i += 1
      
  ig = inf_gen()
  iter(ig) == ig
  cont_1 = iter(ig)
  cont_2 = iter(ig)
  next(cont_1)
  next(cont_1)
  next(cont_2)
  ```

  

* I hope this was simple and clear, because now we're going to make it needlesly complicated with additional features of generators: `throw`, `close`, `send` and `from yield`

* `throw` allows you to send an exception to a generator:

  ```python
  def inf_gen() -> Iterator[int]:
    i = 0
    while True:
      yield i
      i += 1
      
  ig = inf_gen()
  next(ig)
  ig.throw(ValueError, 'For no reason at all')
  ```

* This can be useful to have a bit more control over generator — for example if we want to stop it from working after reaching some value

* There's a more elegant way of stopping the generator than just throwing wrench into its gears: `close()`

* What it does is actually still throws an exception, but specific kind **ask** — `StopIteration`, which will shutdown your iteration gracefully

* And now my favorite part — unlike functions, that take arguments only once, generators have ability to *receive* values every iterations with `send`

* Turns out that `yield` not only returns values, but also accepts them, and we can use it — and `send` is how we do that:
  ```python
  def inf_gen() -> Iterator[int]:
    i = 0
    while True:
      next_i = yield i
      if next_i is not None:  # if we do not send anything, yield returns None to state
        i = next_i
      i += 1
  
  ig = inf_gen()
  next(ig)
  ig.send(100)

* Be careful with sending any values to unprimed generator — it will result in an error, since generator state didn't reach `yield` yet and have nowhere to write received value; generators should be primed with `None` first
  ```python
  ig = inf_gen()
  ig.send(100)  # should throw an error
  ```

* Most difficult feature to expalin would be `yield from`:

  * It can be used as shorthand for `for` — as in *yield* values *from* iterable:
    ```python
    # regular way
    def gen_a(n: int) -> Iterable[int]:
      for i in range(n):
        yield i
        
    # form way
    def gen_b(n: int) -> Iterable[int]:
      yield from range(n)
    ```

  * It can be used to yield from AND send to another generator:

    ```python
    # og generator
    def gen_a() -> None:
      while True:
        val = yield
        print(f'<<< {val} >>>')
        
    # wrapper
    def gen_a_wrapper(g: Callable[[int], None]) -> None:
      yield from g
    ```

  * More examples can be found elsewhere[^1][^2]

* Generators got another implementation as comprehension

* Same general syntax as list or set comprehension, but with regular round brackets:
  ```python
  (x for x in range(5))
  ```

* Unline previous comprehensions this one does not produce all results at once, but rather provicdes iterator that returns values one by one

* It can be converted to list with all values, but that defeats the point of generator

* Last note on generators is how to annotate them

  * Either with `Generator`:
    ```python
    from typing import Generator
    # Generator[yield, send, return]
    def gen() -> Generator[int, int, None]:
    	val = 0
      while True:
        next_val = yield val
        if next_val is not None:
          val = next_val
        val += 1
    ```

  * …or with `Iterable`:
    ```python
    from typing import Iterator
    
    def gen() -> Iterator[int]:
    	val = 0
      while True:
        next_val = yield val
        if next_val is not None:
          val = next_val
        val += 1
    ```

    

[^1]: [Yield from](https://stackoverflow.com/questions/9708902/in-practice-what-are-the-main-uses-for-the-yield-from-syntax-in-python-3-3)
[^2]: [Realpython: generators](https://realpython.com/introduction-to-python-generators/)