# Data types, part 3: Strings

## Sequenced data types

* There is at aleast one more way to split variable types I came up with — sequenced and non-sequenced types.
* Let's take a look at two examples: number `2137` and word `lemon`. Both convey something to us, and to Python both are just few bytes in memory. But what's the difference if we try to apply our new categories to it?
  * Can we go over each of our examples symbol by symbol? Yes. But would it make sense for both equally?
  * Not really: every single letter in `lemon` is equal to any other. But every number in `2137` has vastly different meanings — ones, tens, hundreds and thousands.
  * Python seems to agree with us on this: one of this is sequenced type; other — not.
* Sequenced types — types that can be described as ordered collection of items. String is one of such types; later we'll also see lists, tuples and ranges. There are few more, and even more third-party, but that's beyond the scope for today.
* Just like number before, all sequenced types share some common methods and operations. Two of which we can cover right now, and rest when we get to it:
  * Iterating — the process of going through all the objects in the sequence in order. Used mostly in for loops, but not strictly.
  * Indexing & slicing — these two are connected tightly. Indexing is just accessing object in sequence by its index. As objects in sequence are ordered, each has its own unique place, marked by index, starting at 0, as usual. **show** in lemon case something like this `lemon[1]` would be read as `access and return second letter in the string` 
    * Slicing is interval indexing — with specified start and stop index, and step. We'll see it a bit later today.

## String basics

* Now onto the strings themselves.

* Unlike some other languages, Python does not have concept of a char type, only string. **show** String would be everything between two quotation marks — all the symbols. If you need a single character — just create a string with a single character in it.

  * Strings, as we mentioned before, are immutable — there's no way to change them in place. Every time you seemingly altered the string without reassigning it to the new variable, the new string was actually created and referenced. **ask** This also makes strings hashable.
    * **show** We can't change even smallest part of the string `'lemon'[1] = 'i'`
  * Python does not care about what type of quotation marks are enclosing the string as long as they are **show**:
    * one of these: `" or '`
    * match on both side of the string
    * I'm a fan of `'` single quotation mark myself, just because it looks a little bit lighter
  * **show** What if you adamant about using, say, double quotes and your string contains a quote? `""What, you egg!" - W. Shakespeare"` -> `"\"What, you egg!\" - W. Shakespeare"`
    * Same goes for some other characters including `\n` and personal favorite **show**  `\a`
  * **show** Most of the special symbols in repl output symbols are escaped, but not in print
  * **print**
  * **show** What if our string is really long, entire paragraph? Putting newline both in REPL or script file would mean an end of the line for interpreter and will break something
    * If it has no newlines, jsut go with `\`
      * `\` allows you to split almost any Python line between lines — very handy for staying under PEP limit for line length
    * If it does, you can always put all the line breaks manually with `\n`
    * or use multiline string by putting it between not two, but entire six quotation marks
    * **show** This is the same technique used to document functions and classes — docstrings. Take a good look at them — we'll be talking about them when time comes. But one thing I ask of you — use them as much as possible, it will help you and your colleagues immensely. You can even automatically generate documentation from them.

* All strings in relevant Python versions are unicode strings

  * As MTMs, I'm sure, you know full well what ASCII and non-ASCII is. I sure did in my time, especially with non-breakable whitespaces
  * ASCII is most basic encoding, including only 127 symbols — numbers, latin alphabet and some basic punctuation and control symbols[^4]
  * Unicode, on the othe hand, now includes around 150.000 characters. It has different encodings that differ by character size (UTF-8, 16, 32), has it's own way of defining characters that I'm not nearly qualified enough to talk about, and includes every imaginable alphabet and symbol.
  * **show** 🚳[^2]

* `r, u, f, rf, fr, b, rb, br` strings[^1]

  * There are few specail types of strings in Python, denoted by letter before leftmost quotation mark:

  * `b` is binary string, which hold binary representation of symbols of the string **show** `bytes('🚳', 'utf_8')`

  * `r` is raw string, which ignores special symbols like `\n`  **show** `u = 'a\nb'; r = r'a\nb'; print(u); print(r)`

  * `u` is unicode string. **ask** If all strings in Python are unicode, why this is needed? For compatibility with Python2

  * `f` strings is the one you'll use the most. It allows you to insert variables into the string without shuffling concatenation operators. **show**

    ```python
    int_var = 2
    fl_var = 3.5
    str_var = 'some string'
    combo_var = f'this is combined string; var = {int_var}; float = {fl_var}; string = {str_var}'
    # new self-documenting expressions, introduced in 3.8
    sd_var = f'this is combined string; {int_var=}; {fl_var=}; {str_var=}'
    ```

  * These different types of strings can be combined: `fr` etc.

* `format` is very powerful tool to format strings any way you want. It is so powerful that it features its own mini-language for formatting[^5]. I wouldn't spend much time on it, but give you a link to a documentation and show a few examples **show**

  ```python
  from math import pi
  h = 'hi!'
  print("{:.2f}".format(pi))
  '{:*^10}'.format(h)
  'int: {0:d};  hex: {0:x};  oct: {0:o};  bin: {0:b}'.format(42)
  ```

  

## String operations and methods

* As mentioned before, most of these operations are applicable not only to strings, but to most sequenced objects; for simplicity I would be using «string», btu you can extrapolate
* `+` is concatenation operator, which joins strings together
  * **show** concatenating two strings will create new object since they are immutable
    * **ask** implicit concatenation is also possible for strings only `a = 'hello' "world" '!'`
  * **ask** `-`
  * **ask** `*` 
* To check if some object is or isn't inside string there's `in` and `not in` operator
* Some useful operators that have something to do with numbers: `len(), count('x'), min(), max()`
* Now time to see indexing in action: `'lemon'[1]` — we know already what it does. Slicing look similar: `'lemon'[1:3]`. We can even use step `'synchronization'[1:11:2]`. There are some special indexing options: last object is `-1`, and reverse step is `-1`; and some values can be omitted. This allows you to solve task we actually use in interviews in a single line — reversing the string: `'string'[::-1]` **homework** This is not the best way to do it; why?
* `'string'.index('i')` returns index of first object that matches the argument. `rindex` does the same, but from right to left.
* Some string-specific methods:
  * `strip() / rstrip()` remove specified symbol from left/right side of the string. By default strip whitespaces
  * `replace` replaces specified symbol with another one
  * `split / rsplit` converts string into list of substrings by splitting string on specified symbol
  * `lower / upper / swapcase / capitalize` changes case of the string 
  * `isalnum / isnumeric / isascii` checks if string includes only specified symbols
  * …`help(str)` and much more

## String modules

* If you need more, as usual — batteries included `string` module provides many additional string functions and reference strings, such as ascii_letters or numbers; formatter and templating classes
* You want more headache? Batteries included, you remember — `re` is there for you. It allows you to use regular expressions. Not going to get into it, I've ahd enough in my time.

## Homework

* Find some visual poem you like and try to format it same way using `.format()`



[^1]: https://stackoverflow.com/questions/52360537/i-know-of-f-strings-but-what-are-r-strings-are-there-others
[^2]: https://unicode-table.com/en/1F6B3/
[^3]: [Python string escape characters](https://python-reference.readthedocs.io/en/latest/docs/str/escapes.html)
[^4]: https://www.johndcook.com/blog/2022/05/28/how-to-memorize-the-ascii-table/
[^5]: [String formatting language](https://docs.python.org/3/library/string.html#formatspec)
