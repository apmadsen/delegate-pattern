[Documentation](../documentation.md) >
 v0.1

# Delegate Pattern version 0.1

NEW IN THIS VERSION:

- While version 0.0 only supported stateful delegates, this version also supports stateless delegates. The main differecne between the two, is that while stateful delegate instances exist in a 1:1 relationship with the corresponding class instances, and takes a `delegator` argument in their constructor, stateless delegate instances are shared between the corresponding class instances, and their  `__get__`, `__set__` and `__delete__` functions take a `delegator` argument

## Modules

### [delegate](delegate/module.md)

