## Functional programming

- [Curried functions](#curried-functions)

Voxu is a functional programming language. This means it is designed to follow that specific programming
paradigm, which is a programming style that treats computation as the evaluation of mathematical functions
and avoids changing-state - side effects - and mutable data. It is a declarative programming paradigm, which
means programming is done with expressions or declarations instead of statements. This doesn't mean Voxu
wouldn't partially support imperative programming. It'd be implemented ad-hoc, not being the focus of the
language.

This also means that the language is designed to support the most common functional programming concepts
built-in into the language, such as curried functions, higher-order functions, and lazy evaluation.

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
