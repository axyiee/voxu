## Functional programming

- [Immutability and purity](#immutability-and-purity)
- [Pattern matching](#pattern-matching)
- [Categories, functors, and endofunctors](#categories-functors-and-endofunctors)
- [Monoids, monads, and identity](#monoids-monads-and-identity)
- [Error handling](#error-handling)
- [Curried functions](#curried-functions)

Voxu is a functional programming language. This means it is designed to follow that specific programming
paradigm, which is a programming style that treats computation as the evaluation of mathematical functions
and avoids changing-state - side effects - and mutable data. It is a declarative programming paradigm, which
means programming is done with expressions or declarations instead of statements. This doesn't mean Voxu
wouldn't partially support imperative programming. It'd be implemented ad-hoc, not being the focus of the
language.

You can also think of functional code as mathematical code. It's a way of thinking about code that is
combining pure functions together is like combining a series of algebraic equations. The essence of functional
programming is indeed being declarative; you feel like you're describing what you want to happen, rather than
how it should happen, and the language takes care of the details. It is also worth noting pure functions
always should return values: void/unit, or any other type that isn't known as "exceptions" or "null" values
in other programming languages. If you want a value to be either a value or nothing, you can use a `Maybe`
monad. I'll be soon covering what is a pure function, what is a monad, and other important FP concepts.

This also means that the language is designed to support the most common functional programming concepts
built-in into the language, such as curried functions, higher-order functions, lazy evaluation, "declarativeness",
pure functions, and so on. It also means that the language is designed to be as much as possible immutable.

## Immutability and purity

Immutability is a concept that is very important in pure functional programming. It means that once a value is
created, it can't be changed. This is a very important concept, because it means that you can't have side
effects, which are changes in the state of the program that are not visible in the return value of a function.

There's a common misconception that your functional code will be slower than imperative code, because you
can't mutate values. This is not true. In fact, you can have a very fast functional code, because you can
use memoization, which is a technique that caches the results of a function call, and if the function is called
again with the same arguments, it returns the cached result, rather than recalculating it. This is a very
common technique in functional programming, and it's very useful.

A popular question about immutability is: "How do I change a value then?". The answer is: you don't. You
create a new value, and you return it. Lists are a good example of this. If you want to add an element to a
list, you don't mutate the list, you create a new list with the new element. This is a very common technique
in functional programming, and it's very useful. You may be thinking: "Wouldn't it be expensive to create?"
The answer is: not really. In fact, you are just creating a new reference to the same list, and you are
creating a new list with the new element. You can understand it better by visualizing the "skeleton" of a
list by yourself:

```rust
enum List[A] =
    | Cons(head: A, tail: List[A])
    | Nil

let list = Cons(1, Cons(2, Cons(3, Nil)))
let new_list = Cons(4, list)
```

A list is a recursive data structure, which means it's a data structure that contains itself. In this case,
a list is a `Cons` node, which contains a value and a reference to the next node, which can be another `Cons`
node, or a `Nil` node, which is the end of the list. This is a very common data structure in functional
programming. The only thing you may think as a downside is that you can only append elements to the end of
the list. This is not a problem, because you can always reverse the list by using a simple recursive algorithm:

```rust
let reverse[A] (data: List[A]) List[A] =
    tailrec let go (input: List[A], buffer: List[A]) List[A] =
        match data with
            | case Cons(head, tail) => go(tail, Cons(head, buffer))
            | case Nil => buffer
    go(data, Nil)
```

It works by using a tail-recursive function, which means it's a function that calls itself as the last
expression. It adds an element to the buffer, and then calls itself again with the next element. If the
list is empty, it returns the buffer, which is the reversed list. The reason it is reversed is because
we are adding elements to the beginning of the list, which is the opposite of what we want. As you can
notice, this function is _pure_.

Function purity refers to the fact that a function always returns the same result given the same
input/arguments.

### Pattern matching

Pattern matching is a very important concept in functional programming. It's a way of checking the type of
a value and deconstructing it. It's a very powerful concept, and it's very useful. Voxu also allows you to
define your own pattern matching rules, which is great for creating your own data structures and algorithms.

```rust
let list = Cons(1, Cons(2, Cons(3, Nil)))

match list with
    | case Cons(head, tail) => fmt::println!(head) // Automatic enum destructuring
    // you also could use: head Cons tail => fmt::println!(head)
    | case Nil => fmt::println!("Empty list")
```

```rust
enum List[+A] =
    | Cons(head: A, tail: List[A])
    | Nil

priv type Params = (A, List[A])

pattern[A] :: (&self, params: Params) for Cons[A] =
    (self.head, self.tail)

// match list with
//     | case :: head tail => fmt::println!(head)
//     or
//     | case head :: tail => fmt::println!(head)
```

### Categories, functors, and endofunctors

Categories, functors, and endofunctores are common concepts in functional programming based on mathematical
algebraic structures.

A category is an algebraic structure for modelling data and their relationships. A category **C** consists of a
collection of objects **ob(C)** and a collection of arrows/morphisms **hom(C)** connecting the objects. In other
words, every arrow f can be defined as a pair **[a,b]** of the objects it connects. We write **f: a → b**.

A category also defines an operation for composing arrows, such that for every **f: a → b** and **g : b → c**,
their composition **g ∘ f** is also an arrow, which connects **a** and **c** - i.e. **g ∘ f: a → c**.

An object is a member of the category, and an arrow is a morphism between two objects.

**f** means "f is a function from a to b". **g ∘ f** means "g composed with f". An arrow is a function that
connects an object to another object. A collection of objects and arrows is a category if it satisfies the
following axioms:

- The composition of two arrows is associative. This means that if you have three arrows, you can compose
  them in any order, and the result will be the same. For example, if you have three arrows, **f: a → b**,
  **g: b → c**, and **h: c → d**, you can compose them in any order, and the result will be the same. For
  example, you can compose them as **h ∘ (g ∘ f)**, or **(h ∘ g) ∘ f**, or **h ∘ g ∘ f**;
- For every object there's an identity arrow (i.e. a loop) **iₐ** that connects it to itself: **iₐ: a → a**.
  Identities must have a property that for every **f: a → b**, **iᵦ ∘ f = f = f ∘ iₐ** must be true, which
  means an indentity must be _neutral_ to compositions.

| ![Illustration of a category](https://nikgrozev.com/images/blog/Functional%20Programming%20and%20Category%20Theory%20Part%201%20-%20Categories%20and%20Functors/category.jpg) |
| :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|                     _A category of 4 objects. For every object there must be an identity arrow (i.e. loop). The order of composition should not matter._                      |

In Object Oriented Programming (OOP), a class hierarchy also forms a category. The objects are the possible types
— for example, classes, traits, interfaces, enumerations, etc. —. We consider two types **A** and **B** to be
connected with an arrow if **A** is a subtype of **B**. The composition of two arrows is the subtype relation,
because if **A** is a subtype of **B**, and **B** of **C**, then **A** is a subtype of **C**. The identity arrow
is the identity relation, because every type is a subtype of itself.

| ![Illustration of categories in OOP](https://nikgrozev.com/images/blog/Functional%20Programming%20and%20Category%20Theory%20Part%201%20-%20Categories%20and%20Functors/category_oop.jpg) |
| :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|                                                                    _Category theory in Object Oriented Programming._                                                                     |

In category theory, a functor is a transformation between two categories **A** and **B** that preserves the
structure of the categories (i.e. identity arrows and composition). We could represent a functor as **F: A → B**,
**F** must map every object and arrow from **A** to **B**, in other words, we write: **a ∈ ob(A)** then
**F(a) ∈ ob(B)**, and if **f ∈ Hom(A)** then **F(f) ∈ Hom(B)**.

**a ∈ ob(A)** means "a is an object in category A", and **F(a) ∈ ob(B)** means "F(a) is an object in category B".
**f ∈ Hom(A)** means "f is an arrow in category A", and **F(f) ∈ Hom(B)** means "F(f) is an arrow in category B".
**∈** means "is an element of". Preserving a structure means that:

- If **f: a → b** is an arrow in **A**, then **F(f): F(a) → F(b)** is an arrow in **B**;
- F(idₓ) = id₍F₍ₓ₎₎, which means that each identity arrow in **A** is transformed into an identity arrow in **B**.
- **F(g ∘ f) = F(g) ∘ F(f)**, which means that the composition of two arrows in **A** is transformed into the
  composition of two arrows in **B**.

When a functor **F** maps a category **A** to itself, we call it an endofunctor. For example, the functor
**F: List → List** maps a list of **A** to a list of **F(A)**.

_<Source: https://nikgrozev.com/2016/03/14/functional-programming-and-category-theory-part-1-categories-and-functors/>_

### Monoids, monads, and identity

Identity is a pretty important concept in monads, I already mentioned it briefly in the previous section so
there's no need to go into too much detail again.

A monoid can be defined as a set with an associative binary operation and an identity element (e.g. a neutral
element). The Monoidal Category is a category in which you can "multiply" objects and arrows of type **A** —
combine them into a single **A** object or arrow — using some kind of tensor product (⊗). A Monoid in a
Monoidal Category is an object **M** with two morphisms **μ** and **η** such that the following laws hold:

- **μ** is the multiplication law for the monoid - `M ⊗ M → M`;
- **η** is the identity (unit) law for the monoid - `I → M`;

In other, Monoidal Category is like our Monoid but applied to the category theory itself.

You can think of examples of monoids in a language like Scala for example as:

- A `String` where our identity would be an empty sequence (`""`) and our `combine` would be concatenation
  (`+`).
- An `Int` where our identity would be `0` and our `combine` would be addition (`+`).
- A `List` where our identity would be an empty list (`Nil`) and our `combine` would be concatenation (`++`).

A monad is a monoid in the category of endofunctors, what this means is that it's a way to compose functions
together to form more complex functions. Let's see an example in Scala 3:

```scala
trait Monoid[A]:
    def identity: A
    def combine(x: A, y: A): A

trait Functor[F[_]]: // in scala all functors are endofunctors
    def map[A, B](fa: F[A])(f: A => B): F[B]

// nothing that since it follows the category theory, Monoid#combine is associative, so:
// combine(combine(x, y), z) = combine(x, combine(y, z)
// combine(x, empty) = combine(empty, x) = x

trait Monad[F[_]] extends Functor[F] with Monoid[F] {
    def flatMap[A, B](fa: F[A])(f: A => F[B]): F[B]

    def pure[A](a: A): F[A]
}
```

As you can see, in category theory, in a monad our `μ` (`T × T → T`) is `flatMap` and our `η` (`I → T`) is
`pure`. The `pure` function is the identity function for the monad, and `flatMap` is the `combine` function for
the monad.

The great point of a Monad is that we can compose new structures from the pieces of what we already have without
any _extra lifting_ in contrast to a simple Monoid.

Lifting is the technique of wrapping a value in a context (e.g. `Option`, `List`, `Future`, etc.) so that we can
compose it with other values in the same context.

_<Source: https://blog.softwaremill.com/monoid-in-the-category-of-endofunctors-b85bab43587b>_

### Error handling

Since functional programming is like a series of algebraic equations, and because of the fact algebra does not
haveanything like exceptions or nulls, we need to find a way to handle errors in a functional way. The solution
would be to use a Monad similar to the `Option` monad, but instead of returning `None` when an error occurs, we
return a `Left` with the error message. This is called the `Either` monad. As the name suggests, an `Either` type
can be either a `Left` or a `Right` of type `A` or `B` respectively. Let's see an example in Scala:

```scala
sealed trait Either[+A, +B]

case class Left[+A](value: A) extends Either[A, Nothing]

case class Right[+B](value: B) extends Either[Nothing, B]

def divide(a: Int, b: Int): Either[String, Int] =
    if b == 0 then Left("Division by zero")
    else Right(a / b)
```

This kind of error handing allows us to compose functions that can fail without having to worry about runtime errors,
while providing us a clean, easy, safe, and functional way to handle unexpected behavior.

### Curried functions

Voxu supports curried functions, which means that a function can be called with fewer arguments than it
expects. This is useful when you want to create a function that takes multiple arguments, but you want
to use it in a context where only one argument is available.

For example, you may want to fold (reduce) a list of numbers in a bunch of ways. You can create a folding
context first, then applying any function you want to it. This is done by using the `foldLeft` function,
which is available by the notation: `List[A] # A => (A, A) => A => List[A]`. This function takes a list,
a starting value, a folding function, and returns a new list. The folding function takes two arguments,
the accumulator and the current element, and returns the new accumulator. For example:

```rust
// it is worth nothing that Fold is a type alias for a function type:
// type Fold[A] = (A, A) => A => List[A]
let create_context_from_list (initial: Int) (list: List[u32]) Fold[A] =
    list.foldLeft(initial)

let list = [1, 2, 3, 4, 5]
lazy let context = create_context_from_list(0)(list)
let sum = context((acc, elem) => acc + elem)
let product = context((acc, elem) => acc * elem)
let max = context((acc, elem) => if acc > elem then acc else elem)
fmt::println!("Sum: ${sum}, Product: ${product}, Max: ${max}")
```
