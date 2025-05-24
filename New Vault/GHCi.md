---
categories:
  - "[[Module Notes]]"
module: "[[Functional Programming Module]]"
authors:
  - "[[Matthew]]"
templates:
  - "[[Module Note Template]]"
template_version: "1"
TARGET DECK: Programming Paradigms::Functional Programming
---
(You need to have installed GHCup in order to have `ghci`)
## Running GHCi

Open PowerShell and type `ghci` into the terminal.

![[Pasted image 20250503162740.png|450]]

`ghci` commands start with a colon `:`.

To quit, type `:quit` or `:q`.

---

## Executing Simple Expressions

You type your expression into the console, just like any other interpreter.

![[Pasted image 20250503164014.png|200]]

---

## Type Command

You can ask GHCi what the type of an expression is using the `:t` or `:type` command.

```Haskell
:t not
not :: Bool -> Bool
```

 ---
## Info Command

You can ask GHCi for information about a type, typeclass, function etc. with the `:i` or `:info` command.

```Haskell
:i not
not :: Bool -> Bool     -- Defined in `GHC.Classes'
```

```Haskell
:i Num
type Num :: * -> Constraint
class Num a where
  (+) :: a -> a -> a
  (-) :: a -> a -> a
  (*) :: a -> a -> a
  negate :: a -> a
  abs :: a -> a
  signum :: a -> a
  fromInteger :: Integer -> a
  {-# MINIMAL (+), (*), abs, signum, fromInteger, (negate | (-)) #-}
        -- Defined in `GHC.Num'
instance Num Double -- Defined in `GHC.Float'
instance Num Float -- Defined in `GHC.Float'
instance Num Int -- Defined in `GHC.Num'
instance Num Integer -- Defined in `GHC.Num'
instance Num Word -- Defined in `GHC.Num'
```

```Haskell
:i Integer
type Integer :: *
data Integer
  = GHC.Num.Integer.IS GHC.Prim.Int#
  | GHC.Num.Integer.IP GHC.Prim.ByteArray#
  | GHC.Num.Integer.IN GHC.Prim.ByteArray#
        -- Defined in `GHC.Num.Integer'
instance Read Integer -- Defined in `GHC.Read'
instance Enum Integer -- Defined in `GHC.Enum'
instance Eq Integer -- Defined in `GHC.Num.Integer'
instance Integral Integer -- Defined in `GHC.Real'
instance Num Integer -- Defined in `GHC.Num'
instance Ord Integer -- Defined in `GHC.Num.Integer'
instance Real Integer -- Defined in `GHC.Real'
instance Show Integer -- Defined in `GHC.Show'
```

---

## Load Command

You can load a Haskell `.hs` source code file with the `:l` or `:load` command.

```Haskell
:l yellowstone
```

### Main Command

You can execute the main function of a loaded file using the `:main` command.

```Haskell
:l yellowstone
-- [1 of 2] Compiling Main             ( yellowstone.hs, interpreted )
-- Ok, one module loaded.
:main
"Hello Yellowstone!"
```
