# Nmap Scan with Wireshark

## Overview
A concise summary of how Nmap determines port states, how scans look on the wire, how IDS (e.g., Snort) detects them, and recommended admin responses.

---

## How Nmap determines whether a port is open
- **TCP Connect Scan (`-sT`)**: completes the full **3-way handshake** (SYN → SYN/ACK → ACK). If the handshake completes, Nmap treats the port as **open**; it then usually closes the connection (e.g., with RST) because it doesn't exchange application data.
- **SYN (Stealth) Scan (`-sS`)**: sends **SYN**; if it receives **SYN/ACK** the port is considered **open** — Nmap does **not** complete the handshake and instead sends **RST** to abort. This is faster and avoids full connections, and can be less obvious to naive logging but is still detectable by IDS.
- Nmap decides port state by the **TCP flags in responses**:
  - `SYN/ACK` → port **open** (SYN or SYN/ACK depending on scan)
  - `RST,ACK` → port **closed**
  - **No response** or ICMP unreachable → port **filtered** (firewall drops/blocks)

---

## Typical packet exchange examples (Wireshark filters)
- **Open port (connect scan)**:
  1. Client → Server: **SYN**  
  2. Server → Client: **SYN/ACK**  
  3. Client → Server: **ACK**  ← handshake complete (port open)  
  4. Client → Server: **RST**  ← Nmap tears down immediately  
  - Wireshark filter for SSH example: `tcp.port == 22`

- **Stealth SYN scan**:
  1. Client → Server: **SYN**  
  2. Server → Client: **SYN/ACK** → Nmap records **open**  
  3. Client → Server: **RST**

- **Closed / Filtered**:
  - `RST,ACK` responses indicate **closed**.
  - Re-transmissions + no reply or ICMP errors indicate **filtered**.

---

## Detection by IDS (e.g., Snort)
- **Connect scans** (full 3-way handshakes) are obvious: IDS signatures can count SYN→SYN/ACK→ACK sequences and alert.
- **SYN scans** still generate SYN and SYN/ACK packets — IDS like Snort can detect abnormal volumes of SYNs, short-lived connections, or patterns consistent with port scanning and raise alerts.
- **Conclusion:** Nmap is not truly stealthy against a well-tuned IDS.

---

## Common Nmap flags & examples
- `sudo nmap -sS -F scanme.nmap.org`  
  - `-sS` = SYN (stealth) scan  
  - `-F` = fast (most popular 100 ports)

- `nmap -p 21 scanme.nmap.org` — scan specific port 21

- `nmap --top-ports 1000 scanme.nmap.org` — scan top 1000 ports

---

## Recommended admin responses
1. **Monitor / Log**: Ensure IDS/IPS (e.g., Snort) is enabled and tuned to alert on scan patterns (excess SYNs, many ports probed from same source).
2. **Investigate source**: Check packet source IP, correlate with logs, and decide whether to block.
3. **Harden exposed services**:
   - Close or firewall unused ports.
   - Implement IP whitelisting for critical services (SSH).
   - Use strong authentication (disable password login where possible, use key-based SSH).
   - Rate-limit and employ fail2ban-like solutions to mitigate brute-force attempts.
4. **Network segmentation & filtering**: Place sensitive services behind firewalls and only allow required sources.
5. **Alerting**: Configure priority alerts for scans hitting sensitive ports (21, 22, 3306, etc.).

---

## Someone scanning my network/device?
- Check source of traffic (source IP).
- Decide whether to block the IP (blocking prevents further probes).
- If you cannot close or filter ports, strengthen credentials and implement additional controls (rate-limiting, MFA, keys).

---

## Quick takeaways
- Nmap determines open ports by responses to SYNs—either by full handshake (connect scan) or by seeing SYN/ACK (SYN scan) and aborting with RST.
- A sophisticated IDS (Snort) can detect both connect and SYN scans easily if tuned correctly.
- Use logging + filtering + strong authentication to defend against scanners.

---

## Useful capture / filter hints
- Wireshark filter: `tcp.port == 22`
- Focus on SYN, SYN/ACK, RST patterns and retransmissions to classify open/closed/filtered.
