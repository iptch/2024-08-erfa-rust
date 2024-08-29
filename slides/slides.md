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
## Project Structure

<!-- TODO -->

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

# Enumerations

# Pattern Matching

# Error Handling

::: notes
all three topics, 20 minutes
:::


# Hands-On

Check rustlings content for this

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
