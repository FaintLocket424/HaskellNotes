> Previous: [[Chapter 9 - Functions in Haskell]]

In the previous chapter, you learnt how to define a function in Haskell.

```Haskell
double x = 2 * x
```

But what if we wanted the function to behave differently depending on the value or shape of the input? This is where pattern matching comes in.

You can think of it like a really smart if-else chain.

---
## Pattern Matching Literals

Look at this example function that describes a number:

```Python
def describeNumber(n):
	if n == 0:
		return "It's zero!"
	elif n == 1:
		return "It's one!"
	else:
		return "It's some other number: " + str(n)
```

If the input is `0`, we output `"It's a zero!"`. If it's `1`, we output `"It's one!"` and if it's anything else, we print `"It's some other number: " + str(n)`.

In Haskell, this can be achieved using Pattern Matching on the function arguments.

```Haskell
describeNumber :: Int -> String
describeNumber 0 = "It's zero!"
describeNumber 1 = "It's one!"
describeNumber n = "It's some other number: " ++ show n
```

And let's see it in action:

```Haskell
describeNumber 0
-- "It's zero!"

describeNumber 1
-- "It's one!"

describeNumber 5
-- "It's some other number: 5"
```

> [!warning] Incomplete Patterns!
> There is nothing stopping you from creating a pattern matching function with incomplete patterns.
> 
> What I mean by this is that you could have inputs which don't match any of your patterns.
> 
> ```Haskell
> describeNumber :: Int -> String
> describeNumber 0 = "It's zero!"
> describeNumber 1 = "It's one!"
> ```
> 
> What happens if we try to input 3?
> 
> ```Haskell
> describeNumber 3
> -- *** Exception: Non-exhaustive patterns in function describeNumber
> ```
> 
> Generally this is fixed by having specific edge-cases first, then a general "catch-all" case at the end.

> [!danger] Important: Order Matters!
> If we swap around some of the patterns in `describeNumber`, we get some unexpected behaviour.
> 
> ```Haskell
> -- WRONG ORDER EXAMPLE
> describeNumberWrong :: Int -> String
> describeNumberWrong n = "It's some other number: " ++ show n
> describeNumberWrong 0 = "It's zero!"
> describeNumberWrong 1 = "It's one!"
> ```
> 
> Here's what it does:
> 
> ```Haskell
> describeNumberWrong 0
> -- "It's some other number: 0"
> 
> describeNumberWrong 1
> -- "It's some other number: 1"
> 
> describeNumberWrong 5
> -- "It's some other number: 5"
> ```
> 
> Why does it do this? Because patterns are evaluated top-to-bottom. So in this case, everything is being caught by the wildcard.
> 
> So you have to make sure that your more general patterns are lower.

---

## The Wildcard Pattern

Sometimes, you don't care what the value that you're pattern matching is. In that case, you can use the wildcard `_`. (It's just a normal variable name that is used to indicate unused.)

```Haskell
describeNumber' :: Int -> String
describeNumber' 0 = "It's zero!"
describeNumber' 1 = "It's one!"
describeNumber' _ = "It's some other number (Don't care lmao)."
```

---
## Pattern Matching Tuples

Remember tuples? `(Int, String)` etc.

We can write a function that accepts a tuple, and pattern matches against it.

> [!info] Tuple patterns only match the exact size of the pattern, so a pattern of `(x,y)` will only match size-2 tuples.

```Haskell
getFirst :: (a, b) -> a
getFirst (x, y) = x

getFirst (2, 'a')
-- 2

getFirst (1, 2, 3)
--    * Couldn't match expected type: (a, b0)
--                  with actual type: (a0, b1, c0)
--    * In the first argument of `getFirst', namely `(1, 2, 3)'
--      In the expression: getFirst (1, 2, 3)
--      In an equation for `it': it = getFirst (1, 2, 3)
```

```Haskell
getSecond :: (a, b) -> b
getSecond (x, y) = y

getSecond (2, 'a')
-- 'a'

getSecond (1, 2, 3)
--    * Couldn't match expected type: (a0, b)
--                  with actual type: (a1, b0, c0)
--    * In the first argument of `getSecond', namely `(1, 2, 3)'
--      In the expression: getSecond (1, 2, 3)
--      In an equation for `it': it = getSecond (1, 2, 3)
```

```Haskell
isOrigin :: (Int, Int) -> Bool
isOrigin (0, 0) = True
isOrigin (_, _) = False

isOrigin (3, 4)
-- False

isOrigin (0, 0)
-- True

isOrigin (0, 0, 0)
--    * Couldn't match expected type: (a10, a20)
--                  with actual type: (a0, b0, c0)
--    * In the first argument of `isOrigin', namely `(0, 0, 0)'
--      In the expression: isOrigin (0, 0, 0)
--      In an equation for `it': it = isOrigin (0, 0, 0)
```

In these examples, when we use something like `getFirst (x, y)`, what happens is that the first value in the tuple gets bound to `x`, and the second value gets bound to `y`. It's like doing this in Python:

```Python
def getFirst(t):
	x = t[0]
	y = t[1]
	
	return x
```

And note that there's nothing special about the names `x` and `y`, we can choose literally anything we want, they're just variable names.

> Next: [[Chapter 11 - Function Guards]]
