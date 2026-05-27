# Lab: Creating Conditional Statements in Python

**Course:** Google Cybersecurity Professional Certificate — Course 7  
**Skills demonstrated:** Python conditionals, logical operators, list membership, Boolean logic, access control automation  
**Tools used:** Python 3, Jupyter Notebook

---

## Scenario

As a security analyst, I needed to automate two checks:
1. Whether a user's operating system requires a security update
2. Whether a login attempt was made by an approved user during organisation hours

Both checks reflect real automation tasks in security operations — patch compliance and access control enforcement — where manual review at scale is impractical.

---

## Part 1 — OS Update Check

### Task 1–3: if / else

```python
system = "OS 3"

if system == "OS 2":
    print("no update needed")
else:
    print("update needed")
```

`if` evaluates a condition; `else` handles every other case. OS 2 is the only up-to-date system — any other value means an update is required. The `==` operator performs an exact string match.

### Task 4–5: elif and logical operators

```python
system = "OS 2"

if system == "OS 2":
    print("no update needed")
elif system == "OS 1" or system == "OS 3":
    print("update needed")
```

`elif` (else-if) adds named branches for specific cases, avoiding a catch-all `else` that would also trigger on unexpected values like `"OS 4"`. The `or` operator combines both outdated OS conditions into a single branch, keeping the code concise without losing precision.

**Key distinction:** `else` catches *everything* not matched above — including invalid input. `elif` catches *only* the specific values listed. In security automation, this matters: an unexpected system value should not silently pass as "update needed" or "no update needed" without being flagged.

---

## Part 2 — Login Access Control

### Task 6: Checking against two approved users

```python
approved_user1 = "elarson"
approved_user2 = "bmoreno"
username = "bmoreno"

if username == approved_user1 or username == approved_user2:
    print("This user has access to this device.")
else:
    print("This user does not have access to this device.")
```

Checking each approved user individually works but does not scale. Adding a third user requires modifying the condition itself.

### Task 7: Scaling with an allow list and `in`

```python
approved_list = ["elarson", "bmoreno", "tshah", "sgilmore", "eraab"]
username = "alfarop"

if username in approved_list:
    print("This user has access to this device.")
else:
    print("This user does not have access to this device.")
```

The `in` operator checks whether a value exists anywhere in a list — replacing a chain of `or` conditions with a single, readable expression. Adding or removing approved users now only requires updating the list, not the logic. This is the foundation of allow-list-based access control in Python.

### Task 8: Time-based access check with a Boolean

```python
organization_hours = True

if organization_hours == True:
    print("Login attempt made during organization hours.")
else:
    print("Login attempt made outside of organization hours.")
```

Boolean variables represent binary states — on/off, allowed/denied, within-hours/outside-hours. In a real system, `organization_hours` would be derived from a timestamp comparison rather than hardcoded, but the conditional logic remains the same.

### Task 10: Combining both checks with `and`

```python
approved_list = ["elarson", "bmoreno", "tshah", "sgilmore", "eraab"]
username = "bmoreno"
organization_hours = True

if username in approved_list and organization_hours == True:
    print("Login attempt made by an approved user during organisation hours.")
else:
    print("Username not approved or login attempt made outside of organisation hours.")
```

`and` requires **both** conditions to be True for the `if` branch to execute. If either check fails — wrong user *or* wrong time — the `else` branch fires. This mirrors real access control policy: a login should only succeed when the user is authorised *and* the timing is valid.

---

## Complete Logic Summary

```
                    ┌─────────────────────┐
                    │  username in list?   │
                    └──────┬──────┬────────┘
                          YES     NO
                           │       │
             ┌─────────────▼──┐   └──► "Not approved"
             │ org hours == T?│
             └──┬──────┬──────┘
               YES     NO
                │       │
    "Approved" ◄┘       └──► "Outside org hours"
```

---

## Key Takeaways

| Concept | Purpose in security automation |
|---|---|
| `if / elif / else` | Branch logic for OS compliance checks, alert triage, policy enforcement |
| `==` operator | Exact match — username, OS version, status codes |
| `or` | Match any of several allowed/denied values |
| `and` | Enforce multiple conditions simultaneously (user *and* time) |
| `in` operator | Check list membership — scalable allow/deny lists |
| Boolean conditions | Model binary security states: approved/denied, active/inactive |

**Why this matters:** Conditional statements are the building block of every security automation script — from checking whether a file hash matches a known-bad list, to routing alerts by severity, to validating login attempts against an allow list. Python's readability makes these logic flows easy to audit and maintain, which is critical when the code enforces access control decisions.
