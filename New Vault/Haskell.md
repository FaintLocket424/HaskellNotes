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
## Properties of Haskell

Haskell is a [[The Functional Programming Paradigm|Purely Functional]] language with:

> [!info] Strong Static Typing
> Data types are strictly enforced at compile time.

> [!check] Type Inference
> The compiler can automatically deduce data types based on usage and context, but they must still be statically typed.

> [!example] Lazy Evaluation
> Data is only computed when necessary, more on this later.

---

## The Type System

Before we look at any Haskell code, I think it would be beneficial to first understand [[The Basic Data Types of Haskell]].

Now that you know the difference between an `Int`, `Integer` and `[Int]`, we should probably talk about how types are grouped together into [[The Basic Type Classes of Haskell|Type Classes]].

---

## Getting Started with Haskell

Everyone knows Python, right?

Let's take a very simple Python script and work towards recreating it in Haskell.

```Python
def add_then_mult(a, b):
	return (a+b) * a * b


def main():
	result = add_then_mult(4, 5)
	print(result)
```

We're passing in two numbers, $a=4$ and $b=5$ then returning their sum $a+b$, multiplied by $a \times b$. We then print the result to the screen.

> [!NOTE]
> The first thing you'll need to do is install GHCup. To do so, go to https://www.haskell.org/ghcup/ and run the installer. Note that some steps may take like 10 minutes.
> 
> You can accept the default settings

To recreate the function body, we use some [[Simple Haskell Expressions]] and find out *it's exactly the same*.

```Haskell
(a + b) * a * b
```

So this would work... except it needs a function to live in. So why don't we look at the [[Function Basics in Haskell]].

With that, we can create our function `add_then_mult`.

```Haskell
add_then_mult :: (Num a) => a -> a -> a
add_then_mult a b = (a + b) * a * b
```

And we can call the function with our inputs $a=4$ and $b=5$:

```Haskell
add_then_mult 4 5
-- 180
```

We now just need to print it to the screen, and to do so we can use the `print` function inside a `main` function.

```Haskell
add_then_mult :: (Num a) => a -> a -> a
add_then_mult a b = (a + b) * a * b

main = print $ add_then_mult 4 5
```

And if we use the [[GHCi#Main Command|Main Command]] in GHCi to execute `main`, we will see the result:

```Haskell
:main
180
```

Or, if you want to be fancy, you can put this script into a file like `main.hs` and run `ghc main.hs` on it. This will compile your program into a binary like `main.exe` which you can then execute in the terminal.

```
[nix-shell:~/programming/Dev/haskellTesting]$ ghc main.hs
[1 of 2] Compiling Main             ( main.hs, main.o )
[2 of 2] Linking main

[nix-shell:~/programming/Dev/haskellTesting]$ ls
main  main.hi  main.hs  main.o

[nix-shell:~/programming/Dev/haskellTesting]$ ./main
180
```

Yippee, you have just created your first executable Haskell program!

---

## A Word About Curry

Haskell utilises [[Currying in Haskell|Currying]] to create some interesting emergent behaviour.

---

## More Haskell Types

Probably the most important type in Haskell is the [[Lists in Haskell|List]], there are many more of what are known as [[Type Constructors In Haskell|Type Constructors]].

---

## Pattern Matching

We're now getting towards some of the powerful stuff, **Polymorphism**, via [[Pattern Matching In Haskell|Pattern Matching]].

And a useful tool for this is [[Case Expressions|Case Expressions in Haskell]].

---















%%

Haskell Topics in order

1. functional_paradigm ✅
2. side_effects ✅
3. pure_functions ✅
4. simple_data_types ✅
5. basic_type_classes ✅
6. ghci ✅
7. simple_haskell_expressions ✅
8. functions ✅
9. function_application_operator ✅
10. simplest_executable ✅
11. currying ✅
12. lists ✅
13. type constructors (maybe, either etc.) ✅
14. pattern_matching ✅
15. case_expressions ✅
16. function guards
17. let_bindings
18. where_bindings
19. polymorphism
20. recursion
21. lambda_expressions
22. higher_order_functions
23. lazy_evaluation
24. io
25. kinds
26. custom_data_types
27. functors
28. applicatives
29. monoids
30. monads 

%%