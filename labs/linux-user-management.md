# Lab: Adding and Managing Users with Linux Commands

**Course:** Google Cybersecurity Professional Certificate — Course 4, Module 3  
**Skills demonstrated:** Linux user lifecycle management, group assignment, file ownership, access deprovisioning  
**Tools used:** `useradd`, `usermod`, `chown`, `userdel`, `groupdel`, `sudo`

---

## Scenario

A new employee, `researcher9`, joined the Research department. As the analyst responsible for system access, I managed their entire lifecycle on the Linux system — from onboarding to eventual offboarding. This included creating the account, assigning group membership, transferring file ownership, adding a secondary group when their role expanded, and fully removing the account when they left the organization.

This lifecycle mirrors the identity and access management (IAM) workflows used in real security operations: every stage of the joiner–mover–leaver process requires deliberate, auditable actions to maintain least-privilege access.

---

## What I Did

### Task 1 — Create the user account and assign a primary group

```bash
sudo useradd researcher9
sudo usermod -g research_team researcher9
```

Or equivalently in one command:

```bash
sudo useradd researcher9 -g research_team
```

`useradd` creates a new user account on the system. `usermod -g` sets the user's **primary group** — the group that will own files created by this user by default. Both commands require `sudo` because modifying user accounts is a privileged operation.

Assigning new users to the correct group immediately on creation ensures they inherit the right access from day one and don't accumulate permissions through the default personal group.

### Task 2 — Transfer file ownership to the new employee

```bash
sudo chown researcher9 /home/researcher2/projects/project_r.txt
```

`chown` changes the owning user of a file. `project_r.txt` was previously owned by `researcher2`, but `researcher9` was now responsible for it. Transferring ownership means `researcher9` has full user-level permissions on the file and is the accountable owner — important for audit trails and access reviews.

### Task 3 — Add a secondary group when the role expanded

```bash
sudo usermod -aG sales_team researcher9
```

Several months in, `researcher9` began working across both Research and Sales. The `-aG` flags are critical here:

| Flag | Meaning |
|---|---|
| `-a` | **Append** — add to the group without removing existing group memberships |
| `-G` | Specify a **supplementary** (secondary) group |

Omitting `-a` would **replace** all existing supplementary groups with only `sales_team`, which would silently remove access. This is a common misconfiguration that can break a user's access in production — always use `-aG` together when adding to a secondary group.

### Task 4 — Deprovision the account on departure

```bash
sudo userdel researcher9
sudo groupdel researcher9
```

`userdel` removes the user account and their home directory entries. When `researcher9` was created, Linux automatically created a personal group also named `researcher9`. Since this was not their primary group, `userdel` left it behind — the expected behaviour. `groupdel` cleans up that orphaned group.

Deprovisioning completely — both the user account and any residual groups — is essential. Orphaned groups can be assigned to new files or users, potentially granting unintended access to resources that were associated with the old account.

---

## Key Takeaways

| Command | Purpose in security work |
|---|---|
| `sudo useradd <user> -g <group>` | Create account and assign primary group at once |
| `sudo usermod -g <group> <user>` | Change a user's primary group |
| `sudo usermod -aG <group> <user>` | Add to a secondary group without overwriting existing ones |
| `sudo chown <user> <file>` | Transfer file ownership when responsibility changes |
| `sudo userdel <user>` | Remove account during offboarding |
| `sudo groupdel <group>` | Clean up orphaned personal groups after deletion |

**Why this matters:** Improper user lifecycle management is a leading cause of privilege creep and unauthorized access. Accounts that are not removed when employees leave become vectors for re-entry — either by the former employee or by attackers who discover the dormant credentials. Keeping group memberships accurate and cleaning up after account deletions are direct controls against these risks, and are audited in compliance frameworks such as SOC 2 and ISO 27001.
