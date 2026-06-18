Go is a language that favours simplicity, but many of its design decisions can
initially feel unusual. Especially if you are coming languages with explicit
inheritance models or dynamically typed languages such as Python, you are bound
to feel confused with the language.

One particular idiom caught me off guard when I first encountered it:

```go
var _ io.Reader = (*File)(nil)
```

If you've spent any amount of time reading Go code, there's a good chance you've
come across this line, or some variation of it. It's especially common in
libraries, generated code, and large codebases.

At first glance, the syntax is puzzling. Why are we assigning to `_`? Why is a
`nil` pointer involved? Is this creating a variable? Is `(*File)` a type
conversion or a dereference operation?

As it turns out, this seemingly cryptic line is a deliberate compile-time safety
mechanism used as a convention among Go developers to verify if a type satisfies
an interface.

In this article, we'll break down exactly how this pattern works, why it exists,
and how understanding it provides deeper insight into Go's type system and its
philosophy of implicit interfaces.

## A Quick Refresher on Go Interfaces

Unlike many object-oriented languages, Go does not require types to explicitly
declare an implementation of an interface.

For example, in TypeScript you might write:

```typescript
interface Reader {
    ...
}

class File implements Reader {
    ...
}
```

Go takes a different approach.

Suppose we define a `Reader` interface and a `File` type:

```go
type Reader interface {
    Read([]byte) (int, error)
}

type File struct{}

func (f *File) Read(b []byte) (int, error) {
    return 0, nil
}
```

Then, at no point do we explicitly tell Go to implement `File` as a concrete
type of `Reader`.

Instead, Go is designed to use what is known as **structural typing**. If a
type's method set contains all the methods required by an interface, the type
automatically satisfies that interface.

The compiler performs this check whenever the type is used in a context which
expects that interface.

For example:

```go
var r Reader

r = &File{} // Valid
```

In the example above, if you compiled the code, the compiler asks a simple
question:

> Does `*File` implement every method required by `Reader`?

If the answer is yes and the assignment is allowed. If not, compilation fails.

This implicit implementation model is one of Go's defining characteristics. It
keeps interfaces decoupled from their implementations and allows interfaces to
be defined by the code consuming them rather than the code providing them.

Understanding this behaviour is important because the mysterious line we're
investigating later in this article is simply another way of forcing the
compiler to perform this exact same interface check.

## Breaking Down Compile-Time Interface Assertions

Now that we understand how Go interfaces work, let's revisit the line that
brought us here in the first place:

```go
var _ io.Reader = (*File)(nil)
```

At first glance, this syntax appears cryptic. However, it is simply leveraging
existing Go language features in a clever way to force the compiler to verify if
`*File` satisfies the `io.Reader` interface.

Let's break it down piece by piece.

### The Right-Hand Side: `(*File)(nil)`

This is often the most confusing part.

Contrary to what many developers initially assume, `(*File)` is **not
dereferencing** anything. Instead, this is a type conversion expression.

The syntax: `(Type)(value)` is used throughout Go to convert values from one
type to another.

For example:

```go
type MyInt int

n := MyInt(42)
```

The same concept applies for `(*File)(nil)` which simply means:

> Convert `nil` into a value of type `*File`.

This does not allocate any memory and does not create a `File` instance.

It is conceptually equivalent to:

```go
var f *File = nil
```

The actual value is irrelevant. We only care about the type.

You may also be wondering why the syntax is written as `(*File)(nil)` instead of
simply `*File(nil)`.

The answer lies in Go's parsing rules for type conversions.

In Go, type conversions follow the `Type(value)` syntax. This works without
additional parentheses for simple type names such as `int(42)` or
`MyType(value)`. However, `*File` is not a simple type name but a pointer type
expression. Wrapping it in parentheses tells the compiler to treat the entire
`*File` expression as the target type of the conversion. Without the
parentheses, `*File(nil)` would be parsed differently and would no longer
represent "convert `nil` to a `*File`".

In other words, `(*File)(nil)` is not special syntax invented for compile-time
interface assertions; it is simply the standard Go type conversion syntax
applied to a pointer type.

### The Interface Type: `io.Reader`

The left-hand side declares a variable of type `io.Reader`.

```go
var _ io.Reader
```

By itself, this is unremarkable. However, assigning `(*File)(nil)` to it
triggers Go's normal interface compatibility checks.

The compiler now asks:

> Can a value of type `*File` be assigned to a variable of type `io.Reader`?

This is the exact same check that would occur here:

```go
var r io.Reader

r = &File{}
```

The difference is in our intention to force the compiler to check the
implementtation without needing to create a real variable to be used later.

### The Blank Identifier: `_`

The final piece of the puzzle is the blank identifier.

In Go, `_` discards values, so in the following statement;

```go
result, _ := someFunction()
```

Anything assigned to `_` is immediately ignored and garbage-collected by the
compiler.

By writing:

```go
var _ io.Reader = (*File)(nil)
```

we are effectively telling the compiler:

> Verify that `*File` implements `io.Reader`, then throw everything away.

No variable is created for later use and no runtime behaviour is introduced. The
statement exists solely to perform a compile-time check.

### Putting It All Together

When the compiler encounters:

```go
var _ io.Reader = (*File)(nil)
```

it internally reasons about it as:

> Can a value of type \*File be assigned to a variable of type io.Reader?

If the answer is yes, compilation succeeds. If the answer is no, compilation
fails immediately.

As an example, the following concrete type which does not have the full method
set of an interface like this:

```go
type File struct{}
```

would produce an error similar to:

> \*File does not implement io.Reader (missing method Read)

This pattern does not introduce any special language behaviour. The Go lexicon
has no `implements` keyword or dedicated compiler feature involved to check for
compile-time interface assertions. Instead, compile-time interface assertions
are simply a clever application of Go's existing assignment and interface
satisfaction rules developed and _de facto_ standardised by the community.

## Why Do We Need This Pattern?

At this point, a natural question arises:

> If Go already checks whether a type satisfies an interface, why do we need to
> explicitly write compile-time interface assertions at all?

After all, the compiler already performs this check whenever a type is assigned
to an interface.

```go
var r io.Reader

r = &File{}
```

If `*File` does not implement `io.Reader`, compilation will fail anyway.

So why introduce another line of code?

The answer is that, the compiler only performs interface satisfaction checks
**when a type is used in a context expecting that interface**.

Consider the following example:

```go
type Storage interface {
    Save([]byte) error
    Load() ([]byte, error)
}

type FileStorage struct{}

func (f *FileStorage) Save(data []byte) error {
    return nil
}

func (f *FileStorage) Load() ([]byte, error) {
    return nil, nil
}
```

At this point, `*FileStorage` implicitly satisfies the `Storage` interface.
However, nowhere in the package are we explicitly assigning `*FileStorage` to a
variable of type `Storage`. This means the compiler has no reason to verify the
relationship yet.

Now imagine a future refactor.

```go
func (f *FileStorage) Load() ([]byte, error, bool) {
    return nil, nil, false
}
```

Now `FileStorage` no longer satisfies `Storage` but without a compile-time
interface assertion, this may not be discovered until another package attempts
to use it as a `Storage` . For example, here’s a statement which defines a
variable `s` of type `Storage` which stores an instance of the `FileStorage`
type.

```go
var s Storage = &FileStorage{}
```

Depending on the size of the codebase, this usage may be located far away from
the implementation itself which is often the case in a medium to large-scale
codebase.

Compile-time interface assertions move that verification closer to where it
matters.

```go
var _ Storage = (*FileStorage)(nil)
```

Now the package itself declares an expectation:

> `*FileStorage` is intended to satisfy `Storage`.

If the contract is accidentally ever broken during a refactor, compilation fails
giving the developer ample headroom to decide the next course of action without
introducing breaking changes.

This is especially valuable in larger codebases where interfaces and their
implementations often live in separate packages.

### It Also Serves as Documentation

Compile-time interface assertions are not only a safety mechanism, they are also
a form of executable documentation.

When another developer encounters:

```go
var _ io.Reader = (*File)(nil)
var _ io.Writer = (*File)(nil)
var _ io.Closer = (*File)(nil)
```

they can immediately infer the intended capabilities of the type (`*File` in the
current context) without searching through the entire codebase.

It clearly communicates:

> `File` is expected to behave as a reader, writer, and closer.

### This Is Why Generated Code Uses It So Frequently

Tools such as `oapi-codegen`, `sqlc`, and various mocking or RPC generators
often emit compile-time interface assertions.

Generated code typically establishes contracts between generated interfaces and
developer-provided implementations. By adding these assertions, generated code
can immediately detect when an implementation has drifted away from the expected
contract. Instead of discovering the issue later, developers receive a
compile-time error as soon as the interface relationship is broken.

Ultimately, compile-time interface assertions are not introducing a new
functionality to the language. They are a deliberate way of making an implicit
relationship explicit, and ensuring that relationship remains intact over time.
