> Previous: [[Chapter 3 - The Functional Paradigm]]

How you get Haskell installed differs depending on your operating system.

But regardless of OS, the software you want is [GHCup](https://www.haskell.org/ghcup/), which will install for you the **Glasgow Haskell Compiler** `ghc`, as well as other helpful software like `ghci`, the Haskell language server, cabal, stack etc.

To get it installed, go to the GHCup website and copy the terminal command displayed, it should be the right one for your operating system. Open a terminal window **that is not an administrator terminal**, paste in the command and run it.

> You should generally be weary of running commands you find on the internet but it's not an admin terminal and they're a trusted source.

You can likely just accept the default settings in the installer, with the exception of the Haskell Language Server which you probably want to install as it may be required by your code editor.

> [!danger] Think your installer has frozen?
> It probably hasn't, it just takes forever. Let it cook 🔥.

---
## GHCi

One of the most important tools you have access to now is `ghci`. It's an interactive session where you can type in Haskell code and see its output, just like if you type `python` in the terminal you get an interactive Python session.

It has a bunch of commands that will be extremely useful to you while you're learning Haskell, and I'll bring them up as they become relevant. GHCi commands all start with a `:` and can look something like `:t` or `:type`.

For now, make sure you know how to start a GHCi session by typing `ghci` into the terminal. If it says command not found, try restarting your computer and if that doesn't work, try running the installer again and definitely make sure everything gets to finish since **it can take a long time**.

> Next: [[Chapter 5 - The Basic Types of Haskell]]
