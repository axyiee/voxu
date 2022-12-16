## Syntax and semantics

- [Expressions and statements](#expressions-and-statements)
- [Variables](#variables)
- [Function composition and operators](#function-composition-and-operators)
- [Components](#components)
- [Control flow](#control-flow)
- [Pattern matching](#pattern-matching)
- [Type inference](#type-inference)
- [Data structures](#data-structures)
- [Traits and implementations](#traits-and-implementations)
- [Enumerations and algebraic data types](#enumerations-and-algebraic-data-types)

### Expressions and statements

Voxu is mostly a expression-oriented language, which means that most of the time, the programmer would be
writing expressions instead of statements. This is a very important feature of the language, as it allows
the programmer to write code that is more concise and readable.

A statement is a unit of code that performs an an action, which doesn't produce an value directly. You can
think of it as an instruction to the compiler. The most common statements in Voxu are:

- `[expression]`**`;`**
- Let (variable or function definition) statement
- Macro invocations

An expression statement ignores the value produced by the expression. To differ expressions from expression
statements, the expression statement is terminated by a semicolon (`;`).

Expressions are units of code that produce a value. The most common expressions in Voxu are:

- Literals
- Function and macro invocations
- Variable or field access
- Array and tuple indexing
- Binary and unary operators
- Parenthesized expressions
- Anonymous functions
- If-else expressions

An expression can be wrapped in parentheses to increase readability and isolate its context,
or be wrapped in curly braces to create a block expression, which can receive statements before
the expression itself.

```rust
let x = 5; // statement, 5 is an expression
let y = { // expanded expression
    let z = 10; // this is a statement
    hello(); // also a statement
    z + x // result expression
};
return 2; // also an expression because produces a value
```

A statement may be considered a expression when the return type of the scope is `()` (unit),
which can also be a definition of an empty tuple.

### Variables

A variable is a component used to store data in memory, either on the _stack_ or on the _heap_.

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

### Function composition and operators

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

### Components

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

### Control flow

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

### Pattern matching

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

### Type inference

Voxu's type inference is intended to be smart, and to be able to infer the type of a variable based
on the context.

### Data structures

You can think of data structure as tuples with named fields. They are defined using the `data` keyword
and its fields are immutable by default. You can make a field mutable by using the `mut` keyword.

```rust
data Point (x: f32, mut y: f32?)
```

Voxu code _pascal case_ as the conventional naming style for data structures. So, we can say a regular
expression for data structure definitions could be represented as:
`data [A-Z][a-Z0-9_]* \(((mut)? [a-Z_][a-Z0-9_]* (: [a-Z_][a-Z0-9_]*)?,?)*\)`.

### Traits and implementations

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
