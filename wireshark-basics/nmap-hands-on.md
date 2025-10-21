# Nmap — Information Gathering and Port Scanning

Nmap is an advanced information-gathering tool. It scans for open ports and services running on systems.
Attackers look for openings or access points; Nmap helps identify those by discovering open ports, services, OS information, and well-known vulnerabilities.

Regular port scanning helps find and close possible vulnerabilities. Review and audit all open ports to verify correct usage and to ensure services using those ports are patched and secured.

## Install & Quick Checks

- Check installation:
  - Windows/Mac: open terminal or command prompt and run `nmap`
  - Linux: Nmap is often preinstalled; otherwise `sudo apt-get install nmap`
- Help:
  - `nmap -h`

## Basic Scans

- Ping/host discovery:
  - `nmap -sP scanme.nmap.org`
- Simple scan (default, top 1000 ports):
  - `nmap 45.33.32.156`
  - Output shows: Port, State, Service
- Scan multiple hosts:
  - `nmap www.google.com www.reddit.com`

## Speed & Port Range

- Fast scan (top 100 ports):
  - `nmap -F 45.33.32.156`
- Specific port:
  - `nmap -p 21 45.33.32.156`
- Port range:
  - `nmap -p 1-21 45.33.32.156`
- Service name instead of port number:
  - `nmap -p ftp 45.33.32.156`
- Multiple ports/services:
  - `nmap -p 21,22,80 45.33.32.156`

## Port States

- **Open** — Service accepting connections. If unprotected, this is risky.
- **Filtered** — A firewall or filtering device prevents Nmap from determining state.
  - Example: internal ports may appear filtered from Internet scans but open internally.
- **Closed** — No service is listening on the port.

## Service & Version Detection

- Service/version scanning:
  - `nmap -sV 45.33.32.156`
  - Adds a **Version** column. Use discovered version info to search exploit DBs or CVEs.
  - If vulnerable, apply patches or update the application.

## Aggressive Scan

- Run with elevated privileges:
  - Windows: run CMD as Administrator
  - Linux: run as root
- Aggressive scan:
  - `nmap -A 45.33.32.156`
  - Includes OS detection, version detection, script scanning, and traceroute.
- **Traceroute** shows the path routers/hops that packets traverse to reach the destination.

## Output & Saving Results

- Redirect output to file (hide in terminal):
  - `nmap -p 22 45.33.32.156 > result.txt`
- Output to file and show in terminal:
  - `nmap -p 22 45.33.32.156 -o result.txt`
- View with line numbers:
  - `cat -n result.txt`
- XML output (useful for other tools):
  - `nmap -F 45.33.32.156 -oX result.xml`
  - `.xml` is commonly consumed by third-party applications and parsers.

## Best Practices

- Run port scans regularly to identify exposed services.
- Audit all open ports and associated applications for correct use and up-to-date patches.
- Use version detection to map services to known vulnerabilities (CVE, Exploit-DB).
- Avoid scanning targets without permission; always have authorization.

## Examples Summary

- `nmap -h` — Help
- `nmap -sP <host>` — Ping scan / host discovery
- `nmap <host>` — Default top 1000 ports
- `nmap -F <host>` — Fast scan (top 100 ports)
- `nmap -p 1-100 <host>` — Scan ports 1–100
- `nmap -sV <host>` — Service/version detection
- `nmap -A <host>` — Aggressive scan (OS, version, traceroute)
- `nmap -oX result.xml <host>` — Save results to XML
