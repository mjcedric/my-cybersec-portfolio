# Lab: Creating More Functions in Python

**Course:** Google Cybersecurity Professional Certificate — Course 7, Module 2  
**Skills demonstrated:** Built-in functions, parameterised functions, return values, login anomaly detection  
**Tools used:** Python 3, Jupyter Notebook

---

## Scenario

As a security analyst, I worked with a list of monthly failed login attempts to identify suspicious patterns, then progressively built an `analyze_logins()` function capable of detecting abnormal login behaviour for individual users — culminating in an automated alert when the login ratio exceeded a defined threshold.

---

## Part 1 — Analysing Failed Logins with Built-in Functions

```python
failed_login_list = [119, 101, 99, 91, 92, 105, 108, 85, 88, 90, 264, 223]

print(sorted(failed_login_list))
print(max(failed_login_list))
```

**Output:**
```
[85, 88, 90, 91, 92, 99, 101, 105, 108, 119, 223, 264]
264
```

| Function | Purpose |
|---|---|
| `sorted()` | Returns a new list ordered from smallest to largest — makes outliers visible at both ends |
| `max()` | Returns the single largest value — pinpoints the month requiring immediate investigation |

The sorted output immediately reveals that November and December (264 and 223) are significant outliers compared to the rest of the year (85–119). `max()` confirms 264 as the peak — the month to investigate first for anomalous or malicious activity.

---

## Part 2 — Building the analyze_logins() Function

This function was built iteratively across four tasks, each version adding capability.

### Version 1 — Display basic login info (Tasks 3–4)

```python
def analyze_logins(username, current_day_logins):
    print("Current day login total for", username, "is", current_day_logins)

analyze_logins("ejones", 9)
```

**Output:**
```
Current day login total for ejones is 9
```

Two parameters make the function flexible — the same logic applies to any user. Calling it with `"ejones"` and `9` produces a specific, readable message about that user's activity.

### Version 2 — Add average logins parameter (Task 5)

```python
def analyze_logins(username, current_day_logins, average_day_logins):
    print("Current day login total for", username, "is", current_day_logins)
    print("Average logins per day for", username, "is", average_day_logins)

analyze_logins("ejones", 9, 3)
```

Adding `average_day_logins` as a third parameter provides the baseline needed for comparison. Without context of what is *normal* for a user, a raw login count of 9 is meaningless — it could be routine or suspicious depending on their typical pattern.

### Version 3 — Calculate and display the login ratio (Task 6)

```python
def analyze_logins(username, current_day_logins, average_day_logins):
    print("Current day login total for", username, "is", current_day_logins)
    print("Average logins per day for", username, "is", average_day_logins)
    login_ratio = current_day_logins / average_day_logins
    print(username, "logged in", login_ratio, "times as much as they do on an average day.")

analyze_logins("ejones", 9, 3)
```

**Output:**
```
Current day login total for ejones is 9
Average logins per day for ejones is 3
ejones logged in 3.0 times as much as they do on an average day.
```

`login_ratio` quantifies how far today's activity deviates from the user's baseline — a ratio of 3.0 means ejones logged in three times more than usual, which warrants investigation.

### Version 4 — Return the ratio for downstream use (Task 7)

```python
def analyze_logins(username, current_day_logins, average_day_logins):
    print("Current day login total for", username, "is", current_day_logins)
    print("Average logins per day for", username, "is", average_day_logins)
    login_ratio = current_day_logins / average_day_logins
    return login_ratio

login_analysis = analyze_logins("ejones", 9, 3)
print(login_analysis)
```

`return` sends `login_ratio` back to the caller, where it is stored in `login_analysis`. This is the critical upgrade: a function that only `print()`s its result is a dead end — the value disappears after display. A `return` statement makes the result available for further logic, storage, or passing to another function.

### Version 5 — Trigger an alert based on the ratio (Task 8)

```python
login_analysis = analyze_logins("ejones", 9, 3)

if login_analysis >= 3:
    print("Alert! This account has more login activity than normal.")
```

**Output:**
```
Alert! This account has more login activity than normal.
```

The returned ratio is fed directly into a conditional. A threshold of `>= 3` (three times normal activity) triggers the alert. This completes the full detection pipeline:

```
raw data → function → ratio → conditional → alert
```

---

## print() vs return — Key Distinction

| | `print()` inside function | `return` |
|---|---|---|
| Where output goes | Screen only | Back to the caller |
| Can be stored? | No | Yes — `result = function()` |
| Can trigger further logic? | No | Yes — `if result >= 3:` |
| Use case | Human-readable status messages | Values needed for computation or decisions |

In production security automation, `return` is almost always what you want — the calling code decides what to do with the result (log it, alert on it, store it in a database).

---

## Key Takeaways

| Concept | Purpose in security automation |
|---|---|
| `sorted()` | Reveal outliers in login counts, alert volumes, or event frequencies |
| `max()` | Identify the peak value for prioritisation |
| Parameters | Make functions reusable across users, IPs, or time periods |
| `return` | Pass computed results to calling code for decisions and storage |
| Ratio-based threshold | Detect anomalies relative to a user's own baseline — more reliable than absolute thresholds |

**Why this matters:** Threshold-based detection using a user's own baseline is a foundational technique in User and Entity Behaviour Analytics (UEBA). Rather than flagging anyone who logs in more than 5 times (which might be normal for some users), comparing against *each user's own average* surfaces genuine anomalies while minimising false positives. This lab demonstrates that pattern in Python from first principles.
