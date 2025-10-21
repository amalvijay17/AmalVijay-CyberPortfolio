# Tshark — Terminal Wireshark

---

## Overview
**Tshark** is the terminal/console version of Wireshark. It captures and analyzes packets using the same powerful display and capture filters as Wireshark, but runs in the terminal — making it ideal for scripting, automation, and server-side packet capture.

---

## Why use Tshark
- Runs in terminal (no GUI) — suitable for headless servers and scripts.  
- Low resource usage compared to Wireshark.  
- Fully scriptable with bash, Python, cron, systemd timers, etc.  
- Supports capture scheduling and automatic stop (by count, duration, filesize).  
- Can read/write pcap/pcapng files and apply the same filters as Wireshark.

---

## Install & setup
### Linux (Debian/Ubuntu)
```bash
sudo apt-get update
sudo apt-get install tshark
```
During install you may be asked to allow non-superusers to capture packets — choose as appropriate.

### Windows
- Install Wireshark (Tshark usually included) and set environment variables if needed.
- Use `tshark -D` to list available interfaces.

---

## Basic commands & usage
### List interfaces
```bash
tshark -D
```

### Capture on an interface (write to file)
```bash
tshark -i eth0 -w packet.pcapng
```

### Read/Analyze a capture file
```bash
tshark -r packet.pcapng
```

### Capture only a fixed number of packets
```bash
tshark -i eth0 -c 50
# -c = stop after 50 packets
```

### Capture for a fixed duration
```bash
tshark -i eth0 -a duration:50
# -a duration:SECONDS
```

### Capture until file reaches a certain size
```bash
tshark -i eth0 -a filesize:50 -w packet.pcapng
# filesize: in KB (stops when file >= 50 KB)
```

---

## Columns explained (default output)
- Column 1: Packet number (serial)  
- Column 2: Timestamp (exact capture time)  
- Column 3: Source (IP/host)  
- Column 4: Destination (IP/host)  
- Column 5: Protocol (e.g., TCP, DNS)  
- Column 6: Length (bytes)  
- Column 7: Info (brief summary)

---

## Display filters (analyze packets after capture)
Use `-r` to read a file and `-Y` to apply display filters (Wireshark display filter syntax):
```bash
tshark -r packet.pcapng -Y "dns"
tshark -r packet.pcapng -Y "dns or arp"
tshark -r packet.pcapng -Y "tcp"
tshark -r packet.pcapng -Y "not tcp"
tshark -r packet.pcapng -Y "ip.addr==192.168.0.105"
tshark -r packet.pcapng -Y "ip.src==192.168.0.105"
tshark -r packet.pcapng -Y "ip.dst==192.168.0.105"
# Save filtered packets to a new file:
tshark -r packets.pcapng -Y "dns" -w dns.pcapng
```

---

## Capture filters (apply during capture)
Capture filters use libpcap/BPF syntax and are specified with `-f`:
```bash
tshark -i Wi-Fi -f "port http"
tshark -i Wi-Fi -f "port http or port ssh"
tshark -i Wi-Fi -f "host 192.168.100.3"
tshark -i Wi-Fi -f "src host 192.168.100.3"
tshark -i Wi-Fi -f "dst host 192.168.100.3"
tshark -i Wi-Fi -f "portrange 1-443"
```

---

## Useful examples
- Capture HTTP traffic and save:
```bash
tshark -i eth0 -f "port 80" -w http_capture.pcapng
```
- Capture SSH traffic from a host:
```bash
tshark -i eth0 -f "src host 10.0.0.5 and port 22" -w ssh_from_10.0.0.5.pcapng
```
- Extract DNS packets from a capture:
```bash
tshark -r packets.pcapng -Y "dns" -w dns_only.pcapng
```
- Print only IP addresses and protocol summary (example)
```bash
tshark -r packet.pcapng -T fields -e ip.src -e ip.dst -e _ws.col.Protocol
```

---

## Tips & best practices
- Use **capture filters** (`-f`) to reduce volume and overhead while capturing.  
- Use **display filters** (`-Y`) to analyze reads or to extract subsets to new files.  
- Schedule captures with `cron` or systemd and use `-a duration:` for automatic stop.  
- Combine `tshark` with `awk`, `sed`, `jq`, or Python for automation and reporting.  
- Run as root or grant capture privileges to the user if you need live capture on interfaces.  
- For long captures, rotate files using `-b filesize:`/`-b duration:` (ring buffer options available in dumpcap; `tshark` supports `-b` too).

---

## Quick reference
- List interfaces: `tshark -D`  
- Capture & write: `tshark -i eth0 -w file.pcapng`  
- Read file: `tshark -r file.pcapng`  
- Display filter: `-Y "filter"`  
- Capture filter: `-f "bpf filter"`  
- Autostop by count/duration/filesize: `-c`, `-a duration:`, `-a filesize:`  

---
