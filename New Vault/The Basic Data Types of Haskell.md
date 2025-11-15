---
categories:
  - "[[Module Notes]]"
module: "[[Functional Programming Module]]"
authors:
  - "[[Matthew]]"
templates:
  - "[[Module Note Template]]"
template_version: "1"
TARGET DECK: Programming Paradigms::Functional Programming
---
> [!Important] Important - Remember that [[Haskell]] is a statically typed language!
> The type of every expression, function and variable must be known by the compiler at the time of compilation, or it will not compile.

---

## The "has type of" Operator

Haskell implements the `::` operator, pronounced "has type of".

```Haskell
-- "e" has type of T
e :: T

-- "False" has type of Bool
False :: Bool

-- "not" has type of Bool -> Bool
not :: Bool -> Bool
```

This operator is how we define and represent the type of a function, expression or variable.

---

## Haskell's Basic Types

We're going to look at 9 of Haskell's most basic types, 

1. `Bool`
2. `Int`
3. `Integer` (yes, there's a difference and they will exam you on it)
4. `Float`
5. `Double`
6. `Char`
7. `[]` (Lists)
8. `()` (Tuples)
9. `->` (Functions)

You may notice that they all start with a capital letter and this is by design. **If something starts with a capital letter, you know it's a type.**

Also note that Haskell deliberately has **no null** and this is a key feature of the language. "Possible Missing data" is representing using more complex data types that we will encounter later.

> [!Important] A note for this section
> You're going to see some special constants, `maxBound` and `minBound`, used in this section. When these constants "have type of" a given type, it will return the minimum or maximum value that type can represent.
> 
> Do not worry exactly how that works just yet.
> 
> ```Haskell
> maxBound :: Int
> -- 9223372036854775807
> 
> minBound :: Bool
> -- False
> 
> maxBound :: Char
> -- '\1114111'
> 
> minBound :: Float
> -- * No instnace of (Bounded Float) arising from a use of `minBound'
> ```
> 
> It only works for types which have an upper or lower bound.

### Bool

You guessed it, the `Bool` type is for Boolean values of `True` and `False`.

```Haskell
True :: Bool
False :: Bool
```

### Int

`Int` is for whole numbers. It's represented as a signed long integer value, and as such is bound by the max/min values of a 64/32 bit integer (depending on hardware).

```Haskell
7 :: Int
-9 :: Int

maxBound :: Int
--   9223372036854775807

minBound :: Int
--  -9223372036854775808
```

In `C` these would be called a `long long`.

### Integer

`Integer`, umm, *also stands for integer*. The difference between `Int` and `Integer` is that `Integer` is not bounded, but is less efficient.

```Haskell
7 :: Integer
35789630756296623550269563896647602234563 :: Integer

maxBound :: Integer
-- * No instance for (Bounded Integer) arising from a use of `maxBound'
```

### Float

`Float` is for real numbers $\mathbb{R}$ stored as a single precision floating point.

```Haskell
pi :: Float
-- 3.1415927
```

### Double

`Double` is for real $\mathbb{R}$ numbers stored as a double precision floating point.

```Haskell
pi :: Double
-- 3.141592653589793
```

### Char

`Char` represents individual characters. They are denoted with single quotes.

```Haskell
'a' :: Char
'b' :: Char
```

### Lists

We will go into much more detail about lists later, but for know just know that the type of a list is `[]`, and that it must have some other type inside of the brackets to be a valid type, `[Int]`.

You can't have list type without specifying what the list is going to store, just like how you can't have an array in C without specifying the type it will store.

```Haskell
[10, 20, 30] :: [Int]

['M', 'a', 'k', 's'] :: [Char]

[['O'], ['s', 'c'], ['a'], ['r']] :: [[Char]]

"test" :: [Char]

"Hey Nathaniel, I've been expecting you..." :: String

"invalid type definition" :: []
-- * Expecting one more argument to `[]'
```

Note that a `String` is just a list of characters, `[Char]`.

And note that lists are variable length.

### Tuples

Tuples `()` are for fixed size, ordered collections that can be different types.

```Haskell
('a', 'b') :: (Char, Char)

("wha", 't', 4.0) :: ([Char], Char, Float)

(10, ('a', 'b'), 'c') :: (Int, (Char, Char), Char)

() :: ()
```

Pay particular attention to the last one, the empty tuple. In Haskell it's called a "unit" and represents "no data".

### Functions

Probably the most important type here is the function, `->`.

Much like how a list `[]` type cannot exist without another type `[Int]`, the function type `->` must have extra type information.

```Haskell
func :: Int -> Bool
```

These are known as "function decorators" and are used to explicitly define a function's type. They aren't always necessary as Haskell does have type inference, but sometimes it's useful (or necessary) to define a function's type.

Here are a few examples:

> [!Example] Example 1
> Take the function `not`, which inverts a boolean. This function would take in a `Bool` and output a `Bool`, so would be decorated with:
> 
> ```Haskell
> not :: Bool -> Bool
> ```

> [!Example] Example 2
> Take the function `sum`, which takes two `Int` arguments and returns their sum as a new `Int`.
> 
> If you're used to other languages, you might think the decorator would be `(Int, Int) -> Int` but you would be wrong! That's a tuple with 2 integers in it!
> 
> In Haskell, due to reason's we'll discuss later (currying), multiparameter functions are defined like this:
> 
> ```Haskell
> sum :: Int -> Int -> Int
> -- Takes two Ints and returns an Int
> ```

> [!Example] Other examples
> ```Haskell
> div :: Float -> Float -> Float
> -- Takes two Floats and returns a Float
> 
> reallyOddFunction :: Char -> (Bool, Int) -> String
> -- Takes a Char and a (Bool, Int) tuple and returns a String.
> ```

#### Functions as arguments

Functions in Haskell are "first-class", meaning they can be assigned to variables and passed as arguments into functions.

This makes it possible to have a function which takes another function as an argument, known as a higher-order function.

Say you have a list of integers, `[Int]` and you want to apply a function to every item in the list to get a new, transformed `[Int]`. To do this, you'd need a higher-order function like:

```Haskell
map :: (Int -> Int) -> [Int] -> [Int]
```

Here our function takes two arguments:

1. A function which transforms `Int` values into new `Int` values.
2. A list of `Int` values to apply our function to.

And then it will return the new list of transformed values.

This is an extremely important concept in Haskell and it is used absolutely everywhere, so make sure you understand what's happening here before moving on.

---

## Type Variables

Look at the type for the function `head` which returns the first element in a list.

```Haskell
head :: [a] -> a
```

Huh? What's this type `a`?

**It's a Type Variable!** Notice how it doesn't start with a capital letter, therefore it's not a type per-say.

These allow you to define a function which can work on multiple types, similar to generics in Java.

For the `head` function:

- If you use it on a list of integers, then it becomes `head :: [Int] -> Int`.
- If you use it on a list of characters, then it becomes `head :: [Char] -> Char`.

> [!example]
> Think of it like it replaces `a` with the type which was input.
> 
> ```Haskell
> -- Take this function
> func :: a -> (b, a) -> c -> (a, b, c)
> 
> -- Say we first input an Int type, it would then "become"
> func :: Int -> (b, Int) -> c -> (Int, b, c)
> 
> -- If we then input a ([Char], Int) type, it would be:
> func :: Int -> ([Char], Int) -> c -> (Int, [Char], c)
> 
> -- If we then input a Float type:
> func :: Int -> ([Char], Int) -> Float -> (Int, [Char], Float)
> ```

This is the start of polymorphism in Haskell!

We will learn in the next section about how to place limits on what types a type variable can be.