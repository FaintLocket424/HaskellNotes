> [!important] Side Effect 
> A side effect is a state change that results from a function modifying objects external to its parameter list, like a global variable.

> [!warning]
> Note that simply **reading** a global variable does not qualify as a side effect. Side effects are only when you write to something outside of the function.

Common examples of side effects include:

- Modifying variables outside the function's local scope (global variables).
- Printing to console.
- Writing to files.
- Making network requests.

> [!example]
> Imagine a function `setup_logging_system()` whose job it is to initialise the logger system for a codebase.
> 
> Then imagine another function `read_config_file()` which uses a global file handling object to load config values into memory.
> 
> Now imagine that the logging system is removed, and so `setup_logging_system()` is removed.
> 
> What the programmers doing this refactor didn't take into account, is that `setup_logging_system()` was responsible for initialising the global file handler to handle it's log files. And now with it removed, the `read_config_file()` function will fail.
> 
> That's a side effect.

