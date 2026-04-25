# Rust - Traits & Generics

## Traits

A trait defines shared behaviour - similar to interfaces in other languages.

```rust
trait Summary {
    fn summarize(&self) -> String;

    // Default implementation (can be overridden)
    fn preview(&self) -> String {
        format!("{}...", &self.summarize()[..20])
    }
}

struct Article {
    title: String,
    content: String,
}

impl Summary for Article {
    fn summarize(&self) -> String {
        format!("{}: {}", self.title, self.content)
    }
}

struct Tweet {
    username: String,
    content: String,
}

impl Summary for Tweet {
    fn summarize(&self) -> String {
        format!("@{}: {}", self.username, self.content)
    }
}
```

## Traits as parameters

```rust
// impl Trait syntax (shorthand)
fn notify(item: &impl Summary) {
    println!("{}", item.summarize());
}

// Trait bound syntax (equivalent, more flexible)
fn notify<T: Summary>(item: &T) {
    println!("{}", item.summarize());
}

// Multiple bounds
fn notify(item: &(impl Summary + std::fmt::Display)) { }
fn notify<T: Summary + std::fmt::Display>(item: &T) { }

// where clause - cleaner with many bounds
fn notify<T, U>(t: &T, u: &U)
where
    T: Summary + Clone,
    U: std::fmt::Debug,
{ }
```

## Returning traits

```rust
// Return something that implements Summary (concrete type hidden)
fn make_summary() -> impl Summary {
    Tweet {
        username: String::from("gbraad"),
        content: String::from("Hello!"),
    }
}
```

## Common standard library traits

| Trait | Purpose |
|-------|---------|
| `Display` | `{}` formatting |
| `Debug` | `{:?}` formatting - `#[derive(Debug)]` |
| `Clone` | `.clone()` - `#[derive(Clone)]` |
| `Copy` | implicit copy semantics |
| `PartialEq` / `Eq` | `==` operator |
| `PartialOrd` / `Ord` | `<`, `>` operators |
| `Iterator` | `.next()`, enables `for` loops and adaptors |
| `From` / `Into` | type conversions |
| `Default` | `.default()` zero value |

## Generics

```rust
// Generic function
fn largest<T: PartialOrd>(list: &[T]) -> &T {
    let mut largest = &list[0];
    for item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}

// Generic struct
struct Pair<T> {
    first: T,
    second: T,
}

impl<T> Pair<T> {
    fn new(first: T, second: T) -> Self {
        Pair { first, second }
    }
}

// Conditional implementation (only for T that implements Display + PartialOrd)
impl<T: std::fmt::Display + PartialOrd> Pair<T> {
    fn cmp_display(&self) {
        if self.first >= self.second {
            println!("largest: {}", self.first);
        } else {
            println!("largest: {}", self.second);
        }
    }
}
```

## Iterators

Any type implementing `Iterator` gets all adaptor methods for free:

```rust
trait Iterator {
    type Item;
    fn next(&mut self) -> Option<Self::Item>;
}
```

```rust
let v = vec![1, 2, 3, 4, 5];

// Adaptors (lazy - don't execute until consumed)
let doubled: Vec<i32> = v.iter().map(|x| x * 2).collect();
let evens: Vec<&i32>  = v.iter().filter(|x| *x % 2 == 0).collect();
let sum: i32           = v.iter().sum();
let total: i32         = v.iter().fold(0, |acc, x| acc + x);

// any / all
let has_even = v.iter().any(|x| x % 2 == 0);
let all_pos  = v.iter().all(|x| *x > 0);

// Chaining
let result: Vec<i32> = v.iter()
    .filter(|&&x| x > 2)
    .map(|&x| x * 10)
    .collect();
```

## Closures

```rust
let add = |x, y| x + y;
let double = |x: i32| -> i32 { x * 2 };

// Closures capture their environment
let multiplier = 3;
let triple = |x| x * multiplier;   // captures multiplier by reference

// move closure - takes ownership of captured variables
let s = String::from("hello");
let greet = move || println!("{s}");
```

Closure trait bounds:
- `Fn` - borrows immutably (can call multiple times)
- `FnMut` - borrows mutably (can call multiple times)
- `FnOnce` - takes ownership (can only call once)

```rust
fn apply<F: Fn(i32) -> i32>(f: F, value: i32) -> i32 {
    f(value)
}

let result = apply(|x| x * 2, 5);   // 10
```
