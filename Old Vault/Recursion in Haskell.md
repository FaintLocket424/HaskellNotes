---
category:
  - "[[Jebediahs]]"
tags:
  - Jebediah
module: "[[Functional Programming Module]]"
lecture_covered:
  - "[[Functional Programming Lecture 6]]"
practical_covered: 
template:
  - "[[Jebediah Template]]"
TARGET DECK: COMP2221::Functional Programming
---
> [!Important] Definition
> [[Recursion in Haskell]]

Recursion means to define something in terms of itself.

> [!example] Steps to writing recursive functions
> 1. Define the type
> 2. Enumerate the cases
> 3. Define the simple (*base*) cases
> 4. Define the reduction of other cases into simpler ones.
> 5. (Optional) generalise and simplify

## Example: `drop`

1. Define the type

```Haskell
drop :: Int -> [a] -> [a]
```

2. Enumerate the cases

```Haskell
drop 0 []     = ...
drop 0 (x:xs) = ...
drop n []     = ...
drop n (x:xs) = ...
```

3. Define the base cases

```Haskell
drop 0 []     = []
drop 0 (x:xs) = x:xs
drop n []     = []
drop n (x:xs) = ...
```

4. Define the reduction of other cases to simpler ones 

```Haskell
drop 0 []     = []
drop 0 (x:xs) = x:xs
drop n []     = []
drop n (x:xs) = drop (n-1) xs
```

5. (Optional) generalise and simplify

```Haskell
drop :: Int -> [a] -> [a]
drop 0 xs     = xs
drop _ []     = []
drop n (x:xs) = drop (n-1) xs
```

## Equivalence of Recursion and Iteration

Both pure iteration and pure recursion are Turing complete. This means it's always possible to convert one to the other.


> [!example] Recursion to Iteration
> - Write looping constructs.
> - Manually manage function call stack.


> [!example] Iteration to Recursion
> - Turn loop variables into additional function arguments.
> - And write [[#Tail Recursion|tail recursive]] functions

## Types of Recursion 

### Linear Recursion
The recursive call contains only a single self reference.

```Haskell
length' []     = []
length' (_:xs) = 1 + length' xs
```
Function just calls itself repeatedly until it hits the base case.

### Multiple Recursion
The recursive call contains multiple self references.

```Haskell
fib 0 = 0
fib 1 = 1
fib n = fib (n - 1) + fib (n - 2)
```

### Direct Recursion
The function calls itself recursively

```Haskell
product' []     = []
product' (x:xs) = x * product' xs
```

### Mutual / Indirect Recursion
Multiple functions call each other recursively.

```Haskell
even' :: (Integral a) => a -> Bool
even' 0 = True
even' n = odd' (n - 1)

odd' :: (Integral a) => a -> Bool
odd' 0 = False
odd' n = even' (n - 1)
```

### A Special Case: Tail Recursion

A function is tail recursive if the last result of a recursive call is the result of the function itself.

The last thing a tail recursive function does is call itself with new arguments, or returning a value. It does not have to return to the function context to perform additional calculations.

This makes it very easy to convert tail recursive functions into loops. The complexity remains the same, but the implementation is more efficient.

> [!Example] Not Tail Recursive
> Calls `(*)` after recursing.
> ```Haskell
> product' :: (Num a) => [a] -> a
> product' []     = 1
> product' (x:xs) = x * product' xs
> ```

> [!example] Tail Recursive
> Recursive call to `loop` calls itself "outermost"
> ```Haskell
> product' :: (Num a) => [a] -> a
> product' xs = loop xs 1
> 	where loop [] n     = n
>           loop (x:xs) n = loop xs (x * n)
> ```

%%

In programming, what is the definition of recursion? #flashcard 
A function which is defined in terms of itself.
<!--ID: 1738718277739-->

In programming, what are the steps to writing a recursive function? #flashcard 
1. Define the type.
2. Enumerate the cases.
3. Define the base cases.
4. Define the reduction of other cases into simpler ones.
5. (Optional) generalise and simplify.
<!--ID: 1738718277743-->

In Haskell, how would you, following the 5 steps of writing a recursive function, recursively implement the `drop :: Int -> [a] -> [a]` function, which drops the first `n` elements from a list? #flashcard 
1. Define the type
```Haskell
drop :: Int -> [a] -> [a]
```
2. Enumerate the cases
```Haskell
drop 0 []     = ...
drop 0 (x:xs) = ...
drop n []     = ...
drop n (x:xs) = ...
```
3. Define the base cases
```Haskell
drop 0 []     = []
drop 0 (x:xs) = x:xs
drop n []     = []
drop n (x:xs) = ...
```
4. Define the reduction of other cases to simpler ones
```Haskell
drop 0 []     = []
drop 0 (x:xs) = x:xs
drop n []     = []
drop n (x:xs) = drop (n-1) xs
```
5. (Optionally) generalise and simplify
```Haskell
drop :: Int -> [a] -> [a]
drop 0 xs     = xs
drop _ []     = []
drop n (x:xs) = drop (n-1) xs
```
<!--ID: 1738718722781-->

In programming, describe what a linear recursion is. #flashcard 
The recursive call contains only a single self reference.
```Haskell
length' []     = []
length' (_:xs) = 1 + length' xs
```
<!--ID: 1738718825089-->

In programming, describe what multiple recursion is. #flashcard 
The recursive call contains multiple self references.
```Haskell
fib 0 = 0
fib 1 = 1
fib n = fib (n - 1) + fib (n - 2)
```
<!--ID: 1738718825091-->

In programming, describe the difference between a linear and multiple recursion. #flashcard 
In linear recursion, the recursive call contains only a single reference. Whereas in multiple recursion, the recursive call contains multiple self references.
<!--ID: 1738718917870-->

In programming, describe what direct recursion is. #flashcard 
The function calls itself repeatedly.
```Haskell
product' []     = []
product' (x:xs) = x * product' xs
```
<!--ID: 1738718988718-->

In programming, describe what mutual / indirect recursion is. #flashcard 
Multiple functions call each other recursively.
```Haskell
even' :: (Integral a) => a -> Bool
even' 0 = True
even' n = odd' (n - 1)
--
odd' :: (Integral a) => a -> Bool
odd' 0 = False
odd' n = even' (n - 1)
```
<!--ID: 1738718988720-->

In programming, describe tail recursion. #flashcard 
A recursive function is tail recursive iff the last thing the function does is call itself with new arguments (or return a value). It does not have to return to the function context to perform calculations on the result of the call.
```Haskell
-- Not Tail Recursive
product' :: (Num a) => [a] -> a
product' []     = 1
product' (x:xs) = x * product' xs
--
-- Tail Recursive
product' :: (Num a) => [a] -> a
product' xs = loop xs 1
	where loop [] n     = n
          loop (x:xs) n = loop xs (x * n)
```
<!--ID: 1738719221155-->

In programming, what makes tail recursive functions useful? #flashcard 
Tail recursive functions are easy to convert into loops for a more efficient implementation.
<!--ID: 1738719221157-->

%%