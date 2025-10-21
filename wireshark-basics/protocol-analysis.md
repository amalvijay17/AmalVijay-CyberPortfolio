# FTP & HTTP Sniffing — Practical Summary

> Compact, single-file summary of the steps, observations, Wireshark filters, and security recommendations for capturing and analyzing FTP and HTTP credentials.

---

## 1. Obtaining sample captures

Two quick ways to download sample Wireshark captures:

* **From Wireshark website**

  1. Google: `sample wireshark captures` → click **SampleCaptures**.
  2. Use the search box (Ctrl+F) and search for `ftp`.
  3. Choose `FTPv6-1.cap` (third occurrence) and download.
  4. Open it in Wireshark; use the display filter `ftp` and press Enter.

* **From GitLab (sample Wireshark captures gitlab)**

  1. Visit the top result.
  2. Browse available captures and download what you need.

---

## 2. Why FTP and HTTP are interesting for sniffing

* **FTP (File Transfer Protocol)** is plaintext by default (like HTTP or telnet). Username/password and commands are sent unencrypted → easily intercepted.
* **HTTP** (without TLS) also sends form data in plaintext; credentials can be captured depending on how forms are submitted (GET vs POST).

Both protocols are useful to study because they show how unencrypted protocols leak credentials and data.

---

## 3. Practical lab setup (FTP server on Kali + Metasploitable)

### Install and start FTP server on Kali

```bash
sudo apt-get install vsftpd
sudo service vsftpd start
sudo nano /etc/vsftpd.conf
# (for demo) set: anonymous_enable=YES
```

### Example login sequence (Metasploitable target)

1. From Kali or any client:

```bash
ftp 192.168.100.3
# Username: msfadmin
# Password: msfadmin
```

2. If passive mode needed: type `pass` and Enter in the FTP client to switch to passive mode.
3. Run commands like `ls` to produce traffic for Wireshark to capture.

---

## 4. Capturing with Wireshark

1. Open Wireshark and select the interface (e.g., `eth0`).
2. Start the capture, then perform the FTP login and a few commands.
3. Stop the capture when done.
4. In Wireshark use the **display filter** `ftp` to show only FTP protocol packets.

### Packet flow notes

* Conversation typically starts with the TCP 3-way handshake.
* FTP is an **application-layer** protocol over TCP.
* Server replies are numeric codes (3-digit): e.g., `220`, `331`, `230`, `500`.

**Common FTP response codes seen in captures:**

* `220` — Service ready for new user (server ready to accept connection).
* `331` — Username OK, need password.
* `230` — Login successful.
* `500` — Syntax error, command unrecognized (e.g., illegal PORT command).

---

## 5. Viewing requests and responses separately (Wireshark filters)

* Show only server responses:

```
ftp.response.code
```

* Show a specific response code:

```
ftp.response.code == 230
```

* Show only client requests:

```
ftp.request.command
```

* Show a specific request command (examples):

```
ftp.request.command == "USER"
ftp.request.command == "PASS"
```

---

## 6. Follow the conversation (easier reading)

* Right-click any FTP packet → **Follow** → **TCP Stream**.
* This opens the full conversation in a readable format:

  * Server responses typically shown in one color (blue in the instructor example).
  * Client requests shown in another color (red in the instructor example).
* Here you can clearly see `USER msfadmin` and `PASS msfadmin` in plain text.

---

## 7. Why credentials are visible

* FTP uses plaintext commands for authentication (`USER`, `PASS`) and other operations.
* Anyone able to sniff the network traffic (on the same LAN or via a compromised network device) can read these credentials directly from the capture.

---

## 8. Demonstration: HTTP credential capture

### GET vs POST

* `GET` appends form data to the URL (e.g., `?user=alice&pass=1234`) — visible in requests and logs.
* `POST` places data in the **request body**, not the URL. While slightly more private from a URL/logging standpoint, if the transport is unencrypted HTTP the POST body is still plaintext on the wire and can be captured.

### Wireshark filters to find web login POSTs

```text
http.request.method == "POST"
```

* Inspect packets for `application/x-www-form-urlencoded` in the packet details (HTML Form URL Encoded).
* You can then view the request body and read credentials in plain text.

---

## 9. Defending against sniffing

* **Use encrypted protocols:**

  * SSH instead of FTP (or use **SFTP** — SSH File Transfer Protocol), which encrypts the whole session.
  * HTTPS (HTTP over TLS) instead of plain HTTP to protect web form data in transit.
* **Avoid sending credentials over plaintext protocols.**
* **Use passive/secure configurations** for servers and disable insecure legacy protocols where possible.

### Example: try SSH instead of FTP

```bash
ssh msfadmin@<server_ip>
# Everything is encrypted; packets captured by Wireshark will not show credentials in plaintext.
```

---

## 10. Quick reference: useful Wireshark filters

* `ftp` — show FTP protocol packets
* `ftp.request.command == "USER"` — show USER commands
* `ftp.request.command == "PASS"` — show PASS commands
* `ftp.response.code == 230` — show successful login responses
* `http.request.method == "POST"` — show HTTP POST requests
* `http.request.method == "GET"` — show HTTP GET requests

---

## 11. Summary / Key takeaways

* FTP and plain HTTP transmit sensitive data in cleartext and are vulnerable to interception.
* Wireshark easily reveals FTP credentials via `USER` and `PASS` commands; HTTP login bodies can show credentials when not protected by TLS.
* Use SSH (or SFTP) and HTTPS to protect credentials in transit.

---
