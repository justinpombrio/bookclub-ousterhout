## Chapter 2: The Nature of Complexity

Good definition of complexity:

> Complexity is anything related to the structure of a software system that makes it hard to
> understand and modify the system.

Three symptoms of complexity:

- **Change amplification.** When a seemingly simple change requires modifications in many places.
- **Cognitive load.** When you need to remember a lot of things at once to be able to make a change
  correctly.
- **Unknown unknowns.** Not only does modifying the code require certain pieces of knowledge, but it
  isn't obvious that this knowledge is required. So it's easy to make a change that looks good, but
  actually causes a subtle bug. This is the worst symptom of complexity.

Queue the standard quote:

> "There are two ways of constructing a software design. One way is to make it so simple that there
> are obviously no deficiencies. And the other way is to make it so complicated that there are no
> obvious deficiencies." - C.A.R. Hoare

Ousterhout gives two _causes_ of complexity:

- **Dependencies.** When the code in question relies on other code, and if you want to modify it,
  you must understand and/or modify that other code too.
- **Obscurity.** When important information is not obvious.

This classification feels arbitrary to me. Which always brings to mind Borges' [Celestial Emporium
of Benevolent Knowledge](https://en.wikipedia.org/wiki/Celestial_Emporium_of_Benevolent_Knowledge)

In other words, why these two causes of complexity? Are they exclusive? Are there other causes? Why
break them down this way?

For example, take some code:

    def get_products(db, prev_name):
      """Get a list of products, sorted by name. The list is paginated.
         Returns a tuple of (product_list, new_pagination_mark)."""
      sql = "SELECT Name, Price FROM Product WHERE Name >= %s ORDER_BY Name LIMIT 20"
            % prev_name
      product_list = db.query(sql)
      final_name = product_list[-1][1]
      return (product_list, final_name)

To "understand and modify" this function, you need to know:

- The basics of SQL.
- What pagination is. Otherwise the structure of this function will be a bit mysterious.
- What a SQL injection attack is, and whether `prev_name` could have been constructed by a malicious
  user. If so, part of the behavior of this function is "may pwn your system".

So, how do these things relate to "dependencies" and "obscurity"? For example:

- Is the SQL standard a dependency? Or just the `db.query` function?
- Is "pagination" a dependency, or obscurity? The code that does pagination is _here_, not
  elsewhere, so it shouldn't be a dependency. So it seems that it isn't a dependency or an
  obscurity. But it's also just as obvious as it should be. Maybe it's complexity, but it's "okay"
  complexity, and dependencies and obscurities are about "bad" complexity?
- The SQL injection attack must be obscurity: it's non-obvious important information.
