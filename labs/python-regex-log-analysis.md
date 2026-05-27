# Lab: Using Regular Expressions to Find Patterns in Logs

**Course:** Google Cybersecurity Professional Certificate — Course 7, Module 3  
**Skills demonstrated:** Regular expressions, log parsing, IP address validation, IOC matching  
**Tools used:** Python 3, `re` module (`re.findall()`), Jupyter Notebook

---

## Scenario

As a security analyst, I used Python's `re` module to automate two tasks from real log data:
1. Extract device IDs matching a specific pattern to identify machines running an OS that requires an update
2. Extract and validate IP addresses from a login log, then flag any that appear on a known-suspicious list

Both tasks demonstrate how regex replaces slow manual log-reading with fast, precise pattern extraction.

---

## Part 1 — Extracting Device IDs by Pattern

### The data

```
r262c36 67bv8fy 41j1u2e r151dm4 1270t3o 42dr56i r15xk9h 2j33krk 253be78
ac742a1 r15u9q5 zh86b2l ii286fq 9x482kt 6oa6m6u x3463ac i4l56nq r159r1u
```

Devices beginning with `"r15"` are running an OS that needs updating. The task was to extract only those device IDs automatically.

### Pattern and extraction

```python
import re

target_pattern = "r15\w+"
print(re.findall(target_pattern, devices))
```

**Output:**
```python
['r151dm4', 'r15xk9h', 'r15u9q5', 'r159r1u']
```

| Symbol | Meaning |
|---|---|
| `r15` | Literal match — the exact prefix that identifies the target OS |
| `\w` | Any alphanumeric character (letters and digits) |
| `+` | One or more of the preceding symbol |

Together, `r15\w+` matches any string that starts with `r15` followed by one or more alphanumeric characters — precisely the device ID format in the log.

**What each component does if removed:**
- Without `r15`: matches *every* device ID, not just the target OS
- Without `\w`: only matches the literal string `"r15"` with nothing after it
- Without `+`: matches only `"r15"` plus exactly one character (e.g. `"r15x"` not `"r15xk9h"`)

---

## Part 2 — Extracting and Validating IP Addresses

### The log

```
eraab 2022-05-10 6:03:41 192.168.152.148
iuduike 2022-05-09 6:46:40 192.168.22.115
arutley 2022-05-12 17:00:59 1923.1689.3.24        ← invalid
aestrada 2022-05-09 19:28:12 1924.1680.27.57       ← invalid
dkot 2022-05-12 10:52:00 1921.168.1283.75          ← invalid
...
```

The log contained a mix of valid IPv4 addresses and malformed ones (segments with 4+ digits) caused by data collection errors.

### Pattern v1 — Exact three digits per segment

```python
pattern = "\d\d\d\.\d\d\d\.\d\d\d\.\d\d\d"
```

Matches only `xxx.xxx.xxx.xxx` (exactly 3 digits per segment). This excluded valid IPs like `192.168.22.115` (a two-digit segment) and also correctly excluded `1923.1689.3.24`.

### Pattern v2 — Variable digits with +

```python
pattern = "\d+\.\d+\.\d+"
```

`\d+` matches one or more digits — now extracts all IPs including short-segment ones. But it also extracted invalid addresses like `1923.1689.3.24` with 4-digit segments.

### Pattern v3 — Precise range with {1,3}

```python
pattern = "\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}"
valid_ip_addresses = re.findall(pattern, log_file)
```

`{1,3}` specifies a minimum of 1 and maximum of 3 digits per segment — matching valid IPv4 format exactly. Invalid addresses with 4+ digit segments are excluded.

**Extracted valid IPs:**
```python
['192.168.152.148', '192.168.22.115', '192.168.190.178', '192.168.213.128',
 '192.168.96.200', '192.168.247.153', '192.168.174.117', '192.168.148.115',
 '192.168.168.144']
```

### Pattern evolution summary

| Pattern | Extracts | Problem |
|---|---|---|
| `\d\d\d\.\d\d\d\.\d\d\d\.\d\d\d` | Only xxx.xxx.xxx.xxx | Misses valid IPs with 1–2 digit segments |
| `\d+\.\d+\.\d+\.\d+` | All dot-separated numbers | Also extracts invalid 4+ digit segments |
| `\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}` | Valid IPv4 only | Correct — 1 to 3 digits per segment |

---

## Part 3 — Flag Suspicious IP Addresses

```python
flagged_addresses = ["192.168.190.178", "192.168.96.200",
                     "192.168.174.117", "192.168.168.144"]

for address in valid_ip_addresses:
    if address in flagged_addresses:
        print("The IP address", address, "has been flagged for further analysis.")
    else:
        print("The IP address", address, "does not require further analysis.")
```

**Output (selected):**
```
The IP address 192.168.152.148 does not require further analysis.
The IP address 192.168.190.178 has been flagged for further analysis.
The IP address 192.168.213.128 does not require further analysis.
The IP address 192.168.96.200 has been flagged for further analysis.
The IP address 192.168.174.117 has been flagged for further analysis.
The IP address 192.168.168.144 has been flagged for further analysis.
```

The loop combines regex extraction with allow/deny list logic: first `re.findall()` narrows the log down to valid IPs, then the `for` loop checks each one against the flagged list. This two-stage pipeline — extract, then classify — is the standard structure for log-based IOC detection.

---

## Complete Regex Reference from this Lab

| Symbol | Matches |
|---|---|
| `\w` | Any alphanumeric character (a–z, A–Z, 0–9, _) |
| `\d` | Any digit (0–9) |
| `\.` | A literal period (`.` alone means "any character") |
| `+` | One or more of the preceding element |
| `{n,m}` | Between n and m repetitions of the preceding element |

---

## Key Takeaways

| Concept | Purpose in security work |
|---|---|
| `re.findall(pattern, string)` | Extract all matches from a log or data stream |
| `\w+` with literal prefix | Extract structured IDs starting with a known prefix |
| `\d{1,3}` | Match valid IPv4 segments — exclude malformed data |
| Pattern refinement | Iteratively tighten regex to reduce false positives and false negatives |
| `for` + `in flagged_list` | Classify extracted IOCs against a threat intelligence list |

**Why this matters:** Manual log review does not scale. A log file with 10,000 login attempts cannot be read line by line during an active incident. Regular expressions allow analysts to extract every IP, device ID, username, or hash value matching a specific pattern in milliseconds — and then immediately cross-reference against IOC lists. This combination of `re.findall()` with list membership checks is a foundational pattern in Python-based SIEM automation, log parsers, and threat hunting scripts.
