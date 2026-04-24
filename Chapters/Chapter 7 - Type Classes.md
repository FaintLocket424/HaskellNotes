> Previous: [[Chapter 6 - Type Variables]]

> [!danger] Potential Misconception about Haskell Classes
> Note that the definition of a class in Haskell is not the same as in **Object Orientated Programming**.
> 
> In Haskell, a class is much more similar to an *Interface* in Java.

> [!Important] Definition of a Type Class
> In Haskell, a type class is an interface which specifies a set of functions which any type must implement in order to be an instance of the type class.

Put into simpler terms:

1. A type class will have functions associated with it.
2. All types which are instances of the type class must provide some implementation of each function.
3. It creates a sort of contract, saying that "Any type which is an instance of this class **will** support these methods". (This is where the similarity to OOP interfaces comes from.)

> [!NOTE] Why do we need this?
> These classes allow us to put less restrictive input types on our functions. If we have a function `add` which just sums its two inputs, it doesn't make sense to restrict the function to just `Int` or `Float`. Instead, we can say this function accepts any number, which includes `Int`, `Integer`, `Float`, `Double` etc.

Type Classes can also depend on each other, creating a sort of chain of implementation hoops that a type has a jump through to become part of a type class. If class `A` depends on class `B`, then any type hoping to become a member of type `A` must first become a member of type `B`. This is usually done because the functions in `A` use the functions in `B` and so they have to be sure they're present.

A Type Class can provide a default implementation for a function that can be overridden by the implementer.

---
## The Basic Type Classes of Haskell

Haskell implements these 9 type classes, although there are *many* more...

1. `Eq`
2. `Ord`
3. `Num`
4. `Integral`
5. `Floating`
6. `Bounded`
7. `Show`
8. `Read`
9. `Enum`

### Eq

The `Eq` typeclass provides an interface for testing equality. Any member of `Eq` implements `==` (equal to) and `/=` (not equal to).

### Ord

The `Ord` typeclass is for testing the order of items. Any member of `Ord` implements `>`, `<`, `<=`, `>=`.

Notice that `>=` and `<=` have to test for equality. They can do this because `Ord` inherits from `Eq`, so any `Ord` type must already be part of `Eq`.

### Num

The `Num` typeclass represents all types that behave like numbers. Implements of `Num` include `Int`, `Integer`, `Float`, `Double` and more.

`Num` implements many functions, most notably for now is addition `(+)`, subtraction `(-)` and multiplication `(*)`.

### Integral

The `Integral` typeclass represents whole numbers like `Int` and `Integer`.

`Integral` implements functions like `quot`, `rem`, `div`, `mod`, functions that require integer inputs basically.

> [!important] The `fromIntegral` function
> One really useful function that is implemented is `fromIntegral`, which can take an Integral and convert it to any type in `Num`. This is useful because you cannot do mathematical operations like `(+)` or `(*)` on different types. You cannot do `Int + Float` or `Float + Double`.

### Floating

The `Floating` typeclass represents floating point numbers and is implemented by `Float` and `Double`.

`Floating` provides a bunch of mathematical functions like `pi`, `exp`, `log`, `sqrt`, `(**)`, `sin`, `cos`, `tan` and loads more.

### Bounded

The `Bounded` typeclass represents types which have an explicit minimum and maximum bound.

It implements the constants `minBound` and `maxBound` that take different values depending on the type they're cast as.

> [!warning]- Advanced - Polymorphic Constants
> There's a concept in Haskell called **Polymorphic Constants**. It's where you have a constant that takes on a different value depending on its type.
> 
> Think of them less like a constant, and more like a function that takes no arguments and always returns the same value.
> 
> They're a bit like doing this in Python:
> 
> ```python
> def getTwenty(type):
> 	if type == int:
> 		return 20
> 	elif type == float:
> 		return 20.0
> 	else:
> 		raise TypeError("Invalid type for 20")
> ```
> 
> The reason they exist is because Haskell won't just automatically convert values to the right type. If you create `20` as an `Int`, it's forever an `Int` and if you put it into a function that expects a `Float`, it will not accept it.

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

### Show

The `Show` typeclass provides an interface for converting values to strings with its `show` function.

`show` takes an input of type `Show` and converts it to a string.

```Haskell
show 10
-- "10"

show [10, 20, 30]
-- "[10,20,30]"
```

`Show` is used a lot for printing outputs to the screen. The print functions will often only accept a string or an instance of `Show` (where it will just use `show` internally).

### Read

The `Read` typeclass provides an interface for converting strings to values.

The function `read` takes a string input and converts it to something which is an instance of `Read`.

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

---
## Class Constraints

So how do we actually use these type classes in our functions? We apply a **Class Constraint** to our type variables.

Take this add function we discussed previously:

```Haskell
add :: a -> a -> a
```

We need to be able to use `(+)` on the two inputs, so we have to ensure only instances of `Num` are able to be put into the function.

```Haskell
add :: (Num a) => a -> a -> a
```

This means the type in place of `a` has to be an instance of `Num`.

```Haskell
add 2 3
-- 5

add 'a' 'b'
-- <interactive>:16:1: error: [GHC-39999]  
--    • No instance for ‘Num Char’ arising from a use of ‘add’  
--    • In the expression: add 'a' 'b'  
--      In an equation for ‘it’: it = add 'a' 'b'
```

You can also have multiple class constraints.

```haskell
someFunction :: (Num a, Ord b) => a -> b
```

---
## Investigating Type Classes in GHCi

We can use the `:info` or `:i` command in `ghci` to learn more about a specific type or type class

```GHCi
ghci> :i Double  
type Double :: *  
data Double = GHC.Types.D# GHC.Prim.Double#  
       -- Defined in ‘GHC.Types’  
instance Floating Double -- Defined in ‘GHC.Internal.Float’  
instance RealFloat Double -- Defined in ‘GHC.Internal.Float’  
instance Read Double -- Defined in ‘GHC.Internal.Read’  
instance Enum Double -- Defined in ‘GHC.Internal.Float’  
instance Eq Double -- Defined in ‘GHC.Classes’  
instance Fractional Double -- Defined in ‘GHC.Internal.Float’  
instance Num Double -- Defined in ‘GHC.Internal.Float’  
instance Ord Double -- Defined in ‘GHC.Classes’  
instance RealFrac Double -- Defined in ‘GHC.Internal.Float’  
instance Real Double -- Defined in ‘GHC.Internal.Float’  
instance Show Double -- Defined in ‘GHC.Internal.Float’
```

Running it on a type shows you all the type classes the type is an instance of. We can see here that `Double` is an instance of `Floating`, `Read`, `Enum`, `Eq` etc.

```GHCi
ghci> :i Num  
type Num :: * -> Constraint  
class Num a where  
 (+) :: a -> a -> a  
 (-) :: a -> a -> a  
 (*) :: a -> a -> a  
 negate :: a -> a  
 abs :: a -> a  
 signum :: a -> a  
 fromInteger :: Integer -> a  
 {-# MINIMAL (+), (*), abs, signum, fromInteger, (negate | (-)) #-}  
       -- Defined in ‘GHC.Internal.Num’  
instance Num Double -- Defined in ‘GHC.Internal.Float’  
instance Num Float -- Defined in ‘GHC.Internal.Float’  
instance Num Int -- Defined in ‘GHC.Internal.Num’  
instance Num Integer -- Defined in ‘GHC.Internal.Num’  
instance Num Word -- Defined in ‘GHC.Internal.Num’
```

Running it on a type class shows the methods that class implements, and the types which are instances of it. We can see here that `Num` implements `(+)`, `(-)`, `(*)`, `abs` etc. and that `Double`, `Float`, `Int`, `Integer` are all instances of the class.

> Next: [[Chapter 8 - Simple Haskell Expressions]]
