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
## Using Built-In Functions 

Before learning how to create our own functions, it's probably useful to first learn how to use a function in Haskell.

### Using Single Argument Functions

Let's take the `sqrt` function as an example. This function takes a number $a$ and returns its square root $\sqrt{ a }$.

In Python, you would call this function like this:

```Python
sqrt(25)
# 5
```

Haskell doesn't work this way.

Let's imagine that we have the same function `sqrt` in Haskell which does the same thing.

You would call the function like this:

```Haskell
sqrt 25
-- 5
```

Notice how we just put the number, no brackets.

> [!important]
> Functions are so important in Haskell that they have minimal syntax to make their application easier.

> [!warning]
> You might be tempted to still use brackets like `sqrt (25)`, but you should resist this habit as Haskell programmers are **allergic to brackets**.

### Using Multi-Argument Functions

Take the function `pow`, which takes two arguments $a$ and $b$, and returns $a^b$.

In Python, you'd call the function like so:

```Python
pow(2, 6)
# 64
```

But if we try the same thing in Haskell we'll get an error:

```Haskell
pow (2,6)
-- * No instance for (Show ((Integer, Integer) -> (Integer, Integer)))
```

Don't worry about exactly what this error message means, but in a nutshell it's saying that it cannot print a function to the screen.

Print a function? But this is supposed to return a number?

Well actually, we only gave it one of the two parameters it was expecting.

If you remember back to [[The Basic Data Types of Haskell#Tuples|Tuples]], we'll see that `(2,6)` is actually a tuple of size 2 and type `(Integer, Integer)`. That's why we see that in the error message.

The correct way to call this function is this:

```Haskell
pow 2 6
-- 64
```

If you really wanted to put brackets around it, you would do it as `pow (2) (6)`. But don't unless you have to.

> [!warning]
> Note the error message we get if we supply too few parameters to `pow`:
> 
> ```Haskell
> pow 2
> -- * No instance for (Show (Double -> Double))
> ```
> 
> If you are getting error messages like this, where it's trying to `Show` a function (like `Double -> Double`), you've probably undersupplied parameters, or the compiler can't figure out what parameters go with what function. The exact reason why it's creating a function will be discussed in the section on Currying.
> 
> To figure out the type of a function, use the [[GHCi#Type Command|GHCi Type Command]].

> [!important] When you are allowed to use brackets.
> If your function arguments are the results of functions themselves, then you might have to use some brackets.
> 
> If we don't include any brackets at all:
> 
> ```Haskell
> pow 1+1 3+4
> -- * Could not deduce (Num t0)
> --      from the context: (Floating a, Num t, Num (t -> a -> a),
> --                         Num (a -> a))
> --        bound by the inferred type for `it':
> --                   forall {a} {t}.
> --                   (Floating a, Num t, Num (t -> a -> a), Num (a -> a)) =>
> --                   a -> a
> --        at \<interactive>:68:1-11
> --      The type variable `t0' is ambiguous
> ```
> 
> The compiler has absolutely no idea what to do.
> 
> If we put brackets around the first one then we get this output:
> 
> ```Haskell
> pow (1+1) 3+4
> -- 12
> ```
> 
> `12`? The answer is supposed to be `128`?? This is due to function application having a **higher precedence** than the addition `(+)` operator. So the compiler will do that first.
> 
> When the compiler looks at this code, it sees:
> 
> ```Haskell
> (pow (1+1) 3) + 4
> -- (2^3) + 4 = 12
> ```
> 
> So that final bracket is also necessary to get the desired result.
> 
> ```Haskell
> pow (1+1) (3+4)
> -- 128
> ```

---

## Creating Your Own Functions 

### Creating Single Argument Functions

Let's look at a single variable Python function we can replicate.

```Python
def double(x):
	return 2 * x
```

`double` returns twice the value input into it.

#### Step 1 - Picking a Function Decorator

> [!warning]
> Reasoning about types is a core part of understanding Haskell code.
> 
> Also you **will** have to do this in the exam and it's free marks.
> 
> **Always decorate function definitions with their type**.

The first step when creating a function is determining the type. If you think back to [[The Basic Data Types of Haskell#Functions|Function Decorators]], a logical function type might be `Integer -> Integer`.

But this is actually more restrictive than necessary, and it's always best to use the least restrictive decorator possible. What if we used an `Int`? Or a `Float`? The multiplication `(*)` function will work with all these types, so why not allow our function to accept them?

Instead, we can use a [[The Basic Type Classes of Haskell#Class Constraints|Class Constraint]] to specify that our function will input and output any type in the `Num` typeclass.

```Haskell
double :: (Num a) => a -> a
```

So now our function can accept `Int`, `Integer`, `Float` and `Double`.

#### Step 2 - Defining the function body

Defining the function body is similar to the syntax to call functions.

```Haskell
double x = 2 * x
```

Notice how you use the equals sign to define the function, since functions and variables are pretty much the same thing in Haskell.

This is again going back to Haskell code looking like mathematical notation.

$$
\text{double}(x) = 2 \times x
$$

### Creating Multi-Argument Functions

What if we wanted to define a function like `pow`, that calls the `(**)` operator on two inputs.

If it was Python, it would look like this:

```Python
def pow(a, b):
	return a ** b
```

In Haskell, it would look like this:

```Haskell
pow a b = a ** b
```

As you can see, multiple arguments are put next to each other with spaces between them.

If it were 3 arguments, it would be

```Haskell
add a b c = a + b + c
```

---

## Infix vs Prefix Functions

In Haskell, everything is a function.

This shouldn't be too surprising at this point but it needs emphasising because it defines **everything** you do in the language.

In other languages, operators like `(+)`, `(-)` or `(*)` are just syntax. In Haskell they are what's known as **infix functions**, function which go between the operands.

Naturally, all infix functions are binary operators (functions of 2 arguments) like the ones mentioned above.

> [!important]
> It's important to note that there is no difference between these infix functions and the other functions in Haskell. They are simply defined using symbols instead of words. Any function in Haskell that is defined with a symbol automatically becomes an infix function, and you can define your own with unused symbols like `#`.
> 
> And any time you are referencing an infix function in Haskell, like passing it as an argument to a different function, you surround them in brackets, like so `(+)`.

### Using Infix Functions As Prefix And Vice Versa

But, any infix function can be used as a prefix, and any prefix function can be used infix.

To illustrate this, these two expressions are functionally identical:

```Haskell
5 + 7
-- 12

(+) 5 7
-- 12
```

If you don't believe me, try it in GHCi!

Notice how the prefix version has the arguments applied exactly like above when we were using `pow`. We could instead decide we want to use `pow` as an infix operator:

```Haskell
pow 2 3
-- 8.0

2 `pow` 3
-- 8.0
```

Using \`backticks\`, you can use a prefix function as an infix operator, provided it's a binary function.

### Creating Our Own Infix Functions

We can even create our own infix binary operators using unused symbols, or with combinations of symbols.

```Haskell
a # b = (a * b) + b
2 # 2 -- 6

(+-) a b = a - b + (a * b)
10 +- 2 -- 28
```

Funky!

---

## The Function Application Operator

There's a curious and very useful function in Haskell called the "function application operator", and it has the symbol `($)`.

It's defined like this:

```Haskell
($) :: (a -> b) -> a -> b
f $ x = f x
```

It's an operator which when given a function and an input, will apply that function to the input.


> [!example]
> ```Haskell
> not True
> -- False
> 
> not $ True
> -- False
> 
> show 154
> -- "154"
> 
> show $ 154
> -- "154"
> ```
> 
> It's important to grasp that we're not just putting a `$` in-between the function and it's operands, we're passing a function and a value to the `($)` function.

This may seem useless at first. Why not just do `double 5` instead of `double $ 5`?

That's because this operator is designed for when your arguments are function calls themselves. What if we wanted to double `5 + 5`.

If we tried to do it directly, we'd get the wrong output:

```Haskell
double 5 + 5
-- 15
```

This is because the compiler sees `(double 5) + 5`, because function application is higher precedence than addition.

What makes `($)` useful is that it has the lowest precedence of any operator, so it will always be evaluated last.

So if we separate our function and arguments with the `($)`, we get the correct value.

```Haskell
double $ 5 + 5
-- The compiler will now see (double) (5 + 5)
-- 20
```

Of course we could have just put brackets around the `5 + 5`, for `double (5 + 5)` and that would've worked.

But Haskell programmers are **allergic to brackets** and so will use `($)` when they can to remove them.

You don't have to use this in your own code, but it's important to be able to understand what the operator is doing.