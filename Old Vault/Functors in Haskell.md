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
## The Functor Type Class

The `Functor` type class represents mappable types, with lists `[]` probably being the one you're most familiar with.

Take a look at the definition of a `Functor`.

```Haskell
class Functor f where
	fmap :: (a -> b) -> f a -> f b
```

Looking at the definition of `fmap` seems to provide more questions than answers.

- Its first argument is a function that maps from type `a` to type `b`.
- Its second argument is of type `f a`.
- And it returns a value of type `f b`.

But what does it mean for something to be of type `f a`? Well, `f` is a [[Data Types in Haskell#Concrete Types vs Type Constructors|Type Constructor]]!

This means that it's types like `Maybe` and `[]` (lists) which are functors, not `Int` or `Char`. It wouldn't really make much sense for something to have type `Int Int`.

In short, `fmap` maps values in a functor `f a` to a functor `f b` using the function `(a -> b)`.

> [!example] Example with Lists
> If `fmap` sounds a lot like `map` on lists, you'd be right. In fact, when you call `fmap` on a list, it just calls `map`.
> 
> ```Haskell
> instance Functor [] where
> 	fmap :: (a -> b) -> [a] -> [b]
> 	fmap = map
> ```
> 
> If we put the type of `fmap` into a list context, we see it maps from a list of type `a` to a list of type `b` using the provided function `(a -> b)`.
> 
> ```Haskell
> fmap (*2) [1,2,3]
> -- [2,4,6]
> ```

> [!Example] Example with Maybe
> Another class which implements `Functor` is `Maybe`.
> 
> ```Haskell
> data Maybe a = Nothing | Just a
> ```
> 
> When we `fmap` a function over a `Maybe`, it will apply that function to the value inside a `Just`, or return `Nothing` if it a nothing.
> 
> ```Haskell
> instance Functor Maybe where
>     fmap f (Just x) = Just (f x)
>     fmap f Nothing = Nothing
> ```
> 
> ```Haskell
> fmap (*2) (Just 200)
> -- Just 400
> ```

You will notice from both of these examples that a `Functor` can be like a box that contains values, and `fmap` just applies a function to those values.

## Functor Laws 

> [!important] Functor Laws
> Functors must obey the functor laws:
> 
> 1. Mapping the identity function over a structure should return the original structure.
> 2. Mapping over a container should [[Distributive Property|distribute]] over function composition. It shouldn't matter if it's done in 2 passes or 1.
> 
> ```Haskell
> -- 1
> fmap id c == c
> 
> -- 2
> fmap f (fmap g c) == fmap (f . g) c
> ```

## Implementing Functor in a Type Class

Say we wanted to implement a binary tree in Haskell. This is one way of doing so:

```Haskell
data BinaryTree a = Leaf a | Node a (BinaryTree a) (BinaryTree a) deriving (Eq, Show)
```

But then what if we wanted to implement `Functor` on it? What would that look like?

It would probably involve applying the given function to each leaf in the tree.

The leaves are a simple case as we can apply the function to its value.
The nodes are more complex as we have to apply the function to its value, then `fmap` over the left and right subtrees.

```Haskell
instance Functor BinaryTree where
	fmap f (Leaf a)     = Leaf (f a)
	fmap f (Node a l r) = Node (f a) (fmap f l) (fmap f r)
```

## IO as a Functor

Recall [[IO in Haskell]], where we think of IO in terms of _IO Actions_, where these can be thought of as little boxes that contain some real world data. This makes it sound a lot like a Functor, as the values we get are contained in this IO context.

Here is how Functor is implemented in Haskell.

```Haskell
instance Functor IO where
	fmap :: (a -> b) -> IO a -> IO b
	
	fmap f action = do
		result <- action
		return (f result)
```

We can see that when `fmap` is called on the IO Action, it:

1. Performs the IO Action and stores the result in `result`.
2. Then, applies the given function to `result`.
3. And finally creates a dummy IO Action containing this result.

> [!Example] Example 1
> We can use a function like `reverse` which has type `String -> String` and `fmap` it over an IO Action like `getLine` which is an `IO String`.
> 
> ```Haskell
> main = do
> 	line <- fmap reverse getLine
> 	putStrLn $ "You said " ++ line ++ " backwards!"
> 	putStrLn $ "Yes, you really said " ++ line ++ " backwards!"
> ```
> 
> This is just how we can `fmap (*2)` to `Just 10` to get a `Just 20`.

> [!Example] Example 2
> A more complex example would be applying multiple functions using function composition.
> 
> ```Haskell
> import Data.Char
> import Data.List
> 
> main = do
> 	line <- fmap (intersperse '-' . reverse . map toUpper) getLine
> 	putStrLn line
> ```
> 
> Here, we are creating a function using function composition which will make the string uppercase, then reverse it, then put dashes between all the letters.

## Functions as Functors

Recall that `(->)` is a [[Data Types in Haskell#(->)|Type Constructor]], which takes two types and produces a concrete type that describes a function, and that it has a kind of `* -> * -> *`.

Also recall that for a type to be made a `Functor`, it must have a kind of `* -> *`. So, we could make `(->) r` a functor, but what would that look like?

Well, if we had a function `r -> a` then that would map from type `r` to type `a`, so `(->) r` represents functions which map from type `r` to _something_.

Putting `(->) r` into the `fmap` function gives us more information.

```Haskell
fmap :: (a -> b) -> (r -> a) -> (r -> b)
```

So it appears as though we are taking a function from `r` to `a`, and turning it into a function from `r` to `b`, via a function `a -> b`. Sounds a lot like were taking the output of `r -> a` and piping it into the input of `a -> b` to get a function `r -> b`, which is the basis for *function composition*!

And when we look at the definition of `fmap` over functions you'll see that it is.

```Haskell
instance Functor ((->) r) where
	fmap f g = (\x -> f (g x))
```

Yep, that's function composition alright. In fact, this can be written as

```Haskell
instance Functor ((->) r) where
	fmap = (.)
```

## Lifting Functions 

There's a different way that one can think about `fmap`, in terms of lifting functions into a `Functor` context.

Look at the definition of `fmap` again.

```Haskell
fmap :: (a -> b) -> f a -> f b
```

Remember that, due to function application being curried, if we only give `fmap` the `(a -> b)` function then we get a function which takes `f a` and returns `f b`.

So we can rewrite `fmap` as

```Haskell
fmap :: (a -> b) -> (f a -> f b)
```

Now you can think of `fmap` as taking a function `(a -> b)` and _lifting_ it into a functor context as `(f a -> f b)`.

%%

In Haskell, if a type is an instance of `Functor`, what does that tell you about the nature of the type? #flashcard 
`Functor` represents mappable types; data structures where each element can be transformed in a systematic way using a higher order function.
<!--ID: 1738766313426-->

In programming, describe the functor laws. #flashcard 
1. Mapping the identity function over a structure should return the original structure.
2. Mapping over a container should distribute over function composition. It shouldn't matter if it is done in 2 passes or 1.
<!--ID: 1738766313429-->

In Haskell, what standard types are members of `Functor`? #flashcard 
- `List`
- `Maybe`
- `Either a`
<!--ID: 1738766313430-->

In Haskell, what makes the types which are instances of `Functor` different to those that are instances of `Eq`, `Ord` or `Num`? #flashcard 
`Functor` is applied to type constructors, whereas others like `Eq`, `Ord` or `Num` are applied to concrete types.
<!--ID: 1740163985995-->

In Haskell, what is the type definition of the `fmap` function for functors? #flashcard 
```Haskell
fmap :: (a -> b) -> f a -> f b
```
<!--ID: 1740322362280-->

In Haskell, what kind does a type have to be in order to be an instance of `Functor`? #flashcard 
```Haskell
* -> *
```
It must be a type constructor that takes a single type.
<!--ID: 1740322424886-->

In Haskell, describe how `IO` is implemented has as a `Functor`. #flashcard 
When `fmap` is called on an IO Action, it will perform that IO Action, then apply the given function to the result of the IO Action, then return an IO Action containing the modified result.
```Haskell
fmap f action = do
	result <- action
	return (f result)
```
<!--ID: 1740322945883-->

In Haskell, what can we do to types with kinds higher than `* -> *` in order to apply `Functor` to them? #flashcard 
We partially apply the type constructor until it only has one type parameter left.
- `Either a`
- `(->) r`
<!--ID: 1740322487165-->

In Haskell, describe how would you apply `Functor` to a `BinaryTree a` structure where values are either `Leaf a` or `Node a (BinaryTree a) (BinaryTree a)` (left and right subtrees)? #flashcard 
You would `fmap` a function over the `BinaryTree` where:
- If the value is a leaf, apply the function to the value in the leaf.
- If the value is a node, apply the function to the value in the node, then `fmap` that function over the left and right subtrees.
<!--ID: 1738766369424-->

In Haskell, describe how functions are implemented as `Functor`. #flashcard 
It's equivalent to function composition, `(.)`.
We can apply a function `(a -> b)` to `(r -> a)` to produce a function `(r -> b)`.
```Haskell
instance Functor ((->) r) where
	fmap f g = (\x -> f (g x))
```
<!--ID: 1740322945886-->

In Haskell, describe how `fmap` can be interpreted as "lifting" functions. #flashcard 
The type definition of `fmap` can be interpreted as taking a function `(a -> b)` and lifting it into the functor context as `(f a -> f b)`.
```Haskell
fmap :: (a -> b) -> (f a -> f b)
```
<!--ID: 1740322945888-->

%%
