# Nmap — Timing Templates, Best Practices & NSE

---

## Timing Templates

Timing templates control scan speed and aggressiveness. If you don't set a timing template, Nmap uses `T3` by default.

* `-T5` — **Insane / Fastest**

  ```bash
  nmap scanme.nmap.org -T5
  ```
* `-T0` — **Paranoid / Slowest**

  ```bash
  nmap scanme.nmap.org -T0
  ```

> During a penetration test we often want quick results, so timing templates help speed up scans. Be aware: higher `-T` values send more packets faster and can trigger IDS/IPS.

## Best Practices for Optimal Scan Performance

* We send many TCP SYN packets for ports that *might* be open — tune scans to avoid noise.
* Use **ping scan** when you only need host discovery (skips full port scan):

  ```bash
  nmap -sn scanme.nmap.org
  ```
* Avoid scanning every port unnecessarily — too many packets can be noisy:

  ```bash
  nmap scanme.nmap.org  # (will do a full scan by default)
  ```
* Avoid heavy scans like `-A` or OS detection unless needed — they are noisy and slow:

  ```bash
  # Avoid when possible
  nmap -A scanme.nmap.org
  ```
* Target only relevant ports with `-p` or use `--top-ports` to limit the list:

  ```bash
  nmap --top-ports 5 192.168.100.3
  nmap -p 22,80,443 target
  ```
* Use timing templates (`-T`) to balance speed vs stealth.

**Practical rules:**

* Port scans on a single device are normal; scanning many IPs at once looks abnormal and increases detection risk.
* Prefer `-sn` (ping scan) for discovery to reduce packet volume.
* Avoid `-A` and `-O` unless you need aggressive version/OS detection.

## What is NSE (Nmap Scripting Engine)

* **NSE** = *Nmap Scripting Engine* — automates and extends Nmap functionality.
* NSE scripts are written in **Lua** but are human-readable.
* Use cases include: network discovery, version detection, vulnerability detection, backdoor detection, and even exploitation scripts.
* Many scripts can be noisy — pick scripts carefully.

## The Script Database

Every Nmap installation includes a built-in script database (scripts are usually stored at `/usr/share/nmap/scripts`).

```bash
# List installed scripts
cd /usr/share/nmap/scripts
ls

# Inspect a script
cat <script_name>

# Update the script database (update script metadata)
sudo nmap --script-updatedb
```

---

## Lab — NSE: Default Scripts

**Default script scan**

```bash
nmap -sC scanme.nmap.org
```

Or explicitly run default scripts:

```bash
nmap --script=default scanme.nmap.org
```

**Example: http-auth script**

```bash
nmap --script "http-auth" scanme.nmap.org
```

**Run several scripts using wildcards**

```bash
nmap --script "ftp-*" scanme.nmap.org
```

### Observing HTTP scripts in Wireshark

* Some NSE HTTP scripts show the actual HTTP requests.
* In Wireshark, right-click a TCP packet and: **Follow → TCP Stream** to inspect request/response content.

---

## Lab — NSE: Banners and HTTP Scripts

### Banner scripts

```bash
cd /usr/share/nmap/scripts
ls | grep -e "banner"

# Run banner script (grabs initial banners for services)
nmap --script "banner" scanme.nmap.org
```

**What is a banner?**

* A banner is the initial information a service sends when a connection is made (often reveals software name, version, OS).
* Example outputs you might see:

  * `Apache/2.4.41`
  * `SSH-2.0-OpenSSH_7.6p1 Ubuntu-4ubuntu0.3`

**Banner grabbing** helps identify service type and version before authentication.

### HTTP methods script

```bash
nmap --script "http-methods" scanme.nmap.org
```

* This script asks the server which HTTP methods are supported.
* Example output: `Allow: GET, HEAD, POST, OPTIONS`

### HTTP enumeration

Scripts like `http-enum.nse` enumerate common directories and hidden folders:

```bash
ls | grep -e "http"

# Enumerate common web directories
nmap --script "http-enum" scanme.nmap.org
```

Example output snippet:

```
80/tcp    open     http
| http-enum:
|_  /images/: Potentially interesting directory w/ listing on 'apache/2.4.7 (ubuntu)'
```

Then you can browse: `http://scanme.nmap.org/images/`

---

## Packet analysis tips (Wireshark)

* When an NSE script makes HTTP requests you can filter responses by HTTP status code. Example Wireshark filter for responses with code 400:

```
http.response.code == 400
```

* To find how the server responded to specific requests, look at the `Request URI` column in Wireshark and add it as a column. This helps map request → response pairs.

* Inspect the `HTTP/1.1 400 Bad Request` or `HTTP/1.1 200 OK` lines to see exactly what the server returned.

---

## Quick Command Summary

```bash
# Timing templates
nmap scanme.nmap.org -T5
nmap scanme.nmap.org -T0

# Host discovery (no port scan)
nmap -sn scanme.nmap.org

# Default scripts
nmap -sC scanme.nmap.org
nmap --script=default scanme.nmap.org

# Specific scripts
nmap --script "http-methods" scanme.nmap.org
nmap --script "http-enum" scanme.nmap.org
nmap --script "banner" scanme.nmap.org

# Top ports only
nmap --top-ports 5 192.168.100.3

# Update NSE script DB
sudo nmap --script-updatedb
```

---

### Notes / Tips

* Use `-p` or `--top-ports` to reduce noise and runtime.
* Avoid `-A`/`-O` unless necessary — they're noisy and may trigger alerts.
* Review NSE script code (`/usr/share/nmap/scripts`) if a script's behavior seems unclear.
* Balance speed and stealth: higher `-T` is faster but easier to detect.

---
