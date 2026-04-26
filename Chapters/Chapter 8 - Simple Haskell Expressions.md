> Previous: [[Chapter 7 - Type Classes]]

My goal over the next few chapters is to recreate a simple Python program in Haskell.

```Python
def add_then_mult(a, b):
	return (a + b) * a * b


def main():
	result = add_then_mult(4, 5)
	print(result)
```

We're passing two numbers $a=4$ and $b=5$ then returning their sum $a+b$ multiplied by $a \times b$. We then print the result to the screen.

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
## If-Else Expressions

In regular programming languages, you have if statements:

```Python
def func(x):
	if x > 100:
		return 10
	else:
		return 400
```

In Haskell, they are **expressions**, which return a value. You're not deciding which code path to take, your deciding which value the expression will return.

```Haskell
func x = if x > 100 then 10 else 400
```

> [!warning] It's really important you understand that in Haskell, there is no "step by step" execution of code. It's an **expression** that returns a value.

---
## Back To the Example

In our examples, the function body in Python is `(a + b) * a * b` and in Haskell it's...

```Haskell
(a + b) * a * b
```

wow

It's the same.

In the next part you'll learn how to define the function itself.

> Next: [[Chapter 9 - Functions in Haskell]]
