> Previous: [[Chapter 11 - Function Guards]]

A case expression is just anonymous pattern matching, and it's very similar to `select` or `switch` case statements in other languages, but better!

Let me show you an example in Python first:

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

and here it is in Java:

```Java
int dayCode = 3;

String dayName = switch (dayCode) {
	case 1 -> "Monday";
	case 2 -> "Tuesday";
	case 3 -> "Wednesday";
	default -> "Unknown Day";
};
```

You can see that we're iterating through different cases until we find one that matches our variable. Sound familiar? **Pattern Matching!**

The basic syntax of a case expression is:

```Haskell
case <expression> of <pattern> -> <result>
                     <pattern> -> <result>
                     <pattern> -> <result>
```

And re-writing our example as a Haskell case expression gets us this:

```Haskell
getDayName :: Int -> String
getDayName dayCode =
    case dayCode of
        1 -> "Monday"
        2 -> "Tuesday"
        3 -> "Wednesday"
        _ -> "Unknown Day"
```

> [!info] Remember that we're just pattern matching against a variable.
> They're basically a way to pattern match inside a function instead of at the argument level.

The case expression returns the value from the first branch whose pattern matches the input.

> [!important] Going forward
> Any time I talk about pattern matching, and show you new things you can do with pattern matching, just remember that all of it applies to case expressions, too!

> Next: [[Chapter 13 - Local Bindings]]