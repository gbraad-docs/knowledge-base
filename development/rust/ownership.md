# Rust - Ownership & Borrowing

Rust's ownership system is what makes memory safety without a GC possible. It is checked entirely at compile time.

## Ownership rules

1. Each value has exactly one **owner**.
2. When the owner goes out of scope, the value is **dropped** (memory freed).
3. There can only be **one owner at a time** - assigning moves it.

```rust
{
    let s = String::from("hello");  // s owns the String
    // use s
}   // s goes out of scope, String is dropped here
```

## Move

Assigning a heap-allocated value **moves** ownership - the original variable is no longer valid:

```rust
let s1 = String::from("hello");
let s2 = s1;          // s1 is moved into s2

// println!("{s1}");  // ERROR: s1 was moved
println!("{s2}");     // OK
```

Stack-only types (integers, bools, chars, tuples of these) implement `Copy` and are **copied** instead of moved:

```rust
let x = 5;
let y = x;   // x is copied
println!("{x} and {y}");  // both valid
```

## Clone

To deep-copy heap data explicitly:

```rust
let s1 = String::from("hello");
let s2 = s1.clone();
println!("{s1} and {s2}");  // both valid
```

## References & borrowing

A **reference** lets you use a value without taking ownership - this is called borrowing.

```rust
fn calculate_length(s: &String) -> usize {
    s.len()
}   // s goes out of scope but does NOT drop the String (it doesn't own it)

let s = String::from("hello");
let len = calculate_length(&s);  // pass reference
println!("{s} has length {len}");  // s still valid
```

### Mutable references

```rust
fn change(s: &mut String) {
    s.push_str(", world");
}

let mut s = String::from("hello");
change(&mut s);
```

**Rules:**
- Any number of **immutable** references at the same time - OR —
- Exactly **one mutable** reference
- Not both at the same time (prevents data races at compile time)

```rust
let mut s = String::from("hello");

let r1 = &s;
let r2 = &s;
println!("{r1} and {r2}");  // r1, r2 last used here

let r3 = &mut s;            // OK - r1 and r2 no longer in use
println!("{r3}");
```

## Slices

A slice is a reference to a contiguous sequence - it does not own data.

```rust
let s = String::from("hello world");

let hello: &str = &s[0..5];
let world: &str = &s[6..11];
let all:   &str = &s[..];

// String literals are slices
let literal: &str = "hello";  // &'static str
```

Array slices:

```rust
let a = [1, 2, 3, 4, 5];
let slice: &[i32] = &a[1..3];  // [2, 3]
```

## Lifetimes

Lifetimes ensure references don't outlive the data they point to. The compiler infers them in most cases; explicit annotations are needed when returning references from functions with multiple inputs.

```rust
// 'a is a lifetime annotation - both inputs and output share the same lifetime
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}
```

The `'static` lifetime means the reference is valid for the entire program:

```rust
let s: &'static str = "I live forever";
```
