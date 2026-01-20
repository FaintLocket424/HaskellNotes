---
aliases:
  - Type Classes
---


## Classes in Haskell

> [!danger] Potential Misconception about Haskell Classes
> Note that the definition of a class in Haskell is not the same as in **Object Orientated Programming**.
> 
> In Haskell, a class is much more similar to *Interfaces* in Java.

> [!Important] Definition of a Type Class
> In Haskell, a type class is an interface which specifies a set of functions which any type must implement in order to be an instance of the type class.

Put in simpler terms: 

1. A type class will have functions associated with it.
2. All types which are instances of the type class must provide some implementation of each function.
3. It creates a sort of contract, saying that "Any type which is an instance of this class will support these methods". (This is where the similarity to OOP interfaces comes from.)

> [!NOTE] Why do we need this?
> These interfaces allow us to put less restrictive input types on our functions. If we have a function which takes some values and only adds `(+)` them, it doesn't make sense to restrict it to only taking `Int` values. We might as well let it take `Integer`, `Float`, `Double` etc. since those types can all be added too.

Type classes can also depend on one another, requiring a type to already be a member of a different typeclass before it can be a member of that one. Remember that being a member just means implementing certain methods.

Type classes can provide default implementations of methods but these can be overwritten by the implementor.

---

## Class Constraints

### The Problem

Let's take the addition function, `(+)`:

```Haskell
(+) :: Int -> Int -> Int
```

Where `3 + 7 = 10`, all good. But what if we want to do `3.2 + 7.8`?

Well, we could define `(+)` with Float instead.

```Haskell
(+) :: Float -> Float -> Float
```

Now we can do `3.2 + 7.8 = 11.0`, yippee!

But now `3 + 7 = 10.0`. A float, cringe.

There must be a better way...

### The Solution

Near the end of the section on basic data types, we discussed [[The Basic Data Types of Haskell#Type Variables|Type Variables]]. A method in Haskell to make functions polymorphic.

We could rewrite the `(+)` function using a type variable!

```Haskell
(+) :: a -> a -> a
```

Now it says that if you input two of the same type, you'll get that same type back out.

And we get:

```Haskell
3 + 7 -- 10
3.0 + 7.0 -- 10.0
```

But as it stands, there's nothing stopping us from putting a `Char` or a `(Int, [Bool])` into the function, which makes no sense. The `(+)` operator only works on numbers.

Say we had a typeclass called `Num` which represented integer and floating point numbers. Well, that's exactly what we want to allow into our `(+)` function!

You apply a class constraint with the `=>` operator.

```Haskell
(+) :: (Num a) => a -> a -> a
```

This says, "You can put a variable of any type into this function, as long as that variable's type is an instance of `Num`".

> [!important] This is a Class Constraint!

You can also add multiple class constraints if you wish:

```Haskell
someFunction :: (Num a, Ord b) => a -> b
```

---

## The Basic Type Classes of Haskell

Haskell implements 9 basic type classes, although there are many more.

1. `Eq`
2. `Ord`
3. `Num`
4. `Integral`
5. `Floating`
6. `Bounded`
7. `Show`
8. `Read`
9. `Enum`

> [!note]
> Don't worry about the exact syntax in the examples, you'll learn that next. It's there so you can get more context once you understand the syntax.

### Eq

The `Eq` typeclass provides an interface for testing equality. Any member of the `Eq` typeclass implements `==` and `/=` (Haskell uses `/=` for inequality).

### Ord

The `Ord` typeclass provides an interface for testing the order of items. Any member of `Ord` implements `>`, `<`, `>=`, `<=`.

Because it needs `>=` and `<=`, any type which is an instance of `Ord` must already be an instance of `Eq`.

### Num

`Num` members behave like numbers. All number types are members of `Num`, including `Int`, `Integer`, `Float` and `Double`.

`Num` implements the functions `(+)`, `(-)`, `(*)` as well as some others.

In fact, I lied in the last section about data types. Get rekt. I said that the type of `20` was `Int`:

```Haskell
20 :: Int
```

But actually, it's not, it's a `Num`.

```Haskell
20 :: (Num t) => t
```

> [!warning] Additional Context - Beyond Scope
> In fact, this makes numbers a kind of "Polymorphic Constant".
> 
> - If you put `20` into a function expecting an `Int`, then it will become an `Int`.
> - If you put `20` into a function expecting a `Float`, then it will become a `Float`.
> 
> This is what is happening when we do `20 :: Int` vs `20 :: Float`. In other languages, this is all handled automatically, but in Haskell it happens as a consequence of the type system.
> 
> It's kind of like doing this in Python, but ofc you would never actually do this:
> 
> ```python
> def getTwenty(type):
> 	if (type == int):
> 		return 20
> 	elif (type == float):
> 		return 20.0
> 	else:
> 		raise TypeError("Invalid type for 20")
> ```
> 
> This is also how the `minBound` and `maxBound` constants work. The constants take different values based on their type.

### Integral

The `Integral` class represents whole numbers: `Int` and `Integer`.

`Integral` implements a few functions like `quot`, `rem`, `div`, `mod`. So just a bunch of integer division functions.

A useful function for dealing with numbers is `fromIntegral`.

```Haskell
fromIntegral :: (Integral a, Num b) => a -> b
```

It takes an `Integral` and turns it into the more general `Num`. This is useful for mixing `Int` and `Float`.

> [!example]
> Take the function `length :: [a] -> Int` which returns the length of a list. Notice how it returns an `Int`. This is a problem because `(+)` only works when the types are the same. So what if we wanted to add `1.5` to the length of a list? We can't do `Float + Int`.
> 
> To solve this, we need a way to convert this `Int` value to any `Num` type like `Float`, `Double` or `Integral`. This is achieved with `fromIntegral`.
> 
> ```Haskell
> 10 + length [1, 2, 3, 4]
> -- 14
> 
> 10.0 + length [1, 2, 3, 4]
> -- * No instance for (Fractional Int) arising from the literal `10.0'
> 
> 10.0 + fromIntegral (length [1, 2, 3, 4])
> -- 14.0
> ```
> 
> Yippee.

### Floating

The `Floating` class represents floating point numbers, `Float` and `Double`.

`Floating` implements a bunch of mathematical functions like `pi`, `exp`, `log`, `sqrt`, `(**)`, `sin`, `cos`, `tan` and loads more.

### Bounded

The `Bounded` class represents types which have an explicit minimum and maximum bound.

It implements the constants (really they're functions with no arguments) `minBound` and `maxBound` that we saw earlier.

```Haskell
minBound :: Int
-- -9223372036854775808

maxBound :: Char
-- '\1114111'

maxBound :: Bool
-- True

minBound :: Bool
-- False
```

This also makes them polymorphic constants, with a type signature of `(Bounded a) => a`.

### Show

The `Show` typeclass provides an interface for converting values to strings with its `show` function.

`show` takes an input of type `Show` and converts it to a string.

```Haskell
show 10
-- "10"

show [10, 20, 30]
-- "[10,20,30]"
```

`Show` is used a lot for printing outputs to the screen. The print functions will often only accept a string or an instance of `Show`.

### Read

The `Read` typeclass provides an interface for converting strings to values.

The function `read` takes a string input and converts it to a `Read`.

> [!Warning] Ambiguous Types
> Take this code using the `read` function.
> 
> ```Haskell
> read "4"
> ```
> 
> Trying this gives you an error.
> 
> ```
> Ambiguous type variable `a' in the constraint:
> 	  `Read a' arising from a use of `read' at \<interactive>:1:0-7
> 	  Probable fix: add a type signature that fixes these type variable(s)
>  ```
 > `read` returns a value in the `Read` typeclass, but it can't infer what type the result is. It could be an `Int`, a `Float`, a `List`, anything!
> 
> Remember, a type class is just an interface, it's not a type itself. A value cannot be of type `Read`, it can only be a type which is a member of `Read`.
> 
> We can fix this with a type annotation.
> 
> ```Haskell
> read "4" :: Int
> -- 4
> 
> read "4" :: Float
> -- 4.0
> ```

### Enum

`Enum` members are sequentially ordered types, they can be enumerated.

The best part about `Enum` members is that they can be used in list ranges.

The types in `Enum` will implement the successor `succ` and predecessor `pred` functions.

`Bool`, `Char`, `Ord`, `Int`, `Integer`, `Float` and `Double` are all in this class.

```Haskell
['a'..'e'] -- "abcde"

[LT .. GT] -- [LT,EQ,GT]

[3..7] -- [3,4,5,6,7]

succ 'B' -- 'C'
pred 4 -- 3
```
