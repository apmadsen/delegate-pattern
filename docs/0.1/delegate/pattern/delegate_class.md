[Documentation](../../../documentation.md) >
 [v0.1](../../version.md) >
  [delegate](../module.md) >
   [pattern](module.md) >
    Delegate

# Delegate[T\<[DelegateProtocol](protocols/delegate_protocol.md)\>]

The `Delegate` class handles registration onto class delegators and the instantiation and communication with the delegate protocol classes. Typically the `dlg = delegate(T)` function is used to assign delegates to classes instead of using the `dlg = Delegate[T]()` syntax since it's cleaner.

> NOTE: Delegator classes (eg. classes upon which delegates are specified) using slots, need to define attributes `__weakref__` and `__delegates__`.

## Constructors

### Delegate[T\<[DelegateProtocol](protocols/delegate_protocol.md)>](passthrough: _bool_ = _False_)

The `Delegate` class can be instantiated with or without parameter `passthrough` which specifies if or not communication with delegates should be passed through directly.

When `passthrough` is false, the Delegate class returns the delegate protocol instance when `__get__` in accessed
 on the delegator class, which means that the delegate protocol is visible on the delegator class tree.

When `passthrough` is true, the Delegate class passes `__get__` and `__set__` actions directly onto the delegate protocol instance, which means that whatever is returned from `__get__` is visible on the delegator class tree.

