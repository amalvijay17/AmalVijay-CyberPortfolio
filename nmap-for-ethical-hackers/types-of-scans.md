## TCP/UDP Ports, Port States, and Scan Types

### What is a TCP/UDP Port
- A **port** is a software socket — a "door" that lets applications on a device communicate.
- Ports identify specific applications on a single machine (e.g., HTTP typically uses TCP port 80).
- If a port is **open**, a service or application is listening and can accept connections or datagrams.

---

### The Six Port States
- **Open** — Probe (SYN) → target replies `SYN/ACK`. Service is listening.
- **Closed** — Probe (SYN) → target replies `RST/ACK`. No service is listening.
- **Filtered** — Probe (SYN) → no reply (firewall may drop packets); Nmap cannot determine open/closed.
- **Unfiltered** — Target responds (not blocked by firewall); may be open or closed (further checks needed).
- **Open|Filtered** — Nmap cannot determine if open or filtered (common with stealthy UDP/null/xmas/fin scans).
- **Closed|Filtered** — Nmap cannot determine if closed or filtered (rare).

Example ACK scan to check filtered vs unfiltered: `nmap -sA -p 25 scanme.nmap.org`  
- No response → **filtered**  
- `RST` → **unfiltered**

---

### The Stealth (SYN) Scan
- Command example: `nmap -sS scanme.nmap.org`
- Also called a half-open or SYN scan — Nmap sends `SYN`, waits for `SYN/ACK` (open) or `RST` (closed), then often sends `RST` to avoid completing the handshake.
- TCP window size and MSS can reveal fingerprinting or stealth behavior:
  - **MSS** = maximum segment size (commonly 1460 bytes excluding headers).
  - **Window size** = amount of unacknowledged data host can accept (max 65535).
  - Nmap may use unusual window sizes (e.g., 1024) as a probe — uncommon in normal traffic and can indicate scanning.

Wireshark clues: TCP window small/unusual (e.g., Window: 1024) and specific MSS values can suggest an Nmap SYN probe.

---

### TCP Connect Scan
- Command example: `nmap -sT 192.168.100.3`
- Uses the OS TCP stack to complete the full 3-way handshake (`SYN`, `SYN/ACK`, `ACK`).
- Results are reliable but noisy — the connection is logged on the target.
- Wireshark pattern: `SYN` → `SYN/ACK` → `ACK` → possibly `RST` to close if the scanner cleans up.
- Note: **You will be logged.**

---

### Which Ports Should We Scan
- Default Nmap scan covers top 1000 ports: `nmap -sT scanme.nmap.org`
- Fast scan of common ports (top 100): `nmap -sT scanme.nmap.org -F`
- Choose scope based on goals: focused service enumeration vs broad discovery.

---

### TCP Null, Xmas, FIN, and ACK Scans

**Null Scan**  
- Command example: `nmap -sN -p 21 scanme.nmap.org`  
- Sends a TCP packet with **no flags**.  
- Responses:
  - No response → **open|filtered**
  - `RST` → **closed**
- Use: stealthy evasion; results can be ambiguous.

**Xmas Scan**  
- Command example: `nmap -sX -p 21 scanme.nmap.org`  
- Sends TCP packet with **FIN, PSH, URG** set.  
- Responses:
  - No response → **open|filtered**
  - `RST` → **closed**
- Use: stealthy on systems that follow RFC 793 (older Unix-like hosts).

**FIN Scan**  
- Command example: `nmap -sF -p 21 scanme.nmap.org`  
- Sends only the **FIN** flag.  
- Responses:
  - No response → **open|filtered**
  - `RST` → **closed**
- Use: quiet recon against non-Windows systems.

**ACK Scan**  
- Command example: `nmap -sA -p 21 scanme.nmap.org`  
- Sends packets with **ACK** flag (as if part of an existing session).  
- Responses:
  - No response / ICMP unreachable → **filtered**
  - `RST` → **unfiltered**
- Use: determine firewall rules (filtered vs unfiltered), not to detect open ports.

General note: Null/Xmas/FIN scans help bypass basic IDS/firewalls but often return ambiguous `open|filtered` results. Reliability depends on target OS and network devices.

---

### When to Use UDP Scans
- Command example: `nmap -sU -p 68,69,123 scanme.nmap.org`
- UDP is connectionless — lack of response is common and ambiguous.

Examples and explanations:
- `udp.port == 68 (open|filtered)`  
  - Nmap sends a short/empty UDP datagram. If the service silently drops it or a firewall drops it, Nmap reports **open|filtered**.
- `udp.port == 69 (closed)`  
  - Host responds with **ICMP Destination Unreachable (Port Unreachable)** → Nmap marks the port **closed**.
- `udp.port == 123 (open)`  
  - Nmap crafts a protocol-appropriate payload (e.g., NTP request) and receives a valid response → **open**.
  - Example: NTP service observed on `45.33.32.156` (scanme.nmap.org) responding with NTP v4/v3 replies.

UDP behavior summary:
- No reply → **open|filtered** (could be open service silently dropping datagrams or firewall dropped packets).
- ICMP Port Unreachable → **closed**.
- Valid protocol response → **open**.

---
