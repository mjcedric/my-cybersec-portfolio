# Lab: Decrypting an Encrypted Message

**Course:** Google Cybersecurity Professional Certificate — Course 5  
**Skills demonstrated:** Classical cipher analysis, symmetric decryption, Linux CLI, OpenSSL  
**Tools used:** `cat`, `ls -a`, `tr`, `openssl`, `cd`

---

## Scenario

All files in the analyst home directory had been encrypted. My task was to recover the data by working through two layers of encryption: first a Caesar cipher protecting a hidden file, then AES-256-CBC encryption on the main data file. The decryption key was concealed inside the Caesar-encrypted file — meaning I had to solve the first layer to unlock the second.

This lab simulates a real-world scenario where an attacker (or a security exercise) has obfuscated credentials or instructions using classical and modern cryptographic techniques.

---

## What I Did

### Task 1 — Explore the home directory and read instructions

```bash
ls
cat README.txt
```

The home directory contained two files (`Q1.encrypted`, `README.txt`) and a subdirectory (`caesar`). The README instructed me to look inside `caesar` for a hidden file — establishing that the path to decryption required reading instructions carefully before acting.

### Task 2 — Find and decode the hidden Caesar cipher file

```bash
cd caesar
ls -a
cat .leftShift3
```

Hidden files in Linux are prefixed with a `.` and are invisible to a plain `ls`. Using `ls -a` revealed `.leftShift3` — a file containing scrambled text encrypted with a Caesar cipher shifted 3 positions to the left.

**How a Caesar cipher works:**  
Each letter is shifted a fixed number of positions along the alphabet. A left-shift of 3 means `d` → `a`, `e` → `b`, `f` → `c`, and so on. To decrypt, each character is shifted back by the same amount.

**Decoding with the `tr` command:**

```bash
cat .leftShift3 | tr "d-za-cD-ZA-C" "a-zA-Z"
```

`tr` translates characters from one set to another using a direct mapping. Here:

| Parameter | Meaning |
|---|---|
| `"d-za-cD-ZA-C"` | Input: the encrypted alphabet (shifted left 3) |
| `"a-zA-Z"` | Output: the original unshifted alphabet |

The decoded output revealed the exact `openssl` command needed for the next task — including the decryption password.

### Task 3 — Decrypt the AES-256-CBC encrypted file

```bash
cd ~
openssl aes-256-cbc -pbkdf2 -a -d -in Q1.encrypted -out Q1.recovered -k ettubrute
cat Q1.recovered
```

**Breaking down the OpenSSL command:**

| Flag | Meaning |
|---|---|
| `aes-256-cbc` | Symmetric cipher: AES with a 256-bit key in CBC mode |
| `-pbkdf2` | Applies PBKDF2 key derivation — strengthens the password against brute force |
| `-a` | Input/output is Base64-encoded |
| `-d` | Decrypt mode |
| `-in Q1.encrypted` | Encrypted input file |
| `-out Q1.recovered` | Output file for the recovered plaintext |
| `-k ettubrute` | The decryption password (recovered from the Caesar cipher) |

Running `cat Q1.recovered` revealed the hidden message, confirming successful decryption.

---

## Key Takeaways

| Concept / Tool | Security relevance |
|---|---|
| Caesar cipher | Classical substitution cipher — foundational cryptography concept; still appears in CTFs and encoding challenges |
| `tr` command | Efficient character-level text transformation in the CLI |
| AES-256-CBC | Industry-standard symmetric encryption — used to protect files, disk volumes, and communications |
| PBKDF2 | Key stretching — makes password-based encryption more resistant to brute-force attacks |
| `openssl` CLI | Swiss-army tool for encryption, decryption, certificate management, and key generation |

**Why this matters:** Understanding both classical and modern cryptography is essential for security analysts. Classical ciphers like Caesar appear in CTF competitions and security awareness training. AES-256 is the real-world standard — analysts encounter it when examining encrypted malware payloads, investigating full-disk encryption on seized devices, or reviewing how an application protects data at rest. Being able to work with `openssl` from the command line, and understanding what each flag does, gives analysts direct control over cryptographic operations without relying on a GUI.
