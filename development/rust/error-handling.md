# Rust - Error Handling

Rust has no exceptions. Errors are values, split into two categories:

| Kind | Type | Use case |
|------|------|---------|
| Unrecoverable | `panic!` | Bug, invariant violated |
| Recoverable | `Result<T, E>` | Expected failure (IO, parsing, network) |

## panic!

```rust
panic!("something went terribly wrong");

// Automatic panics
let v = vec![1, 2, 3];
v[99];            // index out of bounds - panics

let n: i32 = "abc".parse().unwrap();  // unwrap panics on Err
```

Set `RUST_BACKTRACE=1` to see a full stack trace.

## Result

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

```rust
use std::fs::File;

let f = File::open("hello.txt");

let file = match f {
    Ok(file) => file,
    Err(e)   => panic!("failed to open: {e}"),
};
```

### Matching on error kind

```rust
use std::io::ErrorKind;

let file = match File::open("hello.txt") {
    Ok(f) => f,
    Err(e) => match e.kind() {
        ErrorKind::NotFound => match File::create("hello.txt") {
            Ok(fc) => fc,
            Err(ce) => panic!("could not create: {ce}"),
        },
        other => panic!("other error: {other:?}"),
    },
};
```

### Shortcuts

```rust
// unwrap - returns T or panics
let f = File::open("hello.txt").unwrap();

// expect - like unwrap but with a message
let f = File::open("hello.txt").expect("hello.txt should exist");

// unwrap_or - return default on Err
let f = File::open("hello.txt").unwrap_or_else(|_| File::create("hello.txt").unwrap());
```

## The `?` operator

Propagates errors up to the caller - equivalent to a `match` that returns `Err` early:

```rust
use std::io::{self, Read};
use std::fs::File;

fn read_username() -> Result<String, io::Error> {
    let mut f = File::open("username.txt")?;  // returns Err early if fails
    let mut s = String::new();
    f.read_to_string(&mut s)?;
    Ok(s)
}

// Chained
fn read_username() -> Result<String, io::Error> {
    let mut s = String::new();
    File::open("username.txt")?.read_to_string(&mut s)?;
    Ok(s)
}

// Even shorter with std::fs
fn read_username() -> Result<String, io::Error> {
    std::fs::read_to_string("username.txt")
}
```

`?` can also be used with `Option<T>` to return `None` early.

## Custom error types

```rust
use std::fmt;

#[derive(Debug)]
enum AppError {
    Io(std::io::Error),
    Parse(std::num::ParseIntError),
    NotFound(String),
}

impl fmt::Display for AppError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            AppError::Io(e)        => write!(f, "IO error: {e}"),
            AppError::Parse(e)     => write!(f, "parse error: {e}"),
            AppError::NotFound(s)  => write!(f, "not found: {s}"),
        }
    }
}

impl From<std::io::Error> for AppError {
    fn from(e: std::io::Error) -> Self {
        AppError::Io(e)
    }
}
```

With `From` implemented, `?` automatically converts the error type.

## Returning errors from main

```rust
fn main() -> Result<(), Box<dyn std::error::Error>> {
    let n: i32 = "42".parse()?;
    println!("{n}");
    Ok(())
}
```

`Box<dyn std::error::Error>` accepts any error type - useful for quick scripts. Use a typed error for libraries.
