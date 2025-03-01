## Variables & Types

```rust
// Basic variable (immutable)
let x = 5;

// Mutable variable
let mut y = 10;

// Type annotation
let z: i32 = 42;

// Common types
let i: i32 = -42;        // Integer (i8, i16, i32, i64, i128)
let u: u32 = 42;         // Unsigned (u8, u16, u32, u64, u128)
let f: f64 = 3.14;       // Float (f32, f64)
let b: bool = true;      // Boolean
let c: char = 'z';       // Character
let s: &str = "hello";   // String slice
let string = String::from("hello"); // Owned string
let arr = [1, 2, 3, 4, 5]; // Array
let v = vec![1, 2, 3];   // Vector
let t = (1, "hello", 3.14); // Tuple
```

[Docs: Variables](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html) | [Docs: Data Types](https://doc.rust-lang.org/book/ch03-02-data-types.html)

## Functions

```rust
// Basic function
fn say_hello() {
    println!("Hello!");
}

// Parameters
fn greet(name: &str) {
    println!("Hello, {name}!");
}

// Return value
fn add(a: i32, b: i32) -> i32 {
    a + b  // No semicolon = return value
}

// Early return
fn check_number(x: i32) -> bool {
    if x < 0 {
        return false;
    }
    true
}
```

[Docs: Functions](https://doc.rust-lang.org/book/ch03-03-how-functions-work.html)

## Control Flow

```rust
// If expression
if x > 5 {
    println!("x is greater than 5");
} else if x == 5 {
    println!("x is 5");
} else {
    println!("x is less than 5");
}

// If as expression
let number = if condition { 5 } else { 6 };

// Loop
loop {
    // Forever loop
    break; // Exit loop
}

// While loop
while condition {
    // Body
}

// For loop
for element in array {
    // Body
}

// Range-based for
for i in 0..5 {
    // 0, 1, 2, 3, 4
}

// Match
match value {
    1 => println!("One"),
    2 => println!("Two"),
    _ => println!("Other"),
}
```

[Docs: Control Flow](https://doc.rust-lang.org/book/ch03-05-control-flow.html)

## Structs & Enums

```rust
// Struct definition
struct User {
    username: String,
    email: String,
    active: bool,
}

// Create instance
let user = User {
    email: String::from("user@example.com"),
    username: String::from("username"),
    active: true,
};

// Access field
println!("{}", user.email);

// Update syntax
let user2 = User {
    email: String::from("another@example.com"),
    ..user // Copy remaining fields from user
};

// Tuple struct
struct Point(i32, i32, i32);
let origin = Point(0, 0, 0);

// Enum
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

// Create enum instance
let msg = Message::Write(String::from("hello"));
```

[Docs: Structs](https://doc.rust-lang.org/book/ch05-00-structs.html) | [Docs: Enums](https://doc.rust-lang.org/book/ch06-00-enums.html)

## Implementation & Methods

```rust
// Methods on struct
impl User {
    // Constructor (associated function)
    fn new(email: String, username: String) -> Self {
        Self {
            email,
            username,
            active: true,
        }
    }
    
    // Method with self reference
    fn is_active(&self) -> bool {
        self.active
    }
    
    // Method that modifies self
    fn deactivate(&mut self) {
        self.active = false;
    }
}

// Usage
let mut user = User::new("email@example.com", "username");
let active = user.is_active();
user.deactivate();
```

[Docs: Methods](https://doc.rust-lang.org/book/ch05-03-method-syntax.html)

## Pattern Matching

```rust
// Match with enum
match msg {
    Message::Quit => println!("Quit"),
    Message::Move { x, y } => println!("Move to {}, {}", x, y),
    Message::Write(text) => println!("Text: {}", text),
    Message::ChangeColor(r, g, b) => println!("Color: {}, {}, {}", r, g, b),
}

// If let (for single pattern)
if let Some(value) = optional {
    println!("Value: {}", value);
}

// while let
while let Some(value) = stack.pop() {
    println!("Value: {}", value);
}

// let with patterns
let (x, y, z) = (1, 2, 3);

// Function parameters
fn print_coordinates(&(x, y): &(i32, i32)) {
    println!("({}, {})", x, y);
}
```

[Docs: Patterns](https://doc.rust-lang.org/book/ch18-00-patterns.html)

## Error Handling

```rust
// Option (for nullable values)
let some_number = Some(5);
let absent: Option<i32> = None;

// Accessing Option
match some_number {
    Some(value) => println!("Value: {}", value),
    None => println!("No value"),
}

// Result (for operations that might fail)
fn divide(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 {
        Err(String::from("Division by zero"))
    } else {
        Ok(a / b)
    }
}

// Using Result
match divide(10.0, 2.0) {
    Ok(result) => println!("Result: {}", result),
    Err(error) => println!("Error: {}", error),
}

// Unwrap (panics on None/Err)
let value = some_number.unwrap();

// Expect (like unwrap with custom message)
let value = some_number.expect("Should have a value");

// ? operator (early return on None/Err)
fn process_file() -> Result<String, io::Error> {
    let content = fs::read_to_string("file.txt")?;
    Ok(content + " processed")
}
```

[Docs: Error Handling](https://doc.rust-lang.org/book/ch09-00-error-handling.html)

## Collections

```rust
// Vec (dynamic array)
let mut v = vec![1, 2, 3];
v.push(4);
let third = &v[2];
for i in &v {
    println!("{}", i);
}

// String (UTF-8 text)
let mut s = String::from("hello");
s.push_str(" world");
s.push('!');
println!("{}", s);

// HashMap (key-value store)
use std::collections::HashMap;
let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Red"), 50);
let blue_score = scores.get("Blue");
```

[Docs: Collections](https://doc.rust-lang.org/book/ch08-00-common-collections.html)

## Ownership & Borrowing

```rust
// Ownership (only one owner)
let s1 = String::from("hello");
let s2 = s1; // s1 is moved, no longer valid

// Borrowing (references)
let s1 = String::from("hello");
let len = calculate_length(&s1); // Immutable borrow

// Mutable borrowing
let mut s = String::from("hello");
change(&mut s);

fn calculate_length(s: &String) -> usize {
    s.len() // s is borrowed, not owned
}

fn change(s: &mut String) {
    s.push_str(", world");
}
```

[Docs: Ownership](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html)

## Generics & Traits

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
struct Point<T> {
    x: T,
    y: T,
}

// Trait definition
trait Summary {
    fn summarize(&self) -> String;
}

// Implement trait
impl Summary for String {
    fn summarize(&self) -> String {
        format!("\"{}\"", self)
    }
}

// Trait bounds
fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}

// Multiple trait bounds
fn process<T: Summary + Display>(item: &T) {
    // ...
}

// Where clause
fn some_function<T, U>(t: &T, u: &U) -> i32
    where T: Display + Clone,
          U: Clone + Debug
{
    // ...
}
```

[Docs: Generics](https://doc.rust-lang.org/book/ch10-01-syntax.html) | [Docs: Traits](https://doc.rust-lang.org/book/ch10-02-traits.html)

## Closures

```rust
// Basic closure
let add_one = |x| x + 1;
let five = add_one(4);

// With type annotations
let add_two: fn(i32) -> i32 = |x: i32| x + 2;

// Capturing environment
let x = 4;
let equal_to_x = |z| z == x; // Captures x
assert!(equal_to_x(4));

// Using move to take ownership
let y = vec![1, 2, 3];
let equals_y = move |z| z == y; // y is moved
```

[Docs: Closures](https://doc.rust-lang.org/book/ch13-01-closures.html)

## Modules & Imports

```rust
// Define module
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

// Using path
crate::front_of_house::hosting::add_to_waitlist();

// Bring into scope
use crate::front_of_house::hosting;
// Now can use: hosting::add_to_waitlist();

// Re-exporting
pub use crate::front_of_house::hosting;

// Nested paths
use std::{cmp::Ordering, io};

// Module in separate file
// In file.rs:
mod front_of_house;
// In front_of_house.rs:
pub mod hosting {
    pub fn add_to_waitlist() {}
}
```

[Docs: Modules](https://doc.rust-lang.org/book/ch07-00-managing-growing-projects-with-packages-crates-and-modules.html)

## Smart Pointers

```rust
// Box (heap allocation)
let b = Box::new(5);

// Rc (reference counting, single-threaded)
use std::rc::Rc;
let data = Rc::new(vec![1, 2, 3]);
let data2 = Rc::clone(&data);

// Arc (atomic reference counting, thread-safe)
use std::sync::Arc;
let shared = Arc::new(vec![1, 2, 3]);

// RefCell (interior mutability, single-threaded)
use std::cell::RefCell;
let data = RefCell::new(5);
*data.borrow_mut() += 1;

// Mutex (mutual exclusion, thread-safe)
use std::sync::Mutex;
let m = Mutex::new(5);
let mut num = m.lock().unwrap();
*num = 6;
```

[Docs: Smart Pointers](https://doc.rust-lang.org/book/ch15-00-smart-pointers.html)

## Concurrency

```rust
// Spawn thread
use std::thread;
let handle = thread::spawn(|| {
    println!("Hello from thread");
});
handle.join().unwrap();

// Channels (message passing)
use std::sync::mpsc;
let (tx, rx) = mpsc::channel();
thread::spawn(move || {
    tx.send("hello").unwrap();
});
let received = rx.recv().unwrap();
```

[Docs: Concurrency](https://doc.rust-lang.org/book/ch16-00-concurrency.html)

## Testing

```rust
// Unit test
#[test]
fn it_works() {
    assert_eq!(2 + 2, 4);
}

// Test with error message
#[test]
fn larger_can_hold_smaller() {
    let larger = Rectangle { width: 8, height: 7 };
    let smaller = Rectangle { width: 5, height: 1 };
    assert!(larger.can_hold(&smaller), "larger should hold smaller");
}

// Test that should panic
#[test]
#[should_panic(expected = "divide by zero")]
fn divide_by_zero_panics() {
    divide(1, 0);
}

// Run tests: cargo test
```

[Docs: Testing](https://doc.rust-lang.org/book/ch11-00-testing.html)

## Useful Resources

- [Learn Rust in Y Minutes](https://learnxinyminutes.com/docs/rust/) - Super quick syntax overview
- [Rust Playground](https://play.rust-lang.org/) - Try Rust code in your browser
- [Rust By Example](https://doc.rust-lang.org/rust-by-example/) - Learn by examples
- [Rust Cookbook](https://rust-lang-nursery.github.io/rust-cookbook/) - Common programming tasks
- [The Rust Book](https://doc.rust-lang.org/book/) - Official comprehensive guide
- [Rustlings](https://github.com/rust-lang/rustlings) - Small exercises to learn Rust
- [Rust Cheat Sheet](https://cheats.rs/) - Comprehensive reference
