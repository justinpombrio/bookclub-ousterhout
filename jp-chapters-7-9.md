## Chapter 7: Different Layer, Different Abstraction

"In a well-designed system, each layer provides a different abstraction from the layers above and
below it; if you follow a single operation is it moves up and down through layers by invoking
methods, the abstractions change with each method call." Totally agreed.

**Question:** "For example, virtually everyone who creates a Java `InputStream` will also create a
`BufferedInputStream`, and buffering is a natural part of I/O, so these classes should have been
combined." I follow the reasoning, but am hesitant somehow. Somehow, the
InputStream/BufferedInputStream _feels_ like a good abstraction to me, but I'm not sure why. Is
there something to my intuition, or is it just bad intuition? Maybe it's a useful pattern,
misapplied?

Ousterhout argues against decorators (`decorate :: (A: Interface) => A -> A`), and gives some
alternatives. But he's missing a big one! Rust iterators make heavy use of decorators, all with the
same pattern: (i) there's a small core interface (`Iterator::next`) that everything implements, and
that the decorator wraps; and (ii) there's a much much larger interface (the rest of `Iterator`)
that comes for free.

_Pass through variables_ are tricky. A pass through variable is a variable that gets passed
through a long chain of variables, in order to reach a lower layer, that most of the methods being
passed through shouldn't need to know about. He didn't have much in the way of good solutions.
Biggest thing was merging these variables together into a "context" object, so there's only one of
them, or trying to pass them through some other way.

**Question:** any good solutions to pass through variables? I have two ideas:

- One is a somewhat involved language feature ("ambience").
- The other is making an opaque wrapper around the "pass through variable". For example, instead of
  passing down a `Certificate` with all sorts of certificate related methods on it, you would pass
  down a `CertificateInABox`, which _has_ a `Certificate` (and only a `Certificate`), but has zero
  public methods. So the _only_ thing you can do with it is pass it around. Thus the functions it's
  passed through still need to know that it exists, but nothing beyond that, which is a smaller
  dependency.

## Chapter 8: Pull Complexity Downwards

"It is more important for a module to have a simple interface than a simple implementation." Put
differently, if there's complexity related to a module, have the module deal with it rather than its
users. Amen.

I see a clear difference in what Rust and Go aim for with regards to this: Rust handles the
complexity, and Go leaves it to the user. This is, I think, very much on purpose for both languages.
For example:

- Rust has distinct types for valid unicode strings (`String`), byte arrays (`&[u8]`), and OS
  strings (`OsString`). Go _only_ has byte arrays, called `string`, and uses them for all these
  distinct purposes. It must just throw errors if you try to use one kind of string where another is
  needed.
- The Rust `std::fs` module docs say: "This module contains basic methods to manipulate the contents
  of the local filesystem. All methods in this module represent cross-platform filesystem
  operations. Extra platform-specific functionality can be found in the extension traits of
  std::os::$platform." In contrast, Go filesystem operations pretend that every OS is a Unix, and if
  you do a Unix-only operation on a non-Unix platform then it silently fails or returns dummy
  values.
- Rust's package manager lets users specify minimum and maximum versions of a dependency, and it
  deals with the constraint-satisfaction problem of finding appropriate verions to use. On the other
  hand, Go's package manager only lets you specify a minimum version and uses the naive algorithm of
  picking the maximum of the minimums. That runs into trouble if a package you use makes a
  backwards-incompatible change: there's no way to say "I really really just want this specific
  version". There are some legitimate tradeoffs here, but it illustrates the difference in
  philosophy: simple for the user, or simple for the language?

(I got most of these examples from the rant ["I want off Mr. Golang's Wild
Ride"](https://fasterthanli.me/articles/i-want-off-mr-golangs-wild-ride))

One major example of this is _configuration parameters_. They, by design, leak internal details, and
thus should be avoided when it's reasonable to do so, and even then have default values so that most
users can ignore them.

## Chapter 9: Better Together or Better Apart?

- Bring modules together if they share information.
- Bring modules together if it will simplify the interface.
- Bring modules together to eliminate duplication.
- Separate general-purpose and special-purpose code.

#### 9.5: Example: insertion cursor and selection

How about this implementation instead? It "defines errors out of existence".

    struct Editor {
      cursorHead: Position,
      cursorTail: Position,
      ...
    }

    impl Editor {
      fn rightArrow(&mut self) {
        self.cursorHead += 1;
        self.cursorTail = self.cursorHead;
      }

      fn shiftRightArrow(&mut self) {
        self.cursorHead += 1;
      }

      fn selection(&self) -> (Position, Position) {
        (self.cursorHead.min(self.cursorTail), self.cursorHead.max(self.cursorTail))
      }

      ...
    }

#### 9.6: Example: separate class for logging

Generally agreed: put logging code in your main code, even if it makes it a bit more verbose. Though
Rust has such good ergonomics for annotating your error enums with how to print them that I think
it makes that approach reasonable too.

#### 9.7: Example: editor undo mechanism

Hmm:

    public void redo();
    public void undo();

Shared mutable state, anyone? Rust will make your life miserable if you try to do this. Maybe you
can do something like this, to get the state under control:

    trait Action<S> {
      fn redo(&self, state: &mut S);
      fn undo(&self, state: &mut S);
    }

    struct History<S> { ... }

    impl History<S> {
      fn addAction(&mut self, action: Action<S>);
      fn addFence(&mut self);

      fn undo(&mut self);
      fn redo(&mut self);
    }

#### 9.8: Splitting and joining methods

Say you have a big complicated function `f()`, and want to split it.

- Good: `caller -> f() -> g()`.
- Good, but rarely works: `caller -> f(); caller -> g()`
- Bad (too many functions; likely to be shallow):
  `caller -> f() -> g(); caller -> h(); caller -> i()`.
