+++
title = "C++ to Rust II"
description = "26/??? Continuing my journey from C++ to Rust"
date = 2022-04-15
draft = false
slug = "rust-for-cpp-continues"

[taxonomies]
categories = ["journal"]
tags = ["learning", "rust"]

[extra]
comments = true
+++

Methods are always called as `.`.

There are many different ways of choosing the size of your ints in rust:

```
let x: bool = true;
let x = 34;   // type int
let x = 34u;  // type uint
let x: u8 = 34u8;
let x = 34i64;
let x = 34f32;
```

The `uint` is pointer sized, E.g, on a 32bit system means 32 bit unsigned integer.

The `let` statement allows you to create new `x` variables, hiding the previous one.

You can use the `as` keyword to cast stuff.

```
let x = 34u as int;     // cast unsigned int to int
let x = 10 as f32;      // int to float
let x = 10.45f64 as i8; // float to int (loses precision)
let x = 4u8 as u64;     // gains precision
let x = 400u16 as u8;   // 144, loses precision (and thus changes the value)
println!("`400u16 as u8` gives {}", x);
let x = -3i8 as u8;     // 253, signed to unsigned (changes sign)
println!("`-3i8 as u8` gives {}", x);
//let x = 45u as bool;  // FAILS!
```

#### Unique pointers

Rust enforces memory safety by type checking pointers.

So... The way you create pointers are using the `Box<T>` keyword. And they're dereferenced the same way you'd do it in C++:

```
fn foo() {
    let x = Box::new(75);
    println!("`x` points to {}", *x);
}
```

The mutability of the pointer will follow whatever the type is saying, so if you create a type `let` its pointer won't be mutable, on the other hand a `mut` pointer would.

Pointers can be returned from a function, if they do, their memory is not freed (meaning there are no dangling pointers in Rust). However, they will eventually go out of scope and therefore will be freed.

Pointers are unique (or linear).

```
fn foo() {
    let x = Box::new(75);
    let y = x;
    // x can no longer be accessed
    // let z = *x;   // Error.
}
```

Even if a pointer is passed to another function, it can no longer be accessed:

```
fn bar(y: Box<int>) {
}

fn foo() {
    let x = Box::new(75);
    bar(x);
    // x can no longer be accessed
    // let z = *x;   // Error.
}
```

Rust unique pointers are similar to C++ `std::unique_ptr`. The difference is that this is checked in runtime in C++ and in Rust it's compile time.

Calling `Box::new(x)` to an existing `x` variable won't create a ref. to that value, but will simply copy it.

```
fn foo() {
    let x = 3;
    let mut y = Box::new(x);
    *y = 45;
    println!("x is still {}", x);
}
```

Rust has move rather than copy semantics, in general.

#### Borrowed pointers

To have a reference to an existing value we use `&`, this is the C++ fill for C++ pointer/reference, seems like it works the same as passing an item as a ref. to a function. Use `*` to deref.

The `&` op. doesn't allocate memory, if a borrowed ref. goes out of scope, no memory is deleted.

Just like pointers references are immutable by default.

`let`s are just a keyword for auto, more or less, they're not really imposing anything.