[![Test](https://github.com/apmadsen/delegate-pattern/actions/workflows/python-test.yml/badge.svg)](https://github.com/apmadsen/delegate-pattern/actions/workflows/python-test.yml)
[![Coverage](https://github.com/apmadsen/delegate-pattern/actions/workflows/python-test-coverage.yml/badge.svg)](https://github.com/apmadsen/delegate-pattern/actions/workflows/python-test-coverage.yml)
[![Stable Version](https://img.shields.io/pypi/v/delegate-pattern?label=stable&sort=semver&color=blue)](https://github.com/apmadsen/delegate-pattern/releases)
![Pre-release Version](https://img.shields.io/github/v/release/apmadsen/delegate-pattern?label=pre-release&include_prereleases&sort=semver&color=blue)
![PyPI - Python Version](https://img.shields.io/pypi/pyversions/delegate-pattern)
[![PyPI Downloads](https://static.pepy.tech/badge/delegate-pattern/week)](https://pepy.tech/projects/delegate-pattern)

# delegate-pattern: Python implementation of the Delegation Pattern.

delegate-pattern provides a basic implementation of the well-known Delegation Pattern.

## What is delegation

Delegation is a pattern in object oriented programming where a class (delegator) delegates responsibilities to one or more delegates.

This allows for greater code reusability and reduced class complexity and may help adhering to the DRY (Do not Repeat Yourself) and SoC (Separation of Concerns) principles.

## Example

Consider a trivial task of delegating the printing of an objects name to the console. Here the delegator `SomeClass` delegates the task to the delegate `PrintNameDelegate` which is not much more than a function wrapped in a class.

The delegate class is always initialized with an argument specifying its delegator, and while this example shows the use of a protocol class called `NamedClassProtocol`, this is merely included for convenience, and will not be enforced at runtime. In fact the type of `delegator` can be anything, but delegate constructors cannot have any other arguments.

Note that it's strongly recommended to use weak references to the delegator in the delegate constructor.

```python
from typing import Protocol
from weakref import ref
from delegate.pattern import delegate

class NamedClassProtocol(Protocol):
    _name: str

class PrintNameDelegate:
    def __init__(self, delegator: NamedClassProtocol): # delegator type annotation is syntactic sugar and not enforced in any way
        self.delegator = ref(delegator)

    def __call__(self):
        print(self.delegator()._name)

class NamePropertyDelegate:
    def __init__(self, delegator: NamedClassProtocol): # delegator type annotation is syntactic sugar and not enforced in any way
        self.delegator = ref(delegator)

    def __get__(self) -> str:
        return self.delegator()._name

    def __set__(self, value: str):
        self.delegator()._name = value

class SomeClass:
    _name: str
    def __init__(self, name: str) -> None:
        self._name = name

    name_printer = delegate(PrintNameDelegate) # => PrintNameDelegate instance
    name = delegate(NamePropertyDelegate, str) # => string getter

some_instance = SomeClass("Neo")
some_instance.name_printer() # prints Neo

name = some_instance.name # => Neo
some_instance.name = "Trinity"
new_name = some_instance.name # => Trinity
```

# API

## Delegate[T\<DelegateProtocol>] class

The `Delegate` class handles registration onto class delegators and the instantiation and communication with the delegate protocol classes. Typically the `dlg = delegate(T)` function is used to assign delegates to classes instead of using the `dlg = Delegate[T]()` syntax since it's cleaner.

### Delegate[T\<DelegateProtocol>](passthrough: _bool_ = _False_) constructor

The `Delegate` class can be instantiated with or without parameter `passthrough` which specifies if or not communication with delegates should be passed through directly.

When `passthrough` is false, the Delegate class returns the delegate protocol instance when `__get__` in accessed
 on the delegator class, which means that the delegate protocol is visible on the delegator class tree.

When `passthrough` is true, the Delegate class passes `__get__` and `__set__` actions directly onto the delegate protocol instance, which means that whatever is returned from `__get__` is visible on the delegator class tree.

## delegate(T<DelegateProtocol>, passthrough_type: _type[Tr]_) -> _Tr_ function

The `delegate` function is the preferred way of registering delegates, and makes typing easier when registering passthrough delegate protocols.

### Example 1

```python
from delegate.pattern import delegate

class SayHelloDelegate:
    def __init__(self, delegator: object):
        ...

    def __call__(self) -> str:
        return "Hello world"

class Class1:
    dlg = delegate(SayHelloDelegate)

inst = Class1()
inst.dlg() # => 'Hello world'
```

### Example 2

```python
from delegate.pattern import delegate

class PropertyDelegate:
    def __init__(self, delegator: object):
        self.prop = ""

    def __get__(self) -> str:
        return self.prop

    def __set__(self, value: str):
        self.prop = value

class Class1:
    dlg = delegate(PropertyDelegate, str)

inst = Class1()
inst.dlg # => ''
inst.dlg = "Some value"
inst.dlg # => 'Some value'
```