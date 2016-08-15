- Feature Name: global_asm
- Start Date: 2016-03-18
- RFC PR: https://github.com/rust-lang/rfcs/pull/1548
- Rust Issue: https://github.com/rust-lang/rust/issues/35119

# Summary
[summary]: #summary

This RFC exposes LLVM's support for [module-level inline assembly](http://llvm.org/docs/LangRef.html#module-level-inline-assembly) by adding a `global_asm!` macro. The syntax is very simple: it just takes a string literal containing the assembly code.

Example:
```rust
global_asm!(r#"
.globl my_asm_func
my_asm_func:
    ret
"#);

extern {
    fn my_asm_func();
}
```

# Motivation
[motivation]: #motivation

There are two main use cases for this feature. The first is that it allows functions to be written completely in assembly, which mostly eliminates the need for a `naked` attribute. This is mainly useful for function that use a custom calling convention, such as interrupt handlers.

Another important use case is that it allows external assembly files to be used in a Rust module without needing hacks in the build system:

```rust
global_asm!(include_str!("my_asm_file.s"));
```

Assembly files can also be preprocessed or generated by `build.rs` (for example using the C preprocessor), which will produce output files in the Cargo output directory:

```rust
global_asm!(include_str!(concat!(env!("OUT_DIR"), "/preprocessed_asm.s")));
```

# Detailed design
[design]: #detailed-design

See description above, not much to add. The macro will map directly to LLVM's `module asm`.

# Drawbacks
[drawbacks]: #drawbacks

Like `asm!`, this feature depends on LLVM's integrated assembler.

# Alternatives
[alternatives]: #alternatives

The current way of including external assembly is to compile the assembly files using gcc in `build.rs` and link them into the Rust program as a static library.

An alternative for functions written entirely in assembly is to add a [`#[naked]` function attribute](https://github.com/rust-lang/rfcs/pull/1201).

# Unresolved questions
[unresolved]: #unresolved-questions

None