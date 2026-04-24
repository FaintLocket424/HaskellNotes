> Previous: [[Chapter 3 - The Functional Paradigm]]

Before we look at any Haskell code, it's important to understand the data types available to you in Haskell, and how they are represented.

> [!Important] Remember that Haskell is a statically typed language!
> The type of every expression, function and variable must be known by the compiler at the time of compilation, or it will not compile.
> 
> This means that it's important that *you* understand the types of your program as well.

---
## The "has type of" Operator

Haskell has the operator `::`, which is pronounced "has type of".

So for example, `e :: T` means "`e` has type of `T`"

```Haskell
-- "e" has type of T
e :: T

-- "False" has type of Bool
False :: Bool

-- "x" has type of Int
x :: Int
```

This operator is how we define and represent the type of an expression, variable or function.

---
## Haskell's Basic Types

In this section, we're going to look at 9 of Haskell's most basic types. There are more but these should be familiar from other languages.

1. `Bool`
2. `Int`
3. `Integer` (yes, there's a difference and it's important)
4. `Float`
5. `Double`
6. `Char`
7. `[]` (Lists)
8. `()` (Tuples)
9. `->` (Functions)

> [!info] Notice how they all start with a capital letter and this is by design. **If something starts with a capital letter, you know it's a type.** This is even enforced when you go and make your own types later.

Also note that Haskell has **no `null`** and this is again, by design, and a feature of the language. When data is possibly missing, we have more complex data types that we will encounter later that account for this.

### Bool

The first type we will look at is `Bool`, used for Boolean values of `True` and `False`.

```Haskell
True :: Bool
False :: Bool
```

### Int

`Int` is for whole numbers. It's represented as a signed long integer value, as such it is bound by the maximum and minimum values of a 32/64 bit integer (depending on hardware).

```Haskell
7 :: Int
-9 :: Int

35789630756296623550269563896647602234563 :: Int  
-- <interactive>:3:1: warning: [GHC-97441] [-Woverflowed-literals]  
--    Literal 35789630756296623550269563896647602234563 is out of the Int -- range -9223372036854775808..922  
-- 3372036854775807  
--   
-- 119448532308550851
```

Shown above is what happens if you try and input that statement in `ghci` (You'll find out what that is later).

In C these would be called a `long long`.

### Integer

`Integer` is for, well umm, *also whole numbers*. The difference is that, unlike `Int`, it's not bounded by the 32/64-bit integer limit.

```Haskell
7 :: Integer
35789630756296623550269563896647602234563 :: Integer
```

## Float

`Float` is for real numbers $\mathbb{R}$ stored as a single-precision floating point.

```Haskell
3.4 :: Float -- 3.4
2   :: Float -- 2.0
pi  :: Float -- 3.1415927
```

### Double

`Double` is like a `Float`, only it's double-precision floating point.

```Haskell
3.4 :: Double -- 3.4
2   :: Double -- 2.0
pi  :: Double -- 3.141592653589793
```

### Char

`Char` is for individual characters, denoted with **single quotes** `''`.

```Haskell
'a' :: Char
'b' :: Char

"hello" :: Char  
-- <interactive>:8:1: error: [GHC-83865]  
--    • Couldn't match type ‘[Char]’ with ‘Char’  
--      Expected: Char  
--        Actual: String  
--    • In the expression: "hello" :: Char  
--      In an equation for ‘it’: it = "hello" :: Char
```

### Lists

Lists are covered much more extensively in [[Chapter TODO - Lists]], but here's the basics. A list is a collection of items with the **same data type**.

The type of a list is `[a]`, where `a` must be a "concrete type". You'll find out what that means exactly soon but for now, think of it as a simple type that fully describes itself like `Int`, `Char`, `Bool`.

You **must** specify the type that a list stores, this goes back to Haskell being statically typed.

```Haskell
[10, 20, 30] :: [Int] -- List of Integers

['L', 'u', 'k', 'e'] :: [Char] -- List of Characters (a string)

[['O'], ['s', 'c'], ['a'], ['r']] :: [[Char]] -- List of list of char (list of strings)

"test" :: [Char] -- List of char, string
"The piggy goes oink oink" :: String -- *String is an alias for [Char]*

"an invalid type definition" :: []
-- * Expecting one more argument to `[]'
```

Note that lists are variable length, much like a Python list, as you can see in the `[[Char]]` example.

### Tuples

Tuples `()` are for a fixed size, ordered collection that can be different types. They're very similar to Python tuples.

```Haskell
('a', 'b') :: (Char, Char)

("wha", "t", 4.0) :: ([Char], Char, Float)

(10, ('a', 'b'), 'c') :: (Int, (Char, Char), Char)

() :: () -- The "Unit"
```

> [!important]
> Pay particular attention to that last one, the empty tuple. It's called a "unit" and represents "no data".

### Functions

The most important type in Haskell is the function, `a -> b`. It might feel weird to treat a function like a type but it can make sense.

Much like how a list `[a]` cannot exist without another type (`a`) being specified, the function type `a -> b` must have two extra types! These represent the input type `a` and the output type `b`.

So, a function that takes in an integer and outputs a Boolean value (such as an `isEven` function) would be written as:

```Haskell
isEven :: Int -> Bool
-- isEven takes an Int and returns a Bool
```

These are known as "function decorators" and are used to explicitly define a function's type. They aren't always necessary as Haskell does have type inference, but sometimes it's useful (or necessary) to define a function's type.

> [!warning] But what about a function with more than one input?

If you're used to any other language, you'd think it would be something like `func :: (String, Int) -> Float`. **Nope!** Instead, we do this:

```Haskell
func :: String -> Int -> Float
-- A function that takes a String and an Int and outputs a Float.
```

There will be a lot more on this later in the [[Chapter 8 - Functions in Haskell]].

> [!important] Functions can be arguments!
> Just like how you can pass a `Float` into a function, you can also pass a function as a parameter. This is because functions are **First-Class** in Haskell.
> 
> ```Haskell
> mapInt :: (Int -> Int) -> [Int] -> [Int]
> ```
> 
> The function `mapInt` takes a function (that maps ints to ints) and a list of ints, and outputs a list of ints.

> Next: [[Chapter 5 - Type Variables]]


