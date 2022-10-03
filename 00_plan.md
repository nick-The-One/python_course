# Grand Plan

## Intro

* Why I wouldn't be teaching programming
* Why Python
* Course structure
* Technical prerequisites
* Docs, IDE, do it

## First program

* What's a program
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
  * Sets
  * cyclic reference `l = [1, 2]; l.append(l)`
  * copy, deepcopy
* Dicts
  * Dictionary Merge & Update Operators (3.9+)


weakref?

* flow control
  * case (3.10+)

## Functions

* Function stack frames https://web.archive.org/web/20220809063346/https://sites.cs.ucsb.edu/~pconrad/cs8/topics.beta/theStack/02/
* Positional-only parameters
* unpacking and its order:  https://docs.python.org/3/reference/expressions.html#calls

## OOP

* What is OOP
  * OOP vs Procedural vs Functional
  * OOP features
* Names mangling
* Inheritance lookup
* Name Mangling in sublasses
* Search Order
  * current object — class above from bottom to top, left to right (order of superclasses)
  * search on reference only
  * mro
* operator overloading/interception