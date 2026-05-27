# Lab: Creating and Comparing File Hash Values

**Course:** Google Cybersecurity Professional Certificate — Course 5  
**Skills demonstrated:** File integrity verification, cryptographic hashing, byte-level file comparison  
**Tools used:** `sha256sum`, `cmp`, `cat`, output redirection (`>>`)

---

## Scenario

I was given two files — `file1.txt` and `file2.txt` — that appeared to contain identical content when read normally. My task was to determine definitively whether the files were truly identical by generating and comparing their SHA-256 hash values.

This mirrors a real forensic and security scenario: files can look the same to a human reader while containing hidden differences at the byte level — whether due to tampering, malware injection, steganography, or data corruption. Hash comparison is the definitive test.

---

## What I Did

### Task 1 — Display file contents and observe the deception

```bash
cat file1.txt
cat file2.txt
```

Both files displayed identical text output. To a human reviewer, they were indistinguishable. This is precisely why hash verification exists — the eye cannot detect subtle byte-level differences, but a hash function will.

### Task 1 (continued) — Generate SHA-256 hashes

```bash
sha256sum file1.txt
sha256sum file2.txt
```

Despite the files appearing identical, the two commands produced **completely different hash values**. SHA-256 generates a unique 256-bit (64 hex character) fingerprint for any input. Even a single changed byte — invisible to `cat` — produces a radically different hash output. This property is known as the **avalanche effect**.

### Task 2 — Write hashes to files and compare

```bash
sha256sum file1.txt >> file1hash
sha256sum file2.txt >> file2hash
cat file1hash
cat file2hash
```

Redirecting hash output to files with `>>` creates a persistent record that can be shared, logged, or compared later. This is standard practice in digital forensics — hash values are recorded at the point of evidence collection and used to prove chain of custody and detect any subsequent tampering.

### Task 2 (continued) — Byte-level comparison with cmp

```bash
cmp file1hash file2hash
```

`cmp` compares two files byte by byte and reports the exact position of the first difference — in this case, the very first character of the first line. This confirms the hashes differ and pinpoints where, which is useful when troubleshooting or documenting the discrepancy.

---

## Key Concepts

**Why SHA-256?**  
SHA-256 (Secure Hash Algorithm 256-bit) is a one-way cryptographic function — it is computationally infeasible to reverse a hash back to the original input or to engineer a different file that produces the same hash (collision resistance). These properties make it trustworthy for integrity verification.

**The avalanche effect:**  
A core property of cryptographic hash functions — any small change to the input (even one bit) produces a completely different output hash. This makes tampering immediately detectable.

---

## Key Takeaways

| Command | Purpose in security work |
|---|---|
| `sha256sum <file>` | Generate a SHA-256 integrity fingerprint |
| `sha256sum <file> >> hashfile` | Record hash to a file for logging or comparison |
| `cmp file1 file2` | Byte-level comparison — confirms and locates differences |
| `cat` + visual comparison | Human-readable check (insufficient alone for integrity verification) |

**Why this matters:** Hash verification is used across almost every area of security:
- **Malware analysis** — comparing file hashes against threat intelligence databases (VirusTotal, MISP) to identify known-malicious files
- **Digital forensics** — verifying that evidence collected from a device has not been altered since acquisition
- **Software integrity** — confirming that a downloaded binary matches the vendor-published hash before deployment
- **Incident response** — detecting file tampering on a compromised system by comparing live hashes against a known-good baseline

The ability to generate and compare hashes from the command line is a foundational skill for any analyst working with files, artifacts, or evidence.
