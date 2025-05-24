---
category:
  - "[[Jebediahs]]"
tags:
  - Jebediah
module: "[[Functional Programming Module]]"
lecture_covered:
  - "[[Functional Programming Lecture 8]]"
practical_covered: 
template:
  - "[[Jebediah Template]]"
TARGET DECK: COMP2221::Functional Programming
---
## Lazy Evaluation 

> [!Important] Definition
> Expressions are not evaluated when they are bound to variables. Instead, their evaluation is deferred until their result is needed by other computations.

Lazy evaluation lets us work with infinite data structures.

## Evaluation Strategies

It does not matter the order in which we evaluate expressions in, so long as they terminate.

```Haskell
mult :: Int -> Int -> Int
mult x y = x * y

mult (1+2) (3+4)
```

It doesn't matter if we first sum the arguments `mult (3,7)` or if we apply `mult` for `(1+2)*(3+4)`, we still get the same result `21`.

We can represent a function call in Haskell as a graph:
- Nodes in the graph are either terminal or compound.
- Compound nodes are called *reducible expressions* or *redexes*.

![[Pasted image 20250205150736.png|300]]

Here:
- `1`, `2`, `3` and `4` are terminal expressions.
- `(+)` and `mult` are reducible expressions.

There are two strategies to evaluate this graph, [[#Innermost Evaluation]] and [[#Outermost Evaluation]].

For these strategies, an order of evaluation must be established, typically "left to right" of the child nodes.

### Innermost Evaluation

We evaluate "bottom up".

First evaluate the redexes that only contain terminal expressions, then repeat.

![[Pasted image 20250205151255.png|600]]

### Outermost Evaluation

We evaluate "top down".

First evaluate redexes that are outmost, then repeat.

![[Pasted image 20250205151406.png|600]]

### Termination of Inner and Outermost Evaluation

If the expression is finite, then both innermost and outermost will terminate.

But if the expression in infinite, then innermost will fail to terminate, but outermost will.

```Haskell
inf :: Integer
inf = 1 + inf
fst :: (a, b) -> a
fst (x,_) = x

fst (0, inf)
```

With innermost evaluation, this will never terminate.

```Haskell
fst (0, inf)

fst (0, 1 + inf) -- applies inf

fst (0, 1 + 1 + inf) -- applies inf
```

With outermost evaluation, this will terminate

```Haskell
fst (0, inf)

0 -- applies fst
```

### Sharing 

The problem with outermost evaluation is that you can end up evaluating arguments multiple times.

Take the `square` function.

```Haskell
square :: Int -> Int
square n = n * n
```

Evaluating `square (1+2)` goes like this:

```Haskell
square (1+2)

(1 + 2) * (1 + 2)
3 * (1 + 2)
3 * 3
9
```

We had to evaluate `(1+2)` twice, not very efficient.

Haskell implements sharing of arguments to avoid this.

> [!Example] Without sharing
> ![[Pasted image 20250205155642.png|300]]

> [!Example] With sharing
> ![[Pasted image 20250205155759.png|300]]

%%

In programming, describe lazy evaluation. #flashcard 
Expressions are not evaluated until their result is needed by other computations.
<!--ID: 1738771372705-->

In programming, what type of data structures does lazy evaluation allows us to work with? #flashcard 
Infinite data structures.
<!--ID: 1738771372708-->

In Haskell, describe how you could represent the statement `mult (1+2,3+4)` as a function call graph, where `mult (x, y) = x * y`? #flashcard 
![[Pasted image 20250205150736.png|300]]
<!--ID: 1738771453078-->

In Haskell, what are the different types of nodes called on a function call graph? #flashcard 
1. Terminal
2. Compound / reducible expressions / redexes
<!--ID: 1738771453081-->

In Haskell, describe the difference between innermost and outermost evaluation. #flashcard 
- Innermost is a "bottom up" approach, evaluating the redexes with only terminal expressions, then repeating.
- Outermost is a "top down" approach, evaluating the outmost redexes first and repeating.
<!--ID: 1738771851924-->

In Haskell, describe why innermost evaluation fails on infinite expressions. #flashcard 
Because innermost evaluates all the expressions from the bottom up, it will try to evaluate the infinite structure, which will always fail.
```Haskell
inf :: Integer
inf = 1 + inf
fst :: (a, b) -> a
fst (x,_) = x
-----
fst (0, inf)
fst (0, 1 + inf) -- applies inf
fst (0, 1 + 1 + inf) -- applies inf
```
This will never terminate.
<!--ID: 1738771851926-->

In Haskell, describe how outermost evaluation is able to handle infinite expressions? #flashcard 
Because it only evaluates the expressions as necessary, it can avoid computing the infinite value.
```Haskell
inf :: Integer
inf = 1 + inf
fst :: (a, b) -> a
fst (x,_) = x
--
fst (0, inf)
0 -- applies fst
```
<!--ID: 1738771851928-->

In Haskell, describe the big issue with outermost evaluation, and describe how Haskell circumvents this. #flashcard 
Outermost can lead to arguments being evaluated multiple times.
```Haskell
square :: Int -> Int
square n = n * n
--
square (1+2)
(1 + 2) * (1 + 2)
3 * (1 + 2)
3 * 3
9
```
To combat this, Haskell implements argument sharing.
<!--ID: 1738772948982-->

In Haskell, describe the function call graph for `square (1+2)` when using argument sharing. `square n = n * n` #flashcard 
![[Pasted image 20250205155759.png|300]]
<!--ID: 1738772948983-->

%%