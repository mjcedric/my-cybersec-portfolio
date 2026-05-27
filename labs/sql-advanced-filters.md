# Lab: Applying Advanced Filters in SQL

**Course:** Google Cybersecurity Professional Certificate — Course 5  
**Skills demonstrated:** SQL filtering with comparison operators, date/time analysis, logical operators, incident scoping  
**Tools used:** SQL (`WHERE`, `BETWEEN`, `AND`, `OR`, `NOT`, `LIKE`), MariaDB

---

## Scenario

My team was investigating a recent security incident. I needed to interrogate the `log_in_attempts` table to identify suspicious login activity — attempts made after hours, outside a known country, on specific dates, and within certain event ID ranges. I also needed to filter employee records by department to scope update and notification tasks.

This lab builds directly on basic `SELECT`/`WHERE` queries, adding the precision filters that make SQL useful for real incident investigation.

---

## Part 1 — Filtering by Date, Time, and ID

### Retrieve login attempts after a specific date

```sql
SELECT * FROM log_in_attempts WHERE login_date > '2022-05-09';
SELECT * FROM log_in_attempts WHERE login_date >= '2022-05-09';
```

`>` excludes the boundary date; `>=` includes it. When scoping an investigation to events after a known incident date, getting the boundary condition right matters — missing the first day of activity can leave key events out of the review.

### Narrow to a date range with BETWEEN

```sql
SELECT * FROM log_in_attempts WHERE login_date BETWEEN '2022-05-09' AND '2022-05-11';
```

`BETWEEN` is inclusive on both ends and more readable than chaining `>=` and `<=`. For incident timelines, defining a precise window prevents pulling in unrelated data that slows analysis.

### Flag logins outside business hours

```sql
SELECT * FROM log_in_attempts WHERE login_time < '07:00:00';
SELECT * FROM log_in_attempts WHERE login_time BETWEEN '06:00:00' AND '07:00:00';
```

Logins before business hours begin (07:00) are worth reviewing — they may indicate automated credential stuffing, a user in an unexpected time zone, or an attacker working around monitoring schedules. Narrowing to the 06:00–07:00 window reduced noise by excluding overnight attempts unrelated to the shift-start period.

### Filter by event ID range

```sql
SELECT event_id, username, login_date FROM log_in_attempts WHERE event_id >= 100;
SELECT event_id, username, login_date FROM log_in_attempts WHERE event_id BETWEEN 100 AND 150;
```

Event IDs provide a sequential handle for narrowing to a specific batch of log entries — useful when a previous step identified the approximate range of events associated with an incident.

---

## Part 2 — Filtering with AND, OR, and NOT

### Retrieve failed logins after business hours

```sql
SELECT * FROM log_in_attempts
WHERE login_time > '18:00:00' AND success = 0;
```

`AND` requires both conditions to be true. Failed logins (`success = 0`) after 18:00 combine two risk signals: out-of-hours timing and authentication failure. Together they narrow the result set to the highest-priority events for further investigation. Note: MySQL/MariaDB stores Boolean values as integers — `0` for FALSE (failed), `1` for TRUE (successful).

### Retrieve logins on specific dates of interest

```sql
SELECT * FROM log_in_attempts
WHERE login_date = '2022-05-09' OR login_date = '2022-05-08';
```

`OR` returns rows matching either condition. When a suspicious event is identified on a specific date, pulling the day before as well captures any precursor activity — reconnaissance, failed attempts, or earlier successful logins by the same actor.

### Exclude logins from a specific country

```sql
SELECT * FROM log_in_attempts
WHERE NOT country LIKE 'MEX%';
```

`NOT` negates the following condition. The country field stored values as both `'MEX'` and `'MEXICO'`, so combining `NOT` with `LIKE 'MEX%'` excluded both variants cleanly in a single expression. This pattern is useful whenever a field has inconsistent formatting — wildcards handle the variation while `NOT` inverts the match.

### Filter employees by department

```sql
SELECT * FROM employees WHERE department = 'Finance';
SELECT * FROM employees WHERE department = 'Sales';
SELECT * FROM employees WHERE department = 'Finance' OR department = 'Sales';
SELECT * FROM employees WHERE NOT department = 'Information Technology';
```

Scoping queries to specific departments supports targeted security operations: posting compliance notices to Finance and Sales, distributing update instructions to non-IT staff, or excluding the IT team (who manage their own systems) from a mass patch notification. `NOT` on a department filter is often more efficient than listing every included department when the exclusion list is shorter.

---

## Key Takeaways

| Operator | Purpose in security work |
|---|---|
| `>`, `>=`, `<`, `<=` | Precise date/time boundary filtering |
| `BETWEEN x AND y` | Inclusive range — cleaner than chaining two comparisons |
| `AND` | Combine multiple risk signals (time + failure, date + country) |
| `OR` | Expand scope to cover multiple values or dates |
| `NOT` | Exclude known-good values to surface anomalies |
| `NOT LIKE 'val%'` | Exclude pattern matches — handles inconsistent field formatting |

**Why this matters:** The operators in this lab are what turn a basic SQL lookup into a genuine investigative tool. Real security incidents don't happen at a single moment — they span time ranges, involve multiple countries, and mix successful and failed events. Being able to construct precise, multi-condition queries means an analyst can go from "something suspicious happened" to "here are the 14 specific events worth examining" in minutes rather than hours.
