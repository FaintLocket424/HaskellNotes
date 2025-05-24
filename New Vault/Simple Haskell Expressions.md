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
## Arithmetic Expressions

The most basic [[Haskell]] expressions are the 5 simple arithmetic operations of:

1. Addition: `a + b`
2. Subtraction: `a - b`
3. Multiplication: `a * b`
4. Division: `a / b`
5. Exponentiation: `a ** b` or `a ^ b`

These all work exactly the same as you'd expect from other languages.

If you open [[GHCi]] and type in these expressions, you should get the results you expect.

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

Haskell implements your standard Boolean `and`, `or` and `not` comparisons.

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

