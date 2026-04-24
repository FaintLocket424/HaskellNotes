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
