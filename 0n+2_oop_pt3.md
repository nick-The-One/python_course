# OOP in Python, part 2

## Inheritance

* Inheritance is a mechanism that allows one class to, well, inherit attributes and methods from other. Of course implicitely.
  * This allows you to rewrite logic of classes without copy-pasting the code: just change attributes/methods that need it

* Basic syntax is very simple: you use brackets with `class` and pass all parent classes as arguments — note that I said arguments, not argument. Multiple inheritance is possible.
* You can (and in case of `__init__`) most likely should access superclass methods by either `SuperClass.method(self)` or `super().method()` 
* Most important part to keep in mind about inheritance: attributes/methods in child classes overwrite same attributes/methods in parent classes for that scope.

* We'll go over search order and method resolution order (MRO) next time

https://realpython.com/inheritance-composition-python/