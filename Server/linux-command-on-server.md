# Linux Commands Cheat Sheet

## File and Directory Management
- `ls` → List files and directories
- `ls -a` → List all files (including hidden files)
- `ls -l` → List files in long format (detailed info)
- `pwd` → Show current working directory
- `cd <directory>` → Change directory
- `cd ..` → Move one directory up
- `mkdir <dir-name>` → Create a new directory
- `rmdir <dir-name>` → Remove empty directory
- `rm -rf <dir-name>` → Force remove a directory with all files inside

## File Operations
- `touch <file-name>` → Create an empty file
- `cp <source> <destination>` → Copy file/directory
- `mv <source> <destination>` → Move or rename file/directory
- `rm <file-name>` → Delete a file
- `cat <file-name>` → Display file contents
- `nano <file-name>` → Open file in Nano editor
- `vim <file-name>` → Open file in Vim editor

## User & Permission
- `whoami` → Show current logged-in user
- `id` → Show user ID and groups
- `chmod <permissions> <file>` → Change file permissions
- `chown <user>:<group> <file>` → Change file owner

## System Info
- `uname -a` → Show system information
- `df -h` → Show disk usage
- `du -sh <dir>` → Show size of a directory
- `top` → Show running processes
- `htop` → Better process viewer (if installed)
- `free -h` → Show memory usage
- `uptime` → Show how long the system has been running

## Networking
- `ifconfig` → Show network interfaces (older systems)
- `ip a` → Show IP addresses
- `ping <host>` → Check connectivity
- `curl <url>` → Fetch data from a URL
- `wget <url>` → Download file from URL
- `netstat -tulnp` → Show listening ports (if available)
- `ss -tulnp` → Show listening ports (modern replacement)

## Package Management (Ubuntu/Debian)
- `apt update` → Update package list
- `apt upgrade` → Upgrade installed packages
- `apt install <package>` → Install a package
- `apt remove <package>` → Remove a package

## Process Management
- `ps aux` → List all running processes
- `kill <pid>` → Kill a process by PID
- `kill -9 <pid>` → Force kill a process
- `systemctl status <service>` → Check status of a service
- `systemctl start <service>` → Start a service
- `systemctl stop <service>` → Stop a service
- `systemctl restart <service>` → Restart a service
- `systemctl enable <service>` → Enable service at boot
- `systemctl disable <service>` → Disable service at boot

## Archive & Compression
- `tar -czvf archive.tar.gz <folder>` → Create compressed archive
- `tar -xzvf archive.tar.gz` → Extract archive
- `zip -r archive.zip <folder>` → Create zip file
- `unzip archive.zip` → Extract zip file

## Useful Shortcuts
- `clear` → Clear terminal screen
- `history` → Show previously used commands
- `!!` → Run last command again
- `!<number>` → Run command from history by number
- `ctrl + c` → Stop current process
- `ctrl + d` → Logout from terminal
