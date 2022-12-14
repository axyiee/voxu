# 🧊 voxu

A strongly and statically typed, memory-efficient functional programming language.

This is the repository for the compiler, lexical analyzer and parser, and the standard library
for the Voxu programming language. It is worth noting the language itself isn't even near
completion for now, so it is all right to write everything in a different language and then
migrate it to Voxu once it is ready.

It's fair to tell newcorners about the fundamentals, concepts, design decisions, and of course,
the purpose and semantics of Voxu, while it is still in its early stage.

For that reason, this document is written in a way that is easy to understand, and is intended
to become a reference or formal specification of the language as it evolves.

As this is still in its first moments, I would like to hear your feedback and suggestions,
so feel free to contact me at:

- [Discord](https://discord.com/users/273562710745284628)
- [The issues of this repository](https://code.axyria.dev/voxu/issues)

At long last, Voxu is still my first programming language ever, so I'd appreciate any help or
advice anyone can give me, as I love to learn new concepts and listen to feedback.

## 📖 Table of Contents

- [Memory efficiency](#memory-efficiency)
- [Strong and static typing](#strong-and-static-typing)
- [Functional programming](#functional-programming)
- [Standard library](#standard-library)
- [Macros](#macros)
- [The compiler](#the-compiler)
- [Syntax and semantics](#syntax-and-semantics)
- - [Expressions and statements](#expressions-and-statements)
- - [Variables](#variables)
- - [Function composition and operators](#function-composition-and-operators)
- - [Components](#components)
- - [Control flow](#control-flow)
- - [Pattern matching](#pattern-matching)
- - [Type inference](#type-inference)
- - [Data structures](#data-structures)
- - [Traits and implementations](#traits-and-implementations)
- - [Enumerations and algebraic data types](#enumerations-and-algebraic-data-types)

### Memory efficiency

Voxu is designed to be as memory efficient as possible, in other words, it must be able to
allocate as little memory as possible — just the amount of memory needed to store data —
and free it as soon it isn't necessary anymore.

This of course includes a memory model, which is a set of rules that define how memory is
allocated and freed, and how it is accessed.

First of all, it is important to understand how memory management works in software, and how
it instructs the computer to allocate and free memory.

It is interesting to understand the difference between physical and virtual memory first.
Physical memory is the actual memory chips that are installed in the computer, and it is what
the computer uses to store data.

Each operating system kernel has its implementation of a **virtual address space (VAS)** per
process, which allows the process to access virtual _or logic_ memory.

The operating system is also responsible for _address binding_ (also called address translation),
which is the process of mapping virtual addresses to physical addresses. To first understand how
address binding works, we need to look for another concept called **page table**.

Each VAS has a page table. Each entry in the page table is called a **page table entry (PTE)**, and
it contains information indicating where a memory page is located in physical memory. Address binding
works at the hardware level by communicating with a component called Memory Management Unit (MMU),
which is located in the CPU.

When we need to access or write to a memory location, the CPU sends an instruction to query the MMU
for the physical address of that memory location. The MMU then looks for the page table entry in the
page table and returns a section containing the physical page number and the page offset.

To finally access the complete physical address, the physical page number needs to be combined with
the page offset. The CPU also prefers to query the Translation Lookaside Buffer (TLB) after the MMU
has returned the physical address if applicable.

The TLB consists of a portion of the page table cached inside the MMU chip, and it is used to speed up
address binding. The original page table is stored in the main memory, and it is consulted by MMU if
the page is not found in the TLB. The point of TLB is to avoid consulting the main memory when possible
every time a new virtual address is mapped.

The purpose of Voxu is to allow the programmer to write safe code that is as efficient as possible while
allowing the user to do unsafe things if they want to.

Voxu is designed to be a low-level language, and it is not intended to be used as a high-level language.
It is intended to follow a memory ownership model similar to [Rust's].

It is worth noting that Voxu is not a garbage collected language, and it is not intended to be one. It is
cool to note as well that as seen in other languages, abstractions of stack and heap memory are implemented
in Voxu.

[rust's]: https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html

**Sources**:

- [Memory Management in Computer System, Mohammad M Rahman (2022)](https://www.researchgate.net/publication/361691619_Memory_Management_in_Computer_System)

### Strong and static typing

Voxu is a strongly and statically typed language, which means that the type of a variable is known
at compile time, and it cannot be changed at runtime.

This is a very important feature of the language, as it allows the compiler to perform type checking
and type inference, which are very important for the safety of the language.

### Functional programming

Voxu is a functional programming language. This means it is designed to follow that specific programming
paradigm, which is a programming style that treats computation as the evaluation of mathematical functions
and avoids changing-state and mutable data. It is a declarative programming paradigm, which means programming
is done with expressions or declarations instead of statements. This doesn't mean Voxu wouldn't partially
support imperative programming. It'd be implemented ad-hoc, not being the focus of the language.

This also means that the language is designed to support the most common functional programming concepts,
built-in into the language, such as curried functions, higher-order functions, and lazy evaluation.

### Standard library

Voxu's standard library is designed to be lightweight and memory efficient, while still being powerful and
allowing users to have a great experience while using the language. This means the standard library would be
big enough to be useful, but small enough to be lightweight.

### Macros

Voxu supports macros, which are a way to define custom syntax and semantics for the language. Macros are
very powerful, and they are used to implement some standard library features.

### The compiler

Voxu is a compiled language to native code through LLVM. The focus of this compiler is to be fast, and to
tell the programmer exactly what is wrong with their code, and where it is.

### Syntax and semantics

#### Expressions and statements

Voxu is mostly a expression-oriented language, which means that most of the time, the programmer would be
writing expressions instead of statements. This is a very important feature of the language, as it allows
the programmer to write code that is more concise and readable.

Statements are used to define variables, functions, components, etc. In other words, a statement is
everything that doesn't produce a value to the returned value.

An expression is everything that produces a value to the returned value. This includes function calls,
variable access, arithmetic operations, and so on. An expression can easily be assigned as a code block
wrapped in curly braces, in case you need to write a statement before returning a value, or simply a
direct value. For example:

```rust
let x = 5; // expression
let y = { // expanded expression
    let z = 10; // this is a statement
    hello(); // this is also a statement because it isn't being returned
    z + 5
};
```

A statement may be considered a expression when the return type is `()` (unit), which can also be a
definition of an empty tuple.

#### Variables

Variables are used to store data in memory, essentially in the stack, they are a way to give a
name to a memory location. They are defined using the `let` keyword, and they are immutable by
default. This means that once a variable is defined, it cannot be changed. To change a variable,
you need to use the `mut` keyword, which makes the variable mutable. For example:

```rust
let x = 5; // immutable variable
let mut y = 10; // mutable variable
let x: u8 = 5; // immutable variable with explicit type annotation
```

Voxu code _snake case_ as the conventional naming style for variables. That said, a regular expression
to `let` definitions can be represented as:
`let (mut)? [a-Z_][a-Z0-9_]* (: [a-Z_][a-Z0-9_]*)? = [expression];`.

#### Function composition and operators

Functions are a way to define a block of code that can be called from anywhere in the program,
as long as it is available in the scope with the correct visibility. Functions are defined using
the `let` keyword, and you can define parameters by wrapping them in parentheses. The return type
is simply defined after the parameters, and it is required. For example:

```rust
let add (x: u32, y: u32) string =
    x + y
```

The concept of a 'operator' in Voxu is a bit different from most programming language, while being
pretty similar to Scala functions.

Calling functions with dot notation (.) as you can see in other languages is supported, but you can
use a space instead and omit parentheses if the function has a single parameter.

Operators like `!` in booleans are defined by using the `prefix` keyword:

```rust
prefix let ! (x: bool) bool =
    if x then false else true

let x = !true
```

Voxu code _snake case_ as the conventional naming style for functions. So, we can say a regular
expression for function definitions could be represented as:
`(prefix)? let [a-Z_][a-Z0-9_]* (\([a-Z_][a-Z0-9_]* (: [a-Z_][a-Z0-9_]*)?\))? (: [a-Z_][a-Z0-9_]*)? = [expression];`.

#### Components

Components are essentially the same as the concept of modules seen in other programming languages.
Each module is defined in a file, and you can move the logic to another folder naming the file as
`component.vx`. You can import third-party components using the `use` keyword:

```rust
use std::io
use std::fmt
use std::{io, fmt}
```

Voxu code _snake case_ as the conventional naming style for components and files. So, we can say
a regular expression for component importing definitions could be represented as:
`use [a-Z_][a-Z0-9_]*(::[a-Z_][a-Z0-9_]*)*;`.

#### Control flow

Voxu supports the most common control flow structures, such as `if` and `else`, which are pretty
much the same as in other languages.

```rust
if x == 5 {
    // do something
} else if x == 10 {
    // do something else
} else {
    // do something else
}
```

So, we can say a regular expression for `if` statements could be represented as:
`if [expression] \{ [expression] \} (else if [expression] \{ [expression] \})* (else \{ [expression] \})?`.

#### Pattern matching

Pattern matching is a way to match a value against a pattern, and execute code based on the result.
It is a very powerful feature, and it is used in many places in the standard library. You can think
of Voxu's pattern matching implementation as a somewhat similar to Scala's and ReasonML's pattern
matching.

```rust
let x = 5
match x {
|    case 10 => "ten"
|    if > 2 => "greater than two"
|    if < 0 =>
        fmt::println!("negative number")
        "negative"
|    case _ => "other"
}
```

So, we can say a regular expression for `match` statements could be represented as:
`match [expression] \{ (\| case [expression] => [expression]) | (\| if [expression] => [expression])* \}`.

#### Type inference

Voxu's type inference is intended to be smart, and to be able to infer the type of a variable based
on the context.

#### Data structures

You can think of data structure as tuples with named fields. They are defined using the `data` keyword
and its fields are immutable by default. You can make a field mutable by using the `mut` keyword.

```rust
data Point (x: f32, mut y: f32?)
```

Voxu code _pascal case_ as the conventional naming style for data structures. So, we can say a regular
expression for data structure definitions could be represented as:
`data [A-Z][a-Z0-9_]* \(((mut)? [a-Z_][a-Z0-9_]* (: [a-Z_][a-Z0-9_]*)?,?)*\)`.

#### Traits and implementations

Traits are a way to define a set of methods that can be implemented by a type. They are defined using
the `trait` keyword, and they can be implemented by a type using the `impl` keyword.

Implementations can also be defined for a data structure independently from any traits, also using the
`impl` keyword.

```rust
trait Add =
    let hi: Self;
    let add (&self, other: &Self) Self;

impl Add for u32 =
    let hi = 10;
    let add (&self, other: &Self) Self =
        self + other

data Point (x: f32, y: f32)

impl Point =
    let hello (&self) string = "hello"
```

Voxu code _pascal case_ as the conventional naming style for traits. So, we can say a regular expression
for trait definitions could be represented as:

```regex
trait [A-Z][a-Z0-9_]* \{
    (let [a-Z_][a-Z0-9_]* (&self, [a-Z_][a-Z0-9_]*: &[A-Z][a-Z0-9_]*) [A-Z][a-Z0-9_]*;)*
    (let [a-Z_][a-Z0-9_]*: [A-Z][a-Z0-9_]*;)*
\}
```

### Enumerations and algebraic data types

Enumerations are a way to define a type that can be one of a set of variants. They are defined using the
`enum` keyword, and they can also have type class implementations (traits) and an indepent implementation.

```rust
enum Option[T] =
    | Some (value: T)
    | None

enum Hello (message: String)
    | Error: "An error occurred"
    | Success: "Success"
    | Other(message_: String): message_
```

Voxu code _pascal case_ as the conventional naming style for enumerations. So, we can say a regular expression
for enumeration definitions could be represented as:

```regex
enum [A-Z][a-z0-9_]_ (\[([a-Z\_]a-z0-9_]_)?,?)\])? \(([a-Z\_]a-z0-9_]_ (: [a-Z\_]a-z0-9_]_)?,?)_\)? \{
case [a-Z\_]a-z0-9_]_ \(?(([a-Z\_]a-z0-9_]_ (: [a-Z\_]a-z0-9_]_)?,?)_\)?\)?;? (: [expression]*)?
\}
```
