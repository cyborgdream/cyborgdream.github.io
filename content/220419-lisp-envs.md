+++
title = "Instaling Lisp environments and not having fun"
description = "26/??? I found a book that understands me"
date = 2022-04-19
draft = false
slug = "lisp-envs"

[taxonomies]
categories = ["journal", "tutorial"]
tags = ["learning", "lisp"]

[extra]
comments = true
+++

# Building abstractions with Data

Can I learn this while having skipped a huge part of chapter 1 and not resolved a single exercise?

Waait a second! They're using a specific language here. WELL, no one told me what to use so I think I'll just set up Clojure.

Seems like your editor is a huge part of Clojure. I don't think Sublime in its refined simplicity will be able to do this fancy stuff... Let's see, their thing is called REPL (read eval print loop), a friend showed me the concept and honestly, I really liked it. I bet is very CPU intensive though and can't really understand how complex systems would keep this going... But maybe, the whole point of Lisp is that it never gets complex? That'd be very magical though.

Wow, this little tool is so powerful, so here's what you should do to get it running on your Sublime:

#### Installing REPL in Sublime 4

1. Have Sublime 4
2. Install the Clojure-Sublimed package
    1.  `Package Control: Install Package` → `Clojure Sublimed`
    2.  Assign syntax to Clojure files:
        -   open any clj/cljc/cljs file,
        -   run `View` → `Syntax` → `Open all with current extension as...` → `Clojure Sublimed` → `Clojure (Sublimed)`.
3. Configure a nREPL server:
    1. Add the following to your `~/.clojure/deps.edn`:
    ```clojure
:aliases {:nREPL
          {:extra-deps
            {nrepl/nrepl {:mvn/version "0.9.0"}}}}
```
You can run it like this:
```shell
clj -M:nREPL -m nrepl.cmdline
```
4. Go back to your sublime, preference pannels and:
`Clojure Sublimed: Connect`
Copy the port you see running on your Shell.

And general commands can be found on this [Readme](https://github.com/tonsky/Clojure-Sublimed#how-to-use).

# Some weird clojure things

Most REPL environments support a few tricks to help with interactive use. For example, some special symbols remember the results of evaluating the last three expressions:

-   `*1` (the last result)
-   `*2` (the result two expressions ago)
-   `*3` (the result three expressions ago)

```
user=> (+ 3 4)
7
user=> (+ 10 *1)
17
user=> (+ *1 *2)
24
```

```
(require '[clojure.repl :refer :all])
```

Makes a bunch of these incantations available.

Not sure what something is called? You can use the `apropos` command to find functions that match a particular string or regular expression.

```
user=> (apropos "+")
(clojure.core/+ clojure.core/+')
```

And then to look it up in the docs:

```
user=> (doc doc)

clojure.repl/doc
([name])
Macro
  Prints documentation for a var or special form given its name
```

Okay, that's pretty cool.

Huh, it seems like the book doesn't really translate to Clojure, that's annoying. I might just change to "Common Lisp":

Damn, this is really a technology of the past. All this Lisp installation party mined my interest A LOT. I'll watch the lessons to see if it helps.

Also, I gave up Common Lisp and will use DrRacket now, seems like it's what everyone uses anyway, I shouldn't have tried to get fancy.

The lessons are SOO BORING, god help me.