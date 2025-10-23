# Advanced Packaging Tool (APT)

- `apt-cache search <package>` — Search for package (`apt search`)  
- `apt-cache show <package>` — Show information about package (`apt show`)  
- `apt-get install <package>` — Install package (`-y` for yes) (`apt install`)  
- `apt-get remove <package>` — Remove package (but keep configs) (`apt remove`)  
- `apt-get autoremove` — Remove unused dependencies (`apt autoremove`)  
- `apt-get purge <package>` — Remove package and configs (`apt purge`)
- Note : Repositories are storage location from which software package can be retrived and installed on a computer
- `nano /etc/apt/sources.list` — Edit repositories (`apt edit-sources`)  
- `apt-get update` — Update package lists (`apt update`)  
- `apt-get upgrade` — Upgrade all upgradable packages (`apt upgrade`)  
- `apt-get dist-upgrade` — Full system upgrade (`apt full-upgrade`)  
- `dpkg -l` — List installed packages
- `dpkg -l | grep '^ii'` (Shows installed packages)
- `dpkg -i package.deb` — Install `.deb` package  
- `dpkg -r package.deb` — Remove `.deb` package
