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
> [!note]
> The traditional method of function application is **Eager Evaluation**, which is what languages like [[The Java Programming Language|Java]] or C use.
> 
> In this method, function arguments are evaluated completely before the function is called. Once all arguments have been computed to their final values, they are then passed to the function to be used.

In lazy evaluation, computation of function arguments is deferred until the value is read for some use like printing to screen, branching based on the value etc.

Instead of the immediate computation, it creates a "thunk", aka a promise, to calculate the value when it's called for.

This has the benefit of allowing infinite data structures and avoiding unnecessary computation, while having the drawbacks of a more complex execution model and potentially increased memory usage.

> [!example]
> Consider this Python snippet
> 
> ```Python
> def func(a, b):
> 	return a + b
> 
> c = 10 + 30
> d = 20 - 1
> print(func(c, d))
> ```
> 
> If it were being evaluated eagerly, then it would look like this:
> 
> 1. Compute `c = 40`
> 2. Compute `d = 19`
> 3. Call `func(40, 19)`
> 4. Compute `40+19=59`
> 5. Return `59` from the function.
> 6. Print `59`.
> 
> If it were being evaluated lazily, then it would look something like this:
> 
> 7. `c` still just equals `10 + 30`.
> 8. `d` still just equals `20 - 1`
> 9. Call `func` with `c` and `d` not evaluated as `func((10+30), (20 - 1))`
> 10. Return from the function `a+b`, still not evaluated `return (10+30) + (20 - 1)`
> 11. Only when `print` is called, does calculation get performed, from first to last functions applied: `10+30=40`, `20-1=19`, `40+19=59`.
