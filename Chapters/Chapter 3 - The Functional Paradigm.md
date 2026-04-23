> [!quote] A programming paradigm where the building blocks of computation is the **application of functions to arguments**.

What this means is that everything you do in Haskell is just put values into functions, then get the result of that and put it into a different function. That's it.

![[meme_wait_its_all.jpg|500]]

---

## Differences With the Imperative Style

The imperative style is what you will be used to programming in, with languages like Python, JavaScript, C etc. These languages focus on providing explicit, step-by-step instructions on how to get the desired output.

By contrast, Functional Programming emphasises *what* needs to be computed, but not necessarily how it will be achieved. It's **declarative programming**.

Take the factorial function below, where the result of $4!$ is $4 \times 3!$ until you're left with $4 \times 3 \times 2 \times 1=24$.

$$
	F_{n} = \begin{cases}
	1 & n=1 \\
	n \space F_{n-1} & \text{otherwise}
	\end{cases}
$$

Let's implement this function in Python using both the imperative style, and the functional style, since Python can sort of do both.

```python
# Imperative
def factorial(n):
    f = 1
    for i in range(1, n + 1):
        f = f * i
	
    return f
```

```python
# Functional
def factorial(n):
	if n == 1:
		return 1
	else:
		return n * factorial(n - 1)
```

Notice how the functional style looks quite similar to the mathematical notation from before? This happens a lot in functional programming.

---
## What Makes a Purely Functional Language Special?

- It supports and encourages a programming style with function application and composition as basic building blocks.
- Forbids variable assignment and **Side Effects**.
- All functions are required to be **Pure Functions**.

> [!warning] Fake Functional Languages
> Many languages are **multi-paradigm**, meaning they can support multiple paradigms simultaneously. This includes Python, JavaScript, Rust, Kotlin, Java etc.
> 
> While you *can* do "functional" programming in them, they do not enforce the functional style and have weak language level support for key functional elements.

---
## Side Effects

> [!important] A side effect is a state change that results from a function modifying objects external to its parameter list, like a global variable.

Common examples of side effects include:

> [!example] Modifying variables outside the function's local scope (global variables)
> ```python
> state = 0
> 
> def f(n):
> 	global state
> 	state += 1
> 	return n * state
> ```

> [!example] Printing to console
> ```python
> def double(n):
> 	val = 2 * n
> 	print(f"double {n} is {val}")
> 	return val
> ```

> [!example] Writing to files
> ```python
> def g(text):
> 	with open("file.txt", "a") as f:
> 		f.write(text)
> ```

> [!example] Making network requests
> ```python
> import requests
> 
> def something(status)
> 	x = requests.get("https://github.com/FaintLocket424/HaskellNotes")
> 	
> 	return x.status_code == status
> ```

All of these are side effects because they are affecting things besides themselves. When you print to the terminal, you're changing it. When you write to a file, you're outputting a whole file that's external to the function.

> [!warning] A note on pure functions...
> You might start to think that there's quite a lot of restrictions on pure functions, and how you might do anything in Haskell if every function has to be pure. Well, I'll let you in on a little secret... *it's impossible to write a completely pure program*. If a program was actually completely pure, then it would have no output! It would simply do some calculations then that's it, since it would not be able to print to terminal, or save a file, or make a network request etc.
> 
> The way we get around this in Haskell is by cheating a little with some advanced data types that will keep all the impure stuff contained and away from our nice pure code. If you're curious, that's what the venerable **Monad** is actually for.
> 
> While all this is far beyond your understanding right now, I want you to have this in the back of your mind that real programs are more complex than just some pure functions.

---

## Pure Functions

> [!important] What makes a function pure?
> 1. The function must be **deterministic**. The exact same input arguments must always produce the exact same output return value.
> 2. The function must have **no Side Effects**.

Here are some code snippets that demonstrate this:

```python
# A simple, pure function
def f(n):
	return n * 2

f(2) # 4
# Some code
f(2) # 4
```

Notice how no matter what we put in `# Some code`, `f(2) === f(2)`.

```python
# An impure function with no side effects
counter = 0

def h(n):
	return n + counter

h(2) # 2
counter += 1
h(2) # 3
```

Notice how `h(2) != h(2)`, that's non-deterministic and therefore impure.

```python
# An impure function with side effects
counter = 0

def g(n):
	global counter
	counter += 1
	return n + counter

g(2) # 3
g(2) # 4
```

Now, even with no code between them, the same input produces a different output. It's now has a side effect and is non-deterministic, making it very impure.

---
## Why Learn Functional Programming?

We discussed earlier how many languages are multi-paradigm and can support multiple paradigms. While this doesn't make them functional programming languages, it makes them more versatile.

My argument to you is this:

> Learning functional programming makes you a better imperative programmer because you can take what you learn in the extremely rigid world of Haskell and transfer it over to the more lenient world of Python or C.
> 
> The functional paradigm introduces less bugs and makes for often more readable code (but there is a limit...) and it just gives you another option to pick from when you're solving a problem.

Also, if there's anything you should take away from this module it's this: **stop using global variables**. Just pretend they don't exist.

> Next: [[Chapter 4 - The Basic Types of Haskell]]
