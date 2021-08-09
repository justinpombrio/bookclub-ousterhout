## Chapter 6: General-Purpose Modules are Deeper

Should code be special-purpose or general-purpose? Ousterhout says the sweet spot is in between:
"somewhat general purpose".

For example, consider writing a text editor. Here is a very specialized interface for modifying its
text contents:

    void backspace(Cursor cursor)
    void delete(Cursor cursor)
    void deleteSelection(Selection selection)
    ...

This is bad because it tangles together text manipulation and the UI. Also, though Ousterhout didn't
mention it, it must make implementing `undo` a nightmare. Instead of this, it's better to have a
more general interface:

    void insert(Position position, String newText)
    void delete(Position start, Position end)

How can you find an appropriately general interface like this? Three strategies:

- "What is the simplest interface that will cover all my current needs?"
- "In how many sites will this method be used?" Too few uses -> too specialized.
- "Is this API easy to use for my current needs?" Too hard to use -> may be too general.
