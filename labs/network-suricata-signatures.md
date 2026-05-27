# Lab: Exploring Signatures and Logs with Suricata

**Course:** Google Cybersecurity Professional Certificate — Course 6  
**Skills demonstrated:** IDS rule analysis, custom signature creation, alert log analysis, JSON log parsing  
**Tools used:** Suricata, `jq`, `cat`, `ls`, fast.log, eve.json

---

## Scenario

As a security analyst responsible for monitoring network traffic, I configured Suricata — an open-source intrusion detection and prevention system (IDS/IPS) — to inspect traffic against a custom ruleset. I examined the anatomy of a Suricata rule, triggered it against a packet capture file, and analysed the resulting alert logs in both the legacy `fast.log` format and the standard `eve.json` format.

---

## Understanding a Suricata Rule

Every Suricata signature has three components: **action**, **header**, and **rule options**.

```
alert http $HOME_NET any -> $EXTERNAL_NET any (msg:"GET on wire"; flow:established,to_server; content:"GET"; http_method; sid:12345; rev:3;)
```

### Action

```
alert
```

The action determines what Suricata does when a packet matches the rule:

| Action | Behaviour |
|---|---|
| `alert` | Generate an alert and log the packet (IDS mode) |
| `drop` | Generate an alert AND drop the packet (IPS mode only) |
| `pass` | Allow the packet through — used to create exceptions to drop rules |
| `reject` | Drop the packet and send a TCP reset to both endpoints |

In this lab, `alert` was used — Suricata detects and logs but does not block traffic.

### Header

```
http $HOME_NET any -> $EXTERNAL_NET any
```

| Field | Value | Meaning |
|---|---|---|
| Protocol | `http` | Rule applies to HTTP traffic only |
| Source | `$HOME_NET any` | Any port on the internal network (172.21.224.0/20) |
| Direction | `->` | Outbound traffic only |
| Destination | `$EXTERNAL_NET any` | Any port on any external network |

`$HOME_NET` and `$EXTERNAL_NET` are variables defined in `/etc/suricata/suricata.yaml`, allowing rules to reference the organisation's network without hardcoding IP ranges.

### Rule Options

```
msg:"GET on wire"; flow:established,to_server; content:"GET"; http_method; sid:12345; rev:3;
```

| Option | Purpose |
|---|---|
| `msg:"GET on wire"` | The alert text printed in the log when this rule fires |
| `flow:established,to_server` | Match only packets in an established TCP connection flowing from client to server |
| `content:"GET"` | Match the literal string `GET` in the HTTP method field |
| `sid:12345` | Unique signature ID — used to reference and tune specific rules |
| `rev:3` | Revision number — tracks changes to the rule over time |

**In plain language:** This rule alerts whenever a client on the internal network sends an HTTP GET request to an external server.

---

## Task 2 — Run Suricata and Inspect fast.log

```bash
ls -l /var/log/suricata          # empty before running
sudo suricata -r sample.pcap -S custom.rules -k none
ls -l /var/log/suricata          # fast.log and eve.json now present
cat /var/log/suricata/fast.log
```

| Flag | Meaning |
|---|---|
| `-r sample.pcap` | Read from a pcap file instead of a live interface |
| `-S custom.rules` | Use only the specified rules file |
| `-k none` | Disable checksum validation (not needed for pcap replay) |

The `fast.log` output produced one line per alert, each containing:
- Timestamp
- Alert message (`GET on wire`)
- Source IP and port → Destination IP and port
- Traffic direction

`fast.log` is a deprecated, human-readable format suited for quick spot-checks and rule testing. It is **not** recommended for production incident response due to its limited data.

---

## Task 3 — Parse eve.json with jq

`eve.json` is Suricata's primary log format — it records every alert and network telemetry event in structured JSON, making it suitable for ingestion by SIEMs, log aggregators, and analysis scripts.

### Raw output (difficult to read)

```bash
cat /var/log/suricata/eve.json
```

Raw JSON is dense and unparseable at a glance.

### Pretty-printed with jq

```bash
jq . /var/log/suricata/eve.json | less
```

`jq .` reformats the JSON with indentation and colour, making the structure immediately readable. `less` enables scrolling through large output.

### Extract specific fields

```bash
jq -c "[.timestamp, .flow_id, .alert.signature, .proto, .dest_ip]" /var/log/suricata/eve.json
```

This extracts a compact summary of each event — timestamp, flow ID, alert name, protocol, and destination IP — producing one line per event. This is the kind of query used to rapidly triage a large log file and identify which events warrant deeper investigation.

| Field | Security relevance |
|---|---|
| `.timestamp` | When the event occurred |
| `.flow_id` | Unique ID linking all packets in the same network flow |
| `.alert.signature` | The rule `msg` that triggered — identifies what was detected |
| `.proto` | Protocol (TCP, UDP, HTTP...) |
| `.dest_ip` | Destination — key for identifying C2 connections |

### Correlate a full network flow

```bash
jq "select(.flow_id==<id>)" /varricata/eve.json
```

All log entries sharing a `flow_id` belong to the same network conversation. Filtering by `flow_id` reconstructs the complete sequence of events between two endpoints — request, response, follow-on activity — within a single investigation pivot.

---

## fast.log vs eve.json

| | fast.log | eve.json |
|---|---|---|
| Format | Plain text, one line per alert | Structured JSON, one object per event |
| Data richness | Minimal — message, IPs, timestamp | Full — all packet metadata, flow data, payload details |
| Use case | Quick rule testing and QA | Production logging, SIEM ingestion, scripted analysis |
| Status | Deprecated | Current standard |

---

## Key Takeaways

| Command / Concept | Purpose |
|---|---|
| Suricata rule: action | Defines response — alert, drop, pass, or reject |
| Suricata rule: header | Scopes traffic — protocol, source, direction, destination |
| Suricata rule: options | Refines match — content, flow state, SID, message |
| `suricata -r pcap -S rules` | Replay captured traffic against a ruleset for testing |
| `jq . eve.json` | Pretty-print JSON logs for human readability |
| `jq -c "[fields]"` | Extract specific fields for rapid triage |
| `jq "select(.flow_id==x)"` | Reconstruct a complete network flow from logs |

**Why this matters:** Suricata is widely deployed as both an IDS (detection only) and IPS (detection + blocking) across enterprise networks, cloud environments, and security appliances. The ability to read and write Suricata rules, understand why an alert fired, and parse `eve.json` with `jq` are directly applicable skills in network security monitoring, SOC operations, and threat hunting roles.
