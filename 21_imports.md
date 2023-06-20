# Modules, packages and imports

* One of the selling points of the Python is the «batteries included» concept — meaning that it comes with everything one might need to use it in the project in almost any domain[^1]

* But including such niche things like tracing memory allocation, reading WAV files, `fcntl` system calls (whatever that is) and mysterious «iPad dress» in the main namespace would be a huge pain to deal with — name clashes, slower startup, useless autocomplete etc.

* Not only that, but there are a lot of things that either not covered but these inculded tools, or done better elsewhere — so much so that there's a separate PEP[^2] that covers this topic

* To resolve both of these issues, concept of imports was introduced — although I can't say with a full certainty that it was «indtroduced» and not present from the start of the language, since I didn't do any research into it

* Import is the mechanism of adding objects from separate namespace into current one — be it classes, functions, variables or even other modules

* To import anything into the namespace, there needs to be a source of code to import — and that would be a *module*

* A module is a Python file that contains code in it — simple as that; as such, any code you written becomes a module once saved, and can be imported from any other module or interactive session

  * Of course, Python wouldn't be Python if it was that simple; module also can by Python compiled code, C extension, .zip file, in-memory image, generated code sent over network, Java class or .NET component — but we'll just skip all of them graciously as those are none of our concern

* Now that we know about modules and import mechanism concept, it should be easy to implement it in the code:
  ```python
  import module_a
  ```

* This statement does a few things at the execution:

  * find requested module — this we'll discuss further down the line

  * compile to bytecode if needed — if no .pyc file present, or one present is older that last change of module source or compiled using different Python version

  * load it

  * creates new namespace `module_a` in the caller namespace

  * executes all code in the `module_a`

  * makes everything from `module_a` accessible in the `module_a` namespace of the caller:
    ```python
    dir(module_a)
    ```

* To use anything from imported module, dot notation is used:
  ```python
  import module_a
  
  module_a.funca()
  ```

* Alternative to that would be importing something directly into the current namespace by using `import from`
  ```python
  from module_a import funca
  funca()
  ```

* …or highly not recommended `from import *`, which would import everything from module into current namespace and can break things:
  ```python
  # module_a
  int = 1
  
  # importing module
  from module_a import *
  a = int('1')
  >>> TypeError: 'int' object is not callable
  ```

* Additional keyword that can be used with imports is `as` that renames imported object in the current namespace:
  ```python
  import module_a as a
  a.funca()
  ```

* If one module imports another, that module also can be accessed from current context:
  ```python
  # module_a
  a = 'a from module a'
  
  # module_b
  import module_a
  b = 'b from module b'
  
  # current
  import module_b
  module_b.module_a.a
  ```

* On import code of the imported module will be executed, so beware of what you put there
  ```python
  # module a
  a = 'a from module a'
  print('this has no relevance to the functionality of the module')
  
  # current
  import module_a
  
  >>>this has no relevance to the functionality of the module
  ```

* Now, in the examples above all modules that were imported were located right next to the current module/interactive session, but something like `math` isn't, yet still can be imported without any errors:
  ```python
  import math
  >>>...
  ```

* To achieve this, module have to be found before loading, and like with many things that involve searching, this is done via lookup chain

* For imports module search path looks like this:

  * Local directory — one from where interactive session was started or where script is located

  * `PYTHONPATH` environmental variable (if set)

  * Standard library location (where Python was installed more or less)

  * `.pth` files — text files with one path per line; almost nobody uses that

  * `site packages`, where all of the external modules are installed via pip

  * As usual, there's a way to check all the paths used in lookup: 
    ```python
    import sys
    sys.path
    ```

  * `sys.path`, being a list, can be modified by adding new paths there, which will be searched on the all future imports until the end of the script/interactive session

  * `module.__file__` attribute contains path to the module in the filesystem

* To further organize modules, they can combined into *packages*

* Package is esentially a folder containing one or more modules from filesystem point of view; and from Python PoV it's a separate namespace, containing more (module) namespaces

* This helps with both organize modules in a sensible way, and to awoid module name clashes:
  ```python
  # |
  # |-current.py
  # |-package_a
  # | |-module_a.py
  # | |-module_b.py
  # |-package_b
  #   |-module_a.py
  
  # current 
  from package_a import module_a as aa
  from package_b import module_a as ba
  ```

* Packages can contain another packages (subpackages), to import them just use more dot notations:
  ```python
  from package_a.subpackage_a.subpackage_b.subpackage_c import module aabcm
  ```

* Now, importing package itself wouldn't do anything useful, despite being valid statement:

  ```python
  import package_a
  package_a.module_a
  >>>AttributeError: module 'package_a' has no attribute 'module_a'
  ```

* To make this work — as in to make importing package add all modules to current namespace — we need to export these packages via `__init__.py` file

* Before Python 3.3 this file was required for *all* packages — it had to be present in the directory, even containing nothing at all, just for this directory to be considered a package; now you *can* create packages without it, but for regular packages it's still required — without one it will become *namespace package* — more on that later

* `__init__.py` is executed first on package import and everything defined in it is made global for this package:

  ```python
  # __init__.py
  A = 'this is global A'
  
  # current
  import package
  package.A
  >>>this is global A
  
  # package.module
  from package import A
  ```

* And since this file is executed on import and everything from it added to the namespace, importing own modules in it would make them available in the current one:
  ```python
  # __init__.py
  import package.module
  
  # current
  import package
  package.module
  ```

* `__init__` also has a special attribute `__all__` that defines what exactly would be imported with `from package import *` with a list of string names of modules to be exported:

  ```python
  # |
  # |-current.py
  # |-package_a
  # | |-module_a.py
  # | |-module_b.py
  # | |-module_c.py
  # |-package_b
  #   |-module_a.py
  
  # package_a init:
  # empty
  
  # current
  from package_a import *
  dir()
  >>>[...ll ]
  
  # package_a init:
  _all_ = ['module_a', 'module_b']
  
  # current
  from package_a import *
  dir()
  >>>[..., module_a, module_b]
  ```

* Same attribute can be used im modules to define which attributes are exported with star import:
  ```python
  # module a
  __all__ = ['a', 'b']
  
  a = 'a'
  
  def b() -> None:
      return None
  
  class C:
      pass
  
  # current
  from module_a import *
  dir()
  >>>[..., a, b]
  ```

* If you create a package without an `__init__.py` it becomes namespace package — a package that can be a part of other pacakage, without sharing the same folder. This is a pretty advanced feature, that, for example, could enable partial distribution of packages, and I don't really understand it good enough to explain, but this[^3] article does a pretty good job of it

* Another import-related quirk of package is the way of import inside of packages: import in modules inside packages skip current directory in the search path:
  ```python
  # |
  # |-current.py
  # |-package_a
  # | |-__init__.py
  # | |-module_a.py
  # | |-module_b.py
  # | |-module_c.py
  # |-package_b
  #   |-module_a.py
  
  # module_a
  import module_c
  
  # current
  from package_a import module_c
  >>>ModuleNotFoundError: No module named 'module_c'
  ```

* To work around this either use absolute import (make sure it's present in the search path):
  ```python
  # module_a
  from package_a import module_c
  ```

* …or use relative import — using dot to denote current directory, double dot for parent directory and so on:
  ```python
  # |
  # |-current.py
  # |-package_a
  # | |-__init__.py
  # |-subpackage_a
  # | |-__init__.py
  # | |-module_a.py
  # | |-module_c.py
  # |-subpackage_b
  # | |-__init__.py
  # | |-module_a.py
  
  
  # subpackage_a module_a
  from . import module_c
  
  # subpackage_b module_a
  from ..subpackage_a import module_a
  ```

* Relative import re-enables local folder lookup for modules in packages, but this time explicitely — thus disabling lookup chain; if relatively imported module is not found in the specified location, import will fail

* Relative import only works with `from importpython` construction

* Relative import only works in packages, and removes ability to execute file with relative import:
  ```python
  python module_a
  >>>ImportError: attempted relative import with no known parent package
  ```

* It's important to note that importing package again after it has been imported will not do anything — module code ran only once on first import

* If you need to reload already imported module to include all the changes, use `importlib.reload` — this will re-run the module code and update everything in it's namespace
  ```python
  from importlib import reload
  import module_a
  reload(module_a)
  ```

* To give you ability to install new packages, Python comes with a tool, called `pip` that installs, updates and removes packages to `site-package` on command

* By default all packages are searched on PyPi, but you can pass private index host to pip to use it

* Installation can't be simpler:
  ```bash
  pip install package_name
  ```

* Pip will install specified package of latest version (unless something else specified) in the `site-packages` and all of its dependencies, where they can be picked up by import mechanism

* One should be aware of where they run it from — virtual environment or just the system, because on this depends in which `site-packages` it will be installed: system or venv one

* Installing packages into system is highly discouraged — it might, or rather it will at some point cause some version clash issue in nth level dependency and it might be very difficult issue to debug and resolve

* So make sure you run virtual environment before using pip — a separate copy of the Python environment that is not available from the outside of it and in this way preventing any package clashes

* In general you shouldn't do it, but rather use some dependency manager, like poetry or… Just use poetry

* And for Python tools that are distributed via pip use pipx[^4] — isolated envorinment for tools



[^1]: [Python Library Reference](https://docs.python.org/3/library)
[^2]: [Recommended 3rd paty modules: PEP](https://peps.python.org/pep-0206/)
[^3]: [Namespace packages](https://realpython.com/python-namespace-package/)
[^4]: [Pipx](https://pypa.github.io/pipx/)