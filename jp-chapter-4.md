## Chapter 4: Modules should be Deep

Modules! A module is anything that can be thought of as having an interface that's separate from its
implementation. Examples include classes, modules, namespaces, services, and even functions.

Chris points out: you don't actually want deeper modules. Doubling the complexity of a module for no
reason is not good.

#### Interfaces

Interfaces can be split into formal and informal parts:

- **Formal part of interface:** the interface as shown in the code, including its function
  signatures and types.
- **Informal part of the interface:** everything else a developer needs to know to use the module.

Having a clear interface reduces the unknown unkowns. Having a simple interface reduces the
complexity that module adds to the rest of the system, and makes it easier to change that module's
implementation without touching other modules.

#### Abstractions

**Abstraction:** "a simplified view of an entity that omits _unimportant_ details".

- If an abstraction includes unimportant details, that increases cognitive load, and adds to the
  complexity of the code that uses it.
- If an abstraction excludes important details, it's a _false abstraction_. To design a good
  abstraction, you must "understand what is important and look for designs that minimize the amount
  of information that is important".

Modules should be deep, not shallow. Meaning that they have a lot of functionality/code, but a
small/simple interface. For example:

- `HashMap` is a deep module: it hides the details of its implementation, and presents a relatively
  simple interface. (_But_ it's a large interface! I would distinguish between the size and the
  complexity of an interface, and say that it's important to reduce the complexity of an interface
  moreso than its size.)
- Garbage collection is a deep module. It has no interface at all! And you don't need to know about
  the implementation, as long as it isn't performance critical.
- `LinkedList` is a shallow module: it doesn't take a whole lot of code to implement. So much so
  that many Linux Kernal data structures 
- Unix file systems are a false abstraction. I'm strongly disagreeing with Ousterhout here. Sure,
  the _formal_ interface is small, and it does hide a lot of implementation details. But it's also
  _enormously complex_. Like, docs describing how those five functions work (from a user's
  perspective, ignoring the implementation) are _at least_ dozens of pages long.

"Interfaces should be designed to make the common case as simple as possible."

----

Overall, this chapter is fantstic. Separating code into modules with simple interfaces is
everything. I would make two changes:

- More emphasis on informal interfaces. The formal interface to Unix files may only be five
  functions, but the informal interface (i.e., docs fully describing them) is huge.
- More emphasis on correctness. If you fully capture the requirements of an interface in its type
  signature, then the type system can make it impossible to misuse.
