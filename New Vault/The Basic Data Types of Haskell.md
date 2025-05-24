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
> [!Important]
> [[Haskell]] is a statically typed language, the type of every expression, function and variable must be known by the compiler at the time of compilation, or it will not compile.

## "Has type of" Operator

Haskell implements the `::` operator, pronounced "has type of".

```Haskell
-- e has type of T
e :: T

-- False has type of Bool
False :: Bool

-- not has type of Bool -> Bool
not :: Bool -> Bool
```

This operator is how we define and represent the type of a function, expression or variable.

---

## Haskell's Basic Types

We're going to look at 9 of Haskell's most basic types, 

1. `Bool`
2. `Int`
3. `Integer` (yes, there's a difference)
4. `Float`
5. `Double`
6. `Char`
7. `[]` (Lists)
8. `()` (Tuples)
9. `->` (Functions)

You may notice that they all start with a capital letter, this is by design. If something starts with a capital letter, you know it's a type.

Also note that Haskell deliberately has **no null**. This is solved using more complex data types that we will encounter later.

> [!Important]
> There's special constants in Haskell called `maxBound` and `minBound` which will give you the upper and lower bounds of a type, when they "have type of" the one you're interested in.
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
> It only works for types which are bounded.

### Bool

You guessed it, the `Bool` type is for Boolean values of `True` and `False`.

```Haskell
True :: Bool
False :: Bool
```

### Int

`Int` is for integers. It's represented as a signed long integer value, and as such is bound by the max/min values of a 64/32 bit integer (depending on hardware).

```Haskell
7 :: Int
-9 :: Int

maxBound :: Int
--   9223372036854775807

minBound :: Int
--  -9223372036854775808
```

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

You can't have list type without specifying what the list is going to store.

```Haskell
[10, 20, 30] :: [Int]

['M', 'a', 'k', 's'] :: [Char]

[['O'], ['s', 'c'], ['a'], ['r']] :: [[Char]]

"test" :: [Char]

"Hey Nathaniel, I'm always watching" :: String

"wrong" :: []
-- * Expecting one more argument to `[]'
```

Note that a `String` is just a list of characters, `[Char]`.

### Tuples

Tuples `()` are for fixed size, ordered collections that can be different types.

```Haskell
('a', 'b') :: (Char, Char)

("wha", 't', 4.0) :: ([Char], Char, Float)

(10, ('a', 'b'), 'c') :: (Int, (Char, Char), Char)

() :: ()
```

Note the last one, the empty tuple, called a "unit" and represents "no data". It is also technically a type, just a type that only has one possible value.

### Functions

Probably the most important type here is the function, `->`.

Much like how a list `[]` type cannot exist without another type `[Int]`, the function type `->` must have extra type information.

```Haskell
func :: Int -> Bool
```

These are known as "function decorators" and are used to explicitly define a function's type. They aren't always necessary as Haskell does have type inference, but sometimes it's useful (or necessary) to define a function's type.

Here are a few examples:

> [!Example] Example with `not`
> Take the function `not`, which is a Boolean `¬` operator that takes a `Bool` as input and produces a new `Bool` as output. This would be decorated with:
> 
> ```Haskell
> not :: Bool -> Bool
> ```

> [!Example] Example with `(+)`
> Take the function `(+)`, which takes two `Int` arguments and returns their sum as a new `Int`.
> 
> If you're used to other languages, you might think the decorator would be `(Int, Int) -> Int` but you would be wrong! That's a tuple with 2 integers in it!
> 
> In Haskell, due to reason's we'll discuss later (currying), multiparameter functions are defined like this:
> 
> ```Haskell
> (+) :: Int -> Int -> Int
> -- Takes two Ints and returns an Int
> ```

> [!Example] Other examples
> ```Haskell
> (/) :: Float -> Float -> Float
> -- Takes two Floats and returns a Float
> 
> reallyOddFunction :: Char -> (Bool, Int) -> String
> -- Takes a Char and a (Bool, Int) tuple and returns a String.
> ```

### Functions as arguments

Functions in Haskell are "first-class", meaning they can be assigned to variables and passed as arguments into functions.

And if you have a function that takes another function as an argument (known as a higher-order function, more on these later), this is represented in their type signature.

```Haskell
map_int_to_float :: (Int -> Float) -> [Int] -> [Float]
```

This is a function which would take 2 arguments:

1. A function that converts integer `Int` values to float point `Float` values.
2. A list of integer `Int` values.

This function would then use the conversion on every value in the list, returning a new list where all the values are `Float`.

This is an extremely important concept in Haskell and it is used absolutely everywhere, so make sure you understand what's happening before moving on.

---

## Type Variables

Look at the type for the function `head` which returns the first element in a list.

```Haskell
head :: [a] -> a
```

Huh? What's this type `a`?

**It's a Type Variable!** Notice how it doesn't start with a capital letter, therefore it's not a type per-say.

These work much like generics in [[The Java Programming Language|Java]] or C#, allowing a function to accept multiple different types.

For the `head` function:

- If you pass it a list of integers `[Int]`, then it will return to you an `Int`.
- If you pass it a string `[Char]` then it will return to you a `Char`.

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
> -- If we then input a [Char] type, it would be:
> func :: Int -> ([Char], Int) -> c -> (Int, [Char], c)
> 
> -- If we then input a Float type:
> func :: Int -> ([Char], Int) -> Float -> (Int, [Char], Float)
> ```

This is the start of polymorphism in Haskell!

We will learn in the next section about how to place limits on what types a type variable can be.