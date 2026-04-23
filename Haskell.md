
> [!abstract]- How to read this
> This page is designed to be your journey to learning Haskell, top to bottom. It's meant to be the trunk to which you branch out of to learn more about a topic, then come back to when you're done.
> 
> If you need to look up a specific thing, you can simply go to that note. But if you're just starting out, then begin at the top of this page and work your way down, opening each link in a new tab, then returning back to here when you're done.


---
## Some Background Info

As you probably know, Haskell is a functional programming language. It was designed back in 1990 which makes it 18 years newer than C and 22 years younger than TypeScript. It's also just **one year older than Python**, seriously.

![[Pasted image 20260423165750.png|350]]

It was designed back in 1990 and is named after Haskell Curry, a guy who liked Logic (Logician). Curry is infamous for the concept of "Currying 🍛" which he helped develop and is named after him. We will come across currying later, it's foundational to Haskell.

---
## Properties of Haskell

Haskell does many things different to other languages. While most languages have at least one of these properties, it's the combination of all of them that makes Haskell (and other languages like it) unique.

> [!info] Strong Static Typing
> Data types are strictly enforced at compile time.
> 
> Every variable will be checked to make sure it's not being used anywhere it shouldn't be. This is similar to languages like Java, C, C++, C# and Go and dissimilar to languages like Python and JavaScript.

> [!check] Type Inference
> The compiler can automatically deduce data types based on usage and context, but it will still verify that all the assumptions it makes align with each other (strong, static typing).
> 
> Languages like OCaml, Rust, Swift do this as well, and languages like C++ and Java incorporate this with features like the `auto` or `var` keywords.

> [!example] Lazy Evaluation
> Expressions are only computed when necessary, more on this later.

> [!tip] Purely Functional
> Haskell is a [[The Functional Programming Paradigm|Purely Functional]] language. This is the biggest one that separates Haskell from basically every other programming language, and is what makes it so different to program in.

