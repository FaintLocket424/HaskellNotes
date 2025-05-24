---
category:
  - "[[Jebediahs]]"
tags:
  - Jebediah
module: "[[Functional Programming Module]]"
lecture_covered:
  - "[[Functional Programming Lecture 7]]"
practical_covered: 
template:
  - "[[Jebediah Template]]"
TARGET DECK: COMP2221::Functional Programming
---
## Higher Order Functions 

> [!Example] Higher Order Function
> A function that:
> - Takes one or more functions as arguments
> - Or returns a function as its result

Due to [[Currying]], every function in Haskell with more than one argument is a higher order function.

```Haskell
add :: (Num a) => a -> a -> a
add x y = x + y

-- "add 1" returns a function!
:type add 1
(Num a) => a -> a
```

Many [[Recursion in Haskell#Linear Recursion|Linear Recursive]] functions on lists can be written using higher order library functions like `map` or `filter`:

```Haskell
-- Applies a function to all elements in a list
map :: (a -> b) -> [a] -> [b]
map _ [] = []
map f xs = [f x | x <- xs]
```

```Haskell
-- Selects elements from a list that satisfy a predicate
filter :: (a -> Bool) -> [a] -> [a]
filter _ [] = []
filter p xs = [x | x <- xs, p x]
```

Other functions include `any`, `all`, `foldr`, `takeWhile`, `dropWhile` etc.

## Function Composition

In mathematics, there is the concept of function composition, combining functions together, possibly under a new label.

$$
(f \circ g) (x) = f(g(x))
$$

Haskell uses the `(.)` operator for this purpose, called "*pointfree*" style.

```Haskell
(.) :: (b -> c) -> (a -> b) -> (a -> c)
f . g = \x -> f (g x)
```

You can see from the function signature how:

- The inner function takes type `a` and converts it to type `b`.
- Then the outer functions takes that type `b` and converts it to type `c`.
- So it creates a composite function that turns `a` into `c`.

```Haskell
odd a = not (even a)
-- No need for "a" since it's just a function.
odd   = not . even
```

The `(.)` operator removes the need to write lambda functions or add lots of brackets.



%%

In programming, describe a higher order function. #flashcard 
A higher order function is one that:
- Takes one or more functions are arguments
- or returns a function as its result.
Basically it's using functions like variables.
<!--ID: 1738765647113-->

In Haskell, how are higher order functions used in functions of multiple arguments? #flashcard 
Functions in Haskell that are multivariate `add :: (Num a) => a -> a -> a` are actually univariate functions chained together to form higher order functions.
<!--ID: 1738765647115-->

In Haskell, what is the operator for function composition? #flashcard 
The `(.)` operator.
```Haskell
(.) :: (b -> c) -> (a -> b) -> (a -> c)
f . g = \x -> f $ g x
```
<!--ID: 1738765647117-->

In Haskell, describe the type signature of the function composition operator `(.) :: (b -> c) -> (a -> b) -> (a -> c)`. #flashcard 
```Haskell
(.) :: (b -> c) -> (a -> b) -> (a -> c)
```
The inner function maps from domain `a` to `b`, then the outer function maps from domain `b` to `c`. This makes the resulting function map from domain `a` to `c`.
<!--ID: 1738765881955-->

In Haskell, how would you create a function `odd x` which is a composition of `not x` and `even x`? #flashcard 
```Haskell
odd = not . even
```
The `x` is not necessary as it simply returns a univariate function.
<!--ID: 1738765881958-->

In Haskell, why do we use the `(.)` operator for function composition? #flashcard 
It removes the need to write lambda functions or add lots of brackets.
<!--ID: 1738765915419-->

%%