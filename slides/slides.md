---
title: Rust & WASM
author: Chritian Sanabria & Jakob Beckmann
institute: Innovation Process Technology AG
date: 14.06.2022
progress: false
hash: true
controls: false
highlightjs: true
ipt-footer: true
transition: none

---

# Intro

- Why Rust and WASM?
- Overview

# Rust

![](./assets/ferris.gif)

## What is it??

- Mozilla (2010) ~ The Rust Foundation
- Multi-paradigm & general-purpose
- Focus: safety & performance
- Stable version: v1.61.0
- Used by AWS, Discord, Meta, Google, Microsoft, ...

## Why the hype??

- Expressiveness
- No null pointers
- Insane concurrency guarantees
- Ecosystem
- Functional features
- Metaprogramming
- Performance
- ...

## Compared to others

- Java: performance
- Go: memory safety and concurrency guarantees
- C++: concurrency guarantees, idiomatic code

## Limitations

- Learning curve...
- Adoption

## Hello World

```{.rust include="./hello/src/main.rs" .numberLines}
```

## Running

```{.shebang im_out="ocb,stdout" .numberLines}
#!/bin/bash
cd ./hello/
cargo run
```

## Compiling

```{.shebang im_out="ocb,stdout" .numberLines}
#!/bin/bash
cd ./hello/
cargo build --release
file ./target/release/hello
```

# Expressiveness

And I don't mean this:

```sh
perl -e 'while(<>){code}morecode'
perl -ne 'code;END{morecode}'
perl -ne 'code}{morecode'
```

## Functional

```rust
fn factorial(n: usize) -> usize {
  (1..n+1).fold(1, |a, b| a * b)
}
```

## Expressions

```{.rust data-line-numbers=""}
let five: i32 = {
    fn_call();
    5
};

assert_eq!(5, five);
```

## Expressions

```{.rust data-line-numbers=""}
let y = if 12 * 15 > 150 {
    "Bigger"
} else {
    "Smaller"
};
assert_eq!(y, "Bigger");
```

## Pattern Matching

```{.rust data-line-numbers=""}
let x = 9;
let message = match x {
    0 | 1  => "not many",
    2 ..= 9 => "a few",
    _      => "lots"
};

assert_eq!(message, "a few");
```

## Pattern Matching

```{.rust data-line-numbers=""}
let maybe_digit = Some(42);

let message = match maybe_digit {
    Some(x) if x < 10 => process_digit(x),
    Some(x) => process_other(x),
    None => panic!(),
};
```

## Pattern Matching

```rust
enum Message {
  Hello { id: i32 },
}
```

### Deconstruct

```{.rust data-line-numbers=""}
let msg = Message::Hello { id: 5 };

match msg {
  Message::Hello { id: my_id @ 3..=7 } => {
    println!("Found an id in range: {}", my_id)
  },
  Message::Hello { id } => {
    println!("Found some other id: {}", id)
  }
}
```

## Mixing

```{.rust data-line-numbers=""}
let mut vals = vec![2, 3, 1, 2, 2];

while let Some(v @ 1) | Some(v @ 2) = vals.pop() {
    println!("{}", v); // Prints 2, 2, then 1
}
```

#

![](./assets/null.png)

## Options

```rust
pub enum Option<T> {
    None,
    Some(T),
}
```

## Options

```{.rust data-line-numbers=""}
fn divide(numerator: f64, denominator: f64) -> Option<f64> {
   if denominator == 0.0 {
    None
  } else {
    Some(numerator / denominator)
  }
}
```

## Options

```{.rust data-line-numbers=""}
let result = divide(2.0, 3.0);

let sum = result + result;  // does not work!!
```

## Options

```{.rust data-line-numbers=""}
let result = divide(2.0, 3.0);

let sum = result.unwrap_or(0.0) + 15.0;
```

## Options

```{.rust data-line-numbers=""}
let result = divide(2.0, 3.0);

if let Some(val) = result {
  println!("my output is {}", val + 15.0);
} else {
  println!("whuutttt??");
}
```

# Ownership

Or why Rust is hard...

## Principles

- Everything is owned
- Everything is immutable by default
- You can share immutable data
- You can't _share_ mutable data
- Lifetime needs to be considered

## Everything is Owned

```{.rust data-line-numbers=""}
let x = String::from("hello");
let y = x;
println!("{}", x); // invalid
```

## Everything is Owned

```{.rust data-line-numbers=""}
fn own(what: String) {}

let x = String::from("hello");
own(x);

println!("{}", x); // invalid
```

## Everything is Owned

```{.rust data-line-numbers=""}
fn borrow(what: &String) {}

let x = String::from("hello");
own(&x);

println!("{}", x);
```

## Immutable Aliases

```{.rust data-line-numbers=""}
let mut s = String::from("hello");

let r1 = &s; // no problem
let r2 = &s; // no problem
println!("{} and {}", r1, r2);
```

## Mutable Borrowing

```{.rust data-line-numbers=""}
let mut s = String::from("hello");

let r1 = &mut s;
let r2 = &mut s;

println!("{}, {}", r1, r2);
```

```txt
Compiling ownership v0.1.0 (file:///projects/ownership)
error[E0499]: cannot borrow `s` as mutable more than once at a time
 --> src/main.rs:4:14
  |
3 |     let r1 = &mut s;
  |              ------ first mutable borrow occurs here
4 |     let r2 = &mut s;
  |              ^^^^^^ second mutable borrow occurs here
5 | 
6 |     println!("{}, {}", r1, r2);
  |                        -- first borrow later used here

For more information about this error, try `rustc --explain E0499`.
error: could not compile `ownership` due to previous error
```

## Mutable Borrowing

```{.rust data-line-numbers=""}
let mut s = String::from("hello");

let r1 = &s;
let r2 = &mut s;

println!("{}, {}", r1, r2); // invalid
```

## The Borrow Checker is Smart

```{.rust data-line-numbers=""}
let mut s = String::from("hello");

let r1 = &s;
let r2 = &s;
println!("{} and {}", r1, r2);
// variables r1 and r2 will not be used after this point

let r3 = &mut s; // no problem
println!("{}", r3);
```

## Scoping

```{.rust data-line-numbers=""}
fn main() {
  let s1 = "hello dolly".to_string();
  let mut rs1 = &s1;
  {
    let tmp = "hello world".to_string();
    rs1 = &tmp;
  }
  println!("ref {}", rs1);
}
```

Is there an issue here?

## Scoping

```{.rust data-line-numbers="|4-7|5,8"}
fn main() {
  let s1 = "hello dolly".to_string();
  let mut rs1 = &s1;
  {
    let tmp = "hello world".to_string();
    rs1 = &tmp;
  }
  println!("ref {}", rs1);
}
```

```txt
error: `tmp` does not live long enough
  --> ref1.rs:7:5
   |
6  |         rs1 = &tmp;
   |                --- borrow occurs here
7  |     }
   |     ^ `tmp` dropped here while still borrowed
8  |     println!("ref {}", rs1);
9  | }
   | - borrowed value needs to live until here
```

## Lifetimes

```{.rust data-line-numbers=""}
#[derive(Debug)]
struct A <'a> {
  s: &'a str
}

fn main() {
  let s = "I'm a little string".to_string();
  let a = A { s: &s };

  println!("{:?}", a);
}
```

## Lifetimes

```{.rust data-line-numbers=""}
fn makes_a() -> A {
  let string = "I'm a little string".to_string();
  A { s: &string }
}
```

Is there an issue?

## Lifetimes

```{.rust data-line-numbers=""}
fn makes_a() -> A {
  let string = "I'm a little string".to_string();
  A { s: &string }
}
```

```txt
3 |      A { s: &string }
  |              ^^^^^^ does not live long enough
4 | }
  | - borrowed value only lives until here
```

# Other Cool Stuff

- Traits == Powerful Interfaces
- Concurrency is easy
- Everything is fast
- Generate code at compile time

# Hands-On

## Install Rust

```sh
curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```

## Create a new Repo

```sh
cargo init hello
cd hello
cargo run
```

## Exercise

- Store the string into a var of type `String`.
- Modify the var before printing to add your name.
- Refactor the modification and printing into a function.
- Loop and call the function 10 times.

## Solution

```rust
fn main() {
    let mut x = "Hello, world!".to_owned();
    for _ in 0..10 {
        print(&mut x);
    }
}

fn print(x: &mut String) {
    x.push_str("Jakob");
    println!("{}", x);
}
```

## Mandelbrot

```
https://github.com/ProgrammingRust/mandelbrot/tree/rayon
```

