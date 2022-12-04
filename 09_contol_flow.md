# Control flow

## Boolean: truth, lies & laziness

* Boolean values in Python, such as `True` and `False` are just special versions of one and zero **show**

* One of its special features is how different datatypes will be casted to Boolean **show**

  * Any number that is not a zero, would be `True` — doesn't matter if it is positive, negative, integer or has a floating point. Only zero will be cast to `False`

  * Any non-empty string will be cast to `True`, even if it only includes a single whitespace. Empty strings will be cast to `False`

  * Any non-empty container type will be cast to `True`, including list with a single empty list as member; any empty one — to `False` — if `lens` returns zero, container is false

  * `None` will always cast to `False`

  * Functions (not their results) and methods will be cast to `True`

  * Classes will be cast to `True`. Class instances will be too, but you can change this behaviour be defining either `__bool__` method, or its fallback `__len__`
    ```python
    class FalseClass:
      pass
      
    class TrueClassBool:
      def __bool__(self):
        return False
      
    class TrueClassLen:
      def __len__(self):
        return 0
    ```

* Python supports all the regular logic operators on Boolean or cast-to-bool values, but with a bit of a twist in case of cast values:

  * they return not `True` or `False`, but one of the values use in comparison

  * moreover, which value is returned is depends on operator 

  * it's always either left or right, since operators are binary

  * `or` will return left value that is cast to `True` or right in any other case **show**

    ```python
    1 or 0
    0 or 1
    0 or False
    1 or 2
    ```

  * Logic behind it is simple: if we know that first value is `True`, there's no reason to check the rest, since single truth is enough to fulfill `or` requirement — might as well return it; but if it is `False`, we need to check the rest of values

  * `and` has opposite logic: if _any_ value is `False`, requirement is failed already **show and ask**

    ```python
    0 and 1
    1 and 0
    0 and False
    1 and 2
    ```

  * But if first value is determined to be `True`, we need to keep going still — _all_ of them still must be truth

  * This logic is called «short-circuit evaluation»

  * For combined operators this logic is still applies on left-to-right basis with respect to precedense:
    ```python
    0 or 1 and 1
    0 or 0 and 1
    0 or 1 and 0
    1 or 2 and 1
    ```

  * for special cases, when you need to get either `True` or `False` you can either cast result to `bool`, or use `any()` and `all()`: it helps to clean up your code in case of combined logic operators of the same kind:
    ```python
    1 or 2 or 3 or 4 or 0 == any((1, 2, 3, 4, 0))
    1 and 2 and 3 and 0 == all((1, 2, 3, 0))
    ```

  * There's one more operator that that you might think is missing **ask** : `not`; it does exctly what it says it does: inverts value and returns it as Boolean:
    ```python
    not 1
    not []
    ```

  * `not` has higher priority than other two and thus will be evaluated first

## Control flow: if, else, elif

* We know how to add data to out program, this is a good first step; and how to operate on it — but only if the operations are straightforward

* But for branching paths in logic we need more: control of the flow of execution, or control flow

  * NB: I always assumed it flow control, but turns out it's other way aroundd

* Most importand and useful tool for this is branching and it's implementation: if-else

* Python gives you not two, but three keywords for this type of branching: **show**

  * `if <condition>:` block after which will be executed if condition can be evaluated to `True` — this is why we took some time to go through Boolean logic
  * `else:` block will be executed if `if` condition is not met
  * `elif <condition>:` block will be executed if original condition is not met, but new one is

* Syntax is simple: **show** `if <condition> :` — and block of code after it should be indented to show that it is should be executed if this branch of execution is picked

  * Python uses it's own flavour of «code blocking»: indentations

  * This might seem weird for people who come from other languages, but it actually pretty intuitive once you get around it

  * But at some point in time you very well might grow to hate becasue it's not easily distinguishable with cursory overlook

  * Use tabs OR spaces — but not both: Python does not like **show**, your colleagues will not like it, and you in a few months will hate it

  * Every next nested block should be indednted deeper — does not matter how much, just the fact it is deeper

  * But coming back to previous point, use something reasonble: either pair of spaces or single tab

  * One quirck is multiline statements: **show**: use either parentesis or `\`
    ```python
    if 1 and 2 and 3 \
    		and 4 and 5:
      pass
    
    if (1 and 2 and 3
       and 4 and 5):
      pass
    ```

  * On the other hand, you can fit a few simple statements into a single line with `;`, given there's no nesting:
    ```python
    x = 1; y = 2; print(x+y)
    ```

* For `condition` you can use anything you want as long as it can be evaluated to Boolean value

* And given what we know about this topic, you can sometimes use shorthands for some conditions:

  * `if val > 0 == if val`
  * `if len(list) > 0 == if list`
  * `if ('substring' in 'string') is True == if 'substring' in 'string'`

* Complex conditions are possible:

  * `if val1 and val2`
  * `if val1 or val2`

* Use parenthesis for grouping conditions and assigning precedense:

  * `if (val1 and val2) or (val3 and val4)`
  * `if val1 and (val2 or val3)`

* Some operators that can be helpful in conditions since they return Boolean:

  * `>, <, >=, <=` — math comparison
  * `==, !=` — check for equality of values
  * `is, is not` — check for equality of objects **ask** — if both of the sides point to the same object in memory

* Parenthesis and complex conditions are still possible:

  * `(val1 >= val2) or (val3 <= val4)`

* Python is very good at providing syntax sugar, and if-else is not the exception:
  ```python
  if <condition>:
  	var = 'true_val'
  else:
    var = 'false_val'
  ```

  can be converted to ternary operator:

  `var = 'true_val' if <condition> else 'false_val'` — similar to how filter works in comprehensions

* With Python 3.8 and up one more neat feature was added, that we discussed before: walrus operator

  * in this case it allows you to assign value of condition calculation (or part of it) to the variable so it can be used later
    ```python
    a = 1
    b = 2
    if a - b > 0:
      c = a - b
      d = a + b + c
    else:
      c = a - b
      d = a + b - c
      
      
    if (c := a - b) > 0:
      d = a + b + c
    else:
      d = a + b - c
    ```

  * This is especially worth it for heavy calculations or expensive calls

  * Walrus is capable of so much more, but I'll let you discover it yourself[^1]

* One thing where Python fell short for 30 years is multiple excluding conditions

* Of course in included weird word `elif` that was portmanteau word of else and if and worked exatly like this

* But starting from Python 3.10 we've got what other languages had for a long time: switch case, or `match case` in Python
  ```python
  a = 3
  if a == 1:
  	print(1)
  elif a == 2:
  	print(2)
  elif a == 3:
  	print(3)
  else:
  	print(-1)
  	
  match a:
    case 1:
      print(1)
    case 2:
      print(2)
    case 3:
      print(3)
    case _:
      print(-1)
  ```

  * `match case` even includes ability to add addition condition and using several value to match against:

    ```python
    a = 1
    b = 2
    match a, b:
      case 0, b if b > 0:
        print(0)
      case 1, b if b < 0:
        print(a)
      case 1, b if b > 0:
        print(b)
      case _:
        print(-1)
    ```

  * …and logical operators between cases, capturing of cases, wildcards and so much more[^2]



[^1]:[Walrus operator](https://realpython.com/python-walrus-operator/)
[^2]: [Match case](https://peps.python.org/pep-0636/)

