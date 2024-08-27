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

- Add slides on mutability
- Add slides on ownership and borrowoing
- Slices
- Lifetimes
- Start explaining references
:::

<!-- TODO(@jakob): add slides immutability -->

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
  fn print_hello(name: &str) {
      println!("Hello, {}!", name);
  }
  
  fn main() {
      let name = String::from("Jakob");
      print_hello(&name);
      // can now be used here again
      print_hello(&name);
  }
</code></pre>

<!-- TODO(@jakob): add slides lifetimes -->
<!-- TODO(@jakob): add slides slices -->

# Hands-On

Check rustlings content for this

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
