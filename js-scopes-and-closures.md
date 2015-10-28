## Prototype chains

Objects are just collections of more references to new objects and prototypes. The only special thing they add is the prototype chain for when you try to access a property that's not in the local object, but is in a parent object.

## Scopes

### Functions

> Functions can be drawn as special objects that contain executable code as well as properties. Every function has a special `[scope]` property that represents the environment it was in when it was defined. If a function is returned from another function then this reference to the old environment is closed over by the new function in a **closure**.

### The global object

If a variable is defined without the `var` keyword, it is added as a property of the global object, e.g. `window` in a web browser or `global` in nodeJS.

## References

- [Learning Javascript with Object Graphs](http://howtonode.org/object-graphs)
- [Why use "closure"?](http://howtonode.org/why-use-closure)
- [What is "this"?](http://howtonode.org/what-is-this)
