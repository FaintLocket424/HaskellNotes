---
categories:
  - "[[Module Notes]]"
module: "[[Functional Programming Module]]"
authors: 
templates:
  - "[[Module Note Template]]"
template_version: "1"
TARGET DECK: Programming Paradigms::Functional Programming
---
Many imperative languages have a "switch case" expression. Some way of running a piece of code depending on the value of a variable.

```Python
day_code = 3

match day_code:
	case 1:
		print("Monday")
	case 2:
		print("Tuesday")
	case 3:
		print("Wednesday")
	case _:
		print("Unknown Day")
```

```Java
int dayCode = 3;

String dayName = switch (dayCode) {
	case 1 -> "Monday";
	case 2 -> "Tuesday";
	case 3 -> "Wednesday";
	default -> "Unknown Day";
};
```

Haskell has a similar concept, except even more powerful.

The basic syntax of a case expression is:

```Haskell
case <expression> of <pattern> -> <result>
                     <pattern> -> <result>
                     <pattern> -> <result>
```

Using the same example as before, but in Haskell:

```Haskell
getDayName :: Int -> String
getDayName dayCode =
    case dayCode of
        1 -> "Monday"
        2 -> "Tuesday"
        3 -> "Wednesday"
        _ -> "Unknown Day"
```

> [!note] Remember, this is just like [[Pattern Matching In Haskell|Pattern Matching]].

During runtime, the value (in this case, `dayCode`), is checked against each branch and the first one that matches will return a value.

---

## Pattern Matching

We can put patterns in the cases for more complex expressions.

Basically, a case expression is for when you want to pattern match while inside a function body.

> [!example] Example 1 - Just like pattern matching
> Say we wanted to make our own `head` function, we could do it like this with function parameter pattern matching:
> 
> ```Haskell
> head' :: [a] -> a
> head' []    = error "No head for empty lists!"
> head' (x:_) = x
> ```
> 
> But using case expressions, it looks like this:
> 
> ```Haskell
> head' :: [a] -> a
> head' xs = case xs of [] -> error "No head for empty lists!"
>                       (x:_) -> x
> ```

> [!example] Example 2 - A more useful *case*
> Let's see an example where you actually need a case expression:
> 
> ```Haskell
> parseCommand :: String -> True
> parseCommand inputStr = 
> 	case (words inputStr) of
> 		["ADD", item]    -> True
> 		["REMOVE", item] -> True
> 		["LIST"]         -> True
> 		[]               -> False
> 		_                -> False
> ```
> 
> Here, we have a function `parseCommand` which take a string command, then splits it up into individual words, then pattern matches against the words.
> 
> We couldn't have used pattern matching on the function parameters because it's just a string, we needed it split by spaces `' '` via words, before we could pattern match on it.
