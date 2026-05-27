# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this project is

A professional cybersecurity portfolio for **Cedric** (GitHub: `mjcedric`, email: `nzuimanto@gmail.com`), built from labs completed during the **Google Cybersecurity Professional Certificate**. Each lab is written up as a professional markdown document explaining the security relevance, not just the commands.

**GitHub repo:** https://github.com/mjcedric/my-cybersec-portfolio (public)  
**Local path:** `C:\Users\mbako\Desktop\my-cybersec-portfolio\`

---

## Repository structure

```
README.md                        ← Portfolio landing page with lab tables by topic
CLAUDE.md                        ← This file
certifications/
  google-cybersecurity-certificate.pdf
labs/                            ← One markdown write-up per lab
  linux-*.md
  sql-*.md
  cryptography-*.md
  network-*.md
  incident-response-*.md
  python-*.md
notebooks/                       ← Jupyter .ipynb files (GitHub renders them natively)
  python-*.ipynb
```

---

## Workflow for adding a new lab

1. Read the source file (Word doc or Jupyter notebook the user provides)
2. Write a markdown write-up in `labs/` following the template below
3. If there is a Jupyter notebook, copy it to `notebooks/` with a matching filename
4. Add a row to the correct section table in `README.md`
5. Commit and push: one commit per lab, descriptive message

```bash
git add labs/<name>.md notebooks/<name>.ipynb README.md
git commit -m "Add <topic> lab write-up and notebook (<key skills>)"
git push
```

---

## Lab write-up template

```markdown
# Lab: <Title>

**Course:** Google Cybersecurity Professional Certificate — Course X, Module Y
**Skills demonstrated:** <comma-separated skills>
**Tools used:** <tools>

---

## Scenario
<1-2 sentences: what the analyst task was>

---

## Part N — <Section title>
<task-by-task breakdown with code blocks and explanation of WHY each step matters>

---

## Key Takeaways

| Concept | Purpose in security work |
|---|---|
| ... | ... |

**Why this matters:** <paragraph connecting the lab to real SOC/analyst work>
```

---

## Labs completed (as of May 2026)

### Linux & Command Line
- `linux-file-navigation.md` — pwd, ls, cd, cat, head
- `linux-grep-filtering.md` — grep, pipes
- `linux-file-management.md` — mkdir, rmdir, mv, rm, touch, nano
- `linux-manage-authorization.md` — ls -la, chmod, least privilege
- `linux-user-management.md` — useradd, usermod, chown, userdel, groupdel
- `linux-help-commands.md` — whatis, man, apropos

### SQL & Data Analysis
- `sql-basic-queries.md` — SELECT, WHERE, ORDER BY, LIKE
- `sql-advanced-filters.md` — BETWEEN, AND, OR, NOT, date/time filtering

### Cryptography
- `cryptography-decrypt-message.md` — Caesar cipher, tr, openssl AES-256-CBC
- `cryptography-file-hashing.md` — sha256sum, cmp, avalanche effect

### Network Security
- `network-wireshark-packet-analysis.md` — display filters, protocol layers
- `network-tcpdump-packet-capture.md` — live capture, pcap, hex/ASCII
- `network-tcpdump-vs-wireshark.md` — tool comparison and combined workflow
- `network-suricata-signatures.md` — IDS rules, fast.log, eve.json, jq

### Incident Response & Threat Intelligence
- `incident-response-virustotal-ioc.md` — VirusTotal, Pyramid of Pain, MITRE ATT&CK

### Python Scripting
- `python-conditional-statements.md` — if/elif/else, Boolean access control
- `python-loops.md` — for/while, break, IP allow-list
- `python-functions.md` — def, string concatenation
- `python-functions-advanced.md` — sorted/max, return vs print, ratio alerting
- `python-strings.md` — str/len/indexing/slicing/.index()
- `python-algorithm-access-control.md` — parallel lists, .append/.remove/.index, login()
- `python-regex-log-analysis.md` — re.findall, \w+, \d{1,3}, IOC flagging

---

## Certifications

| Certification | Status |
|---|---|
| Google Cybersecurity Professional Certificate | Completed |
| CompTIA Security+ | In Progress |
| Splunk Core Certified User | Planned |

---

## Git identity

```
git config --global user.name "mjcedric"
git config --global user.email "nzuimanto@gmail.com"
```
