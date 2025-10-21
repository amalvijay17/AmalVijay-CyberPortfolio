## How OS Fingerprinting Works (and when it fails)

* **Command:**

  ```bash
  nmap -O <target> -v
  ```

  (`-O` = OS detection, `-v` = verbose)
* Nmap sends a variety of *non-standard / crafted TCP SYNs* to probe the target’s TCP stack and observe precise replies.
* It inspects low-level fields and behavior such as:

  * TCP flags (e.g., ACK bit presence)
  * Acknowledgment number usage (e.g., non-zero ACK number with ACK flag unset)
  * TCP option presence and **ordering** (MSS, Timestamps, SACK-permitted, Window Scale, etc.)
  * MSS values (e.g., 1460 vs 640 vs 536)
  * Window size patterns and window-scale values
  * Whether timestamps appear first in options
  * Presence/absence of selective acknowledgements (SACK)
* Every byte/bit and option ordering can be a fingerprintable trait of a TCP/IP stack.
* Nmap collects these responses and compares them to its fingerprint database to guess the OS.
* **When fingerprinting fails or is inaccurate:**

  * Responses are blocked/altered by firewalls, IPS, or middleboxes.
  * The device has no open ports (no meaningful replies).
  * The TCP stack has been deliberately obfuscated or is non-standard.

## Version Discovery (Service Version Detection)

* **Command:**

  ```bash
  nmap -sV <target>
  ```

  (`-sV` = service/version detection)
* Workflow:

  * First ensure the port is open (or open/filtered).
  * Send protocol-specific probes or observe banners/handshakes.
  * Parse returned banner or handshake bytes to extract software name and version (examples below).
* **Examples (Wireshark filters & observations):**

  * SSH: `tcp.port == 22` → handshake banner like `SSH-2.0-OpenSSH_6.6.1p1`
  * HTTP: `tcp.port == 80` → HTTP response header `Server: Apache/2.4.7`
* Depending on the service, Nmap either sends requests (HTTP GET, protocol probes) or reads server banners.

## Using Verbosity in Nmap Output

* Use `-v` to increase verbosity. More `v` means more detail:

  ```bash
  nmap -sV scanme.nmap.org -v
  ```

  (increase to `-vv` etc. for further verbosity)

## Exporting Nmap Results to a File

* Common output flags:

  * `-oN` → Normal (plain text)
  * `-oX` → XML
  * `-oG` → Grepable
  * `-oA` → Write all major formats (prefix)
* **Example saving a single-port scan to plain text:**

  ```bash
  nmap -p 80 scanme.nmap.org -oN scanme.txt
  ```
* **Include the command and its output together in one file:**

  ```bash
  {
    echo "Command: nmap -p 22 -sV scanme.nmap.org -oN scanme.txt"
    nmap -p 22 -sV scanme.nmap.org
  } > scanme.txt
  ```
* Tip: always include the exact nmap command in output files so scans are reproducible and auditable.

---
