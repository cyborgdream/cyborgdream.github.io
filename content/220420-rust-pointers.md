+++
title = "C++ to Rust III"
description = "29/??? More pointers"
date = 2022-04-20
draft = false
slug = "rust-for-cpp-pointers"

[taxonomies]
categories = ["journal"]
tags = ["learning", "rust"]

[extra]
comments = true
+++

Happy 4/20!!!

#### Borrowed pointers part II

```
fn foo() {
    let x = 5;                // type: i32
    let y = &x;               // type: &i32
    let z = y;                // type: &i32
    let w = y;                // type: &i32
    println!("These should all 5: {} {} {}", *w, *y, *z);
}
```

If a mutable value is borrowed, it becomes immutable for the duration of the borrow, meaning that stuff like the following won't work:

```
fn foo() {
    let mut x = 5;            // type: i32
    {
        let y = &x;           // type: &i32
        //x = 4;              // Error - x has been borrowed
        println!("{}", x);    // Ok - x can be read
    }
}
```

Unlike C++, Rust won't automatically reference a value for you:

```
fn foo(x: &i32) { ... }

fn bar(x: i32, y: Box<i32>) {
    foo(&x);
    // foo(x);   // Error - expected &i32, found i32
    foo(y);      // Ok
    foo(&*y);    // Also ok, and more explicit, but not good style
}
```

If you have an immutable variable in rust, you are guaranteed it won't change. All mutable vars are unique. If you have a mut var you know it's not going to change unless you change it.

In Rust it's not possible to create a borrowed reference that will outlive the reference, the lifetime of the reference must be shorter than the lifetime of the reference value (or at least as long, if I understand it correctly). Example:

```
fn foo() {
    let x = 5;
    let mut xr = &x;  // Ok - x and xr have the same lifetime
    {
        let y = 6;
        //xr = &y     // Error - xr will outlive y
    }                 // y is released here
}                     // x and xr are released here

```

There's something called explicit lifetimes, and lifetime-polymorphism, that looks like this: `&'a T (cf &T)` - but it's something for the future.

I'm skipping Rc and raw pointers cause it seems like an uncertain part of the language.

#### Data types

Structs can't be recursive in Rust.

You can use `let` to destructure deep types:

```
fn bar(x: (int, int)) {
    let (a, b) = x;
    println!("x was ({}, {})", a, b);
}
```

Enums are useful in Rust:

```
enum Expr {
    Add(int, int),
    Or(bool, bool),
    Lit(int)
}

fn foo() {
    let x = Or(true, false);   // x has type Expr
}
```

You can do pretty complex things with it using `match`:

```
fn bar(e: Expr) {
    match e {
        Add(x, y) => println!("An `Add` variant: {} + {}", x, y),
        Or(..) => println!("An `Or` variant"),
        _ => println!("Something else (in this case, a `Lit`)"),
    }
}
```

Kind of beautiful, you also have to treat for all cases.

Meh, okay I'm bored of reading stuff I want to implement things...

