# Grand Plan

## Intro

* Why I wouldn't be teaching programming
* Why Python
* Course structure
* Technical prerequisites
* Docs, IDE, do it

## First program

* What's a program
* Writing first Python program – var, loop, if-else, function class
* How Python runs code
* How to run Python code

## Variables and types

* Static typing vs. dynamic typing
  * typing types (+ box examples?)
  * what is object and why does it matter (Lutz, 175)
  * type()
* Mutable and immutable
* References and reference count
  * Comparison:
    * == vs is
      * -5 to 256 https://docs.python.org/3/c-api/long.html
    * id()

  * Garbage collection
    * how often it runs
    * cyclic references
    * force
* Variables: what are they and how to assign them
  * what are variables vs const
  * assignement, referencing and re-assignement, 
  * naming
    * Conventions
  * Hashability
* Numeric values
  * multiple assignment, walrus
  * Int
  * float
  * complex: fraction, decimal
  * literals (Lutz, 134)
* Strings
  * iterables
  * quotes and escapes
  * bytes/bytearrays
  * multiline strings
  * `string` module
  * f-strings support `=` for self-documenting expressions and debugging https://docs.python.org/3/tutorial/inputoutput.html#tut-f-strings
* Bool
* walrus?, unpacking, entire chapter 11
* https://developers.google.com/edu/python/strings
* Lists
  * cyclic reference `l = [1, 2]; l.append(l)`
  * copy, deepcopy
* Dicts
  * Dictionary Merge & Update Operators (3.9+)
* Rest: sets, tuples, collections?
* Files

## Files

* Files are it's own entire beast, so complex and elaborate that there are entire file systems (note plural!), and do they bring headache
  * Try to write something on NTFS drive with mac
  * Try to do anything with ext partition with windows
  * boot anything from non-FAT drive
  * And many, many more such cases
* Taking that in consideration, let's look at most basic Python operations connected to files: open, read, write and close
* Open is self-explanatory


weakref?

* flow control
  * case (3.10+)
* Loops
* Iterators — here or after functions basics? Need to think about it
* Comprehension

## Functions

* Function stack frames https://web.archive.org/web/20220809063346/https://sites.cs.ucsb.edu/~pconrad/cs8/topics.beta/theStack/02/
* Positional-only parameters
* unpacking and its order:  https://docs.python.org/3/reference/expressions.html#calls
  * entire chapter 11

* Arguments:
  * default
  * named/positional
  * args/kwargs
  * packing/unpacking (530)
  * \* (539)
* Annotations (565)
* Generators 

## OOP

* What is OOP
  * OOP vs Procedural vs Functional
  * OOP features
* How to make a class
* Dataclasses
* Inheritance lookup
* Search Order
  * current object — class above from bottom to top, left to right (order of superclasses)
  * search on reference only
  * mro
* operator overloading/interception
* Names mangling, Name Mangling in sublasses

## Rest

* Built-ins cleanup: go through each and every built-in function: https://docs.python.org/3/library/functions.html