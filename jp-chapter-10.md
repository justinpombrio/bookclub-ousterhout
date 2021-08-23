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
  really _can't_ handle the error themselves.
- In the "crash if out of memory" example, programmers typically don't want to think about out of
  memory errors, and couldn't do much beyond crashing anyways.

However, sometimes the caller _does want_ to control what happens in the exceptional case. It might
be difficult or impossible for them to proceed without control. In this case, it's very important to
return a `Result`.
