# Lab: Finding Files with Linux Commands

**Course:** Google Cybersecurity Professional Certificate — Course 4, Module 3  
**Skills demonstrated:** Linux CLI, file system navigation, log inspection  
**Tools used:** `pwd`, `ls`, `cd`, `cat`, `head`

---

## Scenario

As a security analyst, I was given access to an analyst workstation and tasked with locating and reviewing specific files inside the `/home/analyst` directory. This mirrors a real-world situation where an analyst must quickly navigate an unfamiliar Linux system to find logs, user records, or reports during an investigation.

---

## What I Did

### 1. Confirmed working directory and listed contents

```bash
pwd
ls
```

`pwd` prints the absolute path of the current directory — useful at the start of any investigation to confirm you're in the right place before running further commands. `ls` revealed the top-level structure of the analyst's home directory.

### 2. Navigated to the reports directory and inspected subdirectories

```bash
cd reports
ls
```

Navigating into `reports` and listing its contents showed the subdirectories available — in this case, a `users` subdirectory among others. Understanding the directory layout is a prerequisite to efficiently finding evidence during incident response.

### 3. Reviewed user account data

```bash
cd users
cat Q1_added_users.txt
```

`cat` displays the full contents of a file. The `Q1_added_users.txt` file contained records of user accounts added in Q1. Reviewing new account additions is a standard step when investigating potential insider threats or unauthorized access — new accounts created at unusual times can be indicators of compromise.

### 4. Inspected the top of a log file

```bash
cd ../../logs
head <logfile>
```

`head` outputs the first 10 lines of a file by default. On large log files, reading the full file is impractical — `head` lets an analyst quickly check the format, timestamps, and most recent (or earliest) entries before deciding how to filter further.

---

## Key Takeaways

| Command | Purpose in security work |
|---------|--------------------------|
| `pwd` | Confirm location before any destructive or sensitive operation |
| `ls` | Map out directory structure during initial triage |
| `cd` | Move through the file system without a GUI |
| `cat` | Read small files like configs, user lists, or short logs |
| `head` | Sample large log files efficiently |

Being fluent in Linux file navigation is foundational for any blue team role — the majority of servers, SIEM agents, and forensic environments run on Linux. Analysts who can move through the CLI quickly reduce dwell time during active incidents.
