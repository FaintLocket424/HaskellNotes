> Previous: [[Chapter 12 - Case Expressions]]

## Let Bindings

Let bindings allow you to bind values to variables anywhere you want.

```Haskell
let <name> = <expression> in <expression>
```

Any names that you define inside the let are available for use within the second expression.

You can define multiple variables within the same expression:

```Haskell
let <name> = <expression>
	<name> = <expression>
in <expression>

let <name> = <expr>; <name> = <expr> in <expression>
```

> [!example]
> ```Haskell
> cylinder :: (Floating a) => a -> a -> a
> cylinder r h =
> 	let sideArea = 2 * pi * r * h
> 		topArea = pi * r ^ 2
> 	in sideArea + 2 * topArea
> ```

When I say anywhere, I mean anywhere.

```Haskell
myList = [1, 2, let x = 10 in x * x, 4] 

checkNumber :: Int -> String
checkNumber n = 
	if n > 100
	then let diff = n - 100 in "Way over by " ++ show diff
	else "Small enough"
```

---
## Where Bindings

`where` bindings are similar to `let` bindings, but have one key difference. They go at the end of functions.

Take this function:

```Haskell
bmiTell :: Float -> Float -> String
bmiTell weight height
    | weight / height ^ 2 <= 18.5 = "You're underweight."
    | weight / height ^ 2 <= 25.0 = "You're a healthy weight."
    | weight / height ^ 2 <= 30.0 = "You're overweight."
    | otherwise                   = "You're obese."
```

There's a lot of repeated, busy code. `weight / height ^ 2` comes up several times. Sounds like we need a function!

```Haskell
bmiTell :: Float -> Float -> String
bmiTell weight height
    | bmi <= 18.5 = "You're underweight."
    | bmi <= 25.0 = "You're a healthy weight."
    | bmi <= 30.0 = "You're overweight."
    | otherwise   = "You're obese."
    where bmi = weight / height ^ 2
```

Using `where` bindings, we can define functions and values which only exist within the confines of the function they're attached to.

> [!warning] Where bindings do not span across different patterns
> ```Haskell
> func :: Int -> Int -> String
> func 1 y = "Hello: " ++ show z -- BAD, z does not exist in this pattern
> func x y = "Goodbye: " ++ show z
> 	where z = y + 10
> ```

We can use `where` bindings to remove "magic numbers" from our code too by giving them a name.

```Haskell
bmiTell :: Float -> Float -> String
bmiTell weight height
    | bmi <= skinny = "You're underweight."
    | bmi <= normal = "You're a healthy weight."
    | bmi <= fat    = "You're overweight."
    | otherwise     = "You're obese."
    where bmi = weight / height ^ 2
          skinny = 18.5
          normal = 25.0
          fat = 30.0
```

Do note that where bindings are just normal values and functions, and so can do normal function things like pattern match their arguments.

```Haskell
...
where bmi = weight / height ^ 2
      (skinny, normal, fat) = (18.5, 25.0, 30.0)


...
where func (0, 0) = "Origin babyyy"
      func (x, y) = (show x) ++ (show y)
```

---
## Why Use These

> [!check] **Scope!**

By defining a function inside a more local scope, you avoid muddying your codebase with loads of functions that are specific to just a single other function and will never be used again.

You can define values inside blocks with let bindings that only exist in that little bit where they're needed.

> Next: [[Chapter 14 - Currying]]
