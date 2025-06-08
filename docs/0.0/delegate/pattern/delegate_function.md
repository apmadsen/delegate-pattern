[Documentation](/docs/documentation.md) >
 [v0.0](/docs/0.0/version.md) >
  [`delegate.pattern`](/docs/0.0/delegate/pattern/module.md) >
   delegate

## delegate(T<DelegateProtocol>, passthrough_type: _type[Tr]_) -> _Tr_

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