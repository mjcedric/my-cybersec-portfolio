# Lab: Filtering with grep

**Course:** Google Cybersecurity Professional Certificate — Course 4, Module 3  
**Skills demonstrated:** Linux CLI, log filtering, file searching, piping  
**Tools used:** `grep`, `ls`, `cd`, pipe operator `|`

---

## Scenario

As a security analyst, I was given access to a Linux workstation containing server logs and user data files. My task was to extract specific information from those files — error entries from a server log, files matching a naming pattern, and user account records for specific departments or individuals.

This mirrors a real investigative workflow: during an incident, analysts often need to quickly sift through large log and user files without reading them line by line.

---

## What I Did

### Task 1 — Extract error messages from a server log

```bash
cd /home/analyst/logs
grep error server_logs.txt
```

`grep` scans a file line by line and prints every line that contains the specified string. Filtering a log file for the word `error` immediately surfaces failed events without manually reading hundreds of lines. In a real incident, this is often the first step in identifying what went wrong and when.

### Task 2 — Find files by name pattern using a pipe

```bash
cd /home/analyst/reports/users
ls | grep Q1
ls | grep access
```

The pipe operator `|` passes the output of one command directly into another as input. Here, `ls` lists all files in the directory, and `grep` filters that list to only show files whose names contain the target string.

This technique is useful when a directory contains dozens of similarly named files (e.g., quarterly reports, access logs) and you need to scope down quickly — for example, finding all Q1 files when investigating activity in a specific time window.

### Task 3 — Search user records for specific accounts and departments

```bash
grep jhill Q2_deleted_users.txt
grep "Human Resources" Q4_added_users.txt
```

Searching user data files for a specific username confirms whether that account was added or removed during a given period — relevant when investigating unauthorized access or verifying offboarding procedures.

The second command uses quotes around `"Human Resources"` because `grep` treats unquoted spaces as argument separators. Quoting multi-word strings is a common requirement when searching for phrases in log data.

---

## Key Takeaways

| Command / Technique | Purpose in security work |
|---|---|
| `grep <string> <file>` | Extract matching lines from logs or data files |
| `ls \| grep <string>` | Filter a file listing by name pattern |
| `grep "multi word"` | Search for phrases — requires quoting |
| Pipe `\|` | Chain commands to filter and refine output without writing temp files |

`grep` is one of the most frequently used tools in a SOC analyst's daily workflow. Whether parsing authentication logs, searching for a specific IP or username across multiple files, or triaging error output from a service — the ability to filter efficiently directly affects how fast an analyst can respond to an alert.
