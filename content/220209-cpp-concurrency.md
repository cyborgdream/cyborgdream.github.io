+++
title = "C++ concurrency"
description = "19/??? Reviewing something I rarely use"
date = 2022-02-09
draft = false
slug = "cpp-concurrency"

[taxonomies]
categories = ["journal"]
tags = ["cpp", "learning", "performance"]

[extra]
comments = true
+++

### C++11 for concurrency

C++ references allow you to create a new name for an existing object.

Rvalues are typically temp. (unless you do fancy `const` tricks). So they're very related to the idea of moving.

Now I understand the whole copy stuff, it's really because you're not sure if the compiler is doing the right stuff.

Rvalues references only bind to rvalues, never to lvalues.

```
int&& i = 42;
int&& i = j;
```

This is how you implement a copy constructor and a move constructor:

```
X(const X& other):
    data(new int[100])
    {
        std::copy(other.data, other.data+100, data);
    }

X(X&& other):
    data(other.data)
    {
        other.data = nullptr;
    }
```

In the case of the move const. you just copy the pointer to the data and leave the other instance with a nullptr.

These `static_cast<X&&` and `std::move()` are equivalent.

```
X x1;
X x2 = std::move(x1);
X x3 = static_cast<X&&>(x2);
```

Move semantics are used in Thread Libraries a lot as an optimization mechanism to avoid expensive copies that are going to be destroyed anyway.

When you create templates with rvalue references, you allow for a single function template to accept both lvalue and rvalue parameters. So something like `foo(42);` evaluates to `<int>(42)` while `foo(i)` assuming `int i = 43;` will evaluate to `foo<int&>(i)`.

To declare a constructor as default means...?
And wtf is a trivial constructor? And copy assignment operators and destructors? I know they can be copied with `memcpy` and `memmove`.

### Actual concurrency

There are two basic approaches to concurrency. The first is to have multiple single-threaded processes, the second to have multiple threads in a single process. C++ will commonly take the approch of the later, but weird stuff like Erlang likes to do the first: spawning millions of thread and letting them fail thing that Fabi was talking about.

There's a cost in concunrrency of lauching a thread, because the OS has to llocate kernel resources and stack space and add the new thread to the scheduller.


`join()` is a way to telling the base thread to wait for the spawned one.

Both of the following syntaxes just create new thread objects but don't really spawn them:

```
std::thread my_thread((background_task()));
std::thread my_thread{background_task()};
```

You can also use lambda expressions:

```
std::thread my_t([] (
    do_smth();
    do_more();
));
```

Once the thread is started it's necessary to wait for it to finish (by joining it) or leave it to run by its own (by detaching it). If you don't, the thread obj is destroyed. So you must either join or detach a thread even in the presence of exceptions.

You have to be aware when detaching things to not destroy them before they finish running. One common way to avoid the scenario of accessing variables that were destroyed is by making the thread function self-contained and copy the data into the thread rather than just share it.
