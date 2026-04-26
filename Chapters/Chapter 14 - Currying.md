> Previous: [[Chapter 13 - Local Bindings]]

![[meme_function_curry.png|300]]

Currying is a fundamental concept in Haskell where a function that appears to take multiple arguments is actually treated a sequence of functions.

In most languages, when you define a function of multiple arguments, the function is treated as a single unit and receives all its inputs at once.

```python
def mult(a, b):
	return a * b

mult(4, 5) # 20
```

But in Haskell, multi-parameter functions **don't exist**.

When you create a function with 2 arguments, you're actually creating two functions.

1. The first function accepts the first argument. It then returns a new function with that argument "cached".
2. The second function then accepts the second argument and computes the value, using the "cached" first argument.

Clear as mud? Right. Let's look at what this would look like in Python.

If we were to rewrite `mult` using currying, it would look something like this:

```Python
def mult(a):
	def mult_b(b):
		return a * b
	return mult_b

mult(4)(5) # 20
```

Notice how `mult(4)(5)` is starting to look like the way we call functions in Haskell, `mult 4 5`.

Let's analyse what happens when we pass the first argument into the function, and replace `a` with `4`.

```Python
def mult(4):
	def mult_b(b):
		return 4 * b
	return mult_b

mult(4) # mult_b
```

If we then remove the outer function since it's "filled in" the values in the inner function, it looks more normal.

```Python
def mult_b(b):
	return 4 * b

mult_b(5) # 20
```

You can see that the function is using the value from the outer scope. This is known as a **Closure**.

Haskell does all of this automatically when you define a multivariate function.

```Haskell
mult :: Int -> Int -> Int
mult a b = a * b
```

In fact, it's all there in the type signature. The `->` is right-associative, and that means it can be interpreted as:

```Haskell
mult :: Int -> (Int -> Int)
```

You can see it's actually returning a function from the first parameter.

---
## Partial Function Application

What would happen if we didn't give a function enough parameters?

In any other language, that would be an immediate compiler or runtime error. And it could be in Haskell, *or...*

*...it could be done intentionally.*

Take our Python version of `mult` from before:

```python
def mult(a):
	def mult_b(b):
		return a * b
	return mult_b

mult(4)(5) # 20
```

Remember that we're calling the outer function with `4`, then returning the inner function, then calling the inner function with `5` which uses that "cached" value for `a` to then return `4 * 5`.

But what happens if we just do `mult(4)`? Well, as we saw earlier, we get this new function that multiplies its input by `4`.

```python
def mult_b(b):
	return 4 * b
```

We can save this function and reuse it.

```python
quadruple = mult(4)

quadruple(5) # 20
quadruple(16) # 64
quadruple(1) # 4
```

This is the heart of currying. We have given `mult` a partial set of arguments that we can then supply later, but we can reuse the function or use it in new contexts.

Just like we saw in Python, we can do much the same thing in Haskell.

```Haskell
mult :: Int -> Int -> Int
mult a b = a * b

quadruple = mult 4

quadruple 5 -- 20
quadruple 16 -- 64
quadruple 1 -- 4
```

You can try this for yourself in `ghci`.

```GHCi
GHCi, version 9.10.3: https://www.haskell.org/ghc/  :? for help  
ghci> mult a b = a * b  
ghci> mult 4 5  
20  
ghci> quadruple = mult 4  
ghci> quadruple 5  
20  
ghci> quadruple 16  
64
```

> [!important] Note how we defined `quadruple`
> Notice how we omitted the argument from the definition of `quadruple`.
> 
> We could have done `quadruple x = mult 4 x` but it's redundant since `mult 4` already returns a function of a single argument.
> 
> In fact, we can take this even further and define `mult` with even less syntax.
> 
> ```Haskell
> mult = (*)
> ```
> 
> Fun!

> [!example] A Practical Example of Partial Application
> In Haskell, operating on a list is very common and there are many functions built in to the standard library for us to use. One such function is `filter`.
> 
> ```Haskell
> filter :: (a -> Bool) -> [a] -> [a]
> ```
> 
> `filter` takes a function that returns `True` or `False` for a given element `a`, then takes a list of `a`s and returns a new list, keeping only the values that return `True`. It's a filter!
> 
> Now let's say we have created a function `isEven :: Int -> Bool` that returns `True` if the input is an even number.
> 
> If our program has to filter out even numbers from lists often, we can make our life easier by defining a new function that just takes in a list of `Int` and filters it for evenness.
> 
> ```Haskell
> filterEven :: [Int] -> [Int]
> filterEven = filter isEven
> 
> filterEven [1,2,3,4] -- [2,4]
> ```
> 
> We give the `filter` function only one parameter and now we have a function that will always filter out odd numbers from whatever list we give it.
> 
> The more you get into Haskell, the more you'll notice that the order of arguments for the built in functions is designed to facilitate partial application. You'll almost always see the "data being operated on" be the last argument so you can customise the behaviour of the function before putting data into it.

---
## Currying Infix Operators

As we learnt in [[Chapter 9 - Functions in Haskell]], the infix operators like `(+)` are just functions, like `head` or `filter`.

And as such, they too can be partially applied.

The type signature of `(>)` is the following, with two inputs and one output.

```Haskell
(>) :: Ord a => a -> a -> Bool

3 > 4 -- False
```

If we only provide one parameter to `(>)`, we get a new function:

```Haskell
biggerThanThree :: (Ord a, Num a) => a -> Bool
biggerThanThree :: (>3)

biggerThanThree 4 -- True
biggerThanThree 2 -- False
```

This is very useful for higher-order functions like `filter` and `map` that accept a function as a parameter.

```Haskell
filterSmallNumbers :: (Num a) => [a] -> [a]
filterSmallNumbers = filter (>5)

filterSmallNumbers [1..10] -- [6,7,8,9,10]
```

> [!danger] Order of Operations Matters!
> Let's look at the division `(/)` function.
> 
> You should hopefully know that division is not commutative, `a / b != b / a`.
> 
> So when we partially apply the division `(/)` function, it matters if you put the number before or after.
> 
> ```Haskell
> method_1 = (/ 2)
> -- method_1 x = x / 2
> 
> method_2 = (2 /)
> -- method_2 x = 2 / x
> 
> method_1 10 -- 10 / 2 == 5.0
> method_2 10 -- 2 / 10 == 0.2
> ```
> 
> You'll see `(3+)` and `(+3)` used interchangeably but you should know and remember what they mean under the hood. It will help you in debugging your code and figuring out why it isn't doing what you expect.
> 
> And this goes for any non-commutative functions.

> [!error] Subtraction is tricky
> Watch out when trying to partially apply subtraction `(-)`. Because it's also the negative sign, you cannot use it as `(-3)` to partially apply a function which subtracts 3 from the input.
> 
> Instead, you have to not partially apply the function, or use lambda functions which we'll discuss later in [[Chapter TODO - Lambda Functions]].
> 
> ```Haskell
> method_3 = (-3)
> method_3 10
> -- * Could not deduce (Num t0)
> --      from the context: (Num t1, Num (t1 -> t2))
> 
> method_4 = (3-)
> method_4 2
> -- 3 - 2 = 1
> 
> method_5 x = x - 3
> method_5 10
> -- 10 - 3 = 7
> ```

> Next: [[Chapter 15 - Type Constructors]]
