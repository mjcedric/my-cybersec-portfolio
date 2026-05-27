# Lab: Managing Authorization with Linux File Permissions

**Course:** Google Cybersecurity Professional Certificate — Course 4, Module 3  
**Skills demonstrated:** Linux file permissions, access control, principle of least privilege  
**Tools used:** `ls -la`, `chmod`

---

## Scenario

As a security analyst, I was tasked with auditing and correcting file permissions in the `/home/researcher2/projects` directory for a user called `researcher2`, who belongs to the `research_team` group. Several files had been granted excessive permissions — allowing unauthorized users to read or write sensitive data. My job was to identify every over-permissioned file (including hidden ones) and bring permissions in line with the least-privilege principle.

---

## Understanding Linux Permission Strings

Before making changes, I needed to read the 10-character permission string that `ls -la` displays for each file. Each character has a defined meaning:

```
d r w x r w x r w x
│ ╰──┬──╯ ╰──┬──╯ ╰──┬──╯
│   user   group   other
└─ file type: d = directory, - = regular file
```

| Position | Meaning |
|---|---|
| 1 | File type (`d` = directory, `-` = file) |
| 2–4 | User (owner) permissions: read / write / execute |
| 5–7 | Group permissions: read / write / execute |
| 8–10 | Other (everyone else) permissions: read / write / execute |

A `-` in any position means that permission is not granted.

---

## What I Did

### Task 1 — Audit all permissions, including hidden files

```bash
cd /home/researcher2/projects
ls -la
```

The `-l` flag shows the long listing with permission strings, and `-a` reveals hidden files (names starting with `.`). Running both together gives a complete view of every file and its access level — the starting point for any permission audit.

This revealed:
- Several visible project files with their permissions
- A hidden file `.project_x.txt` that also required review

### Task 2 — Remove write access for "other" on project_k.txt

```bash
chmod o-w project_k.txt
```

`project_k.txt` had write permissions granted to `other` — meaning any user on the system, regardless of group membership, could modify it. No file should allow arbitrary users to write unless explicitly required. The `o-w` argument tells `chmod` to remove (`-`) write (`w`) from other (`o`).

### Task 2 (continued) — Restrict project_m.txt to user only

```bash
chmod g-rw project_m.txt
```

`project_m.txt` was a restricted file — only the owning user should be able to read or write it. The group had read and/or write permissions, which violated the need-to-know principle. Removing both read and write from the group (`g-rw`) ensures only `researcher2` can access the file.

### Task 3 — Fix permissions on a hidden archived file

```bash
ls -la          # confirm .project_x.txt permissions
chmod u-w,g-w,g+r .project_x.txt
```

`.project_x.txt` was a hidden, archived file. Archived files should be read-only — no one should be writing to them. However, both the user and group still needed read access. A single `chmod` call handled multiple changes at once:

| Change | Meaning |
|---|---|
| `u-w` | Remove write from user |
| `g-w` | Remove write from group |
| `g+r` | Ensure group retains read access |

Chaining changes with commas in one `chmod` call is cleaner and reduces the risk of leaving the file in an intermediate insecure state between two separate commands.

### Task 4 — Remove group execute on the drafts directory

```bash
chmod g-x drafts
```

The `drafts` subdirectory should only be accessible to `researcher2`. The group had execute permission on it — which on a directory means the ability to enter it and access its contents. Removing group execute (`g-x`) prevents any other member of `research_team` from navigating into `drafts`, even if they could see it listed.

---

## Key Takeaways

| Command | Purpose |
|---|---|
| `ls -la` | View all permissions including hidden files |
| `chmod o-w <file>` | Remove write access from other users |
| `chmod g-rw <file>` | Remove read and write from group |
| `chmod u-w,g-w,g+r <file>` | Multiple permission changes in one command |
| `chmod g-x <dir>` | Prevent group from entering a directory |

**Why this matters:** Misconfigured file permissions are one of the most common causes of unauthorized data access on Linux systems. The principle of least privilege — granting only the minimum access required — directly reduces the blast radius of a compromised account. An attacker who gains access to a low-privilege user cannot escalate or exfiltrate data if files and directories are correctly locked down. Auditing and correcting permissions is a routine but critical task in both hardening and incident response workflows.
