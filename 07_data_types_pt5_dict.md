# Data types, part 5: Dictionaries

## Lookup difficulty, big O notation and hash tables

* Every algorithm has time to complete it's task – that would be called time complexity
* To describe time complexity big O notation is used — it looks like `O(1), O(n), O(n²)` etc
* I'm really bad at explaining math-related thing, and this is undoubtedly came from math, so just the basics:
  * `O(1)` (constant time) is very good — it means that algorithm has constant time complexity. It might be bad if said time is long enough, but generally it's a good thing since it's execution wouldn't be lengthened with growth of data amount. Example of that would be accessing element of list by index — we know where it is since list is ordered by index.
  * `O(log n)` is very math. It is slower than flat access time, but still considered good for, say, sorting. Example is binary search: search in ordered list, but without indexes. Check middle of the list, move to left or right if it's less or more correspondingly, continue until found
  * `O(n)` (linear time) happens when you need to check every single item in the input for algorithm to work: say, printing every item in the list
  * `O(n^2)` (quadratic time) would be a case if you need to find all possible pairs in a list
  * Only most significant part of complexity is used — `O(n²-n)` (quadratic algorithm where last iteration is dropped) would still be `O(n²)` since `n` is insignificant compared to `n²` 
  * There're a few more, you can look them up. Here's a shot summary of how bad things can get[^1][^2][^3]:
    ![](./img/big_o_comp.jpeg)
* Let's remember lists and apply newfound complexity knowledge to them:
  * looking up element by index would be **ask** `O(1)` operation — indexes are ordered and sequential
  * looking up element by value would be **ask** `O(n)` operation — we have to go through all elements to find it (average scenario, remember about significant part)
* To solve issue of accessing elements by value new data structure was devised — hash table.
  * Hash table has explicit indices, unlike list, where index is implicit and just shows position in the list - 1
  * Indices are created by hashing (you should remember that from few lectures ago — this was one of things we discussed while trying to split data types into as much categories as possible) the value
  * Each hash has it's own fixed place
  * When querying hash table for value, value is hashed again and this index is used to fetch value in **ask** `O(1)` time in best case scenario — if there's a hash collision, it rises to `O(n)`
  * Python has it's own built-in implementation of variation of hash table — dictionary

## Dictionary in Python

* Dictionaries in Python are superset of hash tables: it can use (almost) anything as a *key*, not just hash of the value
  * Key in this context means index by which value will be fetched — and we should and will use key from now on; and structure itself would be called either dictionary or key-value mapping (closest definition to one used by Python devs)
  * Keys should be **ask**: immutable, and thus hashable; and unique — although this isn't enforced by interpreter and new value with the same key will just overwrite old one
    * **ask** Strings, numbers and tuples of strings, numbers or tuples can be used as keys
  * Values can be anything you want — «arbitrary value» as stated in docs. This ranges from number and strings to lists, dicts and entire modules
* **ask** Let's fit dictionary into data type categories we used on previous ones:
  * It is a container since it can include any number of other values, as well as being nested
    * Max size is, as usually, limited by architecture and memory available
  * Mutable — contents can be added, removed or changed in-place
    * Since it is mutable container, don't forget about `copy` and `deepcopy`
  * Sequenced — there're actually several iterators connected with dictionary — keys, values and key-value pairs (most useful if you ask me)
  * Surprisingly enough, it's ordered since Python 3.7, but you shouldn't be using it to access key-values in particular order anyway — list is better suited for that. You can, but really shouldn't
* If I understand how time works, we should be in a closing third of the class and only now starting with how to actually use dictionaries in Python
  * Legitimate question would be: why so little time devoted to it, and so much to the theory?
  * Answer is simple: since it's shares categories with some datatypes you all already know, it also shares a lot of methods and operators
  * And understanding the theory is much more important — particular operator can be always looked up in docs, but with theory you will know *what* to look up

## Dictionaries: hands-on

* As per usual, there're several ways of creating dictionary, empty or not:
  * `dict()` — you can pass list of key-value tuples into it: `dict([(1, 'a'), (2, 'b'), (3, 'c')])`
  * `{}` — comma-separated list of key:value pairs: `{1: 'a', 2: 'b', 3: 'c'}`
    * Be careful, omitting `:` will turn this into `set` : `{1, 'a', 2, 'b', 3, 'c'}`
  * Comprehensions, of course (again, this is topic for a later date), enclosed with curly brackets: `{k:v for k,v in iterable}`
  * One more — and this is reason for you to look into some of built-ins on your own — is using `zip()` with `dict()`
    * `zip` allows you to turn several lists into list of tuples with corresponding items from each list (all first items, all second and so on): `dict(zip([1, 2, 3], ['a', 'b', 'c']))`
    * This, in my experience, is used more often than can expected from such convoluted way of creating dictionaries
* Next important operation is retrieving values from dictionary. This is done by key in two ways:
  * `dict[key]` will return value that is associated with `key`
  * `dict.get(key, [default])` will return value that is associated with `key`, but if key is not found — will return `None` or `default` if passed. Retrieving with brackets will raise exception if key is not found
* After creating and retrieving values what left is inserting new values into dictionary, or *updating* it
  * `dict[key] = value` — since it's mutable type, this is possible, quite similar to list
  * `dict.update()`
    * `dict.update(other_dict)` including constructors
    * `dict.update(key1='value', key2='value')` — key should be Python identifier, not string
    * `dict.update([(1, 'a'), (2, 'b'), (3, 'c')])`
    * All values will be overwritten on duplicate keys
* Removing values is simple: `dict.pop(key, [default])`. Returns value if key is present, `None` or `default` if not
* Three of dict-exclusive methods for retrieving data from it:
  * `dict.keys()` will return all keys from dictionary
  * `dict.values()` — values, will always return False if compared, even with itself 
  * `dict.items()` — key-values pairs
  * All of these methods return special view objects[^4] — subject for self-paced learning. They should be only used in iteration context.
* New in Python 3.9+ `dict1 | dict2` will merge them
* Rest of methods are similar to list's methods: `len`, `key in dict`, `reversed` and more[^5]

* You might have noticed visual similarity with JSON files with all the curly brackets and colons. Dict maps on JSON pretty good and Python even provides tools to save dictionaries as JSON files out of the box

## Homework

* Familiarise yourself with Big O notation, view objects and rest of dictionary methods



[^1]: [Big O Notation](https://www.freecodecamp.org/news/big-o-notation-why-it-matters-and-why-it-doesnt-1674cfa8a23c/)
[^2]: [Big O Exampels](https://developerinsider.co/big-o-notation-explained-with-examples/)
[^3]: [Python TimeComplexity](https://wiki.python.org/moin/TimeComplexity)
[^4]: [View objects](https://docs.python.org/3/library/stdtypes.html#dict-views)
[^5]: [Dict methods](https://docs.python.org/3/library/stdtypes.html#typesmapping)