# Lab: Managing Files with Linux Commands

**Course:** Google Cybersecurity Professional Certificate — Course 4, Module 3  
**Skills demonstrated:** Linux file system management, directory structuring, text editing via CLI  
**Tools used:** `mkdir`, `rmdir`, `mv`, `rm`, `touch`, `nano`, `ls`

---

## Scenario

As a security analyst, I was responsible for keeping the `/home/analyst` directory properly organized. The directory had accumulated stray files and an unused subdirectory, and a report file was sitting in the wrong location. I needed to restructure the directory, clean up obsolete files, and document the changes made.

Maintaining a clean, well-structured file system is directly relevant to security work — misplaced logs or reports can slow down incident response, and leaving unused files around increases the noise analysts must filter through.

**Starting structure:**
```
home/
└── analyst/
    ├── notes/
    │   ├── Q3patches.txt
    │   └── tempnotes.txt
    ├── reports/
    │   ├── Q1patches.txt
    │   └── Q2patches.txt
    └── temp/
```

**Target structure:**
```
home/
└── analyst/
    ├── logs/
    ├── notes/
    │   └── tasks.txt
    └── reports/
        ├── Q1patches.txt
        ├── Q2patches.txt
        └── Q3patches.txt
```

---

## What I Did

### Task 1 — Create the logs directory

```bash
mkdir /home/analyst/logs
ls /home/analyst
```

`mkdir` creates a new directory. Dedicated directories for logs keep log data separate from reports and notes — a basic but important housekeeping practice that makes it easier to apply different retention policies or permissions to each type of data.

### Task 2 — Remove the unused temp directory

```bash
rmdir /home/analyst/temp
ls /home/analyst
```

`rmdir` removes an empty directory. Removing unused directories reduces clutter and eliminates locations where files might accidentally be placed and overlooked during investigations.

### Task 3 — Move a misplaced report file

```bash
cd /home/analyst/notes
mv Q3patches.txt /home/analyst/reports/
ls /home/analyst/reports
```

`mv` moves a file from one location to another. The `Q3patches.txt` file belonged in `reports/` alongside the other quarterly patch notes — having it in `notes/` would make it hard to find and inconsistent with the expected directory layout. After moving, `ls` confirmed all three quarterly files were present in `reports/`.

### Task 4 — Delete an obsolete file

```bash
rm /home/analyst/notes/tempnotes.txt
ls /home/analyst/notes
```

`rm` permanently deletes a file. Removing `tempnotes.txt` — a temporary working file no longer needed — keeps the `notes/` directory clean. In a real environment, unused temp files can accumulate sensitive data and should be removed as part of routine hygiene.

### Task 5 — Create a new file for task documentation

```bash
touch /home/analyst/notes/tasks.txt
ls /home/analyst/notes
```

`touch` creates an empty file without opening an editor. It's the standard way to initialize a new file from the command line before writing to it.

### Task 6 — Document the changes using nano

```bash
nano /home/analyst/notes/tasks.txt
```

Added the following to `tasks.txt`:

```
Completed tasks
1. Managed file structure in /home/analyst/
```

`nano` is a terminal-based text editor. Being able to create and edit files entirely within the CLI is essential on remote servers and headless systems where no GUI is available — which describes the majority of production Linux environments a security analyst will encounter.

---

## Key Takeaways

| Command | Purpose in security work |
|---|---|
| `mkdir` | Create directories for organizing logs, reports, or evidence |
| `rmdir` | Remove empty directories during cleanup |
| `mv` | Relocate files to their correct location |
| `rm` | Delete obsolete or temporary files |
| `touch` | Initialize new files (logs, documentation, scripts) |
| `nano` | Edit files directly in the terminal without a GUI |

The ability to manage a Linux file system from the CLI is foundational for security analysts. Whether preserving evidence in a forensic investigation, organizing collected logs during incident response, or maintaining scripts and documentation on a remote server — these commands are used daily in real SOC environments.
