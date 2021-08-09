## Chapter 5: Information Hiding (and Leakage)

#### Information Hiding

Details of a module's implementation should be hidden, and not appear in its interface or spread to
other modules. Some examples (from the book) of information you may want to hide:

- How to store information in a B-tree, and how to access it efficiently.
- How to identify the physical disk block corresponding to each logical block within a file.
- How to implement the TCP network protocol.
- How to schedule threads on a multi-core processor.
- How to parse JSON documents.

Information hiding simplfies a module's interface, thus reducing cognitive load in its users and
increasing the chance that its implementation can be changed without touching any other code. It
reduces dependencies between modules.

You can have _partially hidden information_, which is partially good. For example, having uncommon
functionality in a module which is exposed only via special methods that most users never need to
know about.

"Back-door leakage" is when there is a dependency between modules that isn't visible in their
interfaces. For example, one module writes a file and another reads it, and both depend on the file
format. Also along these line, just because a field is marked as private doesn't mean it's hidden.

"When designing modules, focus on the knowledge that's needed to perform each task, not on the order
in which tasks occur."

"Information hiding can often be improved by making a class slightly larger." (e.g. because it
brings together related code, or merges multiple steps into one.)
