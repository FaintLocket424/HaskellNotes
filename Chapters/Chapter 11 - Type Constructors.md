> Previous: [[Chapter 10 - Currying]]

Type Constructors are how we build complex types from simple ones.

> Remember when I said that everything in Haskell is a function... well type constructors are kind of functions that apply to types. yeah...

> [!info] If you've programmed in Java or C# before, they're kind of like the generics in those languages, where you don't have a `List`, you have a `List<Integer>` or `HashMap<Boolean, String>`.

---
## Concrete Types

Before understanding type constructors, you should know what I mean by a "concrete type".

I mentioned back in [[Chapter 5 - The Basic Types of Haskell]] that a concrete type was a "simple type that fully describes itself". I'll expand on what that means here.

What I mean by "fully describes itself" is that you don't need any additional information to know exactly what the type is. A variable could not be of type `[]` because you don't know what's going inside the list. This is just like C where you have to specify what type a list holds, or what type a pointer points to, so the compiler knows what to do with it, how much memory to allocate etc.

Concrete types in Haskell include:

- `Int`
- `Char`
- `(Int, Bool)`
- `[Int]`

Note how once the list type constructor has its type filled in, it becomes a concrete type.

---
## Type Constructors

A type constructor is a type which requires other types to fully describe it. The best example is what we've been using, a list.

We can "construct" a list of something by using the list type constructor `[]` on a type like `Int` to construct a list of ints `[Int]`.

Most type constructors have a syntax like `Type a` or `Type a b`, where you would use it like `Type Int` or `Type String Char`. However, lists are unique because they have special syntax and are instead written as `[a]` with it between the brackets like `[Int]`. Just know for the future that lists are unique and all the others will look different to them.

> [!warning]- Advanced - Kinds
> There is a concept in Haskell known as **Kinds**, and it's a representation of how many types a type constructor needs in order to be concrete.
> 
> You can see it using the `:k` command in `ghci`.
> 
> The kind of any concrete type is `*`, and this means it needs no extra types to be concrete.
> 
> The kind of a list is `* -> *`, and this means it needs 1 extra type to be concrete.
> 
> The kind of the constructor `Either` is `* -> * -> *` because it needs 2 extra types to be concrete.
> 
> Notice how the kind syntax looks like the function syntax, this is by design. A type constructor is a function on types and so a kind of `* -> *` means it takes 1 concrete type and returns a concrete type, but `* -> * -> *` means it takes 2 concrete types and returns a concrete type.

> Next: [[Chapter 12- Lists]]
