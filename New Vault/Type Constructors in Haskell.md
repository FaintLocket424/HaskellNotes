Type Constructors are used to build complex types from simple ones.

You know how I said everything was a function in Haskell? Well, type constructors are functions that apply to types. Yeah.

They're similar to parameterised types in [[The Java Programming Language|Java]], like how a `List` has to have a type specified, like `List<Integer>` or `HashMap<Boolean, String>`.

---

## Concrete Types

Before understanding type constructors, you should know what I mean by a "concrete type", because I'll use that term a lot going forward.

A type is "concrete" if it's fully qualified and thus contains no type parameters.

Variables can only take the value of a concrete type. A variable can't have type `[]` because you don't know what that list is supposed to contain.

Concrete types include `Int`, `Char`, `(Int, Bool)` etc.

Note that once a type constructor has had all its type parameters filled, it becomes a concrete type e.g. `[Int]`.

---

## Lists as Type Constructors

We've already covered [[Lists in Haskell]], but let's just look at them through the lens of type constructors.

Unlike other type constructors, the list is a little different because it has special syntax, the `[]`.

`[a]` is a type constructor, and it has one type parameter `a`.

Once a concrete type is placed inside the brackets, it becomes a concrete list type.

```Haskell
[Int]
[[Char]]
[(Int, Bool)]
```

---

## The Maybe Type

As mentioned previously, Haskell has no `null` value. Possible values are handled with the `Maybe a` type.

It's used to represent a value which may or may not be present, hence the name.

A `Maybe a` can take two forms:

- `Nothing`
- `Just a`

It's defined in Haskell like this:

```Haskell
data Maybe a = Nothing | Just a
```

What do I mean by taking two forms? Well think about `Bool`. It has two possible values, `False` or `True`. This one is different because it contains data of type `a`.

Using `Maybe a` in practice looks like this:

```Haskell
test_1 = Just 5 -- Maybe Int
test_2 = Nothing -- Maybe Int

test_3 = Just "Hello" -- Maybe [Char]
test_4 = Nothing -- Maybe [Char]
```

Notice how the value inside `Just a` must match the type passed to the type constructor.

Also note that `Nothing` is the same regardless of the type parameter `a`. So `Nothing` could be a `Maybe Int`, `Maybe [Char]`, `Maybe ()`.

Think of it like a function in Python which returns a value, but if it encounters an error, returns `None`.

> [!Example]
> This might seem useless, but consider this scenario:
> 
> > You're creating a function which tells you the index of the first occurrence of an item in a list.
> > How should you handle the case where you try to locate the index of an item that isn't in the list?
> 
> We could make our function return a `Maybe Int`, where if the item exists in the list, it returns the index in a `Just`, e.g. `Just 3`. And if the item does not exist in the array, it returns a `Nothing`.
> 
> ```Haskell
> locateItem :: [a] -> a -> Maybe Int
> ```
> 
> And we can see this function in action:
> 
> ```Haskell
> locateItem [4, 7, 2, 3, 5] 7
> -- Just 1
> 
> locateItem "Hey Nathaniel ;)" 'N'
> -- Just 4
> 
> locateItem [1..10] 69
> -- Nothing
> 
> locateItem "" 'a'
> -- Nothing
> ```

---

## The Either Type

Just as a type constructor can have one type parameter, **it can have two**!

The `Either a b` type in Haskell can take the form of either:

```Haskell
Left a
Right b
```

It's defined in Haskell like this:

```Haskell
data Either a b = Left a | Right b
```

> [!example]
> What's the use for this? A big one is error handling.
> 
> Consider division `(/)`. What happens if you try to divide by zero? Program explodes and you all die. Maybe your divisor is user controlled and hence is vulnerable.
> 
> We could counteract this by writing a new function `safeDivide a b` which will return an error message if the divisor `b == 0`, otherwise it returns `a / b`.
> 
> ```Haskell
> safeDivide :: (Floating a) => a -> a -> Either String a
> 
> 10 `safeDivide` 4
> -- Right 2.5
> 
> safeDivide 10 5
> -- Right 2
> 
> 2 `safeDivide` 0
> -- Left "Error: Division by zero"
> ```
> 
> It's common for programs to have many functions which return an `Either String a`, and to return that error up the chain to be printed out to the user.
