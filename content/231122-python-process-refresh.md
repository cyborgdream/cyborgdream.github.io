+++
title = "Python's processes, threading and memory"
description = "Little nuggets on memory, parallelization, asyncio and multithreading in Python"
date = 2023-11-22
draft = false
slug = "python-process-refresh"

[taxonomies]
categories = ["tutorial"]
tags = ["python", "performance"]

[extra]
comments = true
+++

This is part of a series where I write down things I know or I've learned while trying to keep myself sharp in various subjects that I don't use so often anymore.

This blog post is about Python memory management and various applications for parallel and multithreading computations. Let's go straight to it!

### Python's memory management

Python manages memory through a private heap space. It uses algorithms like garbage collection (that keeps tracks of variables and such by reference counting). Memory for variables is not statically allocated, when variables are declared Python infers the data type and allocates memory accordingly.

When thinking about memory management in different threads or in parallel computing then it's also important to understand the role of the Python GIL. 

The GIL is a kind of lock. When you’re sharing or modifying data using different threads you ideally need something that will keep track of all the changes that are happening, so you avoid data corruption. Only one Python thread can hold the GIL at a time. This means that only one thread can execute Python code at any time, regardless of the number of CPUs. Meaning Python is not truly concurrent, if you define concurrent by being able to run several threads at the same time. You can work around this by using `multiprocessing`

To prevent a Python thread from holding the GIL indefinitely Python causes the current thread every 5ms, releasing the GIL.

Every Python standard lib functions that makes a syscall releases the GIL. E. g disk IO, network IO and `time.sleep()`. Many CPU intensive functions also release the GIL.

If one is doing intensive CPU work it’s advisable to use the `multiprocessing` module, however if one is doing multiple IO-bound tasks, threading is still recommended as most of the time spent on IO is waiting for the IO itself and not processing it or writing it to memory.

Another specific Python trait is that it has a garbage collector. The garbage collector exists for every Python process (we will get into what is a process later on). It keeps tracks of the number of variables that we have running on the code. Once these variables are out of scope, their counter goes down, if it goes to 0 the object is deallocated, and its memory is freed. The garbage collector will also delete any variable by the end of a process’ execution.
Python also employs a cyclic garbage collector because reference counting alone can't deal with cycles — situations where a group of objects reference each other, but are not referenced anywhere else in the code. The cyclic collector periodically searches for such groups of objects and reclaims them.
The garbage collector runs automatically during program execution but can also be manually triggered.

### Python processes, threads and coroutines

First, let's get an overview on each one of these terms so we can dive in into the details.

**Process:** an instance of a program running. There are usually hundreds of these going on at the same time and the OS scheduler suspends them periodically to allow other processes to run. Each process has their own memory, meaning they have to communicate with each other through pipes or sockets (which only carry raw bites), that’s why Python objects must be serialized in order to pass from one process to another.

**Thread:** An execution unit within a process. When a process starts it only has one thread, but it can spawn more. Threads within one process share the same memory space.

**Coroutine:** A function that suspends itself and resume later. `async def`. Python coroutines usually run under a single a thread under one event loop. Frameworks like asyncio, Curio or Trio provide an event loop and support libraries for nonblocking coroutine-based I/O. Coroutines must explicitly cede control with the `yield` or `await` keyword, so that another may proceed concurrently may take place. This means any blocking code in a coroutine blocks the execution of the event loop and all other coroutines.

- Each instance of Python is a process. You can start additional Python processes using `multiprocessing` or `concurrent.futures`. The new spawned process won’t share memory between them.
- Python starts with one thread in which it runs the user’s program and the garbage collector. You can start new thread by using `threading` or `concurrent.futures`

I always found these terms very confusing, so here are some extra points on what are **the differences between multiprocessing (or parallelization) and multithreading**:

- In Python, multithreading involves multiple threads of execution within a single process.
- Threads share the same memory space, which makes for efficient memory usage and easier data sharing.
- Due to the GIL, Python threads are not truly parallel in CPU-bound tasks. Instead, they are useful for I/O-bound tasks where the program spends a lot of time waiting for external events.
- The `threading` module is used to implement multithreading in Python.

Graphic interfaces running on Python might use multithreading to keep the GUI running while running some expensive computation on the back. As shown in the following `tkinter` example:

```python
import tkinter as tk
from threading import Thread
import time

def long_running_task():
    # Simulate a long-running task
    time.sleep(5)
    print("Task finished")

def start_task():
    Thread(target=long_running_task).start()

app = tk.Tk()
app.geometry('200x100')

start_button = tk.Button(app, text='Start Task', command=start_task)
start_button.pack(pady=20)

app.mainloop()
```

- Multiprocessing involves creating multiple processes, each with its own Python interpreter and memory space.
- This allows each process to run on a separate CPU core, bypassing the GIL, and therefore can achieve true parallelism in CPU-bound tasks.
- Data sharing between processes is more complex and less efficient because it requires inter-process communication (IPC) mechanisms like queues or shared memory.
- The `multiprocessing` module is used to implement multiprocessing in Python.

Now that we cleared these one out there are still one point that might get you confused which is within **asyncio and multithreading**. So let's dive in to that too.

Both `asyncio` and multithreading are used to deal with I/O in Python. However, there are some differences between both. So, for multithreading you have an extra overhead of having to switch between threads, besides you still have to manage data with locks and such because the GIL is only taking care of that two threads to access the same data at the same time but these data could still be interfered with and produce bad data.

```python
import threading

# A shared resource
counter = 0

# A lock object to synchronize access to the shared resource
lock = threading.Lock()

def increment():
    global counter
    with lock:
        for _ in range(100000):
            counter += 1

def decrement():
    global counter
    with lock:
        for _ in range(100000):
            counter -= 1

# Creating threads
t1 = threading.Thread(target=increment)
t2 = threading.Thread(target=decrement)

# Starting threads
t1.start()
t2.start()

# Waiting for threads to complete
t1.join()
t2.join()

print(counter)
```

I think the best use case scenario for multithreading instead of asyncio is when integrating with C extensions. If these libraries are releasing the GIL to compute their own operations it might make sense to keep the architecture consistency. Also when working with Python GUIs as explained above. And of course when your tasks are actually computationally heavy and you need a mix of CPU and IO to deal with them.

**Futures:** Futures are found in both contexts of asyncio and multithreading. Though they’re implemented differently in each context.

In the context of multithreading, a `Future` is often called a `concurrent.futures.Future`, provided by the `concurrent.futures` module. It represents a future result of a computational task that can be executed in a separate thread or process pool.

When to use `asyncio` Futures over Tasks:

- When interfacing with lower-level asynchronous code that uses callbacks (e.g., integrating with a library that doesn't use `async`/`await`), you might receive a Future object that you can await.
- When you need a cancellable and checkable future that may be set from outside of the coroutine itself.

```python
import asyncio

async def set_future_result(future, result):
    await asyncio.sleep(2)  # Simulate work
    future.set_result(result)

async def main():
    # Create a Future object
    future = asyncio.Future()

    # Schedule setting the future result
    asyncio.create_task(set_future_result(future, 'Future is done!'))

    # Wait for the future to be set
    result = await future
    print(result)

asyncio.run(main())
```

In `asyncio`, a Future is an object that represents the result of work that has not been completed yet. It acts as a placeholder for the eventual result of an asynchronous operation. When you `await` on a Future in `asyncio`, you're telling the event loop to continue running other tasks until this Future's result is available.

When to use `concurrent.futures.Future` over direct multithreading:

- When you want to execute a computation in a separate thread/process and want an easy way to retrieve its result.
- When you want to submit multiple tasks to a thread/process pool and manage their results.

```python
from concurrent.futures import ThreadPoolExecutor
import time

def compute_square(n):
    time.sleep(2)  # Simulate work
    return n * n

# Using ThreadPoolExecutor to execute a function in a separate thread
with ThreadPoolExecutor() as executor:
    # Schedule the execution and get a Future object
    future = executor.submit(compute_square, 2)

    # Do other things here if needed...

    # Retrieve the result with future.result() which will block until the result is available
    result = future.result()
    print(f"The square of 2 is {result}")
```

### Nuggets of knowledge

Now that you hopefully have the fundamental definitions we can dive into some specific common problems and their solutions:

**Handling race conditions in Python**

Making sure a race condition doesn’t happen means making sure no concurrent processes or threads perform conflicting operations on shared resources. One can use locks to achieve that, lock the data and only allow it to be modified after all the relevant operations were completed. One can use the `Queue` Python module to transfer data between threads. One could use immutable data structures like tuples, to make sure that they’re not being modified. 

One could also use higher level abstractions like `concurrent.futures.ThreadPoolExecutor` which will manage tasks without the need of taking care of individual locks. Here you don’t need to worry about it because you can use tasks that are schedule.

However, it’s important to note that while `ThreadPoolExecutor` handles the synchronization of task submission and execution, if your tasks themselves need to modify shared data, you must still handle synchronization within those tasks to avoid race conditions. This is where you might still need to use locks or other synchronization mechanisms, such as those provided by the `threading` or `multiprocessing` modules.

For example, if you have a counter that is incremented by each task, you would need to ensure that incrementing the counter is an atomic operation to prevent a race condition. This could be done with a lock:

```python
from concurrent.futures import ThreadPoolExecutor
from threading import Lock

counter = 0
lock = Lock()

def increment_counter():
    global counter
    with lock:
        counter += 1

with ThreadPoolExecutor(max_workers=10) as executor:
    futures = [executor.submit(increment_counter) for _ in range(100)]

# Wait for all tasks to complete
for future in futures:
    future.result()

print(counter)
```

On side notes you could also use a `semaphore`, in which you allow a number of threads to use the same memory space. 

There’s also the `Condition` API, for example, but I’ve never used it.

**Decorators and optimization**

You might have heard of decorators in Python. If not, here's a refresher on them:

Decorators are special Python attributes that allows the developer to change the content of a function. They’re just really syntactic sugar to pass one function as the input of a function and to output them. 

A common case of decorator’s use is in order to trigger events. Here’s an example:

```python
# A simple event system

# A dictionary to hold event names and their associated callbacks
event_registry = {}

# The decorator function for registering callbacks
def register_event(event_name):
    def registrar(func):
        event_registry.setdefault(event_name, []).append(func)
        return func
    return registrar

# Using the decorator to register handlers for specific events
@register_event('startup')
def handle_startup():
    print("Handling startup event.")

@register_event('shutdown')
def handle_shutdown():
    print("Handling shutdown event.")

# A function to simulate an event and call the registered functions
def trigger_event(event_name):
    for func in event_registry.get(event_name, []):
        func()

# Simulate events
trigger_event('startup')
trigger_event('shutdown')
```

Or in Flask, you can decorate functions with a route, `@app.route('index')` so when the user is in the index page, trigger the function that was decorated with this route.

It can also be used in lazy evaluation situations. In which we store the value of a computation.

```python
class LazyProperty:
    def __init__(self, function):
        self.function = function
        self.name = function.__name__
    
    def __get__(self, obj, cls):
        if obj is None:
            return self
        value = self.function(obj)
        setattr(obj, self.name, value)
        return value

class ExpensiveObject:
    def __init__(self):
        self._expensive_attribute = None

    @LazyProperty
    def expensive_attribute(self):
        print("Computing the expensive attribute...")
        # Simulate an expensive computation
        import time
        time.sleep(2)  # Delay to simulate expensive computation
        return "Expensive Data"

# Example usage
obj = ExpensiveObject()
print("Object created, expensive attribute not computed yet.")
# The first access to expensive_attribute will compute and store the value
print(obj.expensive_attribute)  
# Subsequent accesses will use the cached value
print(obj.expensive_attribute)
```

Very useful, somewhat tricky, I always have to re-check how to implement decorators that receive arguments, for example, which consists of the next example. Here we will also dive in to the use of decorators in order to optimize our Python code.

You can create a decorator to manage shared resources with a lock (you might recognize this from other optimization libraries like Dask!):

```python
from threading import Lock

lock = Lock()

def synchronized(lock):
    def decorator(function):
        @wraps(function)
        def wrapper(*args, **kwargs):
            with lock:
                return function(*args, **kwargs)
        return wrapper
    return decorator

@synchronized(lock)
def update_shared_resource(value):
    # Update shared resource here
    pass
```

The `decorator` function is needed as an intermediary to correctly bind the `lock` argument. Without it, you wouldn't be able to pass the `lock` to the `wrapper` because the decorator syntax `@` expects a function that takes a single function as an argument and returns a callable. You need that extra step to create a closure that captures the `lock` and uses it in the `wrapper`.

If you didn't need to pass any arguments to your decorator, you could skip the factory part and define a decorator that directly returns the wrapper function. But since you want to pass the `lock`, you need this extra `decorator` function.

You can also do so much more. You can abstract away the boilerplate code needed to set up parallel execution:

```python
from multiprocessing import Pool
from functools import wraps

def parallelize(function):
    @wraps(function)
    def wrapper(*args, **kwargs):
        with Pool(processes=4) as pool:
            return pool.map(function, args[0])  # Assuming the first argument is iterable.
    return wrapper

@parallelize
def compute_square(numbers):
    return [number ** 2 for number in numbers]

# When called, this will execute in parallel.
squares = compute_square(range(10))
```

You can cache results using Python’s ``from functools import lru_cache``. 

You may also time an execution using decorators:

```python
import time

def timeit(function):
    @wraps(function)
    def wrapper(*args, **kwargs):
        start_time = time.time()
        result = function(*args, **kwargs)
        end_time = time.time()
        print(f"Function {function.__name__} took {end_time - start_time} seconds")
        return result
    return wrapper

@timeit
def parallel_function(data):
    # Code that runs in parallel
    pass
```

That's it for this post. Hope it was useful for some people!