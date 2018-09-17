- Feature Name: consistent-inner-doc-comments
- Start Date: 2018-09-16
- RFC PR: (leave this empty)
- Rust Issue: (leave this empty)

# Summary
[summary]: #summary

Allow inner doc comments inside structs, enums, and traits.

# Motivation
[motivation]: #motivation

Currently, inner doc comments (comments which begin with `//!` or `/*!` are only allowed inside of crates, modules, and functions (and maybe some other places), but they are not allowed in traits or structs. In those cases, the following error message is produced:

```
error: expected outer doc comment
 --> src/main.rs:2:5
  |
2 |     //! Foo!
  |     ^^^^^^^^
  |
  = note: inner doc comments like this (starting with `//!` or `/*!`) can only appear before items
```

Why do we want to do this?

- First, for consistency. Inner doc comments are usable inside many constructs already, so why not in traits and structs?
- Second, to satisfy those who prefer to use inner doc comments. There's something to be said for using them over outer doc comments: when visually reading code top to bottom, it can be useful to see the name of a construct before reading its documentation (and common Rust idioms do not involve repeating the trait/struct/function name at the top of its documentation). 

# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation

Inner doc comments can be consistently used anywhere outer doc comments can be used, if there is an associated block with an item. For example:

```rust
trait MyTrait {
  //! MyTrait is for fooable things.
  
  fn foo(self) {
    //! Foo the fooable.
  }
}
```

This is equivalent to the following code which uses outer doc comments:

```rust
/// MyTrait is for fooable things.
trait MyTrait {

  /// Foo the fooable
  fn foo(self) {}
}
```

This style of documentation can also be applied to structs and enums. Note that struct fields and enum variants do not have associated blocks, so the only option for documenting them is outer doc comments, before each field or variant.

```rust
struct MyStruct {
  //! A MyStruct is for structy things.
  
  /// The name of the thing.
  name: String,
}

enum MyEnum {
  //! Various possibilities!
  
  /// Possibility 1: A Foo
  Foo,
  /// Possibility 2: A Bar
  Bar,
}
```

# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation

This is the technical portion of the RFC. Explain the design in sufficient detail that:

- Its interaction with other features is clear.
- It is reasonably clear how the feature would be implemented.
- Corner cases are dissected by example.

The section should return to the examples given in the previous section, and explain more fully how the detailed proposal makes those examples work.

# Drawbacks
[drawbacks]: #drawbacks

Why should we *not* do this?

- Surely there are people who dislike seeing documentation after the header of the thing that's being documented, and this will mean they will see more of that type of thing, if people use it.
- The flexibility will probably lead to more inconsistency in style -- some people will use inner docs in these new places, while most people will presumably continue to use outer doc comments.


# Rationale and alternatives
[rationale-and-alternatives]: #rationale-and-alternatives

Rationale: this is a way to make the language more consistent with itself -- since inner doc comments are already supported in functions and `mod` blocks.

Alternative: Don't do this.

# Prior art
[prior-art]: #prior-art

Inner doc comments are consistent with the Python and Clojure style of documenting items, where the docstring comes after the item header. For example:

```python
class Foo(object):
    """This is a docstring for foo."""
```

```clojure
(defprotocol AProtocol
  "A doc string for AProtocol abstraction"
  (bar [a b] "bar docs"))
```

# Unresolved questions
[unresolved-questions]: #unresolved-questions

- Is there parser trickiness that prevents this from being supported already?
