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
Many imperative languages have a "switch case" expression. Some way of running a piece of code depending on the value of a variable. Haskell has a similar concept, except even more powerful.

The basic syntax of a case expression is:

```Haskell
case <expression> of <pattern> -> <result>
                     <pattern> -> <result>
                     <pattern> -> <result>
```

The expression will be checked against each pattern and stop at the first one that matches. If none match, a runtime error occurs.

## Pattern Matching

Not only can we evaluate based on value, but we can also pattern match.

Normally, to get the head of a list you would write this function:

```Haskell
head' :: [a] -> a
head' []    = error "No head for empty lists!"
head' (x:_) = x
```

But with case expressions we can rewrite it like this:

```Haskell
head' :: [a] -> a
head' xs = case xs of [] -> error "No head for empty lists!"
                      (x:_) -> x
```

