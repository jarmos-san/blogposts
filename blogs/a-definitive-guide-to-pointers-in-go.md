---
title: "Understanding Pointers in Go: A Practical Guide"
description:
  Learn how pointers work in Go with clear explanations and practical examples.
  This guide covers pointer types, memory addresses, dereferencing and why
  pointers matter for writing efficient and reliable Go programs.
publishedOn: 2026-02-25 13:34:03
status: published
coverImage:
  url: https://ik.imagekit.io/jarmos/a-definitive-guide-to-pointers-in-go.png
  alt: A Definitive Guide to Pointers in Go
sitemap:
  loc: /a-definitive-guide-to-pointers-in-go
  lastmod: 2026-02-25 13:34:03
  changefreq: yearly
  priority: 1
---

If you are finding it difficult to grasp the concept of pointers in Go, you are
not alone. Having worked primarily with interpreted languages like Python which
do not expose pointers as part of the language model, I can relate. However,
because Go is a systems oriented language, understanding pointers is essential
for writing performant, memory efficient and predictable software. As a Go
developer, this is not a topic you can safely gloss over.

In this article, I will clarify what pointers are in Go and how to use them
correctly.

## Pointers Are a Type of Variable

The official Go tutorial, "A Tour of Go" defines pointers as follows
([source](https://go.dev/tour/moretypes/1)):

> "Go has pointers. A pointer holds the memory address of a value".

This definition is concise and accurate. A pointer is a variable whose value is
a memory address.

To restate this more explicitly: a pointer does not store the value itself; it
stores the address in memory where that value resides (for example, something
like `0x7ffee4b8c9a0`).

The size of a pointer depends of the system architecture, so on a 32-bit system,
a pointer is 4 bytes and on a 64-bit system, a pointer is a 8 bytes in size.

Since pointers are small (just an address), they allow us to pass and manipulate
large data structures efficiently without copying the entire value. Instead of
duplicating data, we can operate on the original value via its address.

## Declaring and Using Pointers

Go provides two primary operators for working with pointers - the `*` (asterisk)
and the `&` (ampersand), each serving a different purpose depending on the
context.

To declare a pointer type, we use the form `*T` represents "pointer to a value
of type `T`" as described in the example code snippet below:

```go
package main

import "fmt"

func main() {
    var p *int
    fmt.Println(p) // should print <nil>
}
```

In the code above, `p` is declared as a pointer to an `int` and its default
value is `nil` since in Go, an uninitialised pointer is always `nil`.

The `&` operator returns the memory address of a variable as shown in the
example below:

```go
package main

import "fmt"

func main() {
    msg := "Hello World!"
    fmt.Println(&msg) // prints a memory address (e.g., 0x239a5e620070)
}
```

In the code snippet above, the `msg` variable stores the string `"Hello World!"`
and `&msg` returns the address in memory where that string value is stored. The
exact address will vary each time the program is run.

When used with a pointer value, the `*` operator dereferences the pointer.
Dereferencing means accessing the value stored at the memory address the pointer
holds.

```go
package main

import "fmt"

func main() {
    msg := "Hello World!"
    ptr := &msg       // ptr stores the address of msg
    fmt.Println(*ptr) // prints "Hello World!"
}
```

In the example above, `msg` is initialised with a string value of
`Hello World!`. Thereafter, the `ptr` variable stores the address of `msg` and
then `*ptr` accesses (dereferences) the value at that address.

Note the differences between `ptr` and `*ptr` where one holds the address and
the other holds the value at that address.

## Why Pointers Matter in Go

As you can see, pointers play a significant role in Go. They allow developers to
write performant software in ways that may not be as straightforward in
languages like Python. So, what are some real-world use cases of pointers?

For starters, pointers allow us to load large amounts of data (think a few MBs
or more) and manipulate them without copying them across various sections of the
program. Failing to do so may lead to increased memory usage, since the large
data may not only be loaded into memory but also duplicated multiple times
during processing.

Go was built from the ground up to handle concurrent applications (e.g., web
servers). Sharing the state of data and safely modifying it without corruption
or race conditions is of utmost importance in such environments. Go pointers,
combined with mutex locks, allow us to do exactly that with ease. As a side
note, I will discuss writing concurrent programs in Go in a future article.

As discussed earlier, pointers in Go are initialized to `nil`, and we can always
check for the `nil` value before processing data. This particular safety
mechanism is often missing or handled differently in other languages and can
potentially lead to disastrous situations. If you are interested in learning
more about the broader context, check out the presentation
"[Null References: The Billion Dollar Mistake](https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare)"
by Tony Hoare, who introduced the idea of null references.

Go also does not support pointer arithmetic (unlike C), which makes pointer
usage safer and more predictable. You can hold an address and dereference it,
but it is not possible to manually offset it.

## Final Thoughts

Pointers in Go are not inherently complex, as they are conceptually just
variables that store memory addresses. The difficulty lies in understanding when
and why to use them, not in their syntax.

Remember these three rules:

1. `*T` defines a pointer to type `T`.
2. `&value` provides the address of a value.
3. `*pointer` retrieves the value stored at an address.

Once this mental model is internalized, pointers will feel like a natural
extension of Go's type system rather than an intimidating concept.

Pointers are a powerful feature of the language, but improper usage can lead to
buggy and unexpected behavior. Unless specifically required, refrain from
mutating data through pointers.

Most of the time, Go code is sufficiently fast, and its Garbage Collector (GC)
will clean up unused allocations. Using pointers solely to squeeze out marginal
performance gains often provides little benefit.

Instead, perform proper benchmarking and profiling to identify performance
bottlenecks before refactoring. Pointers should be used primarily when mutation
of shared state is required, while immutability and passing by value should
generally be preferred.
