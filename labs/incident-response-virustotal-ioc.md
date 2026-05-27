# Lab: Investigating a Suspicious File Hash with VirusTotal

**Course:** Google Cybersecurity Professional Certificate — Course 6  
**Skills demonstrated:** Threat intelligence analysis, IOC identification, Pyramid of Pain, malware triage, VirusTotal  
**Frameworks used:** Pyramid of Pain, MITRE ATT&CK

---

## Scenario

As a Level 1 SOC analyst at a financial services company, I received an alert about a suspicious file downloaded on an employee's computer. The employee had received an email with a password-protected spreadsheet attachment — the password was included in the same email, a common social engineering technique designed to bypass automated attachment scanning. When the employee opened the file, a malicious payload executed on their machine.

**Incident timeline:**

| Time | Event |
|------|-------|
| 1:11 PM | Employee receives email with file attachment |
| 1:13 PM | Employee downloads and opens the file |
| 1:15 PM | Multiple unauthorized executable files are created |
| 1:20 PM | IDS detects the executables and alerts the SOC |

I was given the SHA256 hash of the malicious file and tasked with using VirusTotal to identify additional indicators of compromise (IOCs) and map them to the Pyramid of Pain.

---

## The File Hash

```
SHA256: 54e6ea47eb04634d3e87fd7787e2136ccfbcc80ade34f246a12cf93bab527f6b
```

A SHA256 hash serves as a unique fingerprint for a file. Submitting it to VirusTotal does not upload the file itself — only the hash — which means the actual malicious content is never transmitted.

---

## VirusTotal Analysis

### Detection Tab
The **Vendors' ratio** showed a high number of security vendors flagging the file as malicious, paired with a negative **Community Score** from the VirusTotal community. The Security Vendors' Analysis listed the specific malware family names assigned by different antivirus engines, providing cross-vendor confirmation of malicious classification.

> A high vendor ratio + negative community score + named malware family detections = high-confidence malicious verdict.

### Details Tab
The Details tab revealed additional hash representations of the same file:
- **MD5** — an older, faster hash (no longer collision-resistant, but still used as a secondary identifier)
- **SHA-1** — intermediate strength hash
- **SHA-256** — the primary, most reliable identifier

These alternate hashes are useful because different threat intelligence platforms and SIEM rules may index malware by different hash types. Recording all three ensures the IOC can be matched across tools.

### Relations Tab
The Relations tab exposed the network infrastructure this malware communicated with — **contacted IP addresses** and **domain names**. The Detections column indicated which of those endpoints had been independently flagged as malicious by security vendors, allowing me to distinguish between legitimate background connections and confirmed malicious C2 (command-and-control) infrastructure.

### Behavior Tab
The Behavior tab aggregated reports from multiple **sandbox environments** where the file was executed under controlled conditions. Sandbox reports revealed:
- **Files created** — the unauthorized executable files the malware dropped on the host
- **Registry modifications** — persistence mechanisms written to the Windows registry
- **Process activity** — child processes spawned by the malicious payload
- **MITRE ATT&CK TTPs** — tactics and techniques mapped to the ATT&CK framework

---

## Pyramid of Pain — IOCs Identified

The **Pyramid of Pain** (by David Bianco) categorises IOCs by how difficult they are for attackers to change once detected and blocked. The higher the level, the more painful it is for an attacker to lose it.

```
        /\
       /TT\         Tactics, Techniques & Procedures  ← hardest to change
      /----\
     /Tools \       Attacker tooling
    /--------\
   / Network  \     Network & host artifacts
  /  Artifacts  \
 /--------------\
/  Domain Names  \  C2 domains
/----------------\
/  IP Addresses   \  C2 IPs
/------------------\
/   Hash Values     \ ← easiest to change (attacker recompiles to get a new hash)
 --------------------
```

| Pyramid Level | IOC Found | Source |
|---|---|---|
| **Hash value** | MD5/SHA-1 of the same malware file | VirusTotal → Details tab |
| **IP address** | Contacted C2 IP address (flagged malicious) | VirusTotal → Relations tab |
| **Domain name** | Malicious C2 domain contacted by malware | VirusTotal → Relations tab |
| **Network/host artifact** | Executable files dropped on the host | VirusTotal → Behavior tab |
| **Tools** | Attacker tooling identified in sandbox | VirusTotal → Behavior tab |
| **TTPs** | MITRE ATT&CK techniques (e.g., T1566 Phishing, T1059 Command Execution) | VirusTotal → Behavior tab |

---

## Key Takeaways

**Why password-protected attachments bypass scanners:** Email security gateways cannot scan the contents of an encrypted/password-protected file — the malicious payload is hidden until the user enters the password and opens the file. This technique is a deliberate attacker choice to evade automated detection.

**Why hash-only IOCs have limited shelf life:** At the bottom of the Pyramid of Pain, hash values are trivial for an attacker to change — recompiling or slightly modifying the binary produces an entirely different hash. Blocking a hash stops *this exact file* but not the next variant. Higher-level IOCs (domains, tools, TTPs) are far more costly for attackers to change and provide more durable detection coverage.

**VirusTotal limitations:** No single tool is authoritative. A low detection count does not mean a file is safe — new or targeted malware may not yet appear in vendor databases. VirusTotal is most valuable as a corroborating source alongside sandbox analysis, behavioral EDR data, and threat intelligence feeds.

| Tool/Concept | Purpose in security work |
|---|---|
| VirusTotal | Crowdsourced threat intelligence — hash, domain, IP, and URL reputation |
| Pyramid of Pain | Prioritise IOC types by attacker cost to replace |
| MITRE ATT&CK | Map observed behaviors to known adversary techniques |
| Sandbox analysis | Observe malware behavior safely in an isolated environment |
| SHA256 hash lookup | Fast, non-destructive first step in malware triage |
