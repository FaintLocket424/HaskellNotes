> Previous: [[Chapter 6 - Type Classes]]

My goal over the next few chapters is to recreate a simple Python program in Haskell.

```Python
def add_then_mult(a, b):
	return (a + b) * a * b


def main():
	result = add_then_mult(4, 5)
	print(result)
```

We're passing two numbers $a=4$ and $b=5$ then returning their sum $a+b$ multiplied by $a \times b$. We then print the result to the screen.

> [!NOTE]
> The first thing you'll need to do is install GHCup. To do so, go to https://www.haskell.org/ghcup/ and run the installer. Note that some steps may take like 10 minutes.
> 
> You can accept the default settings at each stage, but it's probable you want the HLS (Haskell Language Server) for syntax highlighting in VSCode.

---
## Arithmetic Expressions

The most basic Haskell expressions are the 5 simple arithmetic operations of:

1. Addition: `a + b`
2. Subtraction: `a - b`
3. Multiplication: `a * b`
4. Division: `a / b`
5. Exponentiation: `a ** b` or `a ^ b` (yes, there's a difference)

These all work exactly the same as you'd expect from other languages.

If you open GHCi and type in these expressions, you should get the results you expect.

```Haskell
5 + 8
-- 13

47 - 23
-- 24

7 * 8
-- 56

1 / 2
-- 0.5

2 ^ 3
-- 8

2 ** 3
-- 8.0
```

> [!warning]
> Notice the difference between `(**)` and `(^)`. Let's look at the type signature for each:
> 
> ```Haskell
> (^) :: (Num a, Integral b) => a -> b -> a
> 
> (**) :: (Floating a) => a -> a -> a
> ```
> 
> `(^)` takes any number and raises it to an integer power, whereas `(**)` works with floating point exponents.

---

## Comparison Expressions

Haskell implements all the comparison operators that you would expect:

1. Equality: `==`
2. Inequality: `/=`
3. Less than: `<`
4. Less than or equal to: `<=`
5. Greater than: `>`
6. Greater than or equal to `>=`

And they also work how you'd expect.

```Haskell
3 == 3
-- True

3 /= 2
-- True

10 < 1
-- False

3 <= 7
-- True

3 > 1
-- True

4 >= 6
-- False
```

---

## Boolean Expressions 

Haskell implements your standard Boolean AND, OR and NOT comparisons.

1. Logical AND: `a && b`
2. Logical OR: `a || b`
3. Logical NOT: `not a`

```Haskell
True && True
-- True

False && True
-- False

False || False
-- False

True || False
-- True

not True
-- False
```

---
## Back To the Example

The function body in python is `(a + b) * a * b` and in Haskell it's...

```Haskell
(a + b) * a * b
```

wow

It's the same.

In the next part you'll learn how to define the function.

> Next: [[Chapter 8 - Defining Functions]]
