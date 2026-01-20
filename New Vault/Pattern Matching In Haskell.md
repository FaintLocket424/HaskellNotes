So far, when we've written functions, they've been defined in a single way:

```Haskell
addOne :: Int -> Int
addOne x = x + 1
```

The function does a single thing, take a value $x$ and return $x+1$.

But what if we wanted the function to behave differently depending on the value or shape of the input? This is where Pattern Matching comes in.

You can think of it like a really smart if-else chain, a way of implementing this factorial function from right at the start:

```Python
def factorial(n):
	if n == 1:
		return 1
	else:
		return n * factorial(n-1)
```

---

## Pattern Matching Literals

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

If we put this in the concept of another language like Python, it could look something like this:

```Python
def describeNumber(n):
	if n == 0:
		return "It's zero!"
	elif n == 1:
		return "It's one!"
	else:
		return "It's some other number: " + str(n)
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
> describeNumberWrong _ = "It's some other number."
> describeNumberWrong 0 = "It's zero!"
> describeNumberWrong 1 = "It's one!"
> ```
> 
> Here's what it does:
> 
> ```Haskell
> describeNumberWrong 0
> -- "It's some other number."
> 
> describeNumberWrong 1
> -- "It's some other number."
> 
> describeNumberWrong 5
> -- "It's some other number."
> ```
> 
> Why does it do this? Because patterns are evaluated top-to-bottom. So in this case, everything is being caught by the wildcard.
> 
> So you have to make sure that your more general patterns are lower.

---

## The Wildcard Pattern

Sometimes you don't care what the value is in your pattern matching, in which case, use the wildcard `_`.

```Haskell
describeNumber' :: Int -> String
describeNumber' 0 = "It's zero!"
describeNumber' 1 = "It's one!"
describeNumber' _ = "It's some other number (I don't care which)."
```

---

## Pattern Matching on Lists

Pattern matching lists is probably the most important data type for pattern matching, and it becomes incredibly useful.

There are 3 main patterns to use:

```Haskell
[] -- An empty list

[a, b] -- A 2-element list made up of [a, b]
[c, d, e] -- A 3-element list made up of [c, d, e]

(x:t) -- An n-element list (n > 1 here) where the first element is bound to x, and the rest of the list is t.
(x:y:t) -- An n-element list (n > 2 here) where the first element is x, the second is y, and the rest of the list is t.
```

Let's look at some examples.

> [!example] Example 1 - Safe Head
> We saw in the list section that the `head` function (which returns the first element in a list), will throw an exception if passed an empty list.
> 
> What if we created a function, `safeHead` which returns a `Maybe a` instead, with `Just a` for the head value, or `Nothing` if the list is empty.
> 
> ```Haskell
> safeHead :: [a] -> Maybe a
> safeHead []     = Nothing
> safeHead (x:xs) = Just x
> ```
> 
> We could have just made `safeHead` call `head` if the list isn't empty, but this way uses more pattern matching, which is what I'm trying to demo here.

> [!example] Example 2 - Length
> 
> What about implementing our own list length function? Well, we could use pattern matching to recursively compute the length.
> 
> ```Haskell
> length :: [a] -> Int
> length []     = 0
> length (_:xs) = 1 + length xs
> ```
> 
> First, notice that we make the equals signs be in the same place, it's just a style thing.
> 
> Second, notice how no matter what list goes into this, it will always be broken down until it reaches that base case. We'll touch on this more in the recursion section.

> [!example] Example 3 - Adding Two Elements
> 
> Say we wanted a function which parsed commands, like `"set randomTickSpeed 20"`.
> 
> We could do this by using the `words` function which splits a `String` into it's words in a `[String]`.
> 
> ```Haskell
> parseCommand :: [String] -> Bool
> parseCommand ["set", key, value] = True
> parseCommand ["get", key] = True
> parseCommand ["help"] = True
> parseCommand [] = False
> parseCommand _ = False
> 
> parseCommand $ words "set randomTickSpeed 20"
> -- True
> 
> parseCommand $ words "get randomTickSpeed"
> -- True
> 
> parseCommand $ words "get randomTickSpeed this is not quite what it was expecting"
> -- False
> 
> parseCommand $ words []
> -- False
> ```

---

## Pattern Matching Tuples

Remember tuples? `(Int, String)` etc.

We can write a function that accepts a tuple, and pattern matches to it.

Tuple patterns only match the exact size of the pattern, so a pattern of `(x,y)` will only match size-2 tuples.

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
