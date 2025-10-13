# Network Basics

- `tracert` — Windows traceroute  
- `traceroute` — Linux traceroute  
- `ip route` — Find default gateway (first entry)  
- `eth0` — Wired network interface  
- `lo` — Loopback interface  
- `wlan0` — Wireless network interface  
- `ipconfig /all` — Windows network details  
- `cat /etc/resolv.conf` — DNS servers (`nameserver`)  
- `systemctl restart NetworkManager` — Restart NetworkManager (Kali)  
- `nslookup google.com` — Query DNS server (check "Server" section)  
- `sudo service apache2 start` — Start webserver  
- `sudo sh -c 'echo "Hello Hackers! Welcome" > /var/www/html/index.html'` — Write index as non-root  
- `sudo service apache2 stop` — Stop webserver

- `netstat -antp` — Show network connections with options:
  - `-a` → show all (listening + established + closed)  
  - `-n` → numeric addresses/ports (no DNS resolution)  
  - `-t` → show TCP only  
  - `-p` → show PID/program name using the connection
