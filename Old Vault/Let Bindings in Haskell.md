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
aliases:
  - Let Bindings
  - Let Binding
---
Very similar to [[Where Bindings in Haskell|Where Bindings]] are let bindings.

Let bindings allow you to bind to variables anywhere and are very local, and don't span across guards.

```Haskell
cylinder :: (RealFloat a) => a -> a -> a
cylinder r h = 
	let sideArea = 2 * pi * r * h
		topArea = pi * r ^ 2
	in sideArea + 2 * topArea
```

They come in the form `let <bindings> in <expression>`. The bindings are only available within the expression and nowhere outside of it.

Let bindings themselves are just expressions, and as such can be put almost anywhere.

```Haskell
a = 4 * (let a = 9 in a + 1) + 2
-- a = 42

b = [let square x = x * x in (square 5, square 3, square 2)]
-- b = [(25,9,4)]
```

To bind multiple values in-line, you can use semi-colons.

```Haskell
(let a = 100; b = 200; c = 300 in a*b*c, let foo="Hey "; bar = "there!" in foo ++ bar)
-- (6000000,"Hey there!")
```

## Pattern Matching

You can even [[Pattern Matching in Haskell|Pattern Match]] in let bindings.

```Haskell
(let (a,b,c) = (1,2,3) in a+b+c) * 100
-- 600
```

## List Comprehensions

You can also put let bindings inside of [[Lists in Haskell#List Comprehensions|List Comprehensions]].

```Haskell
calcBmis :: (RealFLoat a) => [(a, a)] -> [a]
calcBmis xs = [bmi | (w, h) <- xs, let bmi = w / h ^ 2]
```

We include them much like predicates, but instead of filtering, it binds names. Any names bound within the let binding are visible to the output function (the part before the `|`) as well as all predicates and sections after the let binding. In this example, the only part that cannot see the binding is `(w, h) <- xs` as it's before the binding.

We can alter the expression to only return BMI's over 25.

```Haskell
calcBmis :: (RealFloat a) => [(a, a)] -> [a]
calcBmis xs = [bmi | (w, h) <- xs, let bmi = w / h ^ 2, bmi >= 25.0]
```

Note that we omitted the usual `in` section of the let binding this time because the scope of let bindings is already pre-determined within list comprehensions.

But, we could still define a `let <bindings> in <expression>` within a predicate and it would only be visible to that single predicate.

## When should you not use a let binding?

The main alternative to a let is a [[Where Bindings in Haskell|Where Binding]].

A where binding should be used instead if you need the expression to span across guards.

```Haskell
describeNumber :: Int -> String
describeNumber n
	| n > 0 = "Positive " ++ extraDescription
	| n < 0 = "Negative " ++ extraDescription
	| otherwise = "Zero"
	where
		extraDescription = if even n then "and even" else "and odd"
```

Some also prefer the readability of where bindings, with the names coming after the function they're being used in. This way, the function body is closer to its name and type declaration.