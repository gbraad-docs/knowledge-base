# Rust - Structs, Enums & Pattern Matching

## Structs

```rust
struct User {
    username: String,
    email: String,
    age: u32,
    active: bool,
}

let user = User {
    username: String::from("gbraad"),
    email: String::from("me@gbraad.nl"),
    age: 30,
    active: true,
};

println!("{}", user.username);

// Update syntax - copy remaining fields from another instance
let user2 = User {
    email: String::from("other@example.com"),
    ..user   // moves remaining fields from user
};
```

### Tuple structs

```rust
struct Color(u8, u8, u8);
struct Point(f64, f64);

let black = Color(0, 0, 0);
let origin = Point(0.0, 0.0);
println!("{}", black.0);
```

### Methods

```rust
#[derive(Debug)]
struct Rectangle {
    width: f64,
    height: f64,
}

impl Rectangle {
    // Associated function (constructor, no self)
    fn new(width: f64, height: f64) -> Self {
        Rectangle { width, height }
    }

    // Method (takes &self)
    fn area(&self) -> f64 {
        self.width * self.height
    }

    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

let rect = Rectangle::new(10.0, 5.0);
println!("Area: {}", rect.area());
println!("{rect:#?}");   // needs #[derive(Debug)]
```

## Enums

```rust
enum Direction {
    North,
    South,
    East,
    West,
}

let dir = Direction::North;
```

Enum variants can carry data:

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    Color(u8, u8, u8),
}

impl Message {
    fn call(&self) {
        match self {
            Message::Quit => println!("quit"),
            Message::Move { x, y } => println!("move to {x},{y}"),
            Message::Write(text) => println!("write: {text}"),
            Message::Color(r, g, b) => println!("color: {r},{g},{b}"),
        }
    }
}
```

## Option

`Option<T>` replaces null - a value is either `Some(T)` or `None`:

```rust
let some_number: Option<i32> = Some(5);
let no_number: Option<i32> = None;

// Must handle both cases before using the value
let value = some_number.unwrap_or(0);
let doubled = some_number.map(|n| n * 2);
```

## Pattern matching

### `match`

```rust
let coin = Coin::Quarter;
let value = match coin {
    Coin::Penny   => 1,
    Coin::Nickel  => 5,
    Coin::Dime    => 10,
    Coin::Quarter => 25,
};

// Catch-all
match number {
    1 => println!("one"),
    2 | 3 => println!("two or three"),
    4..=9 => println!("four to nine"),
    other => println!("got {other}"),  // binds the value
    // _ => ()                         // discard catch-all
}

// Destructuring in match
match msg {
    Message::Move { x, y } => println!("{x},{y}"),
    Message::Write(text)   => println!("{text}"),
    _ => (),
}
```

### `if let` - single-arm match shorthand

```rust
let config_max = Some(3u8);
if let Some(max) = config_max {
    println!("max is {max}");
}

if let Some(n) = some_value {
    println!("{n}");
} else {
    println!("nothing");
}
```

### `while let`

```rust
let mut stack = vec![1, 2, 3];
while let Some(top) = stack.pop() {
    println!("{top}");
}
```

## Collections

### Vec

```rust
let mut v: Vec<i32> = Vec::new();
let v2 = vec![1, 2, 3];     // macro shorthand

v.push(4);
v.push(5);

let third = &v[2];           // panics if out of bounds
let third = v.get(2);        // returns Option<&i32>

for n in &v {
    println!("{n}");
}
for n in &mut v {
    *n += 10;                // dereference to modify
}

v.pop();                     // removes and returns last element
v.len();
v.is_empty();
```

### HashMap

```rust
use std::collections::HashMap;

let mut scores: HashMap<String, i32> = HashMap::new();
scores.insert(String::from("Alice"), 100);
scores.insert(String::from("Bob"), 85);

// Access
let alice = scores.get("Alice");     // Option<&i32>
let alice = scores["Alice"];         // panics if missing

// Insert only if key absent
scores.entry(String::from("Charlie")).or_insert(70);

// Iterate
for (name, score) in &scores {
    println!("{name}: {score}");
}
```
