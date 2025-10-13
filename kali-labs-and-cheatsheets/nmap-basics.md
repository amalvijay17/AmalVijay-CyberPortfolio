# Port Scanning Techniques

- `nmap scanme.nmap.org` (SYN scan by default if root)  
- If not root above scan will be connect scan (3-way handshake)  
- `SYN scan` - Half open scan (`-sS`)  
- `Connect scan` - 3-way handshake (`-sT`)

- `nmap -p 21-25 -sS scanme.nmap.org`  
- `nmap -sV scanme.nmap.org`  
- `nmap -vv scanme.nmap.org` (Verbose)  
- `nmap -A 192.168.100.3 -oN scan.txt > /dev/null 2>&1 &` (Run in background, output to `scan.txt`)  
- `nmap -sn 192.168.100.0/24` (Ping scan — list all devices in subnet)  
- `nmap -v -T5 -p 0-65535 -A 192.168.100.3 -oA metasploitable2` (Output `.gnmap`, `.nmap`, `.xml`)  
- `less metasploitable2.nmap`

## Semi-Automated Vulnerability Research
- Nmap Scripting Engine location: `/usr/share/nmap/scripts`  
- Example: `nmap -p 21 --script ftp-vsftpd-backdoor <ip>`

## Scripted Vulnerability Research
- `cat /usr/share/nmap/scripts/vulners.nse`  
- `nmap -sV --script vulners <ip>`  
- `nmap -sV --script vulners --script-args mincvss=10 <ip>`
