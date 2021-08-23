## Chapter 10: Define Errors Out of Existence

I think this is a dangerous chapter. Ousterhout takes too long talking about how to eliminate errors
in an API, and not enough time talking about when you should.

I think the rule for when you should is actually pretty simple. You can write one of these or the
other:

    fn smorgle(factor: i32) -> Result<Smorgle, SmorglingError> {
        if let Some(smorgle) = try_to_smorgle(factor) {
            Ok(smorgle)
        } else {
            Err(SmorglingError::new())
        }
    }

    fn smorgle(factor: i32) -> Smorgle {
        if let Some(smorgle) = try_to_smorgle(factor) {
            smorgle
        } else {
            // either just crash
            panic!("failed to smorgle");

            // or mask the exception, say by retrying
            wait(30 seconds);
            smorgle(factor);

            // or define special cases out of existence
            // (maybe we can smorgle in reverse?)
            smorgle(-factor)
        }
    }

The key is to ask about the caller. Would the caller be happy with how your `smorgle` function
handles the tricky case? Or would you be making the caller's life difficult by always panicking /
retrying / reverse smorglging?

Frequently, you _can_ handle the tricky case just fine yourself and not make your callers deal with
it:

- In the `substring` example, truncating the substring is what most callers want, and the few that
  don't can check the bounds themselves.
- In the NFS example, the caller typically can't do anything without a filesystem anyways, so they
  really _can't_ handle the error themselves. [Oleg pretty strongly disagrees with this. I trust him
  on this.]
- In the "crash if out of memory" example, programmers typically don't want to think about out of
  memory errors, and couldn't do much beyond crashing anyways.

However, sometimes the caller _does want_ to control what happens in the exceptional case. It might
be difficult or impossible for them to proceed without control. In this case, it's very important to
return a `Result`. For example:

- In Javascript, saying `x.foo` if `x` has no `foo` field doesn't error. It returns `undefined`
  instead. But this doesn't actually prevent any errors! It just lets them flow downstream, until
  you try to do something with `undefined`. The only effect of this behavior is making it harder to
  find the source of an error.

## Chapter 11

### Oleg

Top to bottom vs. bottom to top. Both work. Design twice? Nope.

- Write down thoughts. Reread later maybe.
- Draw some pictures.
- List of invariants. (!)

Mostly before.

Obsessed with visibility. [Strong agree from me.]

### Chris

Use type checker to validate thoughts, as part of design process. Takes some notes, some diagrams if
on paper. Less about implementation, and more about high-level context and plans. Write down steps.
Before coding.

### Vikram

Writing comments. Slowly turn them into code. Design issue -> pause and draw on paper. Variety of
diagrams, maybe high-level informal state machine or communication between classes.

### Justin

Depending on what I'm doing, I'll write nothing, or mini todo-lists, or notes about the prior code,
or proofs, or notes from conversations with people about what the goal is, or diagrams of the
architecture, or potential APIs. Very context dependent. I'll do this mostly beforehand, but
sometimes in the middle of coding too.

## Chapter 12

Chris: distinguish between (i) documenting API boundary (fn, module), vs. (ii) documenting
implementation inline. [Strong agree from me.] Was raised with the idea that good code is self
documenting. My counterexample was if you're implementing a complicated algorithm from a paper,
ain't nohow that code gunna be self documenting.

Oleg: documentation should live with code. When a module is deep, a high level description is very
helpful. (Spend an hour understanding -> an intro is helpful.)
