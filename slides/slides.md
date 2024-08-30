---
title: Crab Rave
author: Selim Kälin & Zak Cook & Jakob Beckmann
institute: Innovation Process Technology AG
date: 30.08.2024
theme: moon
revealjs-url: "https://unpkg.com/reveal.js@5.1.0"
progress: false
controls: false
hash: true
highlightjs: true
---

# Why Rust?

::: notes
Session goes from 11:00 to 12:30 (90 minutes), 14:00 to 15:00 (60 minutes) and 15:30 to 16:25 (55
minutes).

Without counting breaks, we have 205 minutes (3.5 hours).
:::

# {background="./assets/crab-with-seatbelt.jpeg"}

::: notes
Talk about the invention of the seatbelt, how it was ok to die in a car.

- Invented in 1959 by Volvo
- Needed laws in the 70s to actually push adoption.
:::

# A Short Overview 

::: notes
10-15 minutes max
:::

## Features

> Easy, expressive concurrency.

::: notes
- performance
- type safety (reliability)
- simple concurrency
- expressiveness (productivity)
:::

## Primitives

- Signed and unsigned integers: `u8`, `i64`, `isize`
- Floating points: `f32`, `f64`
- Unicode characters: `'a'`, `'∞'`
- Booleans: `true`, `false`
- Unit type: `()`

## Compound Types

- Arrays: `[1, 2, 3]`, `[1; 256]`
- Tuples: `(1, true)`, `T(1, "hello")`

::: notes
- arrays are fixed in size
- tuples can also be named
:::

## Functions

```rust
fn print_hello(name: String) {
    println!("Hello, {}!", name);
}
```

::: notes
- intentionally skip over references and slices, we will come back to it
- inner is a macro
  - a code generator run before type checking
  - more powerful than functions
  - much more difficult to write
:::

## Control Flow

```rust
if name == "selim" {
    println!("wow");
} else {
    println!("Hello, {}!", name);
}
```

## Expressions
```rust
let last_name = if name == "selim" {
    // no semi-colon at the end to ensure it is
    // treated as an expression
    "kälin"
} else {
    "who cares"
};
```

# Hands-On: Rustlings

## Try it out!

- Check out the README in `exercises`
- Initialise rustlings
- Do exercise `00_intro`

::: notes
5-10 minutes max
:::


# Ownership

::: notes
30 minutes
:::

## Immutability {data-auto-animate=true}

<pre data-id="code-animation"><code data-trim data-line-numbers="|2,4" rust>
fn main() {
    let x = 5;
    println!("The value of x is: {x}");
    x = 6;      // will fail
    println!("The value of x is: {x}");
}
</code></pre>

## Immutability {data-auto-animate=true}

<pre data-id="code-animation"><code data-trim data-line-numbers="2,4" rust>
fn main() {
    let mut x = 5;
    println!("The value of x is: {x}");
    x = 6;
    println!("The value of x is: {x}");
}
</code></pre>

::: notes
- typing is still respected, cannot change type of variable, even with `mut`
:::

## Everything is Owned

```{.rust data-line-numbers=""}
let x = String::from("hello");
let y = x;
println!("{}", x); // invalid
```

## {data-auto-animate=true}

<pre data-id="code-animation"><code data-trim data-line-numbers rust>
  fn print_hello(name: String) {
      println!("Hello, {}!", name);
  }
</code></pre>

## {data-auto-animate=true}
<pre data-id="code-animation"><code data-trim data-line-numbers rust>
  fn print_hello(name: String) {
      println!("Hello, {}!", name);
  }

  fn main() {
      let name = String::from("Jakob");
      print_hello(name);
      // can no longer use name
  }
</code></pre>

## {data-auto-animate=true}

<pre data-id="code-animation"><code data-trim data-line-numbers rust>
  fn print_hello(name: String) {
      println!("Hello, {}!", name);
  }

  fn main() {
      let name = String::from("Jakob");
      print_hello(name.clone());
      print_hello(name);
  }
</code></pre>

## {data-auto-animate=true}
<pre data-id="code-animation"><code data-trim data-line-numbers rust>
  fn print_hello(name: &str) {
      println!("Hello, {}!", name);
  }

  fn main() {
      let name = String::from("Jakob");
      print_hello(&name);
      print_hello(&name);
  }
</code></pre>

## Mutable Move

```{.rust data-line-numbers=true}
fn print_hello(mut name: String) {
    name.push_str(" Beckmann");
    println!("Hello, {}!", name);
}
```
## Lifetimes

```{.rust data-line-numbers="|2-11|4-6|8-10"}
fn main() {
    let i = 3;
    {
        let borrow1 = &i;
        println!("borrow1: {}", borrow1);
    }
    {
        let borrow2 = &i;
        println!("borrow2: {}", borrow2);
    }
}
```

## {data-auto-animate=true}

<pre data-id="code-animation"><code data-trim data-line-numbers="|4" rust>
  struct A { x: i32 }

  impl A {
      fn borrow(&self) -> &i32 {
          &self.x
      }
  }

  fn main() {
      let a = A { x: 1 };
      println!("{}", a.borrow());
  }
</code></pre>

## {data-auto-animate=true}

<pre data-id="code-animation"><code data-trim data-line-numbers="4|" rust>
  struct A { x: i32 }

  impl<'a> A {
      fn borrow(&'a self) -> &'a i32 {
          &self.x
      }
  }

  fn main() {
      let a = A { x: 1 };
      println!("{}", a.borrow());
  }
</code></pre>
::: notes
- lifetime parameter treated as generic definition
- inferred with only one argument and return
- needed with more than one argument
:::

## {data-auto-animate=true}

<pre data-id="code-animation"><code data-trim data-line-numbers rust>
  struct A<'a> { x: &'a i32 }

  impl<'a> A<'a> {
      fn borrow(&'a self) -> &'a i32 {
          self.x
      }
  }

  fn main() {
      let x = 2;
      let a = A { x: &x };
      println!("{}", a.borrow());
  }
</code></pre>

## {data-auto-animate=true}

<pre data-id="code-animation"><code data-trim data-line-numbers="|13-15" rust>
  struct A<'a> { x: &'a i32 }

  impl<'a> A<'a> {
      fn borrow(&'a self) -> &'a i32 {
          self.x
      }
  }

  fn main() {
      let x = 2;
      let a = A { x: &x };
      {
          let y = 3;
          a.x = &y;
      }
      println!("{}", a.borrow());
  }
</code></pre>

##

```text
error[E0597]: `y` does not live long enough
  --> src/main.rs:14:15
   |
13 |         let y = 3;
   |             - binding `y` declared here
14 |         a.x = &y;
   |               ^^ borrowed value does not live long enough
15 |     }
   |     - `y` dropped here while still borrowed
16 |     println!("{}", a.borrow());
   |                    - borrow later used here

For more information about this error, try `rustc --explain E0597`.
```

::: notes
Ending: let's get back to the code ...
:::

## {data-auto-animate=true}

<pre data-id="code-animation"><code data-trim data-line-numbers rust>
  struct A<'a> { x: &'a i32 }

  impl<'a> A<'a> {
      fn borrow(&'a self) -> &'a i32 {
          self.x
      }
  }

  fn main() {
      let x = 2;
      let a = A { x: &x };
      {
          let y = 3;
          a.x = &y;
      }
      println!("{}", a.borrow());
  }
</code></pre>

## {data-auto-animate=true}

<pre data-id="code-animation"><code data-trim data-line-numbers rust>
  struct A<'a> { x: &'a i32 }

  impl<'a> A<'a> {
      fn borrow(&'a self) -> &'a i32 {
          self.x
      }
  }

  fn main() {
      let x = 2;
      let a = A { x: &x };
      {
          let y = 3;
          a.x = &y;
      }
  }
</code></pre>

::: notes
Compiler is smart enough to figure out we don't access the reference after the lifetime
:::


# Hands-On

Rustlings:

- Immutability:
  - `exercises/01_variables`, try 4 and 5
- Ownership:
  - `exercises/06_move_semantics`, try 2 to 5
- Lifetimes:
  - `exercises/16_lifetimes`, try 1 to 3

::: notes
30 minutes
:::

# Enums

## {data-auto-animate=true}
```rust
  // Simple enum 
  enum Seasons {
    Winter,
    Spring,
    Summer,
    Autumn,
    Bulk,
  }

  let favorite_season = Seasons::Winter;
  let mut current_season = Seasons::Summer;
```
::: notes
enums are not used to group related fields (like structs)
enums give a way of saying that a value is one of a set of possibilities
:::

## {data-auto-animate=true}

<pre data-id="code-animation"><code data-trim data-line-numbers rust>
  // Enums can contain data
  enum Contact{
    Phone(u16, u16, u16, u16),
    Email(String),
  }

  let ipt_phone = Contact::Phone(44, 735, 27, 69);
  let ipt_email = Contact::Email(String::from("info@ipt.ch"));
</code></pre>

::: notes
useful to concisely represent data instead of including an enum inside a struct or similar
constructor is automatically generated for enum containing fields
can contain any kind of data, even structs or other enums
:::

## {data-auto-animate=true}

<pre data-id="code-animation"><code data-trim data-line-numbers="|10" rust>
  // Enums can contain data
  enum Contact{
    Phone(u16, u16, u16, u16),
    Email(String),
  }

  let ipt_phone = Contact::Phone(44, 735, 27, 69);
  let ipt_email = Contact::Email(String::from("info@ipt.ch"));

  ipt_phone.contact();
</code></pre>

## 

```text
error[E0599]: no method named `contact` found for enum `Contact` in the current scope
  --> src/main.rs:13:13
   |
2  |   enum Contact{
   |   ------------ method `contact` not found for this enum
...
13 |   ipt_phone.contact();
   |             ^^^^^^^ method not found in `Contact`
```

## {data-auto-animate=true}

<pre data-id="code-animation"><code data-trim data-line-numbers="|7-15" rust>
  // Enums can contain data
  enum Contact{
    Phone(u16, u16, u16, u16),
    Email(String),
  }

  // Enums can implement methods
  impl Contact{
    fn contact(&self) {
      match self {
        Contact::Phone(a, b, c, d) => println!("dialling {}-{}-{}-{} ...", a, b, c, d),
        Contact::Email(address) => println!("writing to {} ...", address),
      }
    }
  }

  let ipt_phone = Contact::Phone(44, 735, 27, 69);
  let ipt_email = Contact::Email(String::from("info@ipt.ch"));

  ipt_phone.contact(); 
}
</code></pre>

::: notes
methods can be defined on enums
self will be the value that the method gets called on, e.g.
self = ipt_phone = Contact::Phone(...) above
:::

##

```text
dialling 44-735-27-69 ...
```

## Rust's `Option` Enum 

```rust
  enum Option<T> {
    None,
    Some(T),
  }

  let apples: Option<u8> = Some(4);
  let orange: Option<u8> = None;
```
::: notes
value inside Some(T) must be of type defined with Option<T>
:::

## Rust's `Result` Enum 

```rust
  enum Result<T, E> {
    Ok(T),
    Err(E),
  }
```
::: notes
Ok type T can (and usually is) differnt from Err type E
Results must be used -> compiler will warn of unused/ignored Result values
both Ok and Err variants can be () -> but does it make sense?
:::

# Pattern Matching

## 

```rust
  fn real_season(season: Seasons) -> Result<Seasons, String> {
      match season {
          Seasons::Winter => Ok(season),
          Seasons::Spring => Ok(season),
          Seasons::Summer => Ok(season),
          Seasons::Autumn => Ok(season),
          Seasons::Bulk => Err("I am not so sure about that...".to_string()),
      }
  }
```

## 

```rust 
  fn real_season(season: Seasons) -> Result<Seasons, String> {
      match season {
          other => Ok(other),
          Seasons::Bulk => Err("I am not so sure about that...".to_string()),
      }
  }
```

## 

```rust
  // Remeber our apples and oranges
  fn buy_more(fruit: Option<u8>) -> bool {
    match fruit {
      None => true,
      Some(x) => false,
    }
  }
```

::: notes
matches are exhaustive
can use wildcard catch-all pattern by defining a variable that will be used for all non-covered cases
can use _ to do something with non-covered cases without reusing the value
:::

# Error Handling

## To Panic or Not Panic 

```rust
  // Unrecoverable errors use panic! macro
  if totally_broken {
    panic!("nothing we can do about this");
  }
```

---

```rust
  // Recoverable errors using Result
  let some_variable = function_that_could_fail();
  match some_variable {
      Ok(result) => use_result(result),
      Err(error) => println!("this error occurred: {error:?}"),
  }

  // Shortcuts for Result type
  let only_valid = function_that_could_fail()
      .unwrap();
  let only_valid = function_that_could_fail()
      .expect("oh no something bad happened");

  // Or even shorter (only if return types align)
  let only_valid = function_that_could_fail()?;
```
::: notes
when should you panic?
-> there is no way back, program crashes
when to use result type and unwrap?
-> calling code has option to deal with error gracefully
:::

## Match on Different Errors

```rust
  use std::fs::File;

  fn read_tweet(source_path: &str, buffer: &mut [u8]) -> usize {
    let tweet = File::open(source_path);

    match tweet.unwrap() {
      Ok(file) => {
        let bytes_read = file.read(&mut buffer);
        match bytes_read.unwrap() {
          Ok(number_of_bytes) => number_of_bytes,
          Err(err) => panic!("Failed to read the love letter: {err:?}"),
        },
      },
      Err(err) => panic!("Failed to open the love letter: {err:?}"),
    }
  }
```

::: notes
all three topics, 20 minutes
unwrap_or_else() -> closure that allows a potential error to be used for further processing
:::


# Hands-On

Rustlings:

- Enumerations -> `exercises/08_enums`, try 1 and 2
- Pattern Matching -> `exercises/12_options`, try 2 and 3
- Error Handling -> `exercises/13_error_handling`, start with 1

::: notes
20 minutes
:::

# Traits

# Macros

::: notes
20 minutes
:::


# Hands-On

Check rustlings content for this

::: notes
20 minutes
:::
