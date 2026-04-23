> Previous: [[Chapter 4 - The Basic Types of Haskell]]

Take this Python snippet:

```python
# Return the first element of the list.
def head(lst)
	return lst[0]

head([1,2,3,4]) # 1
head("hello") # 'h'
```

Notice how we were able to input both a list of integers, and a string (list of characters) into the same function? That's *polymorphism*! Using what you know about Haskell currently, this is impossible. To make it possible, I will introduce to you **Type Variables**.

The `head` functions actually exists in the Haskell standard library, and its type decorator is this:

```Haskell
head :: [a] -> a
```

What? How can a list be type `a`? It doesn't even start with a capital, smh.

The type variable lets you put any type into the function and it will sort of "auto-complete" the signature. For example:

- If you use it on a list of integers, then it becomes `head :: [Int] -> Int`.
- If you use it on a list of characters, then it becomes `head :: [Char] -> Char`.

It's important to know that the `a` must always be the same type when used. This is better illustrated on a multi-argument function.

```Haskell
-- This comp function takes two lists of the same type and returns whether they are the same.
comp :: [a] -> [a] -> Bool
```

Since this function's arguments are both `[a]`, the type that `a` is "representing" must be the same in both instances.

> [!warning] You could compare `[Int]` and `[Int]` but not `[Int]` and `[Char]`.

You can think of it like a contract that says "these two types will always be the same".

You can have multiple type variables in the same function.

```Haskell
-- The zip function takes two lists and "zips" it up into a single list of tuples.
zip :: [a] -> [b] -> [(a, b)]
```

`a` and `b` do not have to be the same type (but they could be), they just have to be consistent across the usage. So, if you input `[Int]` and `[Char]`, you will get a `[(Int, Char)]`. Or the other way around, if you want a `[(Float, String)]`, then you have to input a `[Float]` and `[String]`.

> [!danger] This system has its limitations
> What if you had a polymorphic function that added two numbers together, such that you could have a single function operate on `Int`, `Integer`, `Float` or `Double`. You could do this:
> 
> ```Haskell
> add :: a -> a -> a
> ```
> 
> But then, what happens if you try and put two `Char`s into it? Universe collapses. To solve this problem, we need **Class Constraints**, explained in the next section.

> Next: [[Chapter 6 - Class Constraints]]
