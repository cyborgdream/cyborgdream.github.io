+++
title = "Threading issues in C++"
description = "24/??? A deeper look in how things can go wrong in C++"
date = 2022-03-19
draft = false
slug = "threading"

[taxonomies]
categories = ["journal"]
tags = ["learning", "cpp", "security"]

[extra]
comments = true
+++

# C++ multithreading mistakes

> What's a thread and a process? 

**A process is a program under execution i.e an active program.** **A thread is a lightweight process that can be managed independently by a scheduler**.

> Differences between concurrency and multithreading and parallelism.

Multithreaded programs splits into two ro more threads that may execute concurrently. Each thread acts as an individual program, but they all share the same memory. Switching between threads is faster than switching between processes. Concurrency and parallelism are often considered to be equivalent, but they're not. All parallel programs are concurrent, but not all concurrent programs are parallel. Parallel computing is the simultaneous use of multiple computer resources to solve a computational problem. Each part of the problem must be independent of the others and simultaneously solvable. In other words, a concurrent program can be run in an interleaved way (where each little piece of the software run at a different time) and the parallel concurrency is when you're running something at the same time as other thread.

# Issues

**Race conditions:** can occur in any scenario in which two threads can produce different behavior, depending on which thread completes first. They need to both have access to a common object, to run at the same time and at least one of them have to alter the state of the race object. One fix for this besides locking threads iss by creating atomic objects. Here are some doc remiders for this section:

```
std::thread.join()
std::thread.deatch()
```

`mutex`: can be used to protect shared data from being simultaneously accessed by multiple threads. A calling thread owns a mutext, from the time it locks until it calls unlock.

`lock_guard`: is a mutex wrapper that provides RAII for owning a mutex for the duration of a scoped block.

```
#include <thread>
#include <mutex>
#include <iostream>
 
int g_i = 0;
std::mutex g_i_mutex;  // protects g_i
 
void safe_increment()
{
    const std::lock_guard<std::mutex> lock(g_i_mutex);
    ++g_i;
 
    std::cout << "g_i: " << g_i << "; in thread #"
              << std::this_thread::get_id() << '\n';
 
    // g_i_mutex is automatically released when lock
    // goes out of scope
}
 
int main()
{
    std::cout << "g_i: " << g_i << "; in main()\n";
 
    std::thread t1(safe_increment);
    std::thread t2(safe_increment);
 
    t1.join();
    t2.join();
 
    std::cout << "g_i: " << g_i << "; in main()\n";
}
```

The good flux is:
1. create a `mutex`
2. `lock_guard` the mutex
3. That's all, stuff will fix itself once the thread has finished its job

But here are some interesting pitfalls that mitigation strategies can bring:
- deadlock
- assuming threads will
        - run in a particuclar order
        - make progress before one thread ends
    - relying on tests to find data races and deadlocks
    - memory alllication and freeing, while it's freed in one thread the other one might still need to use it
        
`std::atomic`: guarantee well defined behavior when being acessed by multiple threads. This is defined by `std::memory_order`.

**deadlock**: As explained earlier the way to fix race condiftions is by making threads mutually exclusive with things like locks. Deadlocks occur whenever two or more control flos block each other in a way that none can continue. Four conditions have to apply for deadlock to occur:
1. mutual exclusion (at least one nonshareable resource must be held, (I don't really understand what this means))
2. hold and wait (a thread must hold a resource while waiting for the availability of another resource)
3. no preemption (resources can't be taken away from a thread while they're being used)
4. circular wait (a thread must wait on another resource on another thread that by its turn is waiting on the resource of the first thread)
To fix this issue you can lock the mutexes in a predefined order, which will prevent the circular wait issue.

```
std::atomic<unsigned int> BankAccount::globalId(1);
  
int deposit(BankAccount *from, BankAccount *to, int amount) {
  std::mutex *first;
  std::mutex *second;
  
  if (from->get_id() == to->get_id()) {
    return -1; // Indicate error
  }
  
  // Ensure proper ordering for locking.
  if (from->get_id() < to->get_id()) {
    first = &from->balanceMutex;
    second = &to->balanceMutex;
  } else {
    first = &to->balanceMutex;
    second = &from->balanceMutex;
  }
  std::lock_guard<std::mutex> firstLock(*first);
  std::lock_guard<std::mutex> secondLock(*second);
  
  // Check for enough balance to transfer.
  if (from->get_balance() >= amount) {
    from->set_balance(from->get_balance() - amount);
    to->set_balance(to->get_balance() + amount);
    return 0;
  }
  return -1;
}
  
void f(BankAccount *ba1, BankAccount *ba2) {
  // Perform the deposits.
  std::thread thr1(deposit, ba1, ba2, 100);
  std::thread thr2(deposit, ba2, ba1, 100);
  thr1.join();
  thr2.join();
}
```
