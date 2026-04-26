> Previous: [[Chapter 10 - Pattern Matching]]

Let's say we want to write the function `absolute x` which returns the absolute value of `x`.

We could write this with an `if-else` expression:

```Haskell
absolute x = if (x<0) then (-x) else x
```

But we could also do it with function guards:

```Haskell
absolute x
| x < 0     = -x
| otherwise = x
```

Function guards are Boolean expressions that sit in front of different values the function can take depending on the input. The guards are tested from top-to-bottom, and it takes the first path that returns `True`.

Guards become a better fit over `if-else` expressions once there are many different paths the function could take, or complex rules that determine the right path.

> [!example] Calculating Grades
> Using normal `if-else`, we get this messy code:
> 
> ```Haskell
> evaluateGrade :: Int -> String
> evaluateGrade score = 
> 	if score >= 90 then "A"
> 	else if score >= 80 then "B"
> 	else if score >= 70 then "C"
> 	else "F"
> ```
> 
> But if we switch it out for guards, it becomes much cleaner
> 
> ```Haskell
> evaluateGrade :: Int -> String
> evaluateGrade score
> 	| score >= 90 = "A"
> 	| score >= 80 = "B"
> 	| score >= 70 = "C"
> 	| otherwise   = "F"
> ```

> Next: [[Chapter 12 - Case Expressions]]