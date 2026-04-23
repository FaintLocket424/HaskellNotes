Haskell does many things different to other languages. While most languages have at least one of these properties, it's the combination of all of them that makes Haskell unique.

> [!info] Strong Static Typing
> - Static typing means data types are strictly enforced at compile time.
> - Strong typing means that once the data type of a variable is set, it cannot be changed.
> 
> Every variable will be checked to make sure it's not being used anywhere it shouldn't be. This is similar to languages like Java, C, C++, C# and Go and dissimilar to languages like Python and JavaScript.

> [!check] Type Inference
> The compiler can automatically deduce data types based on usage and context, but it will still verify that all the assumptions it makes align with each other (strong, static typing).
> 
> Languages like OCaml, Rust, Swift do this as well, and languages like C++ and Java incorporate this with features like the `auto` or `var` keywords.

> [!example] Lazy Evaluation
> Expressions are only computed when necessary, more on this later in [[Chapter TODO - Lazy Evaluation]].

> [!tip] Purely Functional
> Haskell is a Purely Functional language, meaning every function **must** be pure (*you'll find out what this means in [[Chapter 3 - The Functional Paradigm]]*). This is the biggest one that separates Haskell from basically every other programming language, and is what makes it so different to program in.

![[meme_haskell_hoopla.gif|250]]

> Next: [[Chapter 3 - The Functional Paradigm]]
