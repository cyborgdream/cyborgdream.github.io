+++
title = "Sunday sunny deck"
description = "8/???"
date = 2022-01-16
draft = false
slug = "sunday-sunny-deck"

[taxonomies]
categories = ["browser"]
tags = ["rust", "learning"]

[extra]
comments = true
+++

Ok, now I'm kinda back in the groove, let's give it another try:

1. Make a `GET`er.

    How to import libs?
    Apparently I've chosen the reqwest lib and already imported it.
    And that's how you do it on your `cargo.toml`:

    ```
    [dependencies]
    reqwest = { version = "0.11.7", features = ["blocking"] }
    ```

2. How to use this lib?

    Running docs:

    ```
    cargo doc # to build
    cargo doc --open
    ```

3. How to return void?

    `fn foo() -> () { ... }`

    Which is different from:

    A diverging function is guaranteed to never return, i.e. `bar(); println!("this never prints");`. An example of a diverging function is `fn bar() -> ! { panic!() }` or `fn bar() -> ! { loop {} }`.

4. What's an async function?

    There's no official impl :(, so I have to dig it out.

5. Also, I was curious about pointers and references in Rust.

    `String` is an owned string and can grow and shrink dynamically.
    `&str` is a borrowed string and is immutable.

    Apparently every time it's a reference, it's immutable. Unless you add a:

    `&mut Vec<i32>`, noting that when you write something like this... A mutable vector reference does not own its elements but it can modify the elements of the vector it points to. There can only be at most one active mutable vector reference around at any point in time and the original vector variable has to be mut to allow a mutable reference.
    which is different from `&mut [i32]`. It points to a subset of a Vec<i32>, and the rest is the same.

6. I need to deal with the cases that my vector doesn't receive all the arguments I'm using in the code.

    ```
        let host = url_vec[0].to_string();
        let path = "/".to_owned() + url_vec[2];
        let port = url_vec[3];
    ```

    I'm reading into the vector's doc to see if I get some idea that's fancier than treating it with `if`s. Something to be continued...

Had to relearn some things, but it was good!
Tomorrow to my full time job, let's see how much space I'll have left to code in the first day of ideas week and the first time I wake up at 5am to work!
