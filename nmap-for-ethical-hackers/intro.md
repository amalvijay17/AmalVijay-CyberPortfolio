# Nmap - Network Mapper

**Purpose**
- Discover what ports are open, services and applications supported, and server versions.
- Identify potential vulnerabilities on target devices.

**Audience**
- Pen Testers, Ethical Hackers, Network Engineers, SOC Analysts

**Targets**
- scanme.nmap.org
- ginandjuice.shop

**Examples**
- `nmap -sS -p 80,443 webscantest.com`  *(open Wireshark in parallel)*
- `nmap scanme.nmap.org` *(Top 1000 ports)*

**Wireshark — Display filters & interpretation**
- `tcp.flags.syn==1 and tcp.flags.ack==0` — show SYN packets only (no SYN-ACK).
- SYN -> SYN-ACK — indicates the port is available and open.
- `tcp.port==22` — filter traffic for SSH (example).
- Sequence: SYN, SYN-ACK, ACK — handshake completed; connection established. If you receive an RST after, close the connection to avoid leaving it open.
- `tcp.port==25` — example where port appears filtered: you may see SYN and retransmissions but no SYN-ACK or RST.
- Closed port — typically returns RST.
- Filtered port — may be blocked by a firewall (no RST or SYN-ACK), or filtered on the endpoint.

**Scan as tightly as you can**
