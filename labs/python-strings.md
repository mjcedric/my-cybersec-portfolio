# Lab: Working with Strings in Python

**Course:** Google Cybersecurity Professional Certificate — Course 7, Module 2  
**Skills demonstrated:** String manipulation, type conversion, indexing, slicing, ID standardisation, URL parsing  
**Tools used:** Python 3, Jupyter Notebook

---

## Scenario

As a security analyst, I automated three string-handling tasks:
1. Standardising employee IDs to a consistent five-character format
2. Extracting encoded technical information from device IDs
3. Parsing components out of URLs — protocol, website name, and domain extension

String operations underpin a large portion of security automation — log parsing, ID validation, URL analysis, and pattern matching all depend on being able to manipulate and extract data from strings precisely.

---

## Part 1 — Employee ID Standardisation

### Task 1: Type conversion with str()

```python
employee_id = 4186
print(type(employee_id))     # <class 'int'>

employee_id = str(employee_id)
print(type(employee_id))     # <class 'str'>
```

IDs stored as integers cannot be concatenated with strings or have their length checked with `len()`. Converting to a string unlocks string operations while preserving the value.

### Task 2: Length validation with len()

```python
employee_id = str(4186)

if len(employee_id) < 5:
    print("This employee ID has less than five digits. It does not meet length requirements.")
```

`len()` returns the number of characters in a string. Combining it with a conditional creates an automatic compliance check — any ID shorter than five characters is flagged without manual review.

### Task 3: Auto-correct with string concatenation

```python
employee_id = str(4186)

if len(employee_id) < 5:
    employee_id = "E" + employee_id

print(employee_id)    # E4186
```

When the ID fails the length check, it is automatically corrected by prepending `"E"`. The `+` operator concatenates the prefix onto the existing string. This pattern — validate, then auto-fix — is common in data pipeline scripts that process batches of records and need to normalise them before storage.

---

## Part 2 — Device ID Character Extraction

Device IDs encode technical information in specific character positions. Extracting those characters requires indexing and slicing.

### Task 4: Index a single character

```python
device_id = "r262c36"
print(device_id[3])    # 2
```

Python strings are zero-indexed — the first character is at index `0`. Index `3` returns the fourth character (`2`). This is used when a specific position in an ID encodes a known piece of information (device type, region code, hardware version).

### Task 5: Slice a range of characters

```python
print(device_id[0:4])    # r262
```

`[start:end]` returns characters from `start` up to but **not including** `end`. `[0:4]` returns the first four characters. The exclusive end index is a Python convention worth memorising — it means the slice length equals `end - start`.

```
  r   2   6   2   c   3   6
  0   1   2   3   4   5   6   ← index
      └───────┘
      [0:4] = "r262"
```

---

## Part 3 — URL Parsing

### Task 6: Extract the protocol by slicing

```python
url = "https://exampleURL1.com"
print(url[0:8])    # https://
```

`https://` is exactly 8 characters. Slicing `[0:8]` extracts the protocol — useful when validating that all URLs in a log use the secure `https` scheme rather than unencrypted `http`.

### Task 7–8: Locate a substring with .index()

```python
ind = url.index(".com")
print(ind)    # 19
```

`.index()` searches a string for a substring and returns the position where it first appears. Storing this in `ind` makes it reusable — the position doesn't need to be hardcoded for each URL.

### Task 9: Extract the domain extension dynamically

```python
print(url[ind:ind+4])    # .com
```

Using `ind` as the start and `ind + 4` as the end extracts exactly four characters from the found position. This works for any URL of any length — the slice is anchored to where `.com` was found, not to a fixed position.

### Task 10: Extract the website name

```python
print(url[8:ind])    # exampleURL1
```

The website name sits between the end of `https://` (index 8) and the start of `.com` (index `ind`). Using `ind` as the end boundary means this slice also works regardless of how long the website name is.

**Full URL decomposition:**

```
https://exampleURL1.com
│      ││           │└── domain: url[ind:ind+4]
│      ││           └─── ind = 19
│      │└─────────────── website: url[8:ind]
│      └──────────────── starts at index 8
└─────────────────────── protocol: url[0:8]
```

---

## Key Takeaways

| Concept | Purpose in security work |
|---|---|
| `str()` | Convert numeric IDs/ports to strings for manipulation |
| `len()` | Validate field lengths — IDs, passwords, tokens |
| `+` concatenation | Build standardised IDs, log entries, formatted strings |
| `string[i]` | Extract a character at a known position (e.g., type code in device ID) |
| `string[start:end]` | Slice a field out of a fixed-format string |
| `.index(sub)` | Find where a pattern starts — dynamic, not position-dependent |
| `url[ind:ind+4]` | Extract fixed-length data anchored to a found position |

**Why this matters:** Security analysts routinely parse strings that follow structured formats — log lines, device IDs, IP addresses, URLs, file paths, and hash values. Python's string indexing and slicing allow exact extraction of any component without regex, and methods like `.index()` make extraction dynamic enough to handle real-world variation in data length. These techniques appear in log parsers, IOC extractors, data normalisation scripts, and any tool that processes structured text at scale.
