# Data types, part 6: The Rest

## Sets

* One of the types that we skipped in numerical variables discussion that we really shouldn't have is set

* Set is a term that comes from math and means about as what you would expect: several values in a single container
  * Word «container» might give you some ideas about categories in which sets fall in: they are containers, they are iterable and they are mutable
  * Now, what differentiates them from lists in this case **ask**: they're actually hash maps under the hood, and in as with it is a case with proper hash maps it does not allow for duplicate values — duplicate keys, to be precise, but since keys are derived from values, this is the same thing
  * This means that items in sets must be hashable, thus immutable
  * Other difference is that sets are unordered: retrieve order isn't guaranteed
  * All of the time complexity is shared with hash maps
  
* With most of the theory out of the way by either being shared with previously discussed data types, or being too math for me to explain, let's get our hands on:
  * Creating set is simple: either used keyword `set(iter)` with iterator passed to it, or `{a, b...n}` curly brackets. Pay attention to the empty sets: they can be only created with `set()`, curly brackets with no values will create empty dictionary
  
  * Sets do not have a way to access  its members neither via index, nor values — what's the point then? Exactly like in set theory — check for inclusion of value in set, get diffference between sets or simply make unique list
  
  * `add()` or `update()` will add value or values to the set
  
  * `remove()`, `discard()` and `pop()` will remove values from set. `pop` return removed values, rest — aren't. `discard` will not fail if value not found, `remove` will
  
  * Being math concept, sets have a number of related methods:
  
  * `issuperset`, `issubset`, `isdisjoint` (no intersections)
  
  * `difference`, `intersection`, `union` and some more
  
  * Equality operations on sets work in a way that can be applied to their math counterparts, i. e. `>` means is proper superset  of etc.
  
  * ```python
    a = set('abracadabra')
    b = set('alacazam')
    a                                  # unique letters in a
    {'a', 'r', 'b', 'c', 'd'}
    a - b                              # letters in a but not in b
    {'r', 'd', 'b'}
    a | b                              # letters in a or b or both
    {'a', 'c', 'r', 'd', 'b', 'm', 'z', 'l'}
    a & b                              # letters in both a and b
    {'a', 'c'}
    a ^ b                              # letters in a or b but not both
    {'r', 'd', 'b', 'm', 'z', 'l'}
    ```
  
  * The rest is available at docs[^1]
  
* Main use for sets is making sure your list has unique elements

## Tuples

* Tuples is almost the same as the list, but immutable. That's the main difference
* This allow you to use complex values as keys **show**
* Bit faster — due to some compiler optimization, less abstraction layers between them and actual objects and fixed size allows to allocate memory more efficiently[^2]
* Tuples also default data type for multiple assignments (as we already know) and function arguments — we'll se it later
* Creating tuple: `tuple(iter)`, `(1, 2 ... n)` or even `var = 1, 2, ... n`; although be careful with last method — it doesn't work everywhere
  * If you want to create tuple with the size of 1, don't forget about trailing coma **show** `t = (1)`
* Tuples implement all of common sequence operators[^3] save for add and update operators

## More containers

* Sometimes you just need more, and Python does provide: enter `collections`
* If you have some task that requires almost standard data type, but not quite — `collections` most likely has it, or something really close
* Good example would be `defaultdict`: **show**
  * You need to collect list of items belonging to a key
  * One of the ways is go through all the items, looking them up in resulting dictionary and either create empty list if not present and then updating list with new values; or updating outright if present
  * `defaultdict(list)` allows you to skip check step since every new key assigned empty `list` as value *by default*
  * Or if you want to just count entries of certain keys in something, you can go with `d = defaultdict(int)` + `d[key] += 1`…
  * …or `d = Counter()` + `d[key] += 1`. End result would be the same, but `Counter` shows your intent more clearly
* `ChainMap` allows you to combine several mappings (dicts in our case) into sort of single big dictionary — where `get` will return first found key, but every add/update operations will only work with first mapping
* …and more[^4]

## Comprehensions

* One of the ways to create sequence type variables, that I mentioned quite a few times before is comprehension – single-line way of defining list (and more), that combines `map` and `filter` — those are function programming concepts, that we wouldn't touch much

  * `map` is a higher order function (function that takes other function(s) as arguments or returns function(s) as result) that applies some function to each values in a container

    ```python
    l = [1, 2, 3, 4]
    func_inc = lambda x: x + 1
    list(map(func_inc, l))
    ```

  * `filter` is a higher order function that applies some function with binary return to each value in container and returns values for which function was eavaluated to `True`

    ```python
    l = [1, 2, 3, 4]
    is_odd = lambda x: x%2 == 1
    is_odd = lambda x: x&1
    list(filter(is_odd, l))
    ```

  * It's worth noting that both of these functions, being built-ins, are written in C and thus pretty fast[^5]

* Comprehensions, as the name *might* suggest are might be easier to understand than for loop — although not always; but it name means not what you might think it means — it's not «reading comprehension» comprehension, it's «set comprehension» comprehension, aka «set builder notation» or «complete inclusion»[^6]

* Aside from map and filter we also need *source* for comprehension — it should iterator, but for now for simplicity sake let's go with sequenced container — it can be iterator, and that's enough for us today

* To sum it up: comprehension will take every object that passes the *filter* from *iterator*, apply (or *map*) some function to it and collect result to final data structure — be it list, dict or set

* List comprehension hands-on:
  ```python
  res = []
  src = '12345'
  for val in src:
    res.append(int(val) + 1)
  
  res_c = [int(val) + 1 for val in src]
  ```

* This would work (in most cases) faster than for loop because:

  * map and filter are written in C
  * `list.append(val)` operation invokes method search in each iteration, which is somewhat costly
  * `for` loop itself has some overhead, which is negligeble in complex loops, but might be important in simple ones
    * if you concerned about this, you might look into C or Rust instead of Python

* Now to show you both full power of comprehensions, but also when you, probably, should not be using them:
  ```python
  res = []
  src = '12345'
  for val in src:
    if int(val) & 1:
      res.append(int(val) * 10)
    else:
      res.append(-1)
      
  res_c = [int(val) * 10 if int(val) & 1 else -1 for val in src]
  ```

* But sometimes single `if` is enough of a filter, and that can be made more readable by moving it to the end:
  ```python
  src = '12345'
  res_c = [int(val) for val in src if int(val) & 1]
  ```

* Set comprehension is almost the same, but brackets are different and rule of unique values is applied:
  ```python
  res = set()
  src = '12345'
  for val in src:
    if int(val) & 1:
      res.add(int(val) * 10)
    else:
      res.add(-1)
      
  res_c = {conv_val * 10 if (conv_val := int(val) & 1) else -1 for val in src}
  ```

* Tuple comprehension isn't a thing, but any ideas what this'll do?
  ```python
  res_c = (conv_val * 10 if (conv_val := int(val) & 1) else -1 for val in src)
  ```

  * That's for another time, let be a mystery for now

* Dict comprehension looks very similar to set, but now you need to supply both key and value — so you either need source that returns pairs, or map something to the key to get value. We'll go with later and use original value as key and our converted (tenfold for odd, -1 for even) as value:
  ```python
  res = {}
  src = '12345'
  for val in src:
    if int(val) & 1:
      res.update({val: int(val) * 10})
    else:
      res.update({val: -1})
    
  res_c = {val: (int(val)*10 if int(val)&1 else -1) for val in src}
  ```

* Comprehensions are best when used with caution

## Homework

* [Student majors](https://www.testdome.com/questions/python/student-majors/59363)
* [Unique names](https://www.testdome.com/questions/python/merge-names/62365)
* [File owners](https://www.testdome.com/questions/python/file-owners/36510)
* [Ice cream machine](https://www.testdome.com/questions/python/ice-cream-machine/82969)



[^1]: [Set operations](https://realpython.com/python-sets/)
[^2]: [Are tuples faster than lists](https://stackoverflow.com/questions/68630/are-tuples-more-efficient-than-lists-in-python)
[^3]: [Common sequence operators](https://docs.python.org/3/library/stdtypes.html#typesseq-common)
[^4]: [collections](https://docs.python.org/3/library/collections.html)

[^5]: [maps](https://wiki.python.org/moin/PythonSpeed/PerformanceTips#Loops)
[^6]: [Set builder notation](https://en.wikipedia.org/wiki/Set-builder_notation)
