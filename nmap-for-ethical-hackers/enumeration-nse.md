# HTTP / FTP / SMB / TLS Enumeration

**Summary**: Collected Nmap commands, Wireshark steps, and brief explanations for labs on HTTP enumeration, FTP login attacks, SMB enumeration, NSE vulnerability scripts, and TLS/cipher scans.

---

## Lab - HTTP Enumeration (Finding Hidden Folders)

**Goal:** Find hidden folders and common web resources using Nmap's HTTP enumeration script.

**Key command(s):**

```bash
nmap --script "http-enum" 192.168.100.3
```

**Notes:**

* The `http-enum` script probes for common directories, files, and web paths (e.g., `/admin`, `/robots.txt`, `/backup`).
* Use the `http.request` filter in Wireshark to observe HTTP requests and responses while scanning.

---

## Lab - Hacking FTP Logins

**Goal:** Enumerate FTP services, test for anonymous login, and brute-force credentials.

**Commands & steps:**

```bash
# Grab basic banner
nmap --script "banner" 192.168.100.3

# List FTP related scripts
ls | grep -e ftp

# Check for anonymous FTP access
nmap --script "ftp-anon" 192.168.100.3
# or specify port explicitly
nmap --script "ftp-anon" 192.168.100.3 -p 21

# Brute-force FTP using username/password lists
nmap -p 21 --script ftp-brute --script-args userdb=users.txt,passdb=pass.txt 192.168.100.3
```

**Wireshark steps (capture & analyze):**

1. Start capture while running the brute-force script.
2. In Wireshark, go to **Analyze → Follow → TCP Stream** on the FTP conversation.
3. Find the packet where the client issues `USER anonymous` or `PASS <password>`.
4. To search FTP PASS commands: use filter `ftp.request.command == "PASS"`.
5. You can add the `Request Arg` (the password value) as a column to more easily view attempted passwords.

**Notes:**

* Anonymous login (`USER anonymous`) is common but dangerous — it may expose files.
* When you capture FTP credentials in Wireshark, they are in cleartext (FTP is unencrypted).
* You can feed cracked/collected password lists back into `ftp-brute` (see command above).

---

## Lab - SMB Login Enumeration

**Goal:** Enumerate SMB services and discover user accounts via Samba.

**Commands & steps:**

```bash
# Service version detection to find SMB services
nmap -sV 192.168.100.3

# List available smb-related NSE scripts
cd /usr/share/nmap/scripts
ls | grep -e smb

# Enumerate SMB users
nmap --script "smb-enum-users" 192.168.100.3
```

**Notes:**

* SMB (Server Message Block) is a network file sharing protocol; Linux commonly uses Samba to provide SMB services.
* Typical SMB ports: `139/tcp` and `445/tcp`.
* `nmap -sV` shows service and version information (e.g., `Samba smbd 3.X - 4.X`), and often the NetBIOS workgroup name.
* `smb-enum-users` attempts to list users that are present on the SMB server — useful for credential spraying or targeted password attacks.

---

## Lab - NSE Vulnerability Scripts

**Goal:** Use NSE scripts that check for known vulnerabilities for discovered services.

**Commands:**

```bash
# Search for vulners or similar vulnerability scripts
ls | grep -e "vulners"

# Run the vulners script with service version detection
nmap -sV --script "vulners" 192.168.100.3
```

**Notes:**

* The `vulners` NSE script correlates discovered service versions with CVE and vulnerability databases to report likely issues.
* Always validate automated results — false positives and version-parsing issues can happen.

---

## Lab - Scanning for TLS Certificates and Versions

**Goal:** Discover supported TLS versions and cipher suites of HTTPS/TLS-enabled services.

**Commands:**

```bash
# List SSL-related scripts
ls | grep -e ssl

# Enumerate TLS versions and ciphers (example against nmap.org)
nmap --script "ssl-enum-ciphers" nmap.org -p 443
```

**What to look for:**

* Whether the server supports outdated TLS versions (e.g., TLS 1.0 or TLS 1.1) — these are weak and should be avoided.
* Weak or export-grade cipher suites, or cipher suites without forward secrecy.
* The script reports a grade (A to F) based on cipher strength and known weaknesses.

**Wireshark tip:**

* Filter for `tls` or look for `Client Hello` / `Server Hello` packets to inspect negotiated versions and chosen cipher suites.
* In a TLS handshake, the client first sends supported versions and ciphers (Client Hello); the server replies with the selected TLS version and cipher (Server Hello), and provides the certificate.

**Notes:**

* `ssl-enum-ciphers` can be noisy against public sites — use responsibly.
* Use results to advise upgrades (e.g., remove TLS 1.0/1.1, disable RC4/weak ciphers, enable TLS 1.2+ and TLS 1.3, prefer strong ECDHE suites).

---

## General Recommendations & Safety

* Always have authorization before scanning or attacking any systems.
* Prefer non-intrusive scans initially (`-sV`, script discovery) before brute-force or intrusive testing.
* Capture network traffic only where permitted; sensitive data (like cleartext FTP passwords) will be visible in captures.

---
