# Data types, part 2: hands-on

## Hashability and more assignment ways

* One thing I forgot to mention one more way to split types: hashability. 

  * Hashability in python means exactly what it sounds like — ability of object to have hash.
  * Hash — (usually) numerical value that corresponds to the object, calculated via some type of math function
  * Hash function should be deterministic (or idempotent) — always return same value for same object; and robust — should not return same value for different objects
    * this is called hash collision and can lead to very difficult-to-track-down bugs: I vaguely remember reading a story of some game that had to be shipped very soon and at one point builds started to fail. Turns out there was a hash collision between two textures which caused this issue. The solution was to add single white pixel to one of the textures — that changed the hash enough to resolve the issue and ship the game.
      * actually I highly recommend to look into hacks and bugs in gamedev — gives you great insight.
  * Used mostly for speeding up search or access — it's always easier to compare, say, 10-digit numbers than 1024+ character strings
  * **ask** Which types category seems to be most similar to hashability? It's mutability. You can't hash an object that could be changed in place — than its hash wouldn't match.
  * For us it's now not that important, but would be soon — some complex data types require hashability for own members — sets and dicts.

* We already familiar with regular assignment: `var = 'value'` . But Python has more to offer **show**:

  * walrus operator ` var := expression` . Allows you to assign value to variable from expressions inside the statement

    ```python
    if (i: = 5+6) > 10:
      print(i)
    ```

    Has very niche use-cases, like moving dynamic calculations for values in dictionary inside the dictionary definition. But, while it's slim chance you'll use it oftem, might as well let you know about it now.

    * It's new for Python >=3.8; but you shouldn't use anything older anyway because 3.7 EOL is just nine moths away. There's no reason to start a project with version that would be dropped from support so shortly.

  * Multiple assignment — allows you, well, assign multiple values in a single line. Might be useful if you initializing a lot of similar variables in a single place. Syntax is pretty simple:

    ```python
    a, b, c = 1, 2, 3
    # it doesn't care about types of variables
    a, b, c = 1, 'b', ['c']
    # what it does care is about same number of object to both left and right of the =
    a, b, c = 1, 2, 3, 4
    # or does it?
    a = 1, 2, 3, 4
    # does it or does it not?
    a, *b = 1, 2, 3, 4
    ```

    Last two examples will be studied in depth down the line, around complex data types and functions, but in short in first case we assigned all of the values packed into tuple to a single variable; in second we assigned first value to first variable, and rest, packed into list, to the second one.

  * Consecutive assignment allows you to assign same value to a few variables: `a = b = 1` Be careful with mutable types **ask**[^3]

  * One last thing:

    ```python
    _ = 1
    ```

    In REPL `_` on itself is equal to the last exec result. But in scripts `_` usually means that we do not need this value further down the line. As many things in Python it's not a rule, but rather a suggestion.

## Numbers

* Numbers are unsurprisingly simple — they are just numbers, right?

* `int` is our most basic number type — it stores just that, integer numbers. Ranges from -2.147.483.648 through 2.147.483.647 at minimum, on some architectures it might be more **show** `big = 2147483650` ; or so internet would lead you to believe. But for Python we do have a proven source of truth — official documentation. And it says that integers have unlimited precision — as long as you got memory, you can store you numbers there.

  * By default Python integers are decimal. But you can use a range of other bases in specifying them: `0b111; 0o77; 0xFF` 

*  `float` is for when you need more precision — it stores floating number part, separated by `.`; you can't use `,`, it's used for different data type. Good for storing division results; so good, in fact, that any division result will be casted to `float` even if result does not have a floating part. Furthermore, if you do any operations between `int`and `float`, result will be converted to `float`. This applies to all mixed operations, and order of convertion looks like this `int -> float -> complex`

* If you remember standard notation from your highschool days, you can use it with Python: `4e2 == 400; 4e-2 == 0.04` Produces `float`

* Complex numbers are also supported with notation `real_part + img_partJ: 1 + 2j or 1 + 2J` — in this case (and with different bases and standard notation) notation is not case-sensitive. 

* Decimal and fractions 

  * Decimal is special type to keep float values with set precision.
    `from decimal import Decimal; Decimal('0.1') + Decimal('0.1') + Decimal('0.1') - Decimal('0.3')`
  * Fractions is special type to represent, well, fractions — sometimes might be useful.
    `from faractions import Fraction; Fraction(5, 3)`
  * Both of these have their own set of methods and operators, which can be found in documentation. You nost likely wouldn't use them much if at all, but if you do, use `print`

* There's a reason I mentioned all of these. **show**  `0.1 + 0.1 + 0.1 - 0.3`
  What happened here is not exclusively Python problem, it is actually a hardware problem rooted in binary representation of floating numbers — there's simply not enough precision to present exact float. We don't see it because Python tries to show us the shortes meaningful number.[^4] There are some ways to mitigate it. **show**

  * `round(0.1 + 0.1 + 0.1, 10) - round(0.3, 10)`
  * `Fraction(1, 10) + Fraction(1, 10) + Fraction(1, 10) - Fraction(3, 10)`
  * `Decimal('0.1') + Decimal('0.1') + Decimal('0.1') - Decimal('0.3')`
  * float.as_integer_ratio()
  * var.hex() == float.fromhex()

* Booleans are binary values, but in Python they are just names for 1 for True and 0 for False **show** `bool.__mro__` 1 is not the only value that menas true in Python, but we'll talk more about it in Flow control part.

* Now we know a lot about what numbers are and how to assign them in multiple ways, but we still missing one crucial feature — user input. Now in big projects chances of you using user input are pretty slim, unless it's a CLI application — you're more likely to use either config files or web input, which is handled in a different way. But for our purposes it's good enough for now. **show** `input()` allows you to do just that — read an input. It has exactly one argument — prompt, and it's optional. Always returns string stripped of newlines, so to align it better with today's topic, it needs to be converted to numerical value beforehand.

* Output goes hand in hand with input. There are options to format output in Python, such as f-strings, format and % **show**

  ```python
  pi = 3.14159265359
  print("%.2f" % pi)
  print("{:.2f}".format(pi))
  print(f'{pi:.2f}')
  ```

  But that's topic for strings discussion.

## Math

* Numbers are good and great, but pointless on their own (except for 1 and 0) if no operations can be performed on them. For the sake of simplicity let's call them 'math'.
* Like many things in Python and life in general, all math can be divied into several categories:
  * built-ins
  * operators: [^1]
    * Calculations `+ - * ** / // % @` 
      * `**` — power, same as `pow` 
      * `//` — floor division, lowers to nearest smaller int **show on negatives**
      * `%` — modulo
      * `@` — best operator, matrix multiplication. No built-in types support it
    * Comparison `< > <= >= == != ` and `and, or, not` **!!! LAZY**
      * chaining: `a > b > c > d` is the same as `a > b and b > c and c > d`
    * Bitwise `<< >> & | ^ ~ ` **show with bin**
  * functions:
    * Conversions `int, float`
    * Base conversions `bin, oct, hex` **ask** where's `dec` — `int` works just fine
    * Misc `complex, abs, pow, max, min, !sum, divmod = a//b, a%b, round`
      * `sum` does not intended to use with numbers
      * **show** `divmod`
  * Remember that, just like in math, operators have precedence[^2] and you should keep it mind.
  * `math ` — you might ask where's PI, trig functions and square root? `math` is most likely to have them. **show** `dir(math)`
  * `random` is for all your random needs — random values, random ints, random choices from lists etc **show** `dir(random)`
  * `cmath` — complex number math, I have no idea
  * but if need even more, there's `numpy` . It's not in standard installation, it's its own separate package, but it's a standard by this point in any kind of compute-heavy task, and used by Pandas and Spark, so you know it's good.


**Homework**: find a good, nice, complex formula and try to recreate in python using all the knowlegde from today.



[^1]: [Operators list](https://docs.python.org/3/reference/expressions.html#operator-precedence)
[^2]: [Operator precedence][https://docs.python.org/3/reference/expressions.html#operator-precedence]
[^3]: [Multiple assignments](https://note.nkmk.me/en/python-multi-variables-values/)
[^4]: [Floating point: issues and limitations](https://docs.python.org/3/tutorial/floatingpoint.html)

