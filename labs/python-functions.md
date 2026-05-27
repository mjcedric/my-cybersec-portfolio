# Lab: Defining and Calling Functions in Python

**Course:** Google Cybersecurity Professional Certificate — Course 7, Module 2  
**Skills demonstrated:** Python function definition, reusable security automation, string concatenation, iterating over lists  
**Tools used:** Python 3, Jupyter Notebook

---

## Scenario

As a security analyst automating repetitive tasks, I practised two core function patterns:
1. A reusable security alert function that can be called whenever suspicious activity is detected
2. A utility function that converts a list of approved usernames into a formatted string — enabling the data to be logged, written to a file, or passed to another system

---

## Part 1 — Security Alert Function

### Task 1–2: Define and call a basic function

```python
def alert():
    print("Potential security issue. Investigate further.")

alert()
```

**Output:**
```
Potential security issue. Investigate further.
```

**Anatomy of a function definition:**

```
def alert():
│    │      │
│    │      └── parentheses (parameters go here)
│    └── function name
└── def keyword — signals the start of a function definition
```

The function body (indented block) contains the instructions. Defining a function does not execute it — the code only runs when the function is **called** by name.

**Why use a function instead of writing the print statement directly?**  
If the alert needs to appear in 15 different places in a script, a function means writing `alert()` 15 times instead of copying the `print()` statement 15 times. When the message needs to change, it only changes in one place — the definition.

### Task 3: Add a loop inside the function

```python
def alert():
    for i in range(3):
        print("Potential security issue. Investigate further.")

alert()
```

**Output:**
```
Potential security issue. Investigate further.
Potential security issue. Investigate further.
Potential security issue. Investigate further.
```

Functions can contain any Python construct — loops, conditionals, other function calls. Encapsulating a loop inside a function means the entire repeated-alert behaviour is triggered with a single `alert()` call, and the repeat count can be adjusted in one place.

---

## Part 2 — Username List to String Converter

### Task 4–5: Define the function header and iterate over the list

```python
def list_to_string():
    username_list = ["elarson", "bmoreno", "tshah", "sgilmore",
                     "eraab", "gesparza", "alevitsk", "wjaffrey"]
    for i in username_list:
        print(i)

list_to_string()
```

**Output:**
```
elarson
bmoreno
tshah
...
```

Each username prints on its own line. This confirms the loop iterates correctly through the list, but the data is not yet usable as a single value.

### Task 6: Combine usernames with string concatenation

```python
def list_to_string():
    username_list = ["elarson", "bmoreno", "tshah", "sgilmore",
                     "eraab", "gesparza", "alevitsk", "wjaffrey"]
    sum_variable = ""
    for i in username_list:
        sum_variable = sum_variable + i
    print(sum_variable)

list_to_string()
```

**Output:**
```
elarsonbmorenotshahsgilmoreeraabgesparzaalevitskwjaffrey
```

The `+` operator concatenates strings. `sum_variable` starts empty and accumulates each username. The result is a single string — but with no separator, the names run together and are unreadable.

### Task 7: Add a separator for readable output

```python
def list_to_string():
    username_list = ["elarson", "bmoreno", "tshah", "sgilmore",
                     "eraab", "gesparza", "alevitsk", "wjaffrey"]
    sum_variable = ""
    for i in username_list:
        sum_variable = sum_variable + i + ", "
    print(sum_variable)

list_to_string()
```

**Output:**
```
elarson, bmoreno, tshah, sgilmore, eraab, gesparza, alevitsk, wjaffrey,
```

Adding `", "` after each username produces a comma-separated string — a format directly usable in log entries, CSV exports, or as input to another function. The trailing comma is a minor formatting issue addressable by stripping the last two characters (`sum_variable[:-2]`), but the core pattern is established.

---

## Function Structure Summary

```python
def function_name():      # Header: def keyword + name + parentheses + colon
    # Body: indented block
    # All logic goes here
    pass

function_name()           # Call: executes the body
```

---

## Key Takeaways

| Concept | Purpose in security automation |
|---|---|
| `def name():` | Define a reusable block of code |
| Calling a function | Execute that block on demand, as many times as needed |
| Functions with loops | Encapsulate complex repeated behaviour behind a simple call |
| String concatenation (`+`) | Build log entries, formatted reports, or joined data from lists |
| Separator in concatenation | Control output format for readability or downstream processing |

**Why this matters:** Security automation scripts rarely run a task just once. Functions are how analysts package a detection check, an alert, a log formatter, or a data transformation so it can be reused cleanly across a script — or imported into other scripts entirely. A well-named function also makes code self-documenting: `alert()` and `list_to_string()` describe exactly what they do without requiring a comment.
