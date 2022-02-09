+++
title = "C++ classes"
description = "18/??? What do different books have to say about classes"
date = 2022-02-08
draft = false
slug = "cpp-classes"

[taxonomies]
categories = ["journal"]
tags = ["c++", "rust"]

[extra]
comments = true
+++

So here we go, just so I don't forget again, this is what I'm running for my Rusty browser:

`cargo run http://example.org/index.html`

I should go through this: https://aminb.gitbooks.io/rust-for-c/content/borrowed/index.html

But not right now, because right now I'm going to read into C++ stuff and try to remember/learn some things about it. Starting with:

### Classes

```
class Date {
    ...
    Date (int, int, int);
}

Date today = Date(30, 6, 1999);
Date tomorrow(23, 3, 1999);
```

It's often nice to provide several ways of initializing a class obj.

```
class Date {
    int d, m, y;
    public:
    Date(int, int, int);
    Date(int, int);
    Date();
    Date(const char*); //date represented as a string
}
```

Constructors obey the same overloading rules as functions.

```
class Date {
    int d, m, y;
    static Date default_date;
public:
    Date(int dd=0, int mm=0, int yy=0);
    static void set_default(int dd, int mm, int yy);

    Date::Date(int dd, int mm, int yy)
    {
        d = dd ? dd : today.d;
        ...
        //checking that date is valid
    }
}
```

`const` after the empty argument list indicate that this function do not modifiy the state of a date. So, an example of a method like this:

```
inline int Date::year() const
{
    return y;
}
```

Returning a bunch of `*this` in class methods allow us to chain methods:

```
Date& Date::add_year(int n)
{
    ...
    return *this;
}

//repeat for day and month

Date& d;
d.add_day(1).add_month(2)..add_year(3);
```

Using `const_cast<>` will cast away your `const`, but seems like it's not a good idea in general.

I never remember the syntax to overload operators, so here it is:

```
inline bool operator==(Date a, Date b)
{
    return a.day() == b.day && a.month() == b.month() && a.year() == b.year();
}
```

Most classes have a default constructor. (When they don't need arguments).

Local static objects are destroyed at the same time as a global one.

When you do something like the following:

```
Table t1;
Table t2 = t1;
Table t3;
t3 = t2;
```

The constructor here is called 2x, for t1 and t3, but the destructor is called 3x, for all the vars. This behavior is undefined. To fix it we can do initialization like:

```
class Table 
{
    Table (const Table&); //copy constructor
    Table& operator = (const Table&); //copy assignment
}
```

There's a block about the impl, but I don't really get it to be honest. They're initalizing things weirdly and using the `delete` keyword to erase things manually?

```
class Club {
    string name;
    Table members;
    Date date;
    Club(const string& n, Date d);

    Club::Club(const string& n, Date d)
        : name(n), members(), date(d)
        {
            ...
        }
}
```

If a member constructor needs no arguments, the member doesn't need to be mentioned in the initializer.

For types which initialization differs from assignment (that is without default contructors), it's necessary to initialize:

```
class X
{
    const int i;
    Club& c;

    X(int ii, constr string& n, Club& c) : i(ii), c(n, d) {}
}
```

To create a pointer to a class... Always initialize it!


```
const int Curious::c1;
const int* p = &Curious::c1;
```

To add have manager to be added in a list of employees:

```
struct Manager : public Employee
{
    list<Employee*> group;
    ...
}
```

A derived class can always be assinged to it's base. But never the other way around:

```
Employee* pe = &manager;
Manager* pm = &employee;
```

A member of a derived class can use the public and protectd members of is base class. However, it can't use private names.

First the derived class is destroyed, then the members, then the base.

If you have vars that have to be initialized in the base, you need to call the base's constructor on the derived class:

```

Manager::Manager(const string& n, int d)
        : Employee (n , d) , // initialize base
```

I have to develop my own copy assignment operators because otherwise the compiler will create it (and that might be incomplete).

To get polymorphic behavior in C++ the member functions called must be virtual and objs must be manipulated through pointers or references.

Abstract class when all methods are virtual. Classes that aren't "real things" like a `Shape` class. It's merely an interface for other classes and no object can be created.

When you're building abstract classes you should assume the class requires cleanup. So you have to define a virtual destructur in the base class:

```
Ival_box::~Ival_box()
```

And in the derived class you get rid of the the base class stuff explicitly:

```
    void f(Ival_box* p)
    ...
    delete p;
```

On the note of static things, to declare variables as static inside of a class can greatly contribute for code readability and space saving.
Class static data members have class scope. To access it you need to add the scope resolution operator:

```
Martians::martianCount
```

The plan is to go through some of these tomorrow:

- refresh my pointers perspective

- c++ concurrency in action 1 through 7? or 8? or less?

- chapter 22, 23, 24 of elements of programming interviews

- read proxy classes 10.8 on deitel to reinforce the "separate interface from implementation and hide the implementation details" vibe

- memory concepts deitel, there are probably things I forgot about it

- pointer to void bjarne page 100, I'm curious about intrinsics

    - maybe structures page 101
