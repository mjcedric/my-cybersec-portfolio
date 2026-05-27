# Lab: Performing SQL Queries for Security Analysis

**Course:** Google Cybersecurity Professional Certificate — Course 5  
**Skills demonstrated:** SQL querying, data filtering, log investigation, pattern matching  
**Tools used:** SQL (`SELECT`, `WHERE`, `ORDER BY`, `LIKE`), MariaDB

---

## Scenario

As a security analyst, I was given access to an organization's database containing employee records, device information, and login attempt logs. My tasks were to identify devices needing updates, investigate login activity for anomalies, and locate specific employees and machines based on department and building — all through SQL queries.

SQL is one of the primary tools analysts use to interrogate security data at scale. Rather than manually scrolling through thousands of log entries, a well-written query returns exactly the records that matter.

---

## Database Structure

| Table | Contents |
|---|---|
| `machines` | Device ID, operating system, OS patch date, email client |
| `log_in_attempts` | Event ID, username, login date, time, country |
| `employees` | Username, department, office location |

---

## What I Did

### Task 1 — Retrieve all device information

```sql
SELECT * FROM machines;
```

`SELECT *` returns every column from a table — useful for an initial survey of what data is available. In a patch management workflow, this gives a full inventory of all devices before narrowing down which ones need attention.

### Task 1 (continued) — Select specific columns

```sql
SELECT device_id, email_client FROM machines;
SELECT device_id, operating_system, OS_patch_date FROM machines;
```

Selecting only the needed columns keeps output focused and reduces noise. When investigating a specific question — such as which OS patch dates are overdue — returning only the relevant columns makes the results immediately actionable.

### Task 2 — Investigate login activity

```sql
SELECT event_id, country FROM log_in_attempts;
SELECT username, login_date, login_time FROM log_in_attempts;
SELECT * FROM log_in_attempts;
```

Reviewing login attempts by country identifies logins from unexpected geographic locations — a common indicator of compromised credentials or unauthorized access. Filtering by date and time allows analysts to flag logins that occurred outside of business hours, which can indicate automated attacks or insider threats.

### Task 2 (continued) — Sort login data chronologically

```sql
SELECT * FROM log_in_attempts ORDER BY login_date;
```

`ORDER BY` sequences results by a specified column. Sorting login attempts by date surfaces the timeline of activity, which is essential when reconstructing the sequence of events during an incident investigation.

### Task 3 — Filter devices by operating system

```sql
SELECT device_id, operating_system FROM machines;
SELECT * FROM machines WHERE operating_system = 'OS 2';
```

`WHERE` filters rows to only those matching a condition. Querying for all machines running `OS 2` immediately identifies every device that requires a specific update — far faster and more reliable than checking each machine individually.

### Task 4 — Filter employees by department

```sql
SELECT * FROM employees WHERE department = 'Finance';
SELECT * FROM employees WHERE department = 'Sales';
```

Filtering by department retrieves the office locations for targeted groups of employees — in this case, to post a notice about handling confidential financial information. This is a routine use case in security operations: quickly identifying all personnel in a specific scope.

### Task 5 — Locate an employee by office, then expand to a building

```sql
SELECT * FROM employees WHERE office = 'South-109';
SELECT * FROM employees WHERE office LIKE 'South%';
```

The first query pinpoints the employee assigned to a specific machine with a known issue. The second uses the `LIKE` operator with the `%` wildcard to match any office starting with `'South'` — returning all employees in the South building when the entire building was found to have affected machines.

`LIKE` with `%` is particularly useful in security contexts for partial-match searches: finding all log entries from a subnet range, all usernames following a naming pattern, or all files with a specific extension.

---

## Key Takeaways

| SQL Element | Purpose in security work |
|---|---|
| `SELECT *` | Full table survey — initial triage |
| `SELECT col1, col2` | Focused output — only relevant fields |
| `WHERE col = 'value'` | Exact match filtering |
| `WHERE col LIKE 'val%'` | Pattern matching with wildcard |
| `ORDER BY col` | Chronological or logical sequencing of results |

**Why this matters:** Security analysts work with large datasets — thousands of login events, hundreds of devices, entire user directories. SQL is the fastest way to answer precise questions against that data: "Which devices haven't been patched?", "Were there any logins from outside the country last night?", "Who works in this building?" Being able to query a database directly, rather than waiting for a report to be generated, dramatically reduces response time during an investigation.
