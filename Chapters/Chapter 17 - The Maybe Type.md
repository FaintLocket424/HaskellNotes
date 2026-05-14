> Previous: [[Chapter 16 - Lists]]

As I mentioned in [[Chapter 5 - The Basic Types of Haskell]], Haskell has no `null` value. Instead, we handle possible values with the `Maybe a` [[Chapter 15 - Type Constructors|Type Constructor]].

It's used to represent a value which may or may not be present, hence the name. These are good for functions which have the possibility of failing.

- A possible `Int` would be a `Maybe Int`.
- A possible `String` would be a `Maybe String`.

---
## Maybe Values

You know Booleans? A Boolean value can be either `True` or `False`.

A `Maybe a` can take two forms:

- `Nothing`
- `Just a`

> [!info] Context
> Remember that these are the **values** that a variable of type `Maybe a` can take. This is exactly the same as how `0` or `1` are values that a variable of type `Int` can take.

In practice, using `Maybe a` looks like this:

```Haskell
x = Just 5 -- Maybe Int
y = Nothing -- Maybe Int

z = Just "Hello" -- Maybe String
w = Nothing -- Maybe String
```

> [!warning] The Container Analogy
> When beginning to learn Haskell, you will often see things compared to a container that contains values.
> 
> This works well at the beginning but it falls apart the further you go, so be prepared to broaden your thinking later.

You can think of `Maybe a` as wrapping a value of type `a` in a "box" that you can peek into. Either the value is there (`Just a`), or it isn't (`Nothing`).

Note how `Nothing` is the same regardless of the type it references.

> [!Example] Example 1
> Imagine this scenario:
> 
> > You're creating a function which tells you the index of the first occurrence of an item in a list.
> > 
> > How should you handle the case where you try to locate the index of an item that isn't in the list?
> 
> We could make our function return a `Maybe Int`, where if the item exists in the list, it returns the index in a `Just`, e.g. `Just 3`. And if the item does not exist in the array, it returns a `Nothing`.
> 
> ```Haskell
> locateItem :: (Eq a) => [a] -> a -> Maybe Int
> ```
> 
> And we can see this function in action:
> 
> ```Haskell
> locateItem [4, 7, 2, 3, 5] 7
> -- Just 1
> 
> locateItem "I'm always watching" 'a'
> -- Just 4
> 
> locateItem [1..10] 69
> -- Nothing
> 
> locateItem "" 'a'
> -- Nothing
> ```
> 
> This is a much better solution than just having the program crash.

> [!example] Example 2
> Another common example is the `safeHead` function, a safer version of the existing `head` function that - instead of crashing - returns a `Nothing` on an empty list.
> 
> ```Haskell
> safeHead :: [a] -> Maybe a
> safeHead []    = Nothing
> safeHead (x:_) = Just x
> ```

---
## Why Maybe Exists

It's all about **Type Safety**.

Let's look at some Python code to illustrate the issue. *Can you see the error?*

```Python
def get_user_phone(user_id):
    database = {1: "555-0123", 2: "555-9876"}
    return database.get(user_id)

def send_sms_notification(user_id, message):
    phone = get_user_phone(user_id)
    
    formatted_phone = phone.strip() 
    
    print(f"Sending '{message}' to {formatted_phone}")

send_sms_notification(1, "Hello!")

send_sms_notification(3, "Hi!")
```

The problem is the phone number formatting. We don't check that the user ID returned an actual phone number, and not `None`. So when we try to remove the whitespace from it, Python will crash with an `AttributeError`.

> [!check] In Haskell, this scenario is impossible because we are **forced to handle the `Nothing` case**.

Take the `locateItem` function from before, that returns a `Maybe Int`. Let's say we wanted to get the index right after the first `'a'`. In most languages, you would just do something like `locateItem(lst, 'a') + 1` but this doesn't work in Haskell because you cannot add an `Int` and a `Maybe Int`. You first must "extract" the `Int` from the "box" using something like pattern matching.

> [!warning] It's important to remember that you cannot perform operations like `(+)` or `(*)` on a value if it is enclosed in a `Maybe` context. You **must** first extract the value from the context in order to use it.

---
## Pattern Matching Maybe

One way to get the value out of a `Maybe a` is to pattern match.

```Haskell
main :: IO ()
main = case index of
    Just a  -> print $ "Value at index " ++ (show a)
    Nothing -> print $ "Value not in list"
    where index = locateItem [1..10] 9
```

Here, we're taking index (a `Maybe Int`), and using a case expression to pattern match the two possible values it could take:

- If it's `Just a`, then the value inside the `Maybe` is bound to `a` and we can use it like a normal variable.
- If it's `Nothing`, then there is no value and we proceed as such.

> [!info] In the future, we'll learn ways to get around having to pattern match the variable out; instead we will work within the confines of the `Maybe a` context (this is what Functors and Applicatives are for).

You can also write functions that accept a `Maybe a` as an input, and then pattern matches the argument:

```Haskell
greetUser :: Maybe String -> String
greetUser (Just name) = "Hello, " ++ name
greetUser Nothing     = "Hello, mysterious stranger!"
```

---

## From Maybe, To Normal

Inside the `Data.Maybe` module, there is a function `fromMaybe` which may be useful to you.

```Haskell
import Data.Maybe (fromMaybe)

fromMaybe :: a -> Maybe a -> a
```

The function takes a default value and a `Maybe` value, then returns either:

- The value inside the `Maybe` if it's a `Just a`.
- Else, it returns the default value if it's a `Nothing`.

This gives you an easy way to extract the value out of the `Maybe a` context, while still handling the `Nothing` case if there is a sensible default value.

> Next: [[Chapter 18 - The Either Type]]
