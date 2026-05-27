# Lab: Getting Help in the Linux Command Line

**Course:** Google Cybersecurity Professional Certificate — Course 4, Module 3  
**Skills demonstrated:** Linux documentation navigation, command discovery, self-sufficient CLI use  
**Tools used:** `whatis`, `man`, `apropos`

---

## Scenario

As a security analyst working in a Linux environment, I needed to look up unfamiliar commands, find the right options for a specific task, and discover commands I didn't yet know. This lab simulated those real-world moments where you know *what* you need to do but need to confirm the exact syntax or flag — without leaving the terminal.

---

## What I Did

### Task 1 — Quick command lookups with whatis and man

```bash
whatis cat
man cat
```

`whatis` returns a single-line description of a command — useful for a quick memory jog without leaving the workflow. `man` opens the full manual page, including all available options, argument formats, and usage examples.

Knowing how to read a `man` page efficiently is a practical skill: security analysts regularly work with unfamiliar tools on hardened systems where internet access may be restricted. The manual is always available locally.

### Task 1 (continued) — Discover an unknown command with apropos

```bash
apropos first part of file
apropos -a first file
```

`apropos` searches man page summaries for a keyword or phrase, returning a list of matching commands. When you know the *behaviour* you need but not the command name, `apropos` is the right tool. The `-a` flag restricts results to entries that match **all** supplied keywords, which narrows the output when a single keyword returns too many results.

In this case, searching for a command that prints the first part of a file returns `head` — the same command used in the file navigation lab.

### Task 2 — Find the right flag for useradd

```bash
man useradd
```

The goal was to find the option for setting an account expiration date on a temporary user. Reading the `useradd` manual revealed the `-e` flag (`--expiredate`), which accepts a date in `YYYY-MM-DD` format. Using `man` before running a privileged command like `useradd` is good practice — a misused flag on a user-management command can create unintended access or lock out a legitimate user.

### Task 3 — Distinguish between similar commands

```bash
whatis rm
whatis rmdir
```

`rm` removes files; `rmdir` removes empty directories only. A quick `whatis` on both commands clarified the difference without opening a full manual page. This is the fastest way to confirm which command applies to your current situation when two commands seem similar.

### Task 4 — Discover an unknown command by keyword

```bash
apropos create new group
```

Searching with descriptive keywords returned `groupadd` as the relevant command — confirming the correct tool for creating a new group without guessing or searching online.

---

## Key Takeaways

| Command | When to use it |
|---|---|
| `whatis <cmd>` | Quick one-line reminder of what a command does |
| `man <cmd>` | Full reference — all flags, syntax, and examples |
| `apropos <keyword>` | Discover the right command when you know the task but not the name |
| `apropos -a <kw1> <kw2>` | Narrow results by requiring all keywords to match |

**Why this matters:** In a real SOC or IR engagement, analysts frequently encounter unfamiliar systems, custom tools, or commands they haven't used recently. The ability to navigate Linux documentation efficiently — without relying on internet searches — is a mark of genuine CLI fluency. It also reduces the risk of running a command with incorrect flags on a production or forensic system where mistakes are costly.
