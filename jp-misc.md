## Random Unrelated Notes on Software Design

I found these notes lying around, on the topic of software design.

### Final Goals

- Feature complete
- Bug free, and easy to remain bug free
- Easy to extend and modify
- Easy to use

### How to get there

**Outside of the code itself:**

- Tests
- Peer review
- Types (unlike the above, can fully eliminate whole classes of bugs)
- Version control, for ease of collaboration

I think of the first three of these as the trifecta: three complementary ways to eliminate bugs.

**Inside the code:**

- Simplicity! Fewer places for bugs to hide, easier to modify, peer review. Tests go further.
- Stateless! Easier to test and reason about.
- Decoupled! Easier to think about A and B then about combined-A-B-monstrosity.
- Deduplication! Don't repeat yourself.

---

Make it hard to break abstractions, or break the code. I.e., make it hard for someone to misuse your
code, even if they are unfamiliar with it.

### Principles

- All possible instances of data should be valid. (A.k.a. "make illegal states unrepresentable".)
- All possible API calls should be valid.
