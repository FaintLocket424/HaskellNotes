---
category:
  - "[[Jebediahs]]"
tags:
  - Jebediah
module: "[[Functional Programming Module]]"
lecture_covered:
  - "[[Functional Programming Lecture 9]]"
practical_covered: 
template:
  - "[[Jebediah Template]]"
TARGET DECK: COMP2221::Functional Programming
---
Applicatives are all about function application. They stem from [[Type Classes in Haskell#Functor|Functors]].

The `Applicative` typeclass, from the `Control.Applicative` module, is what represents Applicative Functors. Let's look at its definition:

```Haskell
class (Functor f) => Applicative f where
	pure :: a -> f a
	(<*>) :: f (a -> b) -> f a -> f b
```

From this we can see a few things:
1. An `Applicative` type must also be a `Functor`.
2. It implements the `pure` function that turns a single value into a `Functor`.
3. It implements this funky `(<*>)` operator which takes a _functor of functions and a functor and returns a functor..._ what? Let's take a step back.

## The limits of `fmap`

We know that functors like `Maybe` and `List` can be mapped over using `fmap`.

```Haskell
fmap (+3) (Just 5) -- Just 8
fmap (+3) [5,8] -- [8,11]
```

Here we map the function `\x -> x + 3`, written shortly as `(+3)` over `Just 5` and `[5,8]`, to get `Just 8` and `[8,11]`. So we can see how we are applying our function to each value in these Functors.

But what happens if we don't use a unary function?

```Haskell
fmap (+) (Just 5) -- Just (5+) :: (Num a) => Maybe (a -> a)
fmap (+) [5,8] -- [(5+),(8+)] :: (Num a) => [a -> a]
```

We get a Functor of functions! Isn't that Funcy. But what can we do with these?

Well, we can map over them again!

```Haskell
let a = fmap (+) (Just 5) -- Just (5+)
let b = fmap (+) [5,8] -- [(5+),(8+)]

fmap (\f -> f 3) a -- Just 8
fmap (\f -> f 3) b -- [8,11]
```

In the second `fmap`, we are taking those functions like `(+5)` and we are completing the function application by applying `3` to the other side of the function, giving us `(5+3) = 8`.

Consider how you could map `Just (3*)` over `Just 5`.

- Before, we were only dealing with functions by themselves but now our function is in a `Maybe` context.
- Just using `fmap`, we're out of luck.
- `fmap` can only work with mapping a normal function over a Functor.

## Introducing `Applicative`

This is where the `Applicative` typeclass comes in. As a reminder, it defines two functions, `pure` and `(<*>)`.

```Haskell
class (Functor f) => Applicative f where
	pure :: a -> f a
	(<*>) :: f (a -> b) -> f a -> f b
```

We can see that:

1. The `pure` function converts a value to a `Functor` containing said value. (Actually, it puts it in a minimal context containing said value)
2. The `(<*>)` function.
	- Does its type remind you of anything?
	- `(<*>) :: f (a -> b) -> f a -> f b`
	- `fmap  ::   (a -> b) -> f a -> f b`
	- Except this time our function is wrapped up in a `Functor`.

> [!important] Pronunciation of `(<*>)`
> While there is no official way to "pronounce" this operator, some call it "ap" or "apply" as it performs function application.

While `fmap` takes a function and applies it to the values inside a `Functor`, `(<*>)` takes a functor with functions in it and "extracts them", then applies them to the values in a `Functor`.

It may help to look at an instance of an `Applicative`, like `Applicative Maybe`.

```Haskell
instance Applicative Maybe where
    pure x = Just x
    Nothing <*> _ = Nothing
    (Just f) <*> something = fmap f something
```

Firstly, `pure` returns the value inside a `Just`, wrapping the value inside a `Maybe` context.

Next, the `(<*>)` implementation contains two sections.

1. The first definition is if our function is a `Nothing`, then we return `Nothing` as we can't extract a function out of `Nothing`.
2. The second definition pattern matches to the function `f` inside the `Just`, and the second parameter `something` (this could be either a `Just a` or a `Nothing`).
   We then `fmap` with this function over the `something`. In short, we've extracted the function out of the `Functor` and used it to `fmap`.

In practice, it looks like this:

```Haskell
Just (+3) <*> Just 9 -- Just 12

pure (+3) <*> Just 9 -- Just 12

Just (+3) <*> Nothing -- Nothing

Nothing <*> Just (3) -- Nothing
```

We see that `Just (+3)` is the same as `pure (+3)`, and that if either of the values is `Nothing`, then the result is `Nothing`.

We can even keep chaining together `(<*>)` for functions of multiple variables.

```Haskell
pure (+) <*> Just 3 <*> Just 5 -- Just 8

pure (+) <*> Just 3 <*> Nothing -- Nothing

pure (+) <*> Nothing <*> Just 5 -- Nothing
```

What's happening here? Let's examine the first example.

1. The `(<*>)` operator is [[Associative Property#Left Associativity|Left-Associative]], so this expression is evaluated as

	```Haskell
	(pure (+) <*> Just 3) <*> Just 5
	```

2. So first is `pure (+)`, which (in a `Maybe` context) turns the function `(+)` into a `Just (+)`, which is the same as `Just (\x y -> x + y)`. The function is now inside the `Functor`.

	```Haskell
	pure (+) == Just (+) :: (Num a) => Maybe (a -> a -> a)
	```

3. Next is `Just (+) <*> Just 3`. This applies the `3` to `(+)`, partially applying the function and producing `Just (3+)`.

	```Haskell
	Just (+) <*> Just 3 == Just (3+) :: (Num a) => Maybe (a -> a)
	```

4. Then it's `Just (3+) <*> Just 5` which applies the `5` to `(3+)`.

	```Haskell
	Just (3+) <*> Just 5 == Just 8 :: (Num a) => Maybe a
	```

And there we go, we arrive at `Just 8`. This pattern can be repeated for a function of any number of arguments, continually partially applying the function until we get a result.

## Applicative Laws 

Types that are `Applicative` must also satisfy the applicative laws. These laws guarantee that any well-typed expression using `pure` and `<*>` can be written in applicative style.

#### Identity

Applying the identity function `id`, lifted into the `Applicable` context, over a `Functor` should return the original structure, unchanged.

```Haskell
pure id <*> v == v
```

```Haskell
pure id <*> Just 8
-- Just (\x -> x) <*> Just 8
-- Just 8
```

#### Composition 

Composing functions within the applicative context should be equivalent to composing functions outside the context and then lifting the result.

```Haskell
pure (.) <*> u <*> v <*> w == u <*> (v <*> w)
```

This will compose `u` and `v` together within the applicative context to get effectively `u . v` in the context. We then apply this function to `w`.

And on the other side we are simply applying `v`, then applying `u`. Which should be the same as applying `u . v`.

```Haskell
pure (.) <*> Just (+3) <*> Just (*2) <*> Just 8
-- Just ((+3) . (*2)) <*> Just 8
-- Just 19

Just (+3) <*> (Just (*2) <*> Just 8)
-- Just (+3) <*> Just 16
-- Just 19
```

#### Homomorphism

Applying a function `f` to a value `x` _after_ lifting both into the applicative context should be the same as lifting the result of applying `f` to `x` directly. It ensures that `pure` [[Distributive Property|Distributes]] over function application.

```Haskell
pure f <*> pure x == pure (f x)
```

```Haskell
Just (+3) <*> Just 8
-- Just 11

Just (8+3)
-- Just 11
```

#### Interchange

```Haskell
u <*> pure y == pure ($ y) <*> u
```

If you have a function in a context `u` and a value in a context `pure y`, it shouldn't matter in which order you apply them (as long as you adjust for the types).

```Haskell
Just (+3) <*> pure 7
-- Just (7+3)
-- Just 10

pure ($ 7) <*> Just (+3)
-- Just ((+3) 7)
-- Just 10
```

The notation is a little hard to interpret here, let me try to explain.

Recall that the type of `<*>` is

```Haskell
(<*>) :: f (a -> b) -> f a -> f b
```

It's `functor of functions <*> functor of values -> functor of outputs`.

On the left hand side of the interchange rule, we have `u <*> pure y`, where `u` is our functor of functions, and `pure y` is our functor of values.

On the right hand side is where the syntax gets strange, and it helps to know what the [[Function Application Operator in Haskell|$ Operator]] does in Haskell.

`($ y)` partially applies the function application operator `$` such that it becomes a function that, when provided with a function, will apply `y` to it. You can think of it as

```Haskell
($) :: (a -> b) -> a -> b

($ y) == \f -> f y
```

Hence, `pure ($ y)` _is_ in fact a `Functor` of functions. It's functions that take a function as their input.

So, when we do `pure ($ y) <*> u`, we're applying a function to a value (the values just happen to be functions). 

## The Applicative Style 

The concept of taking `<*>` and using it to apply values to functions is known as the "Applicative Style".

We can take a function like `(a -> b)` that doesn't expect values in any context and lift it to work with values in applicative context `(f a -> f b)`.

```Haskell
pure f <*> x <*> y <*> z
```

Consider that `pure f <*> x` is the same as `fmap f x`.

```Haskell
pure (+3) <*> Just 3
-- Just 6

fmap (+3) (Just 3)
-- Just 6
```

Knowing this, we can instead write our applicative style as 

```Haskell
fmap f x <*> y <*> z
```

Or, we can write `fmap` infix.

```Haskell
f `fmap` x <*> y <*> z
```

We're getting close to the standard `f x y z` notation for function application. The missing piece is `<$>`.

```Haskell
f <$> x = fmap f x
```

It's literally just an infix version of `fmap`.

So, finally we can write our applicative style as

```Haskell
f <$> x <*> y <*> z
```


> [!Example]
> Take the function `f` which sums 3 numbers.
> 
> ```Haskell
> f x y z = x + y + z
> ```
> 
> If we wanted to add `4`, `1` and `8` then we can do that.
> 
> ```Haskell
> f 4 1 8
> -- 4 + 1 + 8
> -- 13
> ```
> 
> But what about if we wanted to add `Just 4`, `Just 1` and `Just 8`. We can do that with the same function by using the applicative style.
> 
> ```Haskell
> f <$> Just 4 <*> Just 1 <*> Just 8
> -- Just 13
> ```
> 
> Or we could add `[4]`, `[1]` and `[8]`.
> 
> ```Haskell
> f <$> [4] <*> [1] <*> [8]
> -- [13]
> ```
> 
> Lists are interesting because they compute all possibilities.
> 
> ```Haskell
> f <$> [4] <*> [1,5] <*> [8]
> -- [13,17]
> ```
> 
> More on lists in the next section.

## Lists as Applicatives 

Recall the type of `<*>`.

```Haskell
(<*>) :: f (a -> b) -> f a -> f b
```

If we specify our applicative `f` to be a list, it looks like this.

```Haskell
(<*>) :: [a -> b] -> [a] -> [b]
```

So, we're taking a list of functions and applying it to a list of values. This is more difficult than with `Maybe` because we could have 0, 1, or many functions and 0, 1 or many values to apply them to.

To combat this, `<*>` over lists is implemented using list comprehension, where every function is applied to every value.

```Haskell
[(*0),(+100),(^2)] <*> [1,2,3]
-- [0,0,0,101,102,103,1,4,9]
```

With binary functions, we can apply two lists to them.

```Haskell
[(+),(*)] <*> [1,2] <*> [3,4]
-- [4,5,5,6,3,4,6,8]
```

Let's look closer at what's going on in that last example.

Because `<*>` is [[Associative Property#Left Associativity|Left Associative]], it computes `[(+),(*)] <*> [1,2]` first.

Since each function is applied to each value, we get:

```Haskell
[(1+),(2+),(1*),(2*)]
```

Notice that the first function was applied to all values, then the second.

Then it applies `[3,4]` to the result.

```Haskell
[(1+),(2+),(1*),(2*)] <*> [3,4]
-- [4,5,5,6,3,4,6,8]
```

[!Important] Lists as non-deterministic computations
You can think of `100` or `"hello"` are deterministic, as they only have a single value.

And you can think of lists like `[1,2,3]` as non-deterministic, as if they could be either `1`, `2` or `3`.

So, when you do

```Haskell
let a = [1,2,3]
let b = [4,5,6]
(+) <$> a <*> b
-- [5,6,7,6,7,8,7,8,9]
```

You are computing all the possible values this computation could take. As in, if list `a` is `1` and list `b` is `4`, then the result would be `5`. But, if list `a` is `1` and list `b` is `5`, then the result would be `6`.

Like a superposition of values, it holds all possible values until 

## IO as an Applicative

We already know that [[Functors in Haskell#IO as a Functor|IO is a Functor]].

```Haskell
instance Functor IO where
	fmap :: (a -> b) -> IO a -> IO b
	
	fmap f action = do
		result <- action
		return (f result)
```

`fmap` will apply a function to the result of an IO Action and return an IO Action that does nothing but contains the value.

As an `Applicative`, `IO` looks like this.

```Haskell
instance Applicative IO where
    pure = return
    a <*> b = do
        f <- a
        x <- b
        return (f x)
```

`pure` takes values and puts them in a minimal context, and for `IO` that's `return`, an IO Action that does nothing but contains a value.

If we take `<*>` and look at it's definition in an `IO` context we get:

```Haskell
(<*>) :: IO (a -> b) -> IO a -> IO b
```

This is a little strange but here's an explanation.

The first parameter is an IO Action that, when performed, contains a function.

The second parameter is an IO Action that, when performed, contains a single value.

It will then apply the function in the first IO Action to the value in the second IO Action to produce a third IO Action that contains the result.

Specifically in the implementation, we use [[IO in Haskell#`do` Syntax|do Syntax]] to join multiple IO Actions together.

1. We perform the first IO Action to get the function as `f`.
2. We then perform the second IO Action to get the value as `x`.
3. We then apply `x` to `f` and put it inside an IO Action with `return`. This is the output of `(<*>)`.

[!Example]
Consider this code.

```Haskell
myAction :: IO String
myAction = do
	a <- getLine
	b <- getLine
	return $ a ++ b
```

When this IO Action is performed, it will get two lines from the user, and return them concatenated. This can also be written in the applicative style.

```Haskell
myAction :: IO String
myAction = (++) <$> getLine <*> getLine
```

The first part `(++) <$> getLine` is the same as `fmap (++) getLine` remember. This will perform the IO Action `getLine` and apply the function `(++)` to it. But `(++)` is a binary function `[a] -> [a] -> [a]` and so it's partially applied here and so instead of returning a value, it returns a function in context `IO ([String] -> [String])`.

We then apply this function to the second IO Action, performing the action and concatenating the result with the first.

## Functions as Applicatives 

We've seen that [[Functors in Haskell#Functions as Functors|Functions are Functors]], but they are also applicatives. Granted, this is barely ever used outside of code golf.

Just like Functors, it's the `(->) r` type specifically that is an applicative, to give it a kind of `* -> *`.

```Haskell
instance Applicative ((->) r) where
    pure x = (\_ -> x)
    f <*> g = \x -> f x (g x)
```

The minimal context of a function that could contain a value would be a result, a function which ignores it's parameter and just returns said value.

```Haskell
let f = pure 3 :: r -> Int
f "blah"
-- 3
f 10
-- 3
```

The implementation of `<*>` is a bit odd but we can work it out.

Recall that the type of `(<*>)` is

```Haskell
(<*>) :: f (a -> b) -> f a -> f b
```

If `f` is a function `(->) r` then we can rewrite this as

```Haskell
(<*>) :: ((->) r (a -> b)) -> ((->) r a) -> ((->) r b)
```

If we then rewrite it using infix notation instead it'll look clearer.

```Haskell
(<*>) :: (r -> (a -> b)) -> (r -> a) -> (r -> b)
```

So our parameters are:

1. A function which take a value of type `r` and produces a function from `a` to `b`.
2. A function from type `r` to type `a`.

We then produce a function which maps from type `r` to type `b`.

Examples will make it more clear what this code does.

> [!Example] Example 1
> Take this example.
> 
> ```Haskell
> let f = (+) <$> (+3) <*> (*100)
> ```
> 
> We're taking a function `(+)` and applying two applicatives to it, `(+3)` and `(*100)`, just like we usually do.
> 
> But this time, the result is a function.
> 
> ```Haskell
> f :: (Num a) => a -> a
> ```
> 
> If we pass some values into it we can see what it is doing.
> 
> ```Haskell
> f 5
> -- 508
> 
> f 3
> -- 306
> 
> f 0.25
> -- 28.25
> ```
> 
> From these results we can see what it's doing. It is applying the value, eg `5`, to both the functions `(5+3)` and `(5*100)`, then applying the initial function `(+)` to the result. `(5+3)+(5*100)` to get the result `508`.

> [!attention] But what's really happening?
> Take the expression
> 
> ```Haskell
> (+) <$> (+3) <*> (*100) $ 5
> ```
> 
> Well, first it's going to evaluate `(+) <$> (+3)`. Recall that `f <$> g` is the same as `fmap f g`, which over functions is implemented as:
> 
> ```Haskell
> fmap f g = (\x -> f (g x))
> ```
> 
> We make a function which will take a single value, applies the second function, then applies the first function.
> 
> Putting this into the context of our example we can see
> 
> ```Haskell
> fmap (+) (+3) = \x -> (+) ((+3) x)
> ```
> 
> If we move the functions to infix it makes more sense.
> 
> ```Haskell
> \x -> \y -> (x + 3) + y
> ```
> 
> Because the `(+)` is being partially applied, we're actually returning a binary function which is expecting both the initial value `x`, and then the value to put in the second half of the `(+)`.
> 
> We then take this function and apply `<*> (*100)` to it. Recall that, over functions, `(<*>)` is implemented as:
> 
> ```Haskell
> f <*> g = \x -> f x (g x)
> ```
> 
> Here, `f` is our `\x -> \y -> (x + 3) + y` and `g` is `(*100)`.
> 
> First we apply `x` to `g` to get `(x*100)`.
> Then, we apply `x` and `(x*100)` to `f`.
> 
> ```Haskell
> f <*> g = \x -> (x+3) + (x*100)
> ```
> 
> And now if we apply this function to the value `5`, we get `508` as expected.

In short, when you do `f <$> g <*> h <*> j <*> ...`, you're really doing:

```Haskell
\v -> f (g v) (h v) (j v) ...
```

You're making a function that will call each function with the same value, then put all of those outputs into the "main" function.
