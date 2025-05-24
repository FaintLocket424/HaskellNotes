---
category:
  - "[[Jebediahs]]"
tags:
  - Jebediah
module: "[[Functional Programming Module]]"
lecture_covered:
  - "[[Functional Programming Lecture 4]]"
practical_covered: 
template:
  - "[[Jebediah Template]]"
TARGET DECK: COMP2221::Functional Programming
---
## Lambda Expressions in Haskell

A [[Lambda Expressions|Lambda Expression]] is an anonymous function.

In [[Haskell]], a lambda expression is defined as:

```Haskell
\x -> x + x
```

Note the `\` and the `->`.

## Use Cases for Lambda Expressions

### Expressing Functions of Multiple Arguments

```Haskell
add x y = x + y

-- Equivalent
add = \x -> \y -> x + y
```

This is the idea of [[Currying]].

It emphasises that `add` is a function of one variable that returns another function of the second variable, while keeping context of the original value (a [[Closures|Closure]]).

Pattern matching is still supported in this form.

```Haskell
head = \(x:_) -> x
```

### Higher Order Functions 

Often when using [[Higher Order Functions in Haskell|Higher Order Functions]], the function in question will be defined as a lambda.

```Haskell
length' :: [a] -> Int
length' xs = sum (map (\_ -> 1) xs)
```

Here, we pass in a list like `length' [1,2,3,4]`, it's mapped to make all values 1, `[1,1,1,1]`, then those values are summed to get 4.

It would have been a lot more tedious to define the function.

### Functions which are only used once

```Haskell
-- Generate the first n positive odd numbers.
odds :: Int -> [Int]
odds n = map f [0..n-1]
	where
		f x = x*2 + 1
```

This can be simplified using lambdas

```Haskell
odds :: Int -> [Int]
odds n = map (\x -> x*2 + 1) [0..n-1]
```


%%

In Haskell, how would you define a lambda function that take an input `x` and doubles it? #flashcard 
```Haskell
\x -> x + x
\x -> 2 * x
```
<!--ID: 1738177278389-->

In Haskell, how would you rewrite the function `add x y = x + y` to use lambda expressions of single variables? #flashcard 
```Haskell
add = \x -> \y -> x + y
```
<!--ID: 1738177278392-->

In Haskell, how would you write the function `flatten :: [a] -> [a]` which uses `map` on a list to make all values 7? #flashcard 
```Haskell
flatten :: [a] -> [a]
flatten xs = map (\_ -> 7) xs
```
<!--ID: 1738177278394-->

%%