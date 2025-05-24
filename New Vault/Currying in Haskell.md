---
categories:
  - "[[Module Notes]]"
module: "[[Functional Programming Module]]"
authors:
  - "[[Matthew]]"
templates:
  - "[[Module Note Template]]"
template_version: "1"
TARGET DECK: Programming Paradigms::Functional Programming
aliases:
  - Currying
---
Currying is a fundamental concept in [[Haskell]] where a function that appears to take multiple arguments is actually treated as a sequence of functions.

The "curry" in currying is a reference to logician Haskell Curry, whose name you might recognise as Haskell is named after him.

In most languages, when you define a function of multiple arguments, the function is treated as a single unit and receives all its inputs at once.

```Python
def mult(a, b):
	return a * b

mult(4, 5) # 20
```

But in Haskell, multi-parameter functions don't actually exist.

When you create a function with 2 arguments, you're actually creating two functions.

1. The first function accepts the first argument. It then returns a new function with that argument "cached".
2. The second function then accepts the second argument and computes the value, using the "cached" first argument.

Does that make perfect sense and all is cleared up? No? Yeah thought so. Let's look at what this would look like in Python.

If we were to rewrite `mult` using currying, it would look something like this:

```Python
def mult(a):
	def mult_b(b):
		return a * b
	return mult_b

mult(4)(5) # 20
```

Now this is starting to look more like a Haskell function calling syntax.

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

You can see how the function is using the value from the outer scope. This is known as a [[Closures in Haskell|Closure]] and will be discussed further later.

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

What happens if we don't give a function enough parameters?

In any other language, this would be an immediate compile or runtime error. And it might be in Haskell, or it could be used intentionally.

Take the function `add` which takes two arguments and sums them together.

```Haskell
add :: Int -> Int -> Int
add x y = x + y
```

We could use this to create a single argument function that adds 5 to it's argument.

```Haskell
addFive :: Int -> Int
addFive = add 5

addFive 5 -- 10
addFive 25 -- 30
addFive (-5) -- 0
```

Another example could be the `filter` function.

```Haskell
filter :: (a -> Bool) -> [a] -> [a]
```

Say we have a function `isEven :: Int -> Bool`, which returns `True` if the number input is even.

We could partially apply `filter` with it to create a filter which takes a list and returns the even numbers.

```Haskell
filterEven :: [Int] -> [Int]
filterEven = filter isEven

filterEven [1,2,3,4]
-- [2,4]
```

---

## Currying Infix Operators

As we learned in the [[Function Basics in Haskell|Function Basics]] section, the infix operators like `(+)` are functions, just like `head` or `even`.

And as such, they can be partially applied and curried.

The type signature of `(+)` is the following, with two inputs and one output.

```Haskell
(+) :: (Num a) => a -> a -> a
```

If we just provide one parameter to `(+)`, we get a curried function.

```Haskell
addThree :: (Num a) => a -> a
addThree = (3+)

addThree 3 -- 6
addThree 7 -- 10
```

> [!warning] Order of Operations Matters!
> Let's look at the division `(/)` function.
> 
> You should hopefully know that division is not commutative, `a / b != b / a`.
> 
> So when we partially apply the division `(/)` function, it matters if you put the number before or after.
> 
> ```Haskell
> method_1 :: (Num a) => a -> a
> method_1 = (/ 2)
> -- method_1 x = x / 2
> 
> method_2 :: (Num a) => a -> a
> method_2 = (2 /)
> -- method_2 x = 2 / x
> 
> method_1 10 -- 10 / 2 == 5.0
> method_2 10 -- 2 / 10 == 0.2
> ```
> 
> You'll see `(3+)` and `(+3)` used interchangeably but you should know and remember what they mean under the hood. It will help you debugging your code and figuring out why it isn't doing what you expect.
> 
> And this goes for any non-commutative functions.

> [!danger]
> Watch out when trying to partially apply subtraction `(-)`. Because it's also the negative sign, you cannot use it as `(-3)` to partially apply a function which subtracts 3 from the input.
> 
> Instead, you have to not partially apply the function, or use [[Lambda Functions in Haskell|Lambda Functions]] which we'll discuss later.
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
> method_4 x = x - 3
> method_4 10
> -- 10 - 3 = 7
> ```
