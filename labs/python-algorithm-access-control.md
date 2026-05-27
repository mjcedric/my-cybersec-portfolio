# Lab: Developing an Access Control Algorithm in Python

**Course:** Google Cybersecurity Professional Certificate — Course 7, Module 3  
**Skills demonstrated:** Algorithm design, parallel list operations, user/device authentication, nested conditionals, functions  
**Tools used:** Python 3, Jupyter Notebook

---

## Scenario

As a security analyst, I built a complete Python algorithm that automates verifying whether a user is approved to access a system *and* whether the device they have brought matches the one assigned to them. The algorithm was developed incrementally — each task adding one layer of logic — culminating in a reusable `login()` function.

---

## Data Model: Parallel Lists

```python
approved_users   = ["elarson", "bmoreno", "sgilmore", "eraab",    "gesparza"]
approved_devices = ["8rp2k75", "hl0s5o1", "4n482ts",  "a307vir",  "3rcv4w6"]
```

The two lists are **synchronised by index**: the user at position `i` in `approved_users` is assigned the device at position `i` in `approved_devices`. This design allows a single index value to cross-reference both lists — find the user's position, then use that same position to retrieve their device.

---

## Building the Algorithm Step by Step

### Task 2 — Onboard a new user with .append()

```python
approved_users.append("gesparza")
approved_devices.append("3rcv4w6")
```

`.append()` adds an element to the end of a list. Both lists must be updated simultaneously to keep them synchronised. In a real IAM system this would be a single transaction; in Python, the two `.append()` calls represent that atomic pairing.

### Task 3 — Offboard a departing user with .remove()

```python
approved_users.remove("tshah")
approved_devices.remove("2ye3lzg")
```

`.remove()` deletes the first matching element from a list. Again, both lists must be updated together — removing just the username without removing the device ID would break the index alignment for every subsequent user.

### Task 4 — Check if a user is approved

```python
if username in approved_users:
    print("The user", username, "is approved to access the system.")
else:
    print("The username", username, "is not approved to access the system.")
```

`in` checks membership in a list in a single readable expression. This is the first gate in the authentication check.

### Task 5–6 — Use .index() to bridge both lists

```python
ind = approved_users.index(username)
print(approved_devices[ind])    # retrieves this user's assigned device
```

`.index()` returns the position of a value in a list. Using that same index on `approved_devices` retrieves the device paired to that user — the key operation that links the two parallel lists.

```
approved_users:   ["elarson", "bmoreno", "sgilmore", ...]
                                              ↑ ind = 2
approved_devices: ["8rp2k75", "hl0s5o1", "4n482ts",  ...]
                                              ↑ approved_devices[2] = "4n482ts"
```

### Task 7 — Verify username AND device together

```python
if username in approved_users and device_id == approved_devices[ind]:
    print("The user", username, "is approved to access the system.")
    print(device_id, "is the assigned device for", username)
```

`and` requires both conditions to be `True`. An approved username with the wrong device still fails — this is the principle of **device binding**: access requires the correct identity *and* the correct hardware. This guards against approved users attempting access from unregistered or stolen devices.

### Task 8 — Handle the wrong-device case with elif

```python
elif username in approved_users and device_id != approved_devices[ind]:
    print("The user", username, "is approved, but", device_id, "is not their assigned device.")
```

The `elif` distinguishes two failure modes:
- User not in list → completely unauthorised
- User in list but wrong device → identity confirmed but device mismatch

Providing specific messages for each case makes the system's output actionable — an analyst receiving the wrong-device alert knows to investigate whether a device was swapped or stolen, rather than treating it as a simple failed login.

---

## Task 9 — Complete login() Function

The full algorithm assembled into a reusable function with nested conditionals:

```python
def login(username, device_id):

    if username in approved_users:
        print("The user", username, "is approved to access the system.")
        ind = approved_users.index(username)

        if device_id == approved_devices[ind]:
            print(device_id, "is the assigned device for", username)
        else:
            print(device_id, "is not their assigned device.")

    else:
        print("The username", username, "is not approved to access the system.")

# Example calls
login("sgilmore", "4n482ts")   # approved user, correct device
login("sgilmore", "8rp2k75")   # approved user, wrong device
login("unknown",  "4n482ts")   # unapproved user
```

**Output:**
```
The user sgilmore is approved to access the system.
4n482ts is the assigned device for sgilmore

The user sgilmore is approved to access the system.
8rp2k75 is not their assigned device.

The username unknown is not approved to access the system.
```

The nested structure handles all three outcomes cleanly:

```
login(username, device_id)
│
├── username IN approved_users?
│   ├── YES → "User approved"
│   │   └── device_id == approved_devices[ind]?
│   │       ├── YES → "Correct device"
│   │       └── NO  → "Wrong device"
│   └── NO  → "User not approved"
```

---

## Key Takeaways

| Concept | Purpose in security work |
|---|---|
| Parallel lists | Link user identities to assigned resources (devices, roles, keys) |
| `.append()` / `.remove()` | Programmatic onboarding and offboarding — keep both lists in sync |
| `.index()` | Cross-reference two aligned lists by position |
| `username in list and device == list[ind]` | Enforce both identity *and* device binding in one condition |
| Nested conditionals | Handle multiple distinct outcomes with precise, actionable messages |
| Encapsulating in `login()` | Make the full check reusable — call once per login attempt |

**Why this matters:** This lab implements the foundations of multi-factor access control in code: something you *are* (username) combined with something you *have* (assigned device). The pattern — parallel data structures, index-based cross-referencing, layered conditional checks — appears in real IAM systems, SIEM correlation rules, and any script that needs to validate multiple attributes of an identity simultaneously.
