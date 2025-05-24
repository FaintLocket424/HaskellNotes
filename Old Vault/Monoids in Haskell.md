---
category:
  - "[[Jebediahs]]"
tags:
  - Jebediah
module: "[[Functional Programming Module]]"
lecture_covered: 
practical_covered: 
template:
  - "[[Jebediah Template]]"
TARGET DECK: COMP2221::Functional Programming
---
A monoid is a type with a binary function which satisfies some rules:

1. The binary function is [[Closed]].
2. The binary function is [[Associative Property|Associative]].
3. It has an identity element.

> [!Danger] If you're a mega nerd
> Monoids are technically Semigroups with identity, which themselves are Magmas with Associativity.

That's a lot of words so here's an explanation.

1. A binary function is a function that takes two inputs, like `*` or `+`.
2. A function is closed if it produces a value from the same set as its inputs. So, `Int + Int -> Int`, therefore addition over integers is closed. But division over integers isn't because `Int / Int -> Float`.
3. A function is associative if the order of operations doesn't matter `(10 + 20) + 30 == 10 + (20 + 30)`.
4. An identity element is one where the function has no effect. So, `x * 1 = x`, therefore `1` is the identity element of `*`.

> [!Example] Putting this in Haskell terms
> Lists `[a]` form a monoid with the function `++`. It satisfies all the requirements for a monoid.
> 
> 1. The function `++` is closed. `[a] ++ [a] -> [a]`.
> 2. The function `++` is associative. 
> 	- `("test" ++ "test2") ++ "test3" == "testtest2test3"`
> 	- `"test" ++ ("test2" ++ "test3") == "testtest2test3"`
> 3. The identity element is the empty list `[]`.
> 	- `"test" ++ [] == "test"`
> 	- `[] ++ "test" == "test"`

## The Implementation 

The `Monoid` type class is defined in `Data.Monoid`.

```Haskell
class Monoid m where
	mempty :: m
	mappend :: m -> m -> m
	mconcat :: [m] -> m
	mconcat = foldr mappend mempty
```

The first thing to note is the [[Data Types in Haskell#Kinds|Kind]] of `m`, which is `*`. This tells us that only concrete types like `Int` or `Maybe a` can be members of `Monoid`. Types like `Foldable` or `Maybe` cannot.

The first function (not really a function, more like a polymorphic constant) is `mempty` which returns the identity element of the monoid.

The next is `mappend` which is the binary function of the monoid. Note that the name `mappend` is a bit bad because it implies some sort of `append` which in the majority of monoids isn't the case. Just think of it as combining two monoids.

The final function is `mconcat`, which reduces a list of monoids into a single value. If that sounds like a fold, that's because it kind of is. In fact, it comes with a default implementation which folds the list, using `mconcat` between the items.

> [!Important] The Monoid Laws, In Haskell
> 
> We have the laws of monoids, specifically associativity and the existence of an identity element. Here they are expressed in terms of the `Monoid` type class.
> 
> ```Haskell
> -- Identity element
> mempty `mappend` x = x
> x `mappend` mempty = x
> 
> -- Associativity
> (x `mappend` y) `mappend` z = x `mappend` (y `mappend` z)
> ```
> 
> While it's possible to create types in Haskell which do not follow these laws, it's not advised as it won't be a very useful monoid.

## Lists as Monoids

As mentioned earlier, lists are monoids over the `++` function, with `[]` being the identity element.

```Haskell
instance Monoid [a] where
	mempty = []
	mappend = (++)
```

Notice that we made `[a]` an instance of `Monoid`, unlike [[Applicatives in Haskell|Applicative]] which uses `[]`. This is due to the [[Data Types in Haskell#Kinds|Kind]] of `Monoid` being `*`, requiring a concrete type.

Testing out the monoid, everything works as expected.

```Haskell
[1,2,3] `mappend` [4,5,6]
-- [1,2,3,4,5,6]

("one" `mappend` "two") `mappend` `three`
-- "onetwothree"

"one" `mappend` ("two" `mappend` "three")
-- "onetwothree"

"pang" `mappend` mempty
-- "pang"

mconcat [[1,2],[3,6],[9]]
-- [1,2,3,6,9]

mempty :: [a]
-- []
```

## Numbers as Monoids 

There's a few different ways for numbers to behave like Monoids, namely as products `*` and sums `+`.

### Product 

The product of two numbers behaves like a monoid. It satisfies all the rules required:

1. The binary function `*` is closed, `a * a -> a`.
2. `*` is also associative, with `a * (b * c) = (a * b) * c`.
3. `*` has an identity element, `1`, where `x * 1 = x`.

Haskell encapsulates this in the monoid `Product`, found in `Data.Monoid`. It is defined like this:

```Haskell
newtype Product a = Product { getProduct :: a }
	deriving (Eq, Ord, Read, Show, Bounded)
```

It's a `newtype` wrapper with a single parameter.

It's defined as a monoid like this:

```Haskell
instance (Num a) => Monoid (Product a) where
	mempty = Product 1
	Product x `mappend` Product y = Product (x * y)
```

`mempty` returns the identity element `1`, wrapped in the `Product` type.

`mappend` takes the numbers out of the `Product` type, multiplies them, then puts that result back into a `Product` type.

Also note the class constraint that the types in Product have to be a `Num` in order to be a monoid.

It's a bit clunky to use because of all the type wrapping and unwrapping we have to do, but it does work.

```Haskell
getProduct $ Product 3 `mappend` Product 9
-- 27

getProduct $ Product 3 `mappend` mempty
-- 3

getProduct $ Product 3 `mappend` Product 4 `mappend` Product 2
-- 24

getProduct . mconcat . map Product $ [3,4,2]
-- 24
```

### Sum

The sum of two numbers also behaves likes a monoid. It satisfies all the rules required:

1. The binary function `+` is closed, `a + a -> a`.
2. `+` is also associative, with `a + (b + c) = (a + b) + c`
3. `+` has an identity element, `0`, where `x + 0 = x`.

Haskell encapsulates this in `Sum`, found in `Data.Monoid`. It's defined like this:

```Haskell
newtype Sum a = Sum { getSum :: a }
	deriving (Eq, Ord, Read, Show, Bounded)
```

It's defined as a monoid like this:

```Haskell
instance (Num a) => Monoid (Sum a) where
	mempty = Sum 0
	Sum x `mappend` Sum y = Sum (x + y)
```

`mempty` returns the identity element, `0`, wrapped in the `Sum` type.

`mappend` takes the numbers out the `Sum` wrappers, sums them, then wraps the result back into a `Sum`.

Also note the class constraint. Sum has to contain an integer in order for it to be a monoid.

```Haskell
getSum $ Sum 2 `mappend` Sum 9
-- 11

getSum $ mempty `mappend` Sum 3
-- 3

getSum . mconcat . map Sum $ [1,2,3]
-- 6
```

## Booleans as Monoids 

