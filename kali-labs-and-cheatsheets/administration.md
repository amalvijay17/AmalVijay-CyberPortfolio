# Web & SSH Services (Kali)

## Turn Kali into a web server (Apache)
- `service apache2 start`
- `service apache2 stop`
- `echo "Hello Hackers" > /var/www/html/index.html`
- `echo "Hello Hackers" > /var/www/html/download` (create file inside `/var/www/html`)
- `sudo sh -c 'echo "Hello Hackers" > /var/www/html/index.html'` (as non-root)
- `wget http://<kaliIP>/download` (download from Kali to any remote machine)

## Quick Python web server
- `python3 -m http.server` (Ctrl + C to quit)

## Turn Kali into an SSH server
- `cd /etc/ssh`
- `sudo mkdir old-keys`
- `sudo mv ssh_host_* old-keys`
- `sudo dpkg-reconfigure openssh-server`
- `service ssh start`
- `nano /etc/ssh/sshd_config` (edit config)
  - set `PermitRootLogin` / `PasswordAuthentication` / `ProhibitPassword` as needed
- `service ssh restart`
- `ssh localhost`

## Useful Windows/Linux clients
- `putty`, `winscp` (Windows)
- `scp` (From Linux command line)
  - Example: `scp transfer.txt kali@192.168.6.130:/home/kali`
