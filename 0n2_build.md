# Building & Distributing packages

## Distribution formats

* `sdist` or source distribution — just all the source files packaged with source of any C modules and extensions
  * dependencies are specified
  * everything that needs to be built will be built (if you're lucky) during installation and put where it belongs
  * this includes C extensions, that are very picky about toolchains and libraries used
  * is esentially an archive with all the source files, readme, license and manifest and install script (`setup.py`)
  * considered less preferable options because during installation all the code from `setup.py` will be executed
* `bdist` or built (not binary) distribution takes some of the burden off the shoulders of the end-user and actually builds everything before packaging it — thus providing (mostly) ready to use distribution
  * due to differences in OS and architecture `bdist` will be available only for end users on the same platform it was built on
    * this is usually should be reflected in the name of the package in the PyPi, as well as Python ABI (application binary interface)[^1]
    * although many packages distributed as `none-any` ABI-platform
  * is esentially is archive with libraries and packages
  * can in theory be run without installation, right from the archive, but it is discouraged[^2]
  * `bdist` can take several forms:
    * "dumb" archive/installer — just an archive with libraries/packages
    * RPM — more involved, Linux-specific format, native way of distribution on some platforms
    * egg — deprecated, don't use it
    * wheel — specified by PEP (single way to do it right), includes all of above + naming scheme that reflects platform; external uncommon shared libraries can be included with `auditwheel` or `delocate`
  * since `bdist` can include shared libraries that will not be updated when system ones are, this might be security concern

## Built-in solution

* `distutils` are deprecated and will be removed from Python 3.12
* `setuptools` are usually installed alongside the Python
* some claim that you don't get `setuptools` if building from source, but I built two versions on `ec2` instance and both have it available

## setuptools





[^1]: [Wheel naming convention](https://peps.python.org/pep-0491/#file-name-convention)
[^2]: [Running code from wheel](https://peps.python.org/pep-0491/#is-it-possible-to-import-python-code-directly-from-a-wheel-file)