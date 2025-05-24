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
---
A programming paradigm where the building blocks of computation is the **application of functions** to arguments.

---

## The Differences With The Imperative Style

Imperative programming focuses on _how_ to achieve a result by providing explicit, step-by-step instructions to get the desired output.

Functional programming, in contrast, emphasizes _what_ needs to be computed through the evaluation of functions.

Take the factorial function, written here in mathematical notation.
$$
F_{n} = \cases{1 & n=1 \\ n F_{n-1} & \text{otherwise}}
$$

In the imperative style (shown with Python), this one way you could write it:

```Python
def factorial(n):
	f = 1
	for i in range(1, n+1):
		f = f * i
```

However, in the functional style (also shown with Python), this is how you could do it:

```Python 
def factorial(n):
	if n == 1:
		return 1
	else:
		return n * factorial(n-1)
```

Notice how the functional style resembles the mathematical notation. This happens a lot in functional programming.

---

## Functional Languages

### Fake functional languages

Many languages are **multiparadigm**, and support functional elements. These include:

- Python
- JavaScript
- [[The Java Programming Language|Java]]
- C#
- Kotlin

But while these languages have functional elements, they do not enforce the functional style, and the language level support is pretty weak.

For a purely functional language, we turn to [[Haskell]].

### What makes a purely functional language special?

Functional languages all share some common traits:

- Supports and encourages a programming style with function application and composition as basic building blocks.
- Forbids variable assignment and [[Side Effects]].
- (_Almost_) all functions are required to be [[Pure Functions]].
