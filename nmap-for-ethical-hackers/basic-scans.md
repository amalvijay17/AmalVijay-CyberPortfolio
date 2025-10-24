# Core Nmap Skills, Basic Scans

## Important points
- **Help**
  - `nmap -h` — Show usage, options and quick examples.

- **Find your local IP (Linux)**
  - `ifconfig` — Know which interfaces/subnets you belong to before scanning.

- **Scanning hosts, subnets, and ranges**
  - `nmap 192.168.100.0/24` — Scan entire subnet for live hosts and ports (CIDR).
  - `nmap 192.168.100.1-10` — Scan an explicit IP range.
  - `nmap 192.168.100.0/24 192.168.16.0/24` — Scan multiple subnets in one command.

- **Port selection and presets**
  - `nmap 192.168.100.3` — Default: scans Nmap's top 1000 ports.
  - `nmap -F 192.168.100.3` — Fast: top 100 ports.
  - `nmap --top-ports 20 192.168.100.3` — Top N most common ports.

- **Explicit ports and full port range**
  - `nmap -p 80,443 192.168.100.3` — Scan only ports 80 and 443.
  - `nmap -p 1-100 192.168.100.3` — Scan ports 1 through 100.
  - `nmap -p- 192.168.100.3` — Scan all 65535 TCP ports (`-p 0-65535`).

- **UDP scanning and discovery flags**
  - `nmap -p 80,443 -sU scanme.nmap.org` — UDP scan of specified ports.
  - `nmap -p 80,443 -sU -Pn --disable-arp-ping scanme.nmap.org`  
    - `-sU`: UDP only  
    - `-Pn`: skip host discovery (no ICMP echo, TCP SYN ping, or ARP ping, treat host as up and do port scannning)  
    - `--disable-arp-ping`: skip ARP ping (useful on local networks)
    - 
- Pn (No Ping, Treat All Hosts as Online)
- PS (TCP SYN ping)
- sn (Ping Scan/Host Discovery Only)

- **Host discovery vs port scanning**
  - `nmap -sn 192.168.100.0/24` — Ping scan / host discovery only: determines which hosts are up; **does NOT** list open ports.
  - Run with `sudo` for privileged discovery (ICMP, ARP); unprivileged mode may use TCP probes instead.

- **Local vs Internet discovery differences**
  - ARP-based discovery works only on the same LAN. For public IPs Nmap uses ICMP, TCP pings and other probes.


- **OS fingerprinting and aggressive scans**
  - `nmap -O 192.168.100.3` — OS detection: sends crafted TCP/IP probes to fingerprint the target stack.
  - `nmap -A 192.168.100.3` — Aggressive: version detection, OS detection, script scanning, traceroute, full port scan. Noisy — likely detected by IDS/IPS.

- **Scan types and stealth**
  - `nmap -sS -p 80 192.168.100.3 -v`  
    - `-sS`: TCP SYN (stealth) scan — sends SYN, expects SYN-ACK, then sends RST.  
    - `-v`: verbose shows scan details and type.
  - Without privileges Nmap may fall back to TCP connect scan (completes full TCP handshake).

- **Useful flags summary**
  - `-h` : help  
  - `-sn` : ping/host discovery only (no ports)  
  - `-p` : specify ports or ranges (`-p-`, `-p 1-65535`, `-p 80,443`)  
  - `-F` : fast (top 100 ports)  
  - `--top-ports N` : scan top N common ports  
  - `-sU` : UDP scan  
  - `-sS` : SYN (stealth) scan  
  - `-O` : OS fingerprinting  
  - `-A` : aggressive (version, OS, scripts, traceroute)  
  - `-Pn` : skip host discovery (treat hosts as up)  
  - `--disable-arp-ping` : disable ARP host discovery  
  - `-v` : verbose

- **Practical notes (lab tips)**
  - Use `sudo` for privileged scans to get SYN/ARP behavior and more accurate results.
  - `-sn` will not show ports; it only reveals which hosts are alive.
  - ARP discovery is reliable on LAN; it cannot cross routers.
  - UDP scans often show "closed" via ICMP port unreachable or may appear filtered; expect different behavior than TCP.

## Top 5 commands
```bash
nmap -sn 192.168.100.0/24          # Ping scan — find active devices on subnet
nmap --top-ports 20 192.168.100.3  # Scan top 20 most common ports
nmap -O 192.168.100.3              # OS fingerprinting (TCP/IP stack analysis)
nmap -A 192.168.100.3              # Aggressive scan: ports, versions, OS, traceroute
nmap -p 80 192.168.100.3           # Scan specific port (HTTP)
