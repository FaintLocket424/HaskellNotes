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
Very similar to [[Let Bindings in Haskell|Let Bindings]] are Where bindings.

Take this function `bmiTell` which provides a message of _encouragement_ when you provide it your weight and height.

```Haskell
bmiTell :: (RealFloat a) => a -> a -> String
bmiTell weight height
    | weight / height ^ 2 <= 18.5 = "You're underweight, you emo, you!"
    | weight / height ^ 2 <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"
    | weight / height ^ 2 <= 30.0 = "You're fat! Lose some weight, fatty!"
    | otherwise                   = "You're a whale, congratulations!"
```

Notice how we have some very similar code, especially `weight / height ^ 2`. In any other language we would probably define a variable and then use it a bunch. We can do this in Haskell too!

We can use a Where binding to define a variable that we can use across all of our guards.

```Haskell
bmiTell :: (RealFloat a) => a -> a -> String
bmiTell weight height
    | bmi <= 18.5 = "You're underweight, you emo, you!"
    | bmi <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"
    | bmi <= 30.0 = "You're fat! Lose some weight, fatty!"
    | otherwise   = "You're a whale, congratulations!"
    where bmi = weight / height ^ 2
```

In fact, that's one of the great features of the where binding. it's available across all of the guards in a function. Writing out code in this way has many benefits for speed and readability. In fact, we can go even further than before.

```Haskell
bmiTell :: (RealFloat a) => a -> a -> String
bmiTell weight height
    | bmi <= skinny = "You're underweight, you emo, you!"
    | bmi <= normal = "You're supposedly normal. Pffft, I bet you're ugly!"
    | bmi <= fat    = "You're fat! Lose some weight, fatty!"
    | otherwise     = "You're a whale, congratulations!"
    where bmi = weight / height ^ 2
          skinny = 18.5
          normal = 25.0
          fat = 30.0
```

One thing the where binding cannot do is span across different patterns in a function. You have to define the function globally to get that functionality.

It's a common design pattern to define a function, then define several helper functions using where, then give those functions helper functions with more where clauses.

## Pattern Matching

You can even pattern match in where bindings.

```Haskell
...
where bmi = weight / height ^ 2
	  (skinny, normal, fat) = (18.5, 25.0, 30.0)
```

or we can do

```Haskell
...
where (x:xs) = someInputList
      (_:ys) = someOtherList
```

## Defining Functions 

You can also define a function inside a where binding.

```Haskell
calcBmis :: (RealFloat a) => [(a, a)] -> [a]
calcBmis xs = [bmi w h | (w, h) <- xs]
	where bmi weight height = weight / height ^ 2
```

