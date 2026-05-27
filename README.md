# My Cybersecurity Portfolio

![Google Cybersecurity Certificate](https://img.shields.io/badge/Google-Cybersecurity%20Professional%20Certificate-blue?logo=google)

I recently completed the **Google Cybersecurity Professional Certificate**, a hands-on program covering network security, Linux administration, Python scripting, SQL-based log analysis, and SIEM tools. This portfolio documents the labs I completed throughout the program, written up as real-world analyst scenarios.

---

## Labs & Projects

### Linux & Command Line

| Lab | Skills |
|-----|--------|
| [Finding Files with Linux Commands](labs/linux-file-navigation.md) | `pwd`, `ls`, `cd`, `cat`, `head` — file system navigation and log inspection |
| [Filtering with grep](labs/linux-grep-filtering.md) | `grep`, pipes — extracting errors from server logs, filtering files by name, searching user records |
| [Managing Files with Linux Commands](labs/linux-file-management.md) | `mkdir`, `rmdir`, `mv`, `rm`, `touch`, `nano` — restructuring directories, moving and deleting files, CLI text editing |
| [Managing Authorization with Linux Permissions](labs/linux-manage-authorization.md) | `ls -la`, `chmod` — auditing and correcting file/directory permissions, principle of least privilege |
| [Adding and Managing Users](labs/linux-user-management.md) | `useradd`, `usermod`, `chown`, `userdel`, `groupdel` — full user lifecycle from onboarding to offboarding |
| [Getting Help in the Command Line](labs/linux-help-commands.md) | `whatis`, `man`, `apropos` — navigating Linux documentation and discovering commands |

### SQL & Data Analysis

| Lab | Skills |
|-----|--------|
| [Performing SQL Queries for Security Analysis](labs/sql-basic-queries.md) | `SELECT`, `WHERE`, `ORDER BY`, `LIKE` — querying device, login, and employee data |
| [Applying Advanced Filters in SQL](labs/sql-advanced-filters.md) | `BETWEEN`, `AND`, `OR`, `NOT` — date/time filtering, after-hours logins, incident scoping |

### Cryptography

| Lab | Skills |
|-----|--------|
| [Decrypting an Encrypted Message](labs/cryptography-decrypt-message.md) | Caesar cipher, `tr`, `openssl` AES-256-CBC — classical and modern decryption |
| [Creating and Comparing File Hash Values](labs/cryptography-file-hashing.md) | `sha256sum`, `cmp` — file integrity verification, avalanche effect, forensic hashing |

### Network Security

| Lab | Skills |
|-----|--------|
| [Analyzing Network Packets with Wireshark](labs/network-wireshark-packet-analysis.md) | Packet filtering, protocol layer inspection, DNS/TCP analysis, payload search |
| [Capturing Network Traffic with tcpdump](labs/network-tcpdump-packet-capture.md) | `tcpdump` flags, live capture, saving to pcap, hex/ASCII payload inspection |
| [Comparing tcpdump and Wireshark](labs/network-tcpdump-vs-wireshark.md) | Tool selection, environment fit, strengths/limitations, combined workflow |
| [Exploring Signatures and Logs with Suricata](labs/network-suricata-signatures.md) | IDS rule anatomy, custom signatures, fast.log, eve.json, `jq` log parsing |

### Python Scripting

| Lab | Skills |
|-----|--------|
| [Creating Conditional Statements](labs/python-conditional-statements.md) | `if/elif/else`, `and`/`or`, `in` operator, allow lists, Boolean access control — [notebook](notebooks/python-conditional-statements.ipynb) |
| [Creating Loops](labs/python-loops.md) | `for`/`while` loops, IP allow-list enforcement, `break` on unauthorised access, ID generation |
| [Defining and Calling Functions](labs/python-functions.md) | `def`, reusable alert functions, string concatenation, list-to-string conversion |
| [Advanced Functions — Login Anomaly Detection](labs/python-functions-advanced.md) | `sorted()`, `max()`, parameters, `return` vs `print()`, ratio-based threshold alerting |
| [Working with Strings](labs/python-strings.md) | `str()`, `len()`, indexing, slicing, `.index()` — ID standardisation and URL parsing — [notebook](notebooks/python-strings.ipynb) |
| [Developing an Access Control Algorithm](labs/python-algorithm-access-control.md) | Parallel lists, `.append()`, `.remove()`, `.index()`, nested conditionals, `login()` function — [notebook](notebooks/python-algorithm-access-control.ipynb) |
| [Using Regular Expressions to Find Patterns in Logs](labs/python-regex-log-analysis.md) | `re.findall()`, `\w+`, `\d{1,3}`, IP address extraction, IOC flagging — [notebook](notebooks/python-regex-log-analysis.ipynb) |

### Incident Response & Threat Intelligence

| Lab | Skills |
|-----|--------|
| [Investigating a Suspicious File Hash](labs/incident-response-virustotal-ioc.md) | VirusTotal, IOC analysis, Pyramid of Pain, MITRE ATT&CK, sandbox behavior |

---

## About

Aspiring cybersecurity professional with hands-on experience in Linux CLI, network traffic analysis, Python automation, and security information management. Currently building toward a role in security operations or threat analysis.

**Certification:** [Google Cybersecurity Professional Certificate](https://grow.google/certificates/cybersecurity/)
