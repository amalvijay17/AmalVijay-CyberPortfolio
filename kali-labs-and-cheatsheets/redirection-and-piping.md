# Command Redirection & Piping

- Operator `&`  
  - `&` - Execute commands in the background

- Operator `&&`  
  - `&&` - Run commands sequentially; second runs only if first succeeds

- `>` - Direct output to file (creates/overwrites file)  
- `>>` - Append output to file  
- `<` - Use file as input to a command

- `cat -n <file>` (Numbered lines)  
- `ls /usr/share/nmap/scripts/ | less`  
- `ls /usr/share/nmap/scripts/ | grep brute`  
- `wc -l <filename>` (Count lines)  
- `gnmap` (Grepable nmap output)

- Example: extract IPs for port 25 open and save unique list  
  - `grep 25/open output.gnmap | cut -d " " -f 2 | sort -u > output.txt`
