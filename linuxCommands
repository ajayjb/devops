# Most Used Linux Commands

A comprehensive guide to the most commonly used Linux commands, organized by category.

## File and Directory Operations

### Navigation
- `pwd` - Print working directory (shows current directory path)
- `cd [directory]` - Change directory
- `cd ~` - Go to home directory
- `cd ..` - Go up one directory level
- `cd -` - Go to previous directory

### Listing Files
- `ls` - List files and directories
- `ls -l` - Long format listing with details
- `ls -a` - Show hidden files (starting with .)
- `ls -lh` - Human-readable file sizes
- `ls -R` - Recursive listing of subdirectories

### File Operations
- `touch [filename]` - Create empty file or update timestamp
- `cat [file]` - Display file contents
- `less [file]` - View file with pagination
- `more [file]` - View file page by page
- `head [file]` - Show first 10 lines of file
- `head -n 20 [file]` - Show first 20 lines
- `tail [file]` - Show last 10 lines of file
- `tail -f [file]` - Follow file updates in real-time

### Copying, Moving, and Deleting
- `cp [source] [destination]` - Copy files
- `cp -r [source] [destination]` - Copy directories recursively
- `mv [source] [destination]` - Move or rename files/directories
- `rm [file]` - Remove file
- `rm -r [directory]` - Remove directory recursively
- `rm -rf [directory]` - Force remove directory without prompts
- `rmdir [directory]` - Remove empty directory

### Directory Operations
- `mkdir [directory]` - Create new directory
- `mkdir -p [path/to/directory]` - Create nested directories

## File Permissions and Ownership

- `chmod [permissions] [file]` - Change file permissions
- `chmod 755 [file]` - Set rwxr-xr-x permissions
- `chmod +x [file]` - Add execute permission
- `chown [user]:[group] [file]` - Change file owner and group
- `chgrp [group] [file]` - Change group ownership

## Text Processing

### Searching and Filtering
- `grep [pattern] [file]` - Search for pattern in file
- `grep -r [pattern] [directory]` - Recursive search
- `grep -i [pattern] [file]` - Case-insensitive search
- `grep -v [pattern] [file]` - Invert match (show non-matching lines)
- `grep -n [pattern] [file]` - Show line numbers

### Text Manipulation
- `sed 's/old/new/g' [file]` - Replace text in file
- `awk '{print $1}' [file]` - Print first column
- `sort [file]` - Sort lines alphabetically
- `uniq [file]` - Remove duplicate lines
- `wc [file]` - Count lines, words, and characters
- `wc -l [file]` - Count lines only
- `cut -d':' -f1 [file]` - Cut fields from delimited file

### Comparison
- `diff [file1] [file2]` - Compare files line by line
- `cmp [file1] [file2]` - Compare files byte by byte

## System Information

### System Status
- `uname -a` - Display system information
- `hostname` - Show system hostname
- `uptime` - Show system uptime
- `whoami` - Display current username
- `who` - Show logged-in users
- `date` - Display current date and time
- `cal` - Display calendar

### Resource Monitoring
- `top` - Display running processes (interactive)
- `htop` - Enhanced process viewer (if installed)
- `ps aux` - Show all running processes
- `ps -ef` - Show all processes with full format
- `free -h` - Display memory usage (human-readable)
- `df -h` - Show disk space usage (human-readable)
- `du -sh [directory]` - Show directory size
- `du -h --max-depth=1` - Show sizes of subdirectories

## Process Management

- `kill [PID]` - Terminate process by ID
- `kill -9 [PID]` - Force kill process
- `killall [process_name]` - Kill all processes by name
- `pkill [pattern]` - Kill processes matching pattern
- `bg` - Resume suspended job in background
- `fg` - Bring background job to foreground
- `jobs` - List background jobs
- `nohup [command] &` - Run command immune to hangups

## Networking

### Network Information
- `ifconfig` - Display network interfaces (older systems)
- `ip addr` - Show IP addresses (modern systems)
- `ip link` - Show network interfaces
- `hostname -I` - Display IP address

### Connectivity
- `ping [host]` - Test network connectivity
- `traceroute [host]` - Show route to host
- `netstat -tuln` - Show listening ports
- `ss -tuln` - Socket statistics (modern alternative)
- `curl [URL]` - Transfer data from/to URL
- `wget [URL]` - Download files from web

### Network Tools
- `ssh [user]@[host]` - Secure shell connection
- `scp [file] [user]@[host]:[path]` - Secure copy files
- `rsync -avz [source] [destination]` - Sync files/directories

## Package Management

### Debian/Ubuntu (apt)
- `apt update` - Update package lists
- `apt upgrade` - Upgrade installed packages
- `apt install [package]` - Install package
- `apt remove [package]` - Remove package
- `apt search [package]` - Search for package

### Red Hat/CentOS (yum/dnf)
- `yum update` - Update packages
- `yum install [package]` - Install package
- `yum remove [package]` - Remove package
- `dnf install [package]` - Install package (newer systems)

## User Management

- `useradd [username]` - Create new user
- `userdel [username]` - Delete user
- `passwd [username]` - Change user password
- `su [username]` - Switch user
- `sudo [command]` - Execute command as superuser
- `groups [username]` - Show user groups
- `id [username]` - Display user and group IDs

## File Compression and Archiving

### tar (Tape Archive)
- `tar -cvf [archive.tar] [files]` - Create tar archive
- `tar -xvf [archive.tar]` - Extract tar archive
- `tar -czvf [archive.tar.gz] [files]` - Create compressed tar.gz
- `tar -xzvf [archive.tar.gz]` - Extract tar.gz
- `tar -cjvf [archive.tar.bz2] [files]` - Create tar.bz2
- `tar -xjvf [archive.tar.bz2]` - Extract tar.bz2

### Compression
- `gzip [file]` - Compress file
- `gunzip [file.gz]` - Decompress gzip file
- `zip [archive.zip] [files]` - Create zip archive
- `unzip [archive.zip]` - Extract zip archive

## Searching and Finding

- `find [path] -name [filename]` - Find files by name
- `find [path] -type f` - Find files only
- `find [path] -type d` - Find directories only
- `find [path] -mtime -7` - Find files modified in last 7 days
- `find [path] -size +100M` - Find files larger than 100MB
- `locate [filename]` - Quick file search using database
- `updatedb` - Update locate database
- `which [command]` - Show path of command
- `whereis [command]` - Locate binary, source, and manual

## Input/Output Redirection

- `[command] > [file]` - Redirect output to file (overwrite)
- `[command] >> [file]` - Append output to file
- `[command] < [file]` - Read input from file
- `[command1] | [command2]` - Pipe output to another command
- `[command] 2> [file]` - Redirect error output
- `[command] &> [file]` - Redirect both output and errors

## Disk Management

- `mount [device] [mount_point]` - Mount filesystem
- `umount [mount_point]` - Unmount filesystem
- `fdisk -l` - List disk partitions
- `lsblk` - List block devices
- `blkid` - Display block device attributes

## System Control

- `shutdown -h now` - Shutdown immediately
- `shutdown -r now` - Restart immediately
- `reboot` - Restart system
- `halt` - Halt system
- `systemctl start [service]` - Start service
- `systemctl stop [service]` - Stop service
- `systemctl restart [service]` - Restart service
- `systemctl status [service]` - Check service status
- `systemctl enable [service]` - Enable service at boot

## Environment Variables

- `echo $[VARIABLE]` - Display variable value
- `export [VARIABLE]=[value]` - Set environment variable
- `env` - Display all environment variables
- `printenv` - Print environment variables
- `source [file]` - Execute commands from file in current shell

## Aliases and History

- `alias [name]='[command]'` - Create command alias
- `unalias [name]` - Remove alias
- `history` - Show command history
- `!![number]` - Execute command from history
- `!!` - Execute last command
- `Ctrl+R` - Search command history (interactive)

## Miscellaneous

- `clear` - Clear terminal screen
- `echo [text]` - Display text
- `man [command]` - Display manual page for command
- `info [command]` - Display info documentation
- `[command] --help` - Display help for command
- `ln -s [target] [link]` - Create symbolic link
- `file [filename]` - Determine file type
- `stat [file]` - Display file statistics
- `watch [command]` - Execute command repeatedly

## Quick Tips

### Keyboard Shortcuts
- `Ctrl+C` - Interrupt/kill current command
- `Ctrl+Z` - Suspend current command
- `Ctrl+D` - Exit current shell/logout
- `Ctrl+L` - Clear screen (same as clear command)
- `Ctrl+A` - Move cursor to beginning of line
- `Ctrl+E` - Move cursor to end of line
- `Ctrl+U` - Clear line before cursor
- `Tab` - Auto-complete files/directories/commands

### Wildcards
- `*` - Matches any characters
- `?` - Matches single character
- `[abc]` - Matches any character in brackets
- `[!abc]` - Matches any character not in brackets

This guide covers the essential Linux commands you'll use regularly. Practice these commands to become proficient in Linux system administration and daily operations.
