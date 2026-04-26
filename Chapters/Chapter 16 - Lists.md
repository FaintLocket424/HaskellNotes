> Previous: [[Chapter 15 - Type Constructors]]

## Creating Lists

Lists are created in Haskell using square brackets like most other languages.

```Haskell
my_list = [1, 2, 3, 4]
```

The `:` operator (pronounced "cons") is used to prepend an item to the front of the list.

```Haskell
my_list = 1 : [2, 3, 4]
-- [1,2,3,4]
```

In fact, the square brackets are just syntactic sugar for prepending items to the front of a list.

```Haskell
my_list = 1 : 2 : 3 : 4 : []
-- [1,2,3,4]
```

Lists in Haskell are implemented as **linked lists**, so any indexing or traversing is linear time $\mathcal{O}(n)$.

But `head`, `tail` or using `(:)` is constant time, $\mathcal{O}(1)$.

---
## List Comprehensions

In maths, we often use set comprehensions to construct new sets from existing ones.

$$
	\{ 2, 4 \} = \{ x | x \in \{ 1 .. 5 \}, x \space \text{mod} \space 2 = 0 \}
$$

> *The set of all integers $x$ between 1 and 5 where $x$ is even.*

Haskell supports similar notation for constructing lists.

```Haskell
[2 * x | x <- [1..5], x `mod` 2 == 0]
-- [4,8]
```

`x <- [1..5]` is the generator.
```x `mod` 2 == 0``` is the guard predicate. More on this later.
`2 * x` is the output from each iteration.

Just like the set notation, we're taking the list from the generator as `x`, then filtering `x` by the predicate.

In fact, the predicate is not even necessary.

```Haskell
[2 * n - 1 | n <- [1..5]]
-- [1,3,5,7,9]
-- First 5 odd numbers
```

Comprehensions can contain multiple generators.

```Haskell
[(x,y) | x <- [1,2,3], y <- [4,5]]
-- [(1,4),(1,5),(2,4),(2,5),(3,4),(3,5)]
```

You'll notice that the right-most generator is run for each item in the left-most generator.

- `x=1`
	- `y=4`
	- `y=5`
- `x=2`
	- `y=4`
	- `y=5`
- `x=3`
	- `y=4`
	- `y=5`

This is like a nested for-loop in Python, the rightward generators will run completely for every iteration of a leftward one.

Later (rightward) generators can reference variables from earlier (leftward) ones.

```Haskell
[(x,y) | x <- [1..3], y <- [x..3]]
-- [(1,1),(1,2),(1,3),(2,2),(2,3),(3,3)]
```
> *All pairs $(x,y)$ such that $x,y \in \{1,2,3\}$ and $y \ge x$

### Guard Predicates 

We can restrict the values using guard predicates.

A guard can be any function that returns a `Bool`.

Guards and generators can be freely interspersed, but a guard or generator can only refer to expressions that come before them.

```Haskell
[(x,y) | x <- [1..3], even x, y <- [x..3]]
-- [(2,2), (2,3)]

[(x, y) | x <- [1..3], y <- [x..3], even x, even y]
-- [(2, 2)]

[(x, y) | x <- [1..3], even x, even y, y <- [x..3]]
-- error: Variable not in scope: y :: Integer
```

### Let Bindings

You can use a let binding within a list comprehension to create a new variable or function you can use.

```Haskell
calcBmis :: [(Float, Float)] -> [Float]
calcBmis xs = [bmi | (w, h) <- xs, let bmi = w / h ^ 2]
```

Here, we create the `bmi` variable which takes the value of `w / h ^ 2` for each pair in the initial list. We can then do things with this like create a guard based off it:

```Haskell
calcBmis :: [(Float, Float)] -> [Float]
calcBmis xs = [bmi | (w, h) <- xs, let bmi = w / h ^ 2, bmi >= 25.0]
```

Note how we omit the `in` portion since the scope of the let binding is pre-determined when used inside of a list comprehension. Anything to the right of it, or the output section, can see it.

> [!info] You can still use a proper `let <bindings> in <expression>` but its scope will be limited to the `in` expression, not the whole comprehension.
> ```Haskell
> calcBmis :: [(Float, Float)] -> [(Float, Float)]
> calcBmis xs = [(w, h) | (w, h) <- xs, let bmi = w / h ^ 2 in bmi >= 25.0]
> ```

---
## Pattern Matching

In [[Chapter 10 - Pattern Matching]], we saw that we could pattern match literals like:

```Haskell
describeNumber :: Int -> String
describeNumber 0 = "It's zero!"
describeNumber 1 = "It's one!"
describeNumber n = "It's some other number: " ++ show n
```

We can do the same thing to lists, but we have even more flexibility. There are 3 main patterns to use:

```Haskell
[] -- Empty List
[a, b] -- A 2-element list made up of a and b
(x:xs) -- An n-element list (n > 1) where the first element is x, and the rest of the list is xs.
```

1. In the first case, this pattern will only match empty lists and nothing else.
2. In the second case, this pattern will only match lists which are of length 2, and it will bind the first element to `a`, and the second element to `b`.
3. In the third case, this pattern will only match lists whose length is at least 1, binding the first value to `x` and binding the rest of the list (which could be empty) to `xs`.

Let's take an example list, `[1,2,3]`. This would fail to match against `[]` as it's not empty, and it would fail to match against `[a,b]` because it's not the right length, but it would match against `(x:xs)`. In that case, `x=1` and `xs=[2,3]`.

> [!warning] There's nothing special about `a`, `b`, `x` or `xs`. Those are just variable names and can be anything.
> `[first:rest]` is a valid pattern, just uncommon.

Let's look at some examples.

> [!example]- Example 1 - Length
> 
> What about implementing our own list length function? Well, we could use pattern matching to recursively compute the length.
> 
> ```Haskell
> length :: [a] -> Int
> length []     = 0
> length (_:xs) = 1 + length xs
> ```
> 
> First, notice that we make the equals signs be in the same place, it's just a style thing.
> 
> Second, notice how no matter what list goes into this, it will always be broken down until it reaches that base case. We'll touch on this more in the recursion section.

> [!example]- Example 2 - Adding Two Elements
> 
> Say we wanted a function which parsed commands, like `"set randomTickSpeed 20"`.
> 
> We could do this by using the `words` function which splits a `String` into it's words in a `[String]`.
> 
> ```Haskell
> parseCommand :: [String] -> Bool
> parseCommand ["set", key, value] = True
> parseCommand ["get", key] = True
> parseCommand ["help"] = True
> parseCommand [] = False
> parseCommand _ = False
> 
> parseCommand $ words "set randomTickSpeed 20"
> -- True
> 
> parseCommand $ words "get randomTickSpeed"
> -- True
> 
> parseCommand $ words "get randomTickSpeed this is not quite what it was expecting"
> -- False
> 
> parseCommand $ words []
> -- False
> ```

---
## List Functions

> [!important] You have to remember for this section that Haskell lists are **linked lists**, and that affects the time complexity of certain operations compared to arrays.

### Cons

As mentioned earlier, the `(:)` operator is known as "cons" and it prepends an item to the front of a list.

```Haskell
'a' : "bc" == "abc"
1 : [2, 3] == [1,2,3]
```

Note that it can only be a single item prepended, so you can't add a list to the front.

```Haskell
"ab" : "cd"
-- Couldn't match type `Char' with `[Char]'
```

### Append

Concatenating two lists together is done with `(++)`.

```Haskell
[1, 2] ++ [3, 4] == [1, 2, 3, 4]
"Hello " ++ "World!" == "Hello World!"
```

This operation is much slower than `(:)` so use cons whenever you can.

### Head

The first element in a list can be extracted in constant time $\mathcal{O}(1)$ with `head`.

```Haskell
head [1, 2, 3] == 1
head "Haskell" == 'H'

head []
-- *** Exception: Prelude.head: empty list
```

Note the exception raised with an empty list.

Also note how it returns a single element, so if you're dealing with strings then remember that `""` is a string and `''` is a character.

### Tail

The tail of a list (list minus head) can be extracted in constant time $\mathcal{O}(1)$ with `tail`.

```Haskell
tail [1, 2, 3] == [2, 3]
tail "Haskell" == "askell"

tail []
-- *** Exception: Prelude.tail: empty list
```

Note that `tail` does not return the last item in the list. That's a far more expensive operation.

Also note the exception raised with an empty list.

### Last

The last item in a list can be extracted in linear time $\mathcal{O}(n)$ with `last`.

```Haskell
last [1, 2, 3] == 3
last "Haskell" == 'l'

last ""
-- *** Exception: Prelude.last: empty list
```

Note the exception raised with an empty list.

### Init

The list without it's last element can be extracted in linear time $\mathcal{O}(n)$ with `init`.

```Haskell
init [1, 2, 3] == [1, 2]
init "Haskell" == "Haskel"

init []
-- *** Exception: Prelude.init: empty list
```

### Length

The length of a list can be obtained in linear time $\mathcal{O}(n)$ with `length`.

```Haskell
length [] == 0
length [1, 2, 3] == 3
length "Haskell" == 7
```

### Reverse

The reverse ordering of a list can be obtained in linear time $\mathcal{O}(n)$ with `reverse`.

```Haskell
reverse [] == []
reverse [1, 2 ,3] == [3, 2, 1]
reverse "Haskell" == "lleksaH"
```

### Index

The item at a specific index in a list can be extracted in linear time $\mathcal{O}(n)$ with `(!!)`.

```Haskell
[10, 20, 30] !! 1 == 20
"Haskell" !! 0 == 'H'

"abc" !! 10
-- *** Exception: Prelude.!!: index too large
```

Note the exception if the index is invalid.

### Elem

A value can be checked whether it's an element of a list in linear time $\mathcal{O}(n)$ with `elem`

```Haskell
elem 3 [1, 2, 3, 4] == True
5 `elem` [1, 2, 3, 4] == False
'a' `elem` "team" == True
```

### Zip

Combine two lists into a list of pairs with `zip`.

```Haskell
zip [1, 2, 3] "abc" == [(1, 'a'), (2, 'b'), (3, 'c')]
[1, 2] `zip` [3, 4, 5, 6] == [(1, 3), (2, 4)]
```

Note how the longer list in the second example has been truncated to the length of the shorter one.

### Unzip

Separate a list of pairs into two lists with `unzip`

```Haskell
unzip [(1, 'a'), (2, 'b'), (3, 'c')] == ([1, 2, 3], "abc")
```

### Take

Return the first `n` elements of a list with `take`.

```Haskell
take 3 [1..5] == [1, 2, 3]
take 10 [1, 2, 3] == [1, 2, 3]
```

Note that if you try and request more items than the list has, you just get the whole list.

### Drop

Remove the first `n` elements from a list with `drop`.

```Haskell
drop 3 [1..5] == [4, 5]
drop 10 [1, 2, 3] == []
```

If you drop more items than the list has, you'll get back an empty list.

### Take While

Get the first `n` elements that satisfy a given function with `takeWhile`

```Haskell
takeWhile (<5) [1, 3, 6, 4, 9] == [1, 3]
```

> [!warning] Currying
> If the `(<5)` looks weird to you, go back to [[Chapter 14 - Currying]] to understand why this works.

### Drop While 

Remove the first `n` elements that satisfy a given function with `dropWhile`

```Haskell
dropWhile (<5) [1, 3, 6, 4, 9] == [6, 4, 9]
```

> Next: [[Chapter 17 - The Maybe Type]]
