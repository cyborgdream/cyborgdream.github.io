I started to read this small book based on a series of blog posts by [Nick Cameron](http://www.ncameron.org/) which seems like a friendly introduction to Rust for people who came from C++.

The first article already gave me some cool insights:

>Two compiler options you should know are -o ex_name to specify the name of the executable and -g to output debug info; you can then debug as expected using gdb or lldb, etc.

You don't have to specify types in Rust, they can be inferred. We can omit the explicit types with `let`, but types are required for function arguments. When you don't use an arg. in a function you don't have to name it, you can just `_: T`. The return type of a function is given after `->` if it returns void, you just don't type anything, or you can do `-> ()`. You also don't have to use the `return` keyword, if the last expr. in a function body (or any other body) is not finished with a semicolo, then it is the return value.

 Rust allows you to do this in unsafe blocks, along with many other unsafe things. Crucially, Rust ensures that unsafety in unsafe blocks stays in unsafe blocks and can't affect the rest of your program).

Rust is also memory safe.

Omg there are no ternaries in Rust. Come on.

`loop` loops for ever.

A loop on vecs would look like:

```
fn print_all(all: Vec<i32>) {
    for a in all.iter() {
        println!("{}", a);
    }
}
```

And this is how you get the indices:

```
fn print_all(all: Vec<i32>) {
    for i in ..all.len() {
        println!("{}: {}", i, all.get(i));
    }
}
```

Rust also has something like `switch`, it has to be exhaustive:

```
fn print_some(x: i32) {
    match x {
        0 => println!("x is zero"),
        1 => println!("x is one"),
        10 => println!("x is ten"),
        y => println!("x is something else {}", y),
    }
}
```

You can also use `_ => {}` instead of the last line to catch all remaining options.

Travelling interrupted this flux!