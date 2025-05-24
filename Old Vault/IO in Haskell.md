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
[[Haskell]] is a purely [[Functional Programming Languages|Functional Language]], this means it has no [[Side Effects]]. This makes input and output difficult.

Haskell relies on functions being [[Pure Functions|Pure]], but an input function has absolutely no guarantee that it will receive the same data each time it's called. The user may type something different in.

To get around this, Haskell separates the pure and impure sections of our code, with the impure stuff being grouped into _IO Actions_.

## IO Actions

In Haskell, input and output is handled through "_IO Actions_" and the `IO a` type.

You can think of an IO Action like an instruction, telling Haskell to go do something that is absolutely full of state and side effects like reading or writing to the terminal.

IO Actions contain result types, and is what the `a` in their type `IO a` represents.

> [!Example] Example 1
> An IO Action which results in a `String` (like reading from the terminal) will have the type `IO String`.

> [!Example] Example 2
> An IO Action which has no meaningful result type (like printing to the terminal) will have the type `IO ()`. The empty tuple (or unit) value `()` is used as a placeholder value.

Because IO is necessarily [[Pure Functions|Impure]], you may only:

- Perform IO Actions within another IO Action.
- Extract values out of an IO Action inside IO Actions.

This is how Haskell managed to separate the pure and impure sections of the code.

We can have a pure function `ageFromName` which takes a `String` as input and outputs your age based upon your name. This name could be user inputted in the form of an `IO String` but by extracting the value within an IO Action, we can use our pure functions on the impure data.

How to actually perform IO Actions and extract results from them is explained in the next sections.

## Hello World

The most basic IO Action in Haskell would be the venerable "Hello World" program.

```Haskell
main = putStrLn "Hello World"
```

We first create the `main` function, this is the entry point to a Haskell program. `main` always has the type `main :: IO a`, with some concrete type `a`, and we generally do not specify a type declaration for `main`.

We then assign it a value of `purStrLn "Hello World"`.

Let's look at the type of `putStrLn`

```Haskell
putStrLn :: String -> IO ()
```

We can see that `putStrLn` takes a `String` as input and returns an IO Action. This IO Action will print the provided value to the terminal, with no result value.

## `do` Syntax

We can use `do` syntax to chain together multiple IO Actions into one. Ignore the `<-` for now, we'll get to what that does later.

```Haskell
main = do
	putStrLn "Hello, what's your name?"
	nameVar <- getLine
	putStrLn ("Hey " ++ nameVar ++ ", you rock!")
```

This syntax ends up reading like an imperative program, with a series of steps, each being an IO Action.

The result of a `do` block is the result of it's final IO Action, so the IO Action created by this `do` block is `IO ()`, the type of it's last action `putStrLn`.

## `<-` Syntax

Take a look at this code from the [[#`do` Syntax|do section]] again.

```Haskell
main = do
	putStrLn "Hello, what's your name?"
	nameVar <- getLine
	putStrLn ("Hey " ++ nameVar ++ ", you rock!")
```

Notice that line 3 contains some new syntax, `<-`. It looks like a variable assignment but it's a little more complex than that.

If we look at the type of `getLine` we will see

```Haskell
getLine :: IO String
```

This tells us that `getLine` is an IO Action that contains a result type of `String`. And this makes sense since getting user input will result in some string of characters.

> [!Danger] Important Note
> This code is invalid!
> 
> ```Haskell
> greeting = "Hello, my name is " ++ getLine
> ```
> 
> The `(++)` operator requires it's parameters to be of the same type, `String` in this case, but `getLine` does not return a `String`! It returns an `IO String`, and must have its value extracted inside of an IO Action.

But what's up with the `<-`? Well, you can think of `<-` as "extracting" the result value from the IO Action and giving it a label. Hence, `nameVar` would have the type `String`.

> [!Warning] You can't use `=` for this!
> Some beginners think that doing `name = getLine` will assign `name` to the result of `getLine`, but it will not. All it does it create an alias for `getLine`.

In fact, you can assign a label to any IO Action result type, but it's not always necessary. You technically *can* do:

```Haskell
foo <- putStrLn "Hello, what's your name?"
```

But it's pointless because `foo` would just take the value `()`.

Also note that you cannot bind the last item in a `do` block since that's the result type of the `do` block itself.

## Let Bindings inside do blocks

[[Let Bindings in Haskell|Let Bindings]] can be used within do blocks to define functions.

```Haskell
import Data.Char

main = do
	putStrLn "What's your first name?"
	firstName <- getLine
	putStrLn "What's your last name?"
	lastName <- getLine
	let bigFirstName = map toUpper firstName
		bigLastName = map toUpper lastName
	putStrLn $ "hey " ++ bigFirstName ++ " " ++ bigLastName ++ ", how are you?"
```

We calculated some values and bound them to values.

> [!Important] So when should you use a let binding vs a `<-` binding?
> Remember that you use `<-` to extract values out of IO Actions, and you use let to bind expressions to values.

## Looping Mains 

```Haskell
main = do
	line <- getLine
	if null line
		then return ()
		else do
			putStrLn line
			main
```

This program will take a user input and if it's empty, do nothing, else, print the input back out and run main again. We can call main again because `main` itself is an IO Action.

## The `return` keyword

Look at this code again from the [[#Looping Mains]] section.

```Haskell
main = do
	line <- getLine
	if null line
		then return ()
		else do
			putStrLn line
			main
```

We should probably look at that "do nothing" line `return ()`. From imperative languages, you probably recognise this and think you know what it does.

> [!Danger] You Don't

In imperative languages, `return x` usually means to finish execution and go back to the caller with the resulting value.

In Haskell, `return ()` makes an IO Action out of a pure value `()`. Sure, it's an IO Action that doesn't do anything, but it's an IO Action nonetheless. If we instead used `return "turtle"` then it would be an `IO String` that contains the string `"turtle"`.

The reason we need this is because each line in the do block must be an IO Action, and each branch of our if statement must return a value to the do block. So, we create a fake IO Action which does nothing to satisfy this requirement.

> [!Important] `return ()`
> It's like a blank line with extra steps.

It's important to remember that `return` does not mean to "end execution" like it does in imperative languages. Execution **will** continue through it.

```Haskell
main = do
    return ()
    return "HAHAHA"
    line <- getLine
    return "BLAH BLAH BLAH"
    return 4
    putStrLn line
```

This program will still read a line, then write that line back. It just has a bunch of "do nothings" in between. To the thing running the IO Actions, all it sees is this.

```Haskell
main = do
	--
	--
	line <- getLine
	--
	--
	putStrLn line
```

## IO Functions 

### putChar

```Haskell
putChar :: Char -> IO ()
```

`putChar` take a `Char` and returns an IO Action which will print it to the terminal.

### putStr

```Haskell
putStr :: String -> IO ()
```

`putStr` takes a `String` and returns an IO Action which will print it to the terminal.

> [!important] Note
> Note that it does not go to the next line. This is similar to `print("Yes", end="")` in Python or `Console.Write("Yes")` in C#.

In fact, `putStr` is recursively defined using [[#putChar]], where it will print each character in the string.

```Haskell
putStr :: String -> IO ()
putStr [] = return ()
putStr (x:xs) = do
	putChar x
	putStr x
```

This pretty much converts a `String` like `"Yes"` into 

```Haskell
do
	putChar 'Y'
	putChar 'e'
	putChar 's'
	return ()
```

### putStrLn

We have already seen `putStrLn` but let's give it a definition anyway.

```Haskell
putStrLn :: String -> IO ()
```

`putStrLn` takes a `String` and returns an IO Action which will print it to the terminal, and it will append a `\n` to the end, going to the next line.

### print

```Haskell
print :: (Show a) => a -> IO ()
```

`print` takes a value of any [[Type Classes in Haskell#The Type Classes of Haskell|Typeclass]] that is an instance of [[Type Classes in Haskell#Show|Show]] and:

1. Calls `show` on it to get the `String` form.
2. Then returns an IO Action that will print the `String` form to the terminal.

It's basically just `putStrLn . show` and is a very useful function for printing values to the terminal.

### getChar

```Haskell
getChar :: IO Char
```

`getChar` is an IO Action that reads a single character from the input buffer, and returns an `IO Char` with the result contained in it.

> [!NOTE]
> Due to input buffering, the program won't actually receive any characters until enter is pressed. It will then work with the whole buffer at once, getting characters out of the buffer.

### when

```Haskell
import Control.Monad

when :: (Applicative f) => Bool -> f () -> f ()
```

The `when` function takes a `Bool` and an IO Action. (Technically it's an [[Applicatives in Haskell|Applicative]]).

If the `Bool` is `True`, then it returns the IO Action passed to it.
If the `Bool` is `False`, then it returns the `return ()` IO Action.

So basically, it runs the provided IO Action if the Boolean is true. It's useful for the `if <x> then <IO Action> else <do nothing>` pattern.

Here is a code snippet that takes advantage of it to print and loop only _when_ the input character is not a space.

```Haskell
import Control.Monad

main = do
	c <- getChar
	when (c /= ' ') $ do
		putChar c
		main
```

We use the [[Function Application Operator in Haskell|$ Operator]] to remove the need for brackets around the IO Action.

### sequence 

The sequence function takes a list of IO Actions and returns a single IO Action which will perform each one, in order.

If we then bind that result to a value, it will be a list of the results.

We can replace this code:

```Haskell
main = do
	a <- getLine
	b <- getLine
	c <- getLine
	print [a,b,c]
```

With this code:

```Haskell
main = do
	rs <- sequence [getLine, getLine, getLine]
	print rs
```

Here, `rs :: [String]`.

> [!example] A use for `sequence`
> A common pattern with `sequence` is mapping a function like `print` over a list, then sequencing the result to print the results.

```Haskell
main = sequence (map print [1,2,3,4,5])
-- 1
-- 2
-- 3
-- 4
-- 5
```

We can't just print a list of IO Actions, so we sequence them to run each one individually.

We can even bind the result if we really want to:

```Haskell
main = do
	rs <- sequence (map print [1,2,3,4,5])
	print rs

-- [(),(),(),(),()]
```

This makes sense when you remember that [[#print]] returns an IO Action of `IO ()`.

### mapM

A common pattern in Haskell is mapping over a list with an IO function and then [[#sequence|sequencing]] it. So common in fact that the utility functions `mapM` and `mapM_` were introduced.

`mapM` takes a function and a list. It will then return an IO Action which will perform said function on each element in the list, in order.

`mapM_` does the same, but does not return a value in the IO Action.

```Haskell
-- In an IO and List context (the real definition is more complex)
mapM  :: (a -> IO b) -> [a] -> IO [b]

mapM_ :: (a -> IO b) -> [a] -> IO ()
```

```Haskell
main = do
	t <- mapM print [1,2,3]
	-- 1
	-- 2
	-- 3
	-- t = [(),(),()]
	
	v <- mapM_ print [1,2,3]
	-- 1
	-- 2
	-- 3
	-- v = ()
```

### forM

```Haskell
import Control.Monad

-- In an IO and List context (the real definition is more complex)
forM :: [a] -> (a -> IO b) -> IO [b]
```

Very similar to `mapM` is `forM`. The difference is that their parameters are swapped around. This allows for some creative use cases.

```Haskell
import Control.Monad

main = do
	colours <- forM [1,2,3,4] (\a -> do
		putStrLn $ "Which color do you associate with the number " ++ show a ++ "?"
        color <- getLine
        return color
	)
	putStrLn "The colors that you associate with 1, 2, 3 and 4 are: "
    mapM putStrLn colors
```

Here, we've created a lambda function which takes an integer input `a` and returns an IO Action that contains a string, an `IO String`. Also note the brackets to separate the IO Actions in the lambda from those in `main`.

Overall, the `forM` call will create an IO Action which, when computed, will run our lambda function with each input from the list, so 1 then 2 etc.

We then simply print the result.

```
Which color do you associate with the number 1?
> white
Which color do you associate with the number 2?
> blue
Which color do you associate with the number 3?
> red
Which color do you associate with the number 4?
> orange
The colors that you associate with 1, 2, 3 and 4 are:
white
blue
red
orange
```

We usually use `forM` over `mapM` when we are defining the IO Action inline.

### forever 

```Haskell
import Control.Monad

-- In an IO context, the real definition is more complex
forever :: IO a -> IO b
```

The `forever` function takes an IO Action and returns a new IO Action that will repeat the given one forever.

```Haskell
import Control.Monad
import Data.Char

main = forever $ do
	putStr "Give me some input: "
	l <- getLine
	putStrLn $ map toUpper l
```

This program will take an input and print the input in all caps, forever.