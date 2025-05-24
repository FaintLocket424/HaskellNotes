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
aliases:
  - Pure Function
---
## Criteria for a function to be pure

1. The function must be **deterministic**. The exact same input arguments must always produce the exact same output return value.
2. The function must have **no [[Side Effects]]**. It must not cause any changes outside of its own scope, like modifying global variables, writing to files or printing to the console.

## Examples 

```Python
# A pure function, output is completely determined by the input. f(1) === f(1).
def f(n):
	n ** 2
```

```Python
state = 0

# An impure function, it's output is determined by both its argument and a global state. Note that it's still impure, even though it has no side effect. g(1) != g(1)
def g(n):
	return n + state
```

```Python
state = 0

# An impure function. Not only is its output determined by an external state, but it also has a side effect, modifying the external state. h(1) !== h(1)
def h(n):
	global state
	state += 1
	return n * n
```

