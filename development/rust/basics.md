# Rust - Basics

## Hello world

```rust
fn main() {
    println!("Hello, world!");
}
```

## Variables

Variables are immutable by default. Use `mut` to make them mutable.

```rust
let x = 5;          // immutable
let mut y = 5;      // mutable
y = 6;

const MAX: u32 = 100_000;  // constant, must have type annotation
```

**Shadowing** - redeclare with `let` to change type or value:

```rust
let x = 5;
let x = x + 1;      // shadows the previous x
let x = "hello";    // can even change type
```

## Data types

### Scalar types

| Type | Examples |
|------|---------|
| Integer | `i8`, `i16`, `i32`, `i64`, `i128`, `isize` |
| Unsigned | `u8`, `u16`, `u32`, `u64`, `u128`, `usize` |
| Float | `f32`, `f64` |
| Boolean | `bool` (`true`/`false`) |
| Character | `char` (Unicode scalar, 4 bytes) |

```rust
let n: i32 = -42;
let f: f64 = 3.14;
let b: bool = true;
let c: char = '🦀';
```

### Compound types

```rust
// Tuple - fixed length, mixed types
let tup: (i32, f64, char) = (42, 3.14, 'z');
let (x, y, z) = tup;    // destructure
let first = tup.0;       // index access

// Array - fixed length, same type
let arr: [i32; 5] = [1, 2, 3, 4, 5];
let zeros = [0; 5];      // [0, 0, 0, 0, 0]
let elem = arr[0];
```

## Functions

```rust
fn add(x: i32, y: i32) -> i32 {
    x + y  // no semicolon = expression, this is the return value
}

fn nothing() {  // returns () (unit type) implicitly
    println!("side effect only");
}
```

Expressions vs statements:
- **Statement** - performs an action, no value (`let x = 5;`)
- **Expression** - evaluates to a value (blocks, `if`, `match`, function calls)

```rust
let y = {
    let x = 3;
    x + 1   // expression - block evaluates to 4
};
```

## Control flow

```rust
// if is an expression
let number = 7;
let msg = if number < 10 { "small" } else { "large" };

// loop
let mut count = 0;
let result = loop {
    count += 1;
    if count == 10 {
        break count * 2;  // break with value
    }
};

// while
while count > 0 {
    count -= 1;
}

// for - preferred over manual indexing
let a = [10, 20, 30];
for elem in a {
    println!("{elem}");
}

for i in 0..5 {       // 0, 1, 2, 3, 4
    println!("{i}");
}
for i in 0..=5 {      // 0, 1, 2, 3, 4, 5
    println!("{i}");
}
```

## Strings

Rust has two string types:

| Type | Description |
|------|-------------|
| `&str` | String slice - immutable reference to UTF-8 data |
| `String` | Heap-allocated, owned, growable |

```rust
let s1: &str = "hello";           // string literal, 'static lifetime
let s2: String = String::from("hello");
let s3 = "world".to_string();

// Concatenation
let s4 = s2 + " world";           // moves s2, appends
let s5 = format!("{s1} world");   // doesn't move anything

// Slices
let hello = &s5[0..5];
let len = s5.len();
let is_empty = s5.is_empty();

// Iteration
for c in "hello".chars() {
    println!("{c}");
}
```

## Printing

```rust
println!("{}", value);        // Display
println!("{:?}", value);      // Debug
println!("{:#?}", value);     // Pretty debug
println!("{value}");          // Inline (Rust 1.58+)
eprintln!("to stderr: {}", value);
```
