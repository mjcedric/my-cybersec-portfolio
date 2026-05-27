# Lab: Creating Loops in Python

**Course:** Google Cybersecurity Professional Certificate — Course 7, Module 1  
**Skills demonstrated:** Python iteration, for/while loops, IP allow-list enforcement, loop control with break  
**Tools used:** Python 3, Jupyter Notebook

---

## Scenario

As a security analyst, I automated three repetitive security tasks using Python loops:
1. Displaying network connection failure messages
2. Checking login attempts against an IP allow list and halting on unauthorised access
3. Generating unique employee IDs for a department

---

## Part 1 — Network Connection Messages

### Task 1–2: for loop with range()

```python
for i in range(3):
    print("Connection could not be established.")
```

```
Connection could not be established.
Connection could not be established.
Connection could not be established.
```

`range(3)` generates the sequence `[0, 1, 2]` — the loop body executes once per value. Passing a variable instead of a literal makes the count configurable:

```python
connection_attempts = 3
for i in range(connection_attempts):
    print("Connection could not be established.")
```

In a real system, `connection_attempts` would be set by policy (e.g., lock an account after 5 failed attempts). Driving the loop from a variable means the threshold can be updated in one place without touching the logic.

### Task 3: while loop for the same task

```python
connection_attempts = 0
while connection_attempts < 3:
    print("Connection could not be established.")
    connection_attempts += 1
```

`while` loops repeat until a condition becomes False. The key difference from `for`:

| Loop type | Use when |
|---|---|
| `for` | You know the exact number of iterations in advance |
| `while` | You repeat until a condition is met — count may not be known upfront |

For connection retries, a `while` loop is often more natural in production code — retry until connected or until a maximum is reached, whichever comes first.

---

## Part 2 — IP Allow List Enforcement

### Task 4: Iterate over a list of IP addresses

```python
ip_addresses = ["192.168.142.245", "192.168.109.50", "192.168.86.232",
                "192.168.131.147", "192.168.205.12", "192.168.200.48"]

for i in ip_addresses:
    print(i)
```

Iterating directly over a list (rather than using `range(len(...))`) is cleaner Python and reads as: "for each IP address in the list, do something." This pattern is the foundation of any log-processing loop.

### Task 5: Check each IP against an allow list

```python
allow_list = ["192.168.243.140", "192.168.205.12", "192.168.151.162",
              "192.168.178.71",  "192.168.86.232",  "192.168.3.24",
              "192.168.170.243", "192.168.119.173"]

ip_addresses = ["192.168.142.245", "192.168.109.50", "192.168.86.232",
                "192.168.131.147", "192.168.205.12", "192.168.200.48"]

for i in ip_addresses:
    if i in allow_list:
        print("IP address is allowed")
    else:
        print("IP address is not allowed")
```

**Output:**
```
IP address is not allowed
IP address is not allowed
IP address is allowed
IP address is not allowed
IP address is allowed
IP address is not allowed
```

Combining a `for` loop with an `in` check against an allow list is a fundamental pattern in security automation — it scales to any list size and the logic stays identical whether checking 6 addresses or 600,000.

### Task 6: Halt on unauthorised access with break

```python
for i in ip_addresses:
    if i in allow_list:
        print("IP address is allowed")
    else:
        print("IP address is not allowed. Further investigation of login activity required.")
        break
```

**Output:**
```
IP address is not allowed. Further investigation of login activity required.
```

`break` exits the loop immediately when an unauthorised IP is encountered. This reflects a real security design decision: when accessing **restricted data**, the first unauthorised attempt should stop processing and trigger an escalation path — rather than silently logging all attempts and continuing. Continuing the loop after a policy violation could mask how many other IPs were checked after the breach point.

---

## Part 3 — Employee ID Generation

### Task 7: while loop to generate IDs

```python
i = 5000
while i <= 5150:
    print(i)
    i += 5
```

Generates all integers between 5000 and 5150 divisible by 5 — 31 unique IDs. The `while` loop is appropriate here because the boundary condition (`<= 5150`) is the natural termination criterion, not a count.

### Task 8: Embed an alert at a threshold

```python
i = 5000
while i <= 5150:
    print(i)
    if i == 5100:
        print("Only 10 valid employee IDs remaining")
    i += 5
```

The `print(i)` statement is placed **before** the conditional — ensuring every ID is printed regardless of whether the alert fires. If it were inside the `if`, only the value `5100` would be printed and all others would be silent. This ordering — print first, then check — is a common pattern when you need both a continuous output and conditional side-effects at specific points in the iteration.

---

## Key Takeaways

| Concept | Purpose in security automation |
|---|---|
| `for i in range(n)` | Repeat an action a fixed number of times (retry limits, lockout thresholds) |
| `for i in list` | Process every item in a dataset (IP logs, user lists, alert queues) |
| `while condition` | Repeat until a state changes — ideal for polling or retry logic |
| `if x in list` | Allow/deny list membership check — scalable to any list size |
| `break` | Halt processing immediately on a policy violation |
| Loop variable ordering | Controls what executes unconditionally vs. conditionally each iteration |

**Why this matters:** Almost every security automation script involves iteration — scanning a list of IP addresses, processing lines in a log file, retrying a failed connection, or generating identifiers. Knowing when to use `for` vs `while`, how to enforce allow/deny logic inside a loop, and how to use `break` to halt on a violation are the building blocks of practical Python for security work.
