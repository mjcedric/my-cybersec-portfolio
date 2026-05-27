# Lab: Analyzing Network Packets with Wireshark

**Course:** Google Cybersecurity Professional Certificate — Course 6, Module 2  
**Skills demonstrated:** Packet capture analysis, network protocol inspection, traffic filtering, DNS and TCP analysis  
**Tools used:** Wireshark, display filters (`ip.addr`, `ip.src`, `ip.dst`, `eth.addr`, `udp.port`, `tcp.port`, `tcp contains`)

---

## Scenario

As a security analyst, I was given a `.pcap` (packet capture) file containing network traffic from a system browsing to a website. My task was to inspect the capture in Wireshark, apply filters to isolate relevant traffic, and drill into individual packets to extract protocol-level details — IP addresses, MAC addresses, ports, DNS queries, and payload content.

Packet analysis is a core skill during network incident investigations: it reveals exactly what traffic crossed the wire, what was asked for, and what was returned.

---

## Wireshark Interface Overview

Each row in the packet list represents one captured packet, with key columns:

| Column | Contents |
|---|---|
| No. | Index number in the capture file |
| Time | Timestamp of the packet |
| Source | Source IP address |
| Destination | Destination IP address |
| Protocol | Top-level protocol (TCP, UDP, DNS, ICMP, HTTP...) |
| Length | Total packet size in bytes |
| Info | Human-readable summary of the payload |

**Color coding** provides instant visual triage: light blue packets indicated DNS traffic; green indicated TCP/HTTP traffic — allowing high-volume captures to be scanned at a glance before applying any filters.

---

## What I Did

### Task 1 — Explore the capture and identify ICMP traffic

Scrolling through the unfiltered capture, I located the first packet whose Info column began with `Echo (ping) request` — identifying it as **ICMP** traffic. ICMP Echo requests are the basis of the `ping` command and are commonly used in network reconnaissance to map live hosts.

### Task 2 — Filter by IP address and inspect packet layers

```
ip.addr == 142.250.1.139
```

This filter reduced the view to only packets where `142.250.1.139` appeared as either source or destination — immediately narrowing thousands of packets to the relevant session.

Opening the first TCP packet revealed the **layered structure** of a network packet, each layer visible as an expandable subtree:

| Layer | Subtree | Key data found |
|---|---|---|
| Layer 1–2 | **Frame** | Total frame length, arrival timestamp |
| Layer 2 | **Ethernet II** | Source and destination MAC addresses |
| Layer 3 | **Internet Protocol Version 4** | Source/destination IP, internal protocol |
| Layer 4 | **Transmission Control Protocol** | Source/destination ports, sequence numbers, TCP flags |

The TCP destination port was **80** — the standard port for unencrypted HTTP web traffic. Expanding the **Flags** subtree showed the TCP control bits set for this packet (SYN, ACK, etc.), which indicate the phase of the TCP handshake.

### Task 3 — Filter by source IP, destination IP, and MAC address

```
ip.src == 142.250.1.139      # traffic originating from this IP only
ip.dst == 142.250.1.139      # traffic sent to this IP only
eth.addr == 42:01:ac:15:e0:02  # all traffic involving this MAC address
```

`ip.src` and `ip.dst` allow one-directional filtering — useful when you need to separate inbound from outbound traffic during an investigation. `eth.addr` filters at Layer 2 (Ethernet), catching packets regardless of which IP protocol they carry — helpful when tracking a physical device across protocol changes.

Inspecting the first packet matching the MAC address filter and expanding the **Internet Protocol Version 4** subtree revealed the internal protocol was **TCP**.

### Task 4 — Inspect DNS traffic

```
udp.port == 53
```

DNS uses UDP port 53. This filter isolated all DNS queries and responses in the capture. Opening the **Domain Name System (query)** subtree and expanding **Queries** showed the website being looked up: `opensource.google.com`.

Expanding **Answers** in the fourth DNS packet showed the IP addresses the DNS server returned for that hostname — the resolved addresses the client would then connect to. This is critical in investigations involving suspicious domains: DNS answers show exactly where a client was directed, even if the connection itself was encrypted.

### Task 5 — Inspect TCP web traffic and search payload content

```
tcp.port == 80
```

TCP port 80 filters all standard HTTP web traffic. Inspecting the first matching packet in the **Internet Protocol Version 4** subtree revealed:

| Field | Value |
|---|---|
| Time to Live (TTL) | 64 |
| Destination Address | 169.254.169.254 |
| Header Length | 20 bytes |

TTL values help identify the operating system of the sender (Linux systems typically start at 64; Windows at 128) and can detect TTL-based evasion techniques.

**Searching packet payloads by content:**

```
tcp contains "curl"
```

This filter searches the raw payload data of TCP packets for the string `"curl"` — identifying web requests made with the `curl` command-line tool. Payload content searching is powerful for finding specific usernames, commands, file names, or indicators of compromise within unencrypted traffic.

---

## Key Takeaways

| Filter | Purpose in security work |
|---|---|
| `ip.addr == x.x.x.x` | Isolate all traffic to/from an IP |
| `ip.src` / `ip.dst` | Separate inbound and outbound sessions |
| `eth.addr == xx:xx:xx:xx:xx:xx` | Track a device at the MAC layer |
| `udp.port == 53` | Isolate DNS — reveals what domains a host looked up |
| `tcp.port == 80` | Isolate HTTP web traffic |
| `tcp contains "string"` | Search unencrypted payloads for keywords or IOCs |

**Why this matters:** Wireshark is one of the most widely used tools in network security. During an incident, a packet capture tells the story of what actually happened on the wire — regardless of what logs say or don't say. Being able to filter a large capture down to the relevant session, read the protocol stack layer by layer, and search payload content for indicators of compromise is a practical skill used daily in SOC, IR, and network forensics roles.
