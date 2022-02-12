+++
title = "More C++ concurrency"
description = "20/??? "
date = 2022-02-10
draft = false
slug = "more-cpp-concurrency"

[taxonomies]
categories = ["journal"]
tags = ["cpp", "learning", "security"]

[extra]
comments = true
+++

Continuing yesterday... Hard day to focus on stuff. Don't feel like I got a lot done, but what can you do!

### More concurrency baby

When copy constructors are marked as delete it's ensured that they're not automatically provided by the compiler.

Calling `detach()` on a thread will leave it running on the background (daemon thread, ownership and control are passed over to the C++ Runtime Library. That makes a thread no longer joinable. Can be safe checked using the `joinable()` method.

Because of the risk of not being able to convert a `char*` to string on the right time (and avoiding dangling pointers) is better to cast it so string before passing the buffer to `std::thread`. So, instead of `std::thread t(f, 3, buffer);` do `std::thread t(f, 3, std::string(buffer);` with `char buffer[1024];`.

Sometimes you might want to pass stuff by reference to a thread, the problem is that they're oblivious to types of arguments expected by a function, they just blindly copy stuff. TO fix this, is necessary to wrap elements that need to be reference with `std::ref(data)`.

When an object is moved in the thread context, the original object is "empty", using move semantics to move stuff to a `std::unique_ptr` leave the source obj. with a `nlptr`.

You can transfer ownership of threads, by using move semantics.

`std::thread::hardware_concurrency()` will show you how many CPU cores are available to truly run cuncurrent threads. `std::accumulate` divides the work among the threads.

Identifying threads can be done using `std::thread::id`.

#### Problems with sharing data

Invariant -> a statement that's always true about a particular data structure, e.g this var contains the number of items in the list.

Race condition: there are several ways to avoid it, the simplest is by wraping your data with a protection mechanism. You can use lock-free programming or mutexes (mutual exclusion). You lock a piece of data and once you're finished, you unlock it. They're not a silver bullet as they have their own issues like deadlocks.

Instead of locking and unlocking a thread manually it's recommended to use `std::lock_guard`, which is RAII for mutexes. Here's a simple example of use:

```
std::mutex m;//you can use std::lock_guard if you want to be exception safe 
int i = 0; 
void makeACallFromPhoneBooth() 
{

    //The other men wait outside 
    m.lock();// one man gets a hold of the phone booth door and locks it. 
      //man happily talks to his wife from now....
      std::cout << i << " Hello Wife" << std::endl;
      i++;//no other thread can access variable i until m.unlock() is called
      //...until now, with no interruption from other men
    m.unlock();//man unlocks the phone booth door 
}

int main() 
{
    //This is the main crowd of people uninterested in making a phone call

    //man1 leaves the crowd to go to the phone booth
    std::thread man1(makeACallFromPhoneBooth);
    //Although man2 appears to start second, there's a good chance he might
    //reach the phone booth before man1
    std::thread man2(makeACallFromPhoneBooth);
    //And hey, man3 also joined the race to the booth
    std::thread man3(makeACallFromPhoneBooth);

    man1.join();//man1 finished his phone call and joins the crowd
    man2.join();//man2 finished his phone call and joins the crowd
    man3.join();//man3 finished his phone call and joins the crowd
    return 0;
}
```

If you allow reference data to be called outside the scope of a lock, is bad! Because people can do some injection in your data:

```
class some_data
{
int a;
    std::string b;
public:
    void do_something();
};
class data_wrapper
{
private:
    some_data data;
    std::mutex m;
public:
    template<typename Function>
    void process_data(Function func)
    {
        std::lock_guard<std::mutex> l(m);
        func(data);
    }
};
some_data* unprotected;
void malicious_function(some_data& protected_data)
{
    unprotected=&protected_data;
}
data_wrapper x;
void foo() {
    x.process_data(malicious_function);
    unprotected->do_something();
}
```

So, deadlocking something is when one thread locks one data that the other thread needs and/or vice-versa. The common advice for avoiding deadlock is to always lock the two mutexes is the same order: if you always lock mutex A before B, then you'll never deadlock. We can use the `std::lock` function that allows you to lock two or more mutexes at once without risking deadlocks. Here are some other tips:

* avoid nested locks

* avoid calling user supplied code while holding a lock

* acquire locks in a fixed order (this is a very bad advise, your code shouldn't depend on order, LOL)

* use a lock hierarchy
    using something like "high level mutex", "low level mutex"

#### Some random stuff

* weak_pointers are mainly useful to treat cases of cyclical reference;

* default constructor, equivalent to empty constructor. It's good for initializing trivial objects or primitive types

* default destructor, equivalent to empty destructor. calls the superclass destructor

* copy assignment operators... here's a crazy issue that this thing brings:

```
class Buffer {
    public:
        Beffuer(int size, int* buffer) : size(size), buffer(buffer) {}

        int size;
        int* buffer;
};

const int kBufSize = 2;
int* buffer = new int[kBufSize]{1, 2};

Buffer b1 = Buffer(kBufSize, buffer);

Buffer b2 = b1;
b2.buffer[0] = -2;
```

So if we print `b1.buffer[0` we'll get -2! Because this copy assignment operator is copying the pointer to b2. C++ is crazy broken language.