# Rust

An introductory course covering Rust's core concepts. Rust is a systems language that guarantees memory safety without a garbage collector through its ownership model.

## Course outline

- [Basics](basics.md) - installation, hello world, variables, types, functions
- [Ownership & Borrowing](ownership.md) - ownership, borrowing, references, slices
- [Structs, Enums & Patterns](types.md) - structs, enums, pattern matching
- [Error Handling](error-handling.md) - `Result`, `Option`, the `?` operator
- [Traits & Generics](traits.md) - traits, generics, lifetimes

## Resources

- [The Rust Book](https://doc.rust-lang.org/book/)
- [Rust by Example](https://doc.rust-lang.org/rust-by-example/)
- [Rustlings exercises](https://github.com/rust-lang/rustlings)
- [Standard library docs](https://doc.rust-lang.org/std/)

## Install

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustup update
```

```bash
cargo new hello_world
cargo build
cargo run
cargo test
```
