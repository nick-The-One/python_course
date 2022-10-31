# Data types, part 4: Lists

## Container data types

* All the types we spoke before – numbers of different varieties and strings – could hold a single value per variable. Even strings, which are iterable type and can be considered a list of characters, still has a single value – entire string
* On the other hand there are another type of types, that we are starting with today – containers
* Containers are exactly are what you think they are — they can contain several different values
* Containers are not restricted to single «sort» of data to be put into it — any data (with some restriction, of course, but that's for another time) can be put in
* There are few built-in contianer types — list, dict, set and tuple; and few shipped with Python — mostly found in `collections` module — deque (double-ended queue), namedtuple, counter and much more
* But start would be a list — one of the most used datatypes in Python in my experience

## List theory

* Main differences between list and string are already described above, but let us still use this them both to see what lists are

  * Both string and list are collection of objects, that much is common

    * String can contain only characters. **show** Those might be letters or numbers, weird letters or numbers, symbols or weird symbols, or even invisible chars — but only as long as they are present in Unicode
    * List can contain anything inside of it: **show** numbers, strings, variables, even entire modules

  * Both are sequenced types, and can be accessed by offset/index/slicing **show**

    * list, unlike string, can be not only accessed by index, but also assigned by index **ask** — because lists are mutable datatype. While this is incredibly convinient, it is also pretty dangerous if you are not paying attention and can cause a lot of problems down the line — more about that in functions and class topics

    * **show** 

      ```py
      a = [1, 2, 3]
      b = a
      a.append(4)
      b
      ```

  * Inhereted from being sequenced are the fact they are both ordered — objects inside will be stored in order they were put in

    * If you append to the list not by index, but by using `append` method, new objects will be added to the right

  * String has a set length — defined by it's value. Since list is mutable, it's length is variable and can be changed at any time

    * **ask** Max size of the list in python is defined by your system

      * **show** st1

        ```python
        import sys
        sys.maxsize
        ```

      * Unit of measure for this value is count — this is how much elements can be in the list regardless of type, be it `1` or entirety of pi
      * **ask** This is possible due to list not saving objects directly inside itself, but rather referenced to the objects, and references are collected

  * **show** If list can include any other object, it can include other lists — that would be nesting. And since list can include lists, which can include lists, which can include lists and so, deep nesting is possible

    * List can even include itself as own member **ask** in that case it would be deleted from memory when all other objects that reference it are not doing it anymore
    * From what I can gather there are no limits on nesting depth
    * While lists being mutable are double-edged sword, nested lists are double edged sword with hilt made out of blades

## Lists hands-on

* There are, as usual, a few ways to create a list: **show**

  * Type constructor `list(iterable)`
  * Square brackets (most common way) `[a, b c]`
  * List comprehension (this is the topic for a later lesson, after we will be done with datatypes) `[x for x in iterable]`

* The end result for all these ways is the same, difference in what they accept as the argument:

  * iterable for `list`
  * comma-separated values for `[]`
  * iterable + logic for comprehensions

* Empty list can be created with any of these methods by just not supplying any values

* List, being an iterable, shares a lot of operators and methods with the rest of the group:

  * `[] + []`

    * **show** Unlike string, adding to the list does changes it in-place

      ```py
      l1 = [1, 2, 3]
      id(l1)
      l1 += [4]
      id(l1)
      ```

    * **show** Unlike strings again, values of list can be reassigned in-place:

      ```py
      l2 = [4, 5, 6]
      id(l2)
      l2[1] = 9
      id(l2)
      ```

    * even entire ranges:
      ```py
      l2[1:3] = [7, 8]
      # even if you are reassinging entire range to a sinle value, use iterable
      l2[1:3] = 0
      ```

  * `*` is also possible — it just copies content of the list into iself `n` times **show**

  * **ask** `-` is not possible

  * Indexing works exactly like with strings, with slicing and step and all

  * `list.index(value)` works in reverse — returns index by value

    * `rindex` is string only method

  * To check if some object is or isn't inside string there's `in` and `not in` operator

  * Some useful operators that have something to do with numbers: `len(), count('x'), min(), max()`

    * `min` and `max` will work only on homogenous lists OR if you define compare methods for every datatype present in list

* Some methods are not exclusive to the list, but wouldn't work with string **ask** since list is mutable:

  * `list.sort(key=func, reverse=False)` sorts list in place, ascending. If provided with key function will sort using results of this function **show**

    ```py
    import random
    l1 = [1, 2, 3, 4, 5]
    sort_func = lambda x: x * random.randint(1, 10)
    l1.sort(key=sort_func)
    ```

  * if you need your list both to maintain original order and be sorted either create it in order or make a copy of the list and sort it (but don't forget about referencing the same list!) or **show** use `sorted(list)` function — it returns a sorted list, while not changing original one

    * There's one caveat with `sort` in cpython — you can't access the list while it being sorted. Most likely you will not encounter this, because sorting the list of reasonable size is very quick, but just so you know — this behaviour is undefined and list is considered to be empty while being sorted in cpython[^1]

  * Index assignment as we seen before; and it's copy `list.insert(idx, val)`

  * `list.remove(val)` or `list.pop(idx)` both remove values from list that either equial to (remove) or at index (pop). Pop also returns the value

  * One of the most used methods would be `list.append(val)` — it just adds new value to the list (to the right) **show** if the new values is a list — the whole list would be added

  * If you only need to add values from the list, and not the whole thinf — use `list.extend(list)`

  * Function that can save you — `list = copy(list)` **show**
    ```python
    l2 = copy(l1)
    l1 == l2
    l1 is l2
    ```

  * It's equivalent is `list = list[:]` **ask** because it make a slice of whole list, and not just passes the whole thing

  * **show** nested lists are still might cause you trouble:

    ```python
    l1 = [1, 2, 3]
    l2 = [4, 5, l1]
    l3 = l2.copy()
    l2.append(6)
    l2
    l3
    l1.append(0)
    l2
    l3
    ```

    Solution is available, although it isn't built-in, but shipped with — `deepcopy` , which creates copy of each and every nested object (not limited to lists) it finds:
    ```python
    from copy import deepcopy
    l1 = [1, 2, 3]
    l2 = [4, 5, l1]
    l3 = deepcopy(l2)
    l2.append(6)
    l2
    l3
    l1.append(0)
    l2
    l3
    ```

    ## Homework

    * Take your favourite short literary work (< 1000 words), *split* it into list (don't do it manually, there's a built-in way to do it), and get every 2, 3 ... nth word from it until you couldn't get what the story was about. Write down the threshold. Be proud of it.

[^1]:[cpython implementation of sort](https://docs.python.org/3/library/stdtypes.html#list)
