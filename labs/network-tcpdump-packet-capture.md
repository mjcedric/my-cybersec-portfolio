# Lab: Capturing Network Traffic with tcpdump

**Course:** Google Cybersecurity Professional Certificate — Course 6, Module 2  
**Skills demonstrated:** Live packet capture, network interface identification, traffic filtering, pcap file analysis  
**Tools used:** `tcpdump`, `ifconfig`, command flags (`-i`, `-v`, `-c`, `-n`, `-nn`, `-w`, `-r`, `-X`)

---

## Scenario

As a network analyst on a Linux system, I needed to capture live network traffic, save it to a packet capture file, and then filter and inspect the saved data. Unlike Wireshark which works on pre-captured files via a GUI, `tcpdump` is a command-line tool that can both capture live traffic and read `.pcap` files — making it indispensable on servers and headless systems where no GUI is available.

---

## What I Did

### Task 1 — Identify available network interfaces

```bash
sudo ifconfig
sudo tcpdump -D
```

`ifconfig` lists all active network interfaces and their configuration — IP address, MAC address, and status. The interface used for capture was **eth0**, identified by the `eth` prefix (Ethernet).

`tcpdump -D` provides an alternative method to list capturable interfaces — useful on minimal systems where `ifconfig` may not be installed. Both commands confirm which interface to specify in subsequent capture commands.

### Task 2 — Capture and inspect live traffic

```bash
sudo tcpdump -i eth0 -v -c5
```

| Flag | Meaning |
|---|---|
| `-i eth0` | Capture from the eth0 interface specifically |
| `-v` | Verbose — show detailed IP packet fields |
| `-c5` | Stop after capturing exactly 5 packets |

**Reading tcpdump output:**

Each captured packet is printed with:

```
timestamp  protocol  src.host.port > dst.host.port: flags, seq, ack, win, length
```

The verbose output exposed the full IP header for each packet:

| Field | Security relevance |
|---|---|
| **TOS** (Type of Service) | Traffic priority — unusual values can indicate manipulation |
| **TTL** (Time to Live) | Hop count remaining — helps identify OS fingerprinting and spoofing |
| **Flags** | TCP control bits (SYN, ACK, FIN, RST) — reveal connection state |
| **Protocol** | Internal protocol number (TCP = 6, UDP = 17, ICMP = 1) |
| **Length** | Outer IP packet size in bytes |

By default, tcpdump resolves IP addresses to hostnames. The `>` arrow between source and destination shows the direction of traffic flow, with port numbers appended after a `.` (e.g., `192.168.1.5.443`).

### Task 3 — Save live capture to a pcap file

```bash
sudo tcpdump -i eth0 -nn -c9 port 80 -w capture.pcap &
```

| Flag | Meaning |
|---|---|
| `-nn` | Do not resolve IPs to hostnames or ports to service names — shows raw numbers |
| `-c9` | Capture 9 packets then stop |
| `port 80` | Filter: capture only HTTP traffic on port 80 |
| `-w capture.pcap` | Write captured packets to a file instead of printing to screen |
| `&` | Run in the background, freeing the terminal |

`-nn` is important in investigative contexts: name resolution adds latency and can alert a DNS server that you are inspecting its traffic. Raw IP and port numbers are also unambiguous — no risk of a hostname resolving differently later.

### Task 4 — Read and filter the saved pcap file

```bash
sudo tcpdump -nn -r capture.pcap -v
sudo tcpdump -nn -r capture.pcap -v -X
```

| Flag | Meaning |
|---|---|
| `-r capture.pcap` | Read from a saved file rather than a live interface |
| `-v` | Verbose packet details |
| `-X` | Display packet payload in both **hexadecimal** and **ASCII** side by side |

The `-X` flag is particularly powerful for examining unencrypted traffic — it exposes the raw payload content, allowing an analyst to read HTTP request/response bodies, search for plaintext credentials, or identify encoded malicious content embedded in packets.

---

## tcpdump vs Wireshark

| Feature | tcpdump | Wireshark |
|---|---|---|
| Interface | Command line | GUI |
| Live capture | Yes | Yes |
| Read pcap files | Yes | Yes |
| Usable on headless servers | Yes | No |
| Payload hex/ASCII view | `-X` flag | Built-in |
| Filter syntax | BPF (Berkeley Packet Filter) | Wireshark display filters |

In practice, analysts often capture with `tcpdump` on a remote server (where no GUI is available) and then open the resulting `.pcap` file in Wireshark locally for deeper analysis.

---

## Key Takeaways

| Command | Purpose |
|---|---|
| `sudo ifconfig` / `tcpdump -D` | Identify available capture interfaces |
| `tcpdump -i eth0 -v -c5` | Quick live traffic sample with full IP detail |
| `tcpdump -nn` | Suppress name resolution — faster and unambiguous |
| `tcpdump -w capture.pcap` | Save raw packets for later analysis |
| `tcpdump -r capture.pcap -X` | Replay and inspect saved captures including raw payload |

**Why this matters:** `tcpdump` is present on virtually every Unix-based system and requires no installation. It is the go-to tool for capturing traffic on production servers, cloud instances, network appliances, and forensic environments. Knowing how to capture targeted traffic (`port 80`), suppress noise (`-nn`), save evidence (`-w`), and inspect payloads (`-X`) are practical skills used directly in network incident response.
