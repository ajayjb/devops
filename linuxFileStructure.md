# Linux Filesystem Hierarchy Standard (FHS) – In-Depth Complete Guide

This comprehensive document explains the **Linux filesystem hierarchy**, its directories, their purposes, historical context, and practical usage according to the **Filesystem Hierarchy Standard (FHS)**. Written for beginners through advanced users.

---

## Table of Contents

1. Introduction to Linux Filesystem
2. Root Directory (`/`)
3. Essential Binary Directories
4. User System Resources (`/usr`)
5. Configuration and Data Directories
6. Virtual and Special Filesystems
7. Boot and System Directories
8. The Usr Merge Explained
9. Filesystem Standards and Compliance
10. Advanced Topics
11. Practical Examples and Use Cases
12. Troubleshooting Common Issues
13. Security Considerations
14. Best Practices

---

## 1. Introduction to Linux Filesystem

### What is the Filesystem Hierarchy Standard (FHS)?

The **Filesystem Hierarchy Standard (FHS)** is maintained by the Linux Foundation and defines the directory structure and directory contents in Unix-like operating systems. It ensures consistency across different Linux distributions.

**Current version:** FHS 3.0 (released June 3, 2015)

**Key principles:**
- Everything is a file (regular files, directories, devices, sockets, pipes)
- Single unified directory tree (no drive letters like Windows C:, D:)
- Case-sensitive filenames
- Forward slashes as path separators
- Clear separation between system files and user data

### Filesystem Types in Linux

Linux supports multiple filesystem types:

**Disk filesystems:**
- ext4 (most common on Linux)
- XFS (high performance)
- Btrfs (advanced features, snapshots)
- ZFS (advanced, not in mainline kernel)
- NTFS (Windows compatibility)
- FAT32/exFAT (removable media)

**Virtual filesystems:**
- proc (process information)
- sysfs (device and kernel info)
- tmpfs (RAM-based storage)
- devtmpfs (device files)

### Why Understanding the Filesystem Matters

**For System Administrators:**
- Proper backup strategies
- Disk space management
- Security hardening
- Troubleshooting system issues

**For Developers:**
- Correct installation paths for software
- Understanding library dependencies
- Debugging path-related issues
- Cross-distribution compatibility

**For Users:**
- Finding configuration files
- Understanding where programs store data
- Managing disk space effectively

---

## 2. Root Directory (`/`)

### Overview

The **root directory** (`/`) is the top-level directory of the filesystem hierarchy. Every file and directory starts from here.

**Important:** Don't confuse:
- `/` (root directory)
- `/root` (root user's home directory)

### Characteristics

```bash
ls -ld /
```

Output:
```text
drwxr-xr-x 20 root root 4096 Jan 31 10:00 /
```

**Permissions breakdown:**
- `d` = directory
- `rwxr-xr-x` = owner: rwx, group: r-x, others: r-x
- Owned by root user and root group

### First-Level Directories

Standard directories under `/`:

```text
/
├── bin -> usr/bin
├── boot
├── dev
├── etc
├── home
├── lib -> usr/lib
├── lib64 -> usr/lib64
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin -> usr/sbin
├── srv
├── sys
├── tmp
├── usr
└── var
```

### Mounting the Root Filesystem

During boot, the kernel mounts the root filesystem:

1. Bootloader loads kernel and initramfs
2. Initramfs provides minimal root filesystem
3. Real root filesystem is mounted
4. System transitions to real root (`pivot_root`)

**View current root filesystem:**

```bash
df -h /
mount | grep " / "
findmnt /
```

---

## 3. Essential Binary Directories

### `/bin` – Essential User Binaries

**Purpose:** Contains essential command-line binaries needed for:
- System boot
- Single-user mode
- System recovery
- Basic file operations

**Historical context:**  
Originally, `/bin` contained binaries needed before `/usr` was mounted (when `/usr` could be on a separate partition).

**Contents:**

```bash
ls /bin | head -20
```

Common commands:
- Shell: `bash`, `sh`, `dash`
- File operations: `ls`, `cp`, `mv`, `rm`, `mkdir`, `rmdir`
- Text processing: `cat`, `grep`, `sed`, `awk`
- Compression: `gzip`, `bzip2`, `tar`
- System: `ps`, `kill`, `mount`, `umount`

**Modern status:**

```bash
ls -ld /bin
```

Output on modern systems:
```text
lrwxrwxrwx 1 root root 7 Jan 15 2024 /bin -> usr/bin
```

**Why the symlink?**  
The "usr merge" unified `/bin` and `/usr/bin` to eliminate duplication.

**Example usage:**

```bash
# These are equivalent on modern systems
/bin/ls
/usr/bin/ls
ls
```

### `/sbin` – System Binaries

**Purpose:** System administration binaries, primarily for the root user.

**Historical distinction:**
- `/bin` = regular user commands
- `/sbin` = system/superuser commands

**Contents:**

```bash
ls /sbin | head -20
```

Important commands:
- Filesystem: `fsck`, `mkfs`, `resize2fs`, `tune2fs`
- Networking: `ip`, `ifconfig`, `route`, `iptables`
- System control: `reboot`, `shutdown`, `init`, `systemctl`
- Disk management: `fdisk`, `parted`, `lvm`
- Module management: `modprobe`, `insmod`, `rmmod`

**Example: Checking filesystem:**

```bash
# Must be run as root
sudo fsck -n /dev/sda1  # -n = no changes, just check
```

**Example: Network configuration:**

```bash
# View network interfaces
ip addr show

# View routing table
ip route show
```

**Modern status:**

```bash
ls -ld /sbin
```

Output:
```text
lrwxrwxrwx 1 root root 8 Jan 15 2024 /sbin -> usr/sbin
```

### PATH and Binary Resolution

**Understanding PATH:**

```bash
echo $PATH
```

Typical output:
```text
/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/usr/sbin:/sbin
```

**Order matters:**  
When you type a command, the shell searches directories in order:

1. `/usr/local/bin` (first)
2. `/usr/bin`
3. `/bin` → `/usr/bin` (same as #2 via symlink)
4. `/usr/local/sbin`
5. `/usr/sbin`
6. `/sbin` → `/usr/sbin` (same as #5 via symlink)

**Finding command locations:**

```bash
# Which binary will execute?
which python3

# All locations of a command
whereis python3

# Type and location
type python3

# Follow symlinks to real file
readlink -f $(which python3)
```

**Example output:**

```bash
$ which python3
/usr/bin/python3

$ readlink -f $(which python3)
/usr/bin/python3.11
```

---

## 4. User System Resources (`/usr`)

### Overview

**`/usr`** is the second-largest directory hierarchy (after `/`). It contains the majority of user utilities and applications.

**Name origin:**  
Originally "Unix System Resources," not "user" (common misconception).

**Key principle:**  
`/usr` should be shareable and read-only. Multiple machines can mount the same `/usr` over the network.

### Directory Structure

```text
/usr
├── bin          # User commands
├── sbin         # System administration commands
├── lib          # Libraries for /usr/bin and /usr/sbin
├── lib64        # 64-bit libraries (on some systems)
├── libexec      # Executables called by other programs
├── local        # Local software installation
├── share        # Architecture-independent data
├── src          # Source code
├── include      # Header files for C/C++
└── games        # Games (optional)
```

### `/usr/bin` – User Commands

**Purpose:** Non-essential binaries for all users.

**Size:**

```bash
ls /usr/bin | wc -l
```

Typical result: 1000-3000+ binaries

**Categories of programs:**

**Programming languages and tools:**
```bash
ls /usr/bin | grep -E "python|ruby|perl|node|java|gcc|g\+\+"
```

Examples: `python3`, `ruby`, `perl`, `node`, `npm`, `java`, `gcc`, `g++`

**Text editors:**
```bash
ls /usr/bin | grep -E "vim|nano|emacs"
```

Examples: `vim`, `nano`, `emacs`

**Development tools:**
```bash
ls /usr/bin | grep -E "git|make|cmake|gdb"
```

Examples: `git`, `make`, `cmake`, `gdb`, `valgrind`

**Multimedia:**
```bash
ls /usr/bin | grep -E "ffmpeg|convert|gimp"
```

Examples: `ffmpeg`, `convert` (ImageMagick), `gimp`

**Networking:**
```bash
ls /usr/bin | grep -E "curl|wget|ssh|scp|nc"
```

Examples: `curl`, `wget`, `ssh`, `scp`, `nc` (netcat)

**System information:**
```bash
ls /usr/bin | grep -E "top|htop|free|df|du"
```

Examples: `top`, `htop`, `free`, `df`, `du`

### `/usr/sbin` – System Administration Binaries

**Purpose:** Non-essential system administration programs.

**Examples:**

**Service management:**
```bash
ls /usr/sbin | grep -E "nginx|apache|mysql|postgresql"
```

**Network services:**
```bash
ls /usr/sbin | grep -E "sshd|dhcpd|named|vsftpd"
```

**System utilities:**
```bash
ls /usr/sbin | grep -E "adduser|deluser|visudo|cron"
```

### `/usr/lib` and `/usr/lib64` – Libraries

**Purpose:** Shared libraries (.so files) required by programs in `/usr/bin` and `/usr/sbin`.

**Understanding shared libraries:**

```bash
# List library dependencies of a program
ldd /usr/bin/python3
```

Output example:
```text
linux-vdso.so.1 (0x00007ffd8e9fe000)
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f8b2e400000)
/lib64/ld-linux-x86-64.so.2 (0x00007f8b2e7f0000)
```

**Searching for libraries:**

```bash
# Find all .so files
find /usr/lib -name "*.so*" | head -10

# Find specific library
ldconfig -p | grep libssl
```

**Why lib64?**  
On 64-bit systems, some distributions separate:
- `/usr/lib` = 32-bit libraries
- `/usr/lib64` = 64-bit libraries

Others use:
- `/usr/lib/x86_64-linux-gnu/` = 64-bit
- `/usr/lib/i386-linux-gnu/` = 32-bit

### `/usr/libexec` – Internal Binaries

**Purpose:** Programs executed by other programs, not directly by users.

Examples:
- Git helper programs
- GNOME/KDE internal tools
- Package manager helpers

```bash
ls /usr/libexec | head -10
```

### `/usr/share` – Architecture-Independent Data

**Purpose:** Data files that work on any architecture (not compiled code).

**Contents:**

```bash
ls /usr/share
```

Common subdirectories:

**Documentation:**
```bash
/usr/share/doc        # Package documentation
/usr/share/man        # Man pages
/usr/share/info       # Info pages
```

**Localization:**
```bash
/usr/share/locale     # Translations
/usr/share/i18n       # Internationalization data
```

**Graphics:**
```bash
/usr/share/icons      # Icon themes
/usr/share/pixmaps    # Image files
/usr/share/wallpapers # Wallpapers
```

**Fonts:**
```bash
/usr/share/fonts      # System fonts
```

**Application data:**
```bash
/usr/share/applications  # .desktop files
/usr/share/mime          # MIME type info
```

**Example: Reading man pages:**

```bash
# Man pages are stored as compressed files
ls /usr/share/man/man1/ls.1.gz

# View man page
man ls

# Man page location
man -w ls
```

### `/usr/src` – Source Code

**Purpose:** Source code for the kernel and other packages.

**Typical contents:**

```bash
ls /usr/src
```

Example:
```text
linux-headers-5.15.0-91
linux-headers-5.15.0-91-generic
```

**Installing kernel headers:**

```bash
# Debian/Ubuntu
sudo apt install linux-headers-$(uname -r)

# RHEL/CentOS/Fedora
sudo dnf install kernel-devel
```

### `/usr/include` – Header Files

**Purpose:** Header files (.h) for C/C++ development.

**Example structure:**

```bash
ls /usr/include | head -20
```

Common headers:
```text
stdio.h
stdlib.h
string.h
math.h
pthread.h
```

**Usage in compilation:**

```bash
# Compiler automatically searches /usr/include
gcc program.c -o program

# Manually specify include path
gcc -I/usr/include program.c -o program
```

### `/usr/games` – Games

**Purpose:** Game binaries (optional, not on all systems).

```bash
ls /usr/games
```

Examples: `fortune`, `cowsay`, `sl` (steam locomotive)

---

## 5. `/usr/local` – Locally Installed Software

### Overview

**Purpose:** Software compiled and installed locally by the system administrator, not managed by the package manager.

**Key principle:**  
`/usr/local` is separate from `/usr` to protect manually installed software from being overwritten during system upgrades.

### Structure

Mirrors `/usr`:

```text
/usr/local
├── bin         # Local binaries
├── sbin        # Local system binaries
├── lib         # Local libraries
├── share       # Local shared data
├── include     # Local headers
├── src         # Local source code
├── etc         # Local configuration
└── man         # Local man pages
```

### Use Cases

**1. Custom scripts:**

```bash
# Create a custom script
sudo nano /usr/local/bin/backup-home
```

```bash
#!/bin/bash
tar -czf /backup/home-$(date +%Y%m%d).tar.gz /home
```

```bash
sudo chmod +x /usr/local/bin/backup-home
```

**2. Manual software installation:**

```bash
# Download and extract software
cd /tmp
wget https://example.com/app-1.0.tar.gz
tar -xzf app-1.0.tar.gz
cd app-1.0

# Configure with prefix
./configure --prefix=/usr/local

# Compile and install
make
sudo make install
```

This installs:
- Binary → `/usr/local/bin/app`
- Libraries → `/usr/local/lib/libapp.so`
- Docs → `/usr/local/share/doc/app/`

**3. Programming language installations:**

```bash
# Install Go manually
wget https://go.dev/dl/go1.21.0.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.21.0.linux-amd64.tar.gz

# Add to PATH
export PATH=$PATH:/usr/local/go/bin
```

### Why `/usr/local` Comes First in PATH

```bash
echo $PATH
```

Typical result:
```text
/usr/local/bin:/usr/bin:/bin
```

**Benefit:** Your custom version of a program overrides the system version.

**Example:**

System has Python 3.10:
```bash
/usr/bin/python3 --version
# Python 3.10.12
```

You install Python 3.11 manually:
```bash
sudo make install prefix=/usr/local
/usr/local/bin/python3 --version
# Python 3.11.0
```

Now `python3` runs your version:
```bash
python3 --version
# Python 3.11.0 (from /usr/local/bin)
```

---

## 6. Configuration and Data Directories

### `/etc` – Configuration Files

**Purpose:** System-wide configuration files and scripts.

**Etymology:** "et cetera" (and other things) or "Editable Text Configuration"

**Key rules:**
- Text files only (no binaries)
- ASCII/UTF-8 format preferred
- One configuration per application or service

**Important files and directories:**

**System configuration:**
```bash
/etc/fstab          # Filesystem mount table
/etc/hostname       # System hostname
/etc/hosts          # Static IP to hostname mapping
/etc/resolv.conf    # DNS resolver configuration
/etc/timezone       # System timezone
/etc/locale.gen     # Available locales
```

**User and group management:**
```bash
/etc/passwd         # User account information
/etc/shadow         # Encrypted passwords (readable only by root)
/etc/group          # Group information
/etc/sudoers        # Sudo configuration
```

**Network configuration:**
```bash
/etc/network/       # Network interfaces (Debian)
/etc/sysconfig/     # System config (RHEL)
/etc/netplan/       # Network config (Ubuntu 18.04+)
```

**Service configuration:**
```bash
/etc/nginx/         # Nginx web server
/etc/apache2/       # Apache web server
/etc/mysql/         # MySQL database
/etc/ssh/           # SSH server and client
```

**Package management:**
```bash
/etc/apt/           # APT configuration (Debian/Ubuntu)
/etc/yum.repos.d/   # YUM repositories (RHEL/CentOS)
```

**System initialization:**
```bash
/etc/systemd/       # Systemd configuration
/etc/init.d/        # Init scripts (legacy)
/etc/rc.local       # Local startup script (legacy)
```

**Example: Viewing /etc/fstab:**

```bash
cat /etc/fstab
```

Output:
```text
# <file system> <mount point> <type> <options> <dump> <pass>
UUID=xxx-xxx    /             ext4   defaults   0      1
UUID=yyy-yyy    /home         ext4   defaults   0      2
UUID=zzz-zzz    none          swap   sw         0      0
```

**Example: Adding a static host entry:**

```bash
sudo nano /etc/hosts
```

Add:
```text
192.168.1.100  myserver.local myserver
```

**Configuration file best practices:**
- Always backup before editing: `sudo cp /etc/config /etc/config.backup`
- Use comments to document changes
- Check syntax before restarting services
- Keep track of modifications in version control

### `/home` – User Home Directories

**Purpose:** Personal data storage for regular users.

**Structure:**

```bash
ls -la /home
```

Output:
```text
drwxr-xr-x  5 alice alice 4096 Jan 31 10:00 alice
drwxr-xr-x  4 bob   bob   4096 Jan 30 15:30 bob
```

**Each user's home contains:**

```bash
ls -la /home/alice
```

Common contents:
```text
.bash_history      # Command history
.bash_logout       # Executed on logout
.bash_profile      # Login shell configuration
.bashrc            # Non-login shell configuration
.profile           # Generic shell profile
.ssh/              # SSH keys and config
.config/           # Application configurations
.local/            # User-specific data
Desktop/           # Desktop files
Documents/         # Documents
Downloads/         # Downloaded files
Pictures/          # Images
Videos/            # Videos
```

**Hidden files convention:**  
Files starting with `.` are hidden by default.

```bash
# Show hidden files
ls -la
```

**User environment variables:**

```bash
# View user environment
env | grep HOME
```

Output:
```text
HOME=/home/alice
```

**Home directory permissions:**

```bash
# Recommended permissions
chmod 700 /home/alice     # Only owner can access
chmod 755 /home/alice     # Others can list but not modify
```

### `/root` – Root User's Home

**Purpose:** Home directory for the root (superuser) account.

**Why separate from `/home`?**

1. **Availability:** Root needs access even if `/home` is on a separate partition or network mount that fails
2. **Security:** Isolates root's files from regular user access
3. **Tradition:** Unix convention since early days

**Accessing root's home:**

```bash
# Switch to root
sudo -i

# Check location
echo $HOME
# Output: /root

# Or as regular user (requires root)
sudo ls -la /root
```

**Typical contents:**

```bash
sudo ls -la /root
```

```text
.bash_history
.bashrc
.profile
.ssh/
.vimrc
```

### `/var` – Variable Data

**Purpose:** Files expected to grow or change during normal system operation.

**Etymology:** "variable"

**Key subdirectories:**

**Logs:**
```bash
/var/log/           # System and application logs
/var/log/syslog     # System log (Debian/Ubuntu)
/var/log/messages   # System log (RHEL/CentOS)
/var/log/auth.log   # Authentication log
/var/log/kern.log   # Kernel log
/var/log/dmesg      # Boot messages
/var/log/apt/       # Package manager logs
/var/log/nginx/     # Web server logs
```

**Cache:**
```bash
/var/cache/         # Application cache
/var/cache/apt/     # APT package cache
```

**Temporary files:**
```bash
/var/tmp/           # Temporary files (persistent across reboots)
```

**Spool:**
```bash
/var/spool/         # Queued data
/var/spool/cron/    # Cron jobs
/var/spool/mail/    # Mail delivery
/var/spool/cups/    # Print jobs
```

**Runtime data:**
```bash
/var/run/           # Runtime data (symlink to /run on modern systems)
```

**System libraries:**
```bash
/var/lib/           # Persistent data for programs
/var/lib/mysql/     # MySQL databases
/var/lib/postgresql/ # PostgreSQL databases
/var/lib/docker/    # Docker data
```

**Web content:**
```bash
/var/www/           # Web server content
/var/www/html/      # Default web root
```

**Example: Monitoring logs in real-time:**

```bash
# Follow system log
sudo tail -f /var/log/syslog

# Watch authentication attempts
sudo tail -f /var/log/auth.log

# View last 100 lines of kernel log
sudo tail -100 /var/log/kern.log
```

**Example: Checking disk usage of /var:**

```bash
# Total size of /var
du -sh /var

# Largest subdirectories
sudo du -h /var --max-depth=1 | sort -rh | head -10
```

**Log rotation:**

Logs can grow large. Most systems use `logrotate`:

```bash
# View logrotate configuration
cat /etc/logrotate.conf

# Check specific log rotation
cat /etc/logrotate.d/rsyslog
```

Example configuration:
```text
/var/log/syslog {
    rotate 7
    daily
    missingok
    notifempty
    compress
    delaycompress
}
```

This rotates logs daily, keeps 7 days of history, and compresses old logs.

### `/tmp` – Temporary Files

**Purpose:** Temporary file storage for all users and programs.

**Characteristics:**

```bash
ls -ld /tmp
```

Output:
```text
drwxrwxrwt 10 root root 4096 Jan 31 11:30 /tmp
```

**Permissions explained:**
- `rwxrwxrwx` = All users can read, write, execute
- `t` (sticky bit) = Users can only delete their own files

**Sticky bit example:**

```bash
# Alice creates a file
touch /tmp/alice-file

# Bob cannot delete Alice's file
rm /tmp/alice-file
# Permission denied

# Bob can create his own file
touch /tmp/bob-file
```

**Cleanup:**

Most systems automatically clean `/tmp`:
- At boot (systemd-tmpfiles)
- Periodically (depending on config)

**Configuration:**

```bash
# View tmpfiles.d configuration
cat /usr/lib/tmpfiles.d/tmp.conf
```

Example:
```text
# Clear tmp directories separately, to make them easier to override
D /tmp 1777 root root -
D /var/tmp 1777 root root 30d
```

**Best practices:**
- Don't store important data in `/tmp`
- Use unique filenames: `mktemp` command
- Clean up your temporary files when done

**Example: Creating secure temp files:**

```bash
# Create temporary file
TMPFILE=$(mktemp)
echo "data" > $TMPFILE

# Use it
cat $TMPFILE

# Clean up
rm $TMPFILE

# Create temporary directory
TMPDIR=$(mktemp -d)
echo "file" > $TMPDIR/test
rm -rf $TMPDIR
```

---

## 7. Virtual and Special Filesystems

### `/dev` – Device Files

**Purpose:** Device files representing hardware and pseudo-devices.

**Device file types:**

**Block devices** (buffered I/O):
```bash
ls -l /dev/sda
```

Output:
```text
brw-rw---- 1 root disk 8, 0 Jan 31 10:00 /dev/sda
```

- `b` = block device
- `8, 0` = major:minor number

Common block devices:
- `/dev/sda`, `/dev/sdb` = SATA/SCSI disks
- `/dev/nvme0n1` = NVMe SSD
- `/dev/mmcblk0` = SD card

**Character devices** (unbuffered I/O):
```bash
ls -l /dev/tty
```

Output:
```text
crw-rw-rw- 1 root tty 5, 0 Jan 31 11:45 /dev/tty
```

- `c` = character device

Common character devices:
- `/dev/tty*` = Terminals
- `/dev/null` = Null device (discards all data)
- `/dev/zero` = Provides infinite zeros
- `/dev/random`, `/dev/urandom` = Random number generators

**Special devices:**

**`/dev/null`** - The bit bucket:
```bash
# Discard output
command > /dev/null 2>&1

# Test file permissions without creating file
> /dev/null
```

**`/dev/zero`** - Infinite zeros:
```bash
# Create a 100MB file of zeros
dd if=/dev/zero of=file.bin bs=1M count=100
```

**`/dev/random` and `/dev/urandom`** - Random data:
```bash
# Generate random data
dd if=/dev/urandom bs=1M count=1 | base64 | head -c 32
```

**Device file creation:**

Normally handled by `udev`, but can be done manually:

```bash
# Create block device
sudo mknod /dev/mydev b 8 1

# Create character device
sudo mknod /dev/mychar c 1 3
```

**Finding device information:**

```bash
# List block devices
lsblk

# List all devices
ls -l /dev

# USB devices
lsusb

# PCI devices
lspci
```

### `/proc` – Process Information Filesystem

**Purpose:** Virtual filesystem providing process and kernel information.

**Type:** procfs (process filesystem)

**Characteristics:**
- Not stored on disk
- Generated by kernel in real-time
- Files show current system state

**Important files and directories:**

**System information:**
```bash
cat /proc/cpuinfo      # CPU details
cat /proc/meminfo      # Memory information
cat /proc/version      # Kernel version
cat /proc/uptime       # System uptime
cat /proc/loadavg      # Load average
```

**Example: CPU information:**

```bash
cat /proc/cpuinfo | grep "model name" | head -1
```

Output:
```text
model name      : Intel(R) Core(TM) i7-9750H CPU @ 2.60GHz
```

**Example: Memory information:**

```bash
cat /proc/meminfo | grep -E "MemTotal|MemFree|MemAvailable"
```

Output:
```text
MemTotal:       16384000 kB
MemFree:         2048000 kB
MemAvailable:   10240000 kB
```

**Process-specific directories:**

Each running process has a directory `/proc/[PID]`:

```bash
# View current shell's PID
echo $$

# View process info
ls -l /proc/$$
```

Common files in process directory:
```bash
/proc/[PID]/cmdline    # Command line
/proc/[PID]/cwd        # Current working directory (symlink)
/proc/[PID]/environ    # Environment variables
/proc/[PID]/exe        # Executable (symlink)
/proc/[PID]/fd/        # Open file descriptors
/proc/[PID]/maps       # Memory mappings
/proc/[PID]/stat       # Process status
/proc/[PID]/status     # Human-readable status
```

**Example: View command that started process:**

```bash
# Get PID of nginx
pidof nginx

# View command
cat /proc/$(pidof nginx)/cmdline | tr '\0' ' '
```

**Network information:**

```bash
cat /proc/net/dev      # Network interfaces
cat /proc/net/route    # Routing table
cat /proc/net/tcp      # TCP connections
cat /proc/net/udp      # UDP connections
```

**Kernel parameters:**

```bash
# View all kernel parameters
sysctl -a

# View specific parameter
cat /proc/sys/net/ipv4/ip_forward

# Modify parameter (temporary)
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward

# Modify parameter (permanent)
sudo nano /etc/sysctl.conf
# Add: net.ipv4.ip_forward = 1
sudo sysctl -p
```

### `/sys` – System Devices and Kernel Information

**Purpose:** Interface to kernel objects, devices, and drivers.

**Type:** sysfs (system filesystem)

**Structure:**

```bash
ls /sys
```

```text
block/        # Block devices
bus/          # System buses (PCI, USB, etc.)
class/        # Device classes
dev/          # Device nodes
devices/      # Devices hierarchy
firmware/     # Firmware information
fs/           # Filesystem information
kernel/       # Kernel parameters
module/       # Loaded kernel modules
power/        # Power management
```

**Example: View block devices:**

```bash
ls -l /sys/block/
```

**Example: CPU frequency:**

```bash
# View current CPU frequency
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_cur_freq

# View available governors
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_available_governors
```

**Example: LED control:**

```bash
# List LEDs
ls /sys/class/leds/

# Control keyboard LED (example)
echo 1 | sudo tee /sys/class/leds/input0::capslock/brightness
```

**Example: Device power management:**

```bash
# View power state
cat /sys/class/power_supply/BAT0/status
cat /sys/class/power_supply/BAT0/capacity
```

---

## 8. Boot and System Directories

### `/boot` – Boot Loader Files

**Purpose:** Files required for system boot.

**Critical warning:** Improper modification can make your system unbootable!

**Contents:**

```bash
sudo ls -lh /boot
```

Typical files:
```text
vmlinuz-5.15.0-91-generic           # Kernel image (compressed)
initrd.img-5.15.0-91-generic        # Initial RAM disk
System.map-5.15.0-91-generic        # Kernel symbol table
config-5.15.0-91-generic            # Kernel compile configuration
grub/                                # GRUB bootloader directory
efi/                                 # EFI boot files (UEFI systems)
```

**Kernel (vmlinuz):**
- Compressed Linux kernel
- "vm" = virtual memory
- "linuz" = Linux (z = compressed)

**initrd/initramfs:**
- Initial RAM disk/filesystem
- Contains drivers needed to mount root filesystem
- Temporary root filesystem during boot

**GRUB configuration:**

```bash
# Main configuration (don't edit directly)
cat /boot/grub/grub.cfg

# Custom settings
sudo nano /etc/default/grub

# Update GRUB after changes
sudo update-grub  # Debian/Ubuntu
sudo grub2-mkconfig -o /boot/grub2/grub.cfg  # RHEL/CentOS
```

**Example: Changing kernel parameters:**

Edit `/etc/default/grub`:
```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
```

Change to:
```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nomodeset"
```

Update:
```bash
sudo update-grub
sudo reboot
```

**Boot partition:**

Often `/boot` is a separate partition:

```bash
df -h /boot
```

Typical size: 512MB - 1GB

**Why separate?**
- BIOS boot limitations
- Encryption (boot partition unencrypted)
- Multiple OS installations

### `/run` – Runtime Variable Data

**Purpose:** Runtime data for processes started since last boot.

**Created:** At boot, in RAM (tmpfs)

**Replaced:** Old `/var/run` and `/var/lock`

**Contents:**

```bash
ls /run
```

Common subdirectories:
```text
/run/systemd/       # Systemd runtime data
/run/user/          # User runtime directories
/run/lock/          # Lock files
/run/log/           # Early boot logs
```

**User runtime directory:**

```bash
echo $XDG_RUNTIME_DIR
```

Output:
```text
/run/user/1000
```

This directory is:
- Created at login
- Removed at logout
- User-specific (UID-based)
- Permissions: 700 (owner only)

**Example: Socket files:**

```bash
ls /run/*.sock
```

Sockets for inter-process communication.

### `/srv` – Service Data

**Purpose:** Data served by the system (web, FTP, etc.).

**Structure:**

```text
/srv
├── www/        # Web server data
├── ftp/        # FTP server data
└── git/        # Git repositories
```

**Example: Web server:**

```bash
# Create directory structure
sudo mkdir -p /srv/www/example.com

# Place website files
sudo cp -r /path/to/site/* /srv/www/example.com/

# Configure web server to use /srv/www
```

**Note:** Many distributions still use `/var/www` by default.

### `/opt` – Optional Software

**Purpose:** Add-on software packages.

**Typical structure:**

```text
/opt
├── google/
│   └── chrome/
├── oracle/
│   └── java/
└── teamviewer/
```

**Each package in self-contained directory:**

```bash
/opt/appname/
├── bin/
├── lib/
├── share/
└── etc/
```

**Example: Google Chrome:**

```bash
ls -l /opt/google/chrome
```

**Installing to /opt:**

```bash
# Extract to /opt
sudo tar -xzf app.tar.gz -C /opt/

# Create symlinks to binaries
sudo ln -s /opt/app/bin/app /usr/local/bin/app
```

**Difference from /usr/local:**

| `/opt`               | `/usr/local`       |
| -------------------- | ------------------ |
| Self-contained apps  | Follows FHS        |
| Vendor packages      | Admin compiled     |
| Single directory     | Distributed layout |

---

## 9. The Usr Merge Explained

### What is the Usr Merge?

**Historical setup:**

```text
/bin     # Essential user binaries
/sbin    # Essential system binaries
/lib     # Essential libraries

/usr/bin    # Non-essential user binaries
/usr/sbin   # Non-essential system binaries
/usr/lib    # Non-essential libraries
```

**Problem:** Artificial distinction between "essential" and "non-essential" became meaningless.

**Solution:** Merge everything into `/usr`:

```text
/bin → /usr/bin
/sbin → /usr/sbin
/lib → /usr/lib
/lib64 → /usr/lib64
```

### Timeline

**Distributions that completed usr merge:**
- Fedora (2013)
- Arch Linux (2013)
- Debian 10 "Buster" (2019)
- Ubuntu 20.04 LTS (2020)

### Verification

**Check if your system has usr merge:**

```bash
ls -ld /bin /sbin /lib
```

Merged system output:
```text
lrwxrwxrwx 1 root root 7 Jan 15 2024 /bin -> usr/bin
lrwxrwxrwx 1 root root 8 Jan 15 2024 /sbin -> usr/sbin
lrwxrwxrwx 1 root root 7 Jan 15 2024 /lib -> usr/lib
```

**Verify files are not duplicated:**

```bash
# Compare inodes
stat /bin/ls
stat /usr/bin/ls
```

Same inode = same file (via symlink).

### Benefits

1. **Simplicity:** One location instead of two
2. **Compatibility:** Symlinks maintain backward compatibility
3. **Flexibility:** Easier to move /usr to separate partition
4. **Standards:** Aligns with modern filesystem usage

### Compatibility

**Old scripts still work:**

```bash
#!/bin/bash        # Still valid
#!/usr/bin/bash    # Also valid
```

Both resolve to the same file.

---

## 10. Advanced Topics

### Filesystem Mount Points

**View all mounted filesystems:**

```bash
mount
# or
findmnt
# or
df -h
```

**Mount types:**

```bash
findmnt -t ext4,xfs,btrfs    # Disk filesystems
findmnt -t tmpfs,devtmpfs    # Virtual filesystems
findmnt -t nfs,cifs          # Network filesystems
```

**Example mount output:**

```text
TARGET                SOURCE     FSTYPE  OPTIONS
/                     /dev/sda1  ext4    rw,relatime
├─/boot               /dev/sda2  ext4    rw,relatime
├─/home               /dev/sda3  ext4    rw,relatime
├─/dev                devtmpfs   devtmpfs rw,nosuid
├─/proc               proc       proc    rw,nosuid,nodev,noexec
├─/sys                sysfs      sysfs   rw,nosuid,nodev,noexec
└─/tmp                tmpfs      tmpfs   rw,nosuid,nodev
```

### Disk Usage Analysis

**Find largest directories:**

```bash
# Top-level overview
du -h --max-depth=1 / 2>/dev/null | sort -rh | head -10

# Specific directory
du -h --max-depth=1 /var | sort -rh

# Include files
du -ah /var/log | sort -rh | head -20
```

**Interactive tools:**

```bash
# Install ncdu
sudo apt install ncdu

# Analyze directory
ncdu /var
```

**Find large files:**

```bash
# Find files larger than 100MB
find / -type f -size +100M 2>/dev/null

# Find files larger than 1GB
find /var -type f -size +1G 2>/dev/null -exec ls -lh {} \;
```

### Inodes

**Understanding inodes:**

Every file has an inode containing metadata:
- File size
- Permissions
- Timestamps
- Block locations
- Link count

**View inode usage:**

```bash
df -i
```

Output:
```text
Filesystem      Inodes  IUsed   IFree IUse% Mounted on
/dev/sda1      6553600 234567 6319033    4% /
```

**View file inode:**

```bash
ls -i filename
stat filename
```

**Find files by inode:**

```bash
find / -inum 12345678
```

**Why inodes matter:**
- You can run out of inodes even with free disk space
- Each file uses one inode
- Many small files = high inode usage

### Hard Links vs Symbolic Links

**Hard link:**
- Points to same inode as original
- Cannot cross filesystems
- Cannot link directories

```bash
# Create hard link
ln original.txt hardlink.txt

# Check
ls -li original.txt hardlink.txt
```

Same inode number = same file.

**Symbolic link (symlink):**
- Points to filename, not inode
- Can cross filesystems
- Can link directories

```bash
# Create symbolic link
ln -s original.txt symlink.txt

# Check
ls -l symlink.txt
```

Output:
```text
lrwxrwxrwx 1 user user 12 Jan 31 10:00 symlink.txt -> original.txt
```

**Broken symlinks:**

```bash
# Find broken symlinks
find /path -xtype l
```

### File Permissions in Detail

**Permission structure:**

```bash
ls -l file
```

Output:
```text
-rwxr-xr-x 1 user group 1024 Jan 31 10:00 file
```

Breakdown:
```text
- rwx r-x r-x
│ │   │   │
│ │   │   └─ Others: r-x (read, execute)
│ │   └───── Group: r-x
│ └───────── Owner: rwx (read, write, execute)
└─────────── File type: - (regular file)
```

**File types:**
- `-` = Regular file
- `d` = Directory
- `l` = Symbolic link
- `b` = Block device
- `c` = Character device
- `p` = Named pipe (FIFO)
- `s` = Socket

**Numeric permissions:**

```bash
chmod 755 file
```

| Number | Permission | Binary |
| ------ | ---------- | ------ |
| 0      | ---        | 000    |
| 1      | --x        | 001    |
| 2      | -w-        | 010    |
| 3      | -wx        | 011    |
| 4      | r--        | 100    |
| 5      | r-x        | 101    |
| 6      | rw-        | 110    |
| 7      | rwx        | 111    |

**Special permissions:**

**SUID (Set User ID):**
```bash
chmod u+s file
# or
chmod 4755 file
```

Example: `/usr/bin/passwd` runs with root privileges.

**SGID (Set Group ID):**
```bash
chmod g+s directory
# or
chmod 2755 directory
```

Files created in directory inherit group.

**Sticky bit:**
```bash
chmod +t directory
# or
chmod 1777 directory
```

Example: `/tmp` - users can only delete their own files.

**View special permissions:**

```bash
ls -l /usr/bin/passwd
```

Output:
```text
-rwsr-xr-x 1 root root 68208 Jan 31 2024 /usr/bin/passwd
```

The `s` indicates SUID.

### Access Control Lists (ACLs)

**Extended permissions beyond user/group/other:**

```bash
# Set ACL
setfacl -m u:bob:rx file

# View ACL
getfacl file
```

Output:
```text
# file: file
# owner: alice
# group: users
user::rw-
user:bob:r-x
group::r--
mask::r-x
other::r--
```

**Remove ACL:**

```bash
setfacl -x u:bob file
```

---

## 11. Practical Examples and Use Cases

### Example 1: Setting Up a Development Environment

**Install development tools:**

```bash
# Update package list
sudo apt update

# Install build essentials
sudo apt install build-essential git curl

# Install Node.js to /usr/local
cd /tmp
wget https://nodejs.org/dist/v20.0.0/node-v20.0.0-linux-x64.tar.xz
sudo tar -xJf node-v20.0.0-linux-x64.tar.xz -C /usr/local --strip-components=1

# Verify
which node
# Output: /usr/local/bin/node

node --version
```

**Set up project:**

```bash
# Create project directory
mkdir -p ~/projects/myapp
cd ~/projects/myapp

# Initialize
npm init -y

# Install dependencies (go to ~/.npm-global)
npm install express
```

### Example 2: Web Server Configuration

**Install Nginx:**

```bash
sudo apt install nginx
```

**Configuration location:**

```bash
# Main config
/etc/nginx/nginx.conf

# Site configs
/etc/nginx/sites-available/
/etc/nginx/sites-enabled/
```

**Create website:**

```bash
# Create site directory
sudo mkdir -p /var/www/mysite.com/html

# Create sample page
echo "<h1>Hello World</h1>" | sudo tee /var/www/mysite.com/html/index.html

# Create site configuration
sudo nano /etc/nginx/sites-available/mysite.com
```

```nginx
server {
    listen 80;
    server_name mysite.com www.mysite.com;
    root /var/www/mysite.com/html;
    index index.html;
}
```

**Enable site:**

```bash
# Create symlink
sudo ln -s /etc/nginx/sites-available/mysite.com /etc/nginx/sites-enabled/

# Test configuration
sudo nginx -t

# Reload
sudo systemctl reload nginx
```

**Logs location:**

```bash
# Access log
tail -f /var/log/nginx/access.log

# Error log
tail -f /var/log/nginx/error.log
```

### Example 3: Backup Strategy

**System directories to back up:**

```bash
# User data
/home

# System configuration
/etc

# Optional: installed packages list
dpkg --get-selections > /backup/packages.txt

# Optional: database dumps
/var/lib/mysql
```

**Directories to exclude:**

```bash
/dev
/proc
/sys
/tmp
/run
/mnt
/media
```

**Example backup script:**

```bash
#!/bin/bash
# Save as /usr/local/bin/system-backup

BACKUP_DIR="/backup/$(date +%Y%m%d)"
mkdir -p "$BACKUP_DIR"

# Backup /etc
tar -czf "$BACKUP_DIR/etc-backup.tar.gz" /etc

# Backup /home
tar -czf "$BACKUP_DIR/home-backup.tar.gz" /home

# Backup package list
dpkg --get-selections > "$BACKUP_DIR/packages.txt"

echo "Backup completed: $BACKUP_DIR"
```

**Make executable:**

```bash
sudo chmod +x /usr/local/bin/system-backup
```

**Run:**

```bash
sudo system-backup
```

### Example 4: Finding Configuration Files

**Locate config for a service:**

```bash
# Find all nginx configs
find /etc -name "*nginx*" 2>/dev/null

# Find SSH config
find /etc -name "*ssh*" -type f 2>/dev/null
```

**Search within configs:**

```bash
# Find which config file sets a value
grep -r "Port 22" /etc/ssh/

# Search all configs
grep -r "listen" /etc/nginx/
```

### Example 5: Analyzing System Performance

**CPU usage:**

```bash
# Overall
top

# Per-core
mpstat -P ALL 1

# From /proc
cat /proc/loadavg
```

**Memory usage:**

```bash
# Detailed
free -h

# From /proc
cat /proc/meminfo | grep -E "MemTotal|MemFree|MemAvailable"
```

**Disk I/O:**

```bash
# Install
sudo apt install sysstat

# Monitor
iostat -x 1
```

**Network:**

```bash
# Bandwidth
iftop

# Connections
netstat -tupln
```

---

## 12. Troubleshooting Common Issues

### Issue: System Won't Boot

**Possible causes:**

**1. Corrupted /boot:**

Boot into recovery mode and check:

```bash
# List kernel files
ls -l /boot

# Reinstall kernel
sudo apt install --reinstall linux-image-$(uname -r)
sudo update-grub
```

**2. Wrong fstab entry:**

```bash
# Edit fstab in recovery mode
sudo nano /etc/fstab

# Comment out problematic line
# UUID=xxx /mnt/data ext4 defaults 0 2
```

**3. Filesystem errors:**

```bash
# Check filesystem (run from live USB)
sudo fsck -y /dev/sda1
```

### Issue: Out of Disk Space

**Find what's using space:**

```bash
# Overview
df -h

# Largest directories
sudo du -h / --max-depth=1 2>/dev/null | sort -rh | head -10
```

**Common culprits:**

**1. Large log files:**

```bash
# Find large logs
sudo find /var/log -type f -size +100M

# Truncate log
sudo truncate -s 0 /var/log/large.log

# Or delete old logs
sudo find /var/log -type f -name "*.log" -mtime +30 -delete
```

**2. Package cache:**

```bash
# Clear APT cache (Debian/Ubuntu)
sudo apt clean
sudo apt autoclean

# Clear DNF cache (Fedora/RHEL)
sudo dnf clean all
```

**3. Old kernels:**

```bash
# List installed kernels
dpkg --list | grep linux-image

# Remove old kernels (Ubuntu)
sudo apt autoremove --purge
```

**4. Temporary files:**

```bash
# Clear /tmp
sudo rm -rf /tmp/*

# Clear user cache
rm -rf ~/.cache/*
```

### Issue: Out of Inodes

**Check inode usage:**

```bash
df -i
```

**Find directories with many files:**

```bash
# Count files in directories
sudo find / -xdev -type d -exec bash -c 'echo "$(find "$1" -maxdepth 1 | wc -l) $1"' _ {} \; 2>/dev/null | sort -rn | head -20
```

**Common causes:**
- Many small cache files
- Mail spool with many messages
- Session files

**Solution:**

```bash
# Clear PHP sessions
sudo rm -rf /var/lib/php/sessions/*

# Clear old mail
sudo rm -rf /var/mail/*
```

### Issue: Permission Denied

**Check file permissions:**

```bash
ls -l filename
```

**Check ownership:**

```bash
stat filename
```

**Fix ownership:**

```bash
# Change owner
sudo chown user:group filename

# Recursive
sudo chown -R user:group directory/
```

**Fix permissions:**

```bash
# Files
sudo chmod 644 filename

# Directories
sudo chmod 755 directory/
```

**Fix home directory permissions:**

```bash
sudo chmod 700 /home/username
sudo chown -R username:username /home/username
```

### Issue: Command Not Found

**Check if installed:**

```bash
which command
whereis command
```

**Check PATH:**

```bash
echo $PATH
```

**Fix PATH:**

```bash
# Temporary
export PATH=$PATH:/usr/local/bin

# Permanent (add to ~/.bashrc)
echo 'export PATH=$PATH:/usr/local/bin' >> ~/.bashrc
source ~/.bashrc
```

**Install package:**

```bash
# Find package providing command
apt-file search command

# Install
sudo apt install package-name
```

---

## 13. Security Considerations

### Filesystem Permissions

**Critical file permissions:**

```bash
# /etc/passwd - readable by all
ls -l /etc/passwd
# -rw-r--r-- 1 root root

# /etc/shadow - readable only by root
ls -l /etc/shadow
# -rw-r----- 1 root shadow

# /etc/sudoers - very restricted
ls -l /etc/sudoers
# -r--r----- 1 root root
```

**Home directory security:**

```bash
# Secure home
chmod 700 /home/username

# Allow group read (for web server, etc.)
chmod 750 /home/username
```

**SSH key permissions:**

```bash
# Private key must be restricted
chmod 600 ~/.ssh/id_rsa

# Public key
chmod 644 ~/.ssh/id_rsa.pub

# .ssh directory
chmod 700 ~/.ssh
```

### SUID/SGID Files

**Find SUID files:**

```bash
find / -type f -perm -4000 -ls 2>/dev/null
```

**Find SGID files:**

```bash
find / -type f -perm -2000 -ls 2>/dev/null
```

**Audit regularly:**
- Ensure only necessary files have SUID/SGID
- Unknown SUID files may indicate compromise

### World-Writable Directories

**Find world-writable directories:**

```bash
find / -type d -perm -0002 -ls 2>/dev/null
```

**Ensure sticky bit on world-writable:**

```bash
# /tmp should be
drwxrwxrwt
```

### Immutable Files

**Make file immutable:**

```bash
# Set immutable attribute
sudo chattr +i /etc/important-file

# Even root cannot modify/delete
sudo rm /etc/important-file
# Operation not permitted
```

**Remove immutable:**

```bash
sudo chattr -i /etc/important-file
```

**View attributes:**

```bash
lsattr /etc/important-file
```

---

## 14. Best Practices

### For System Administrators

**1. Separate partitions:**

```bash
/boot       # 512MB
/           # 20-50GB
/home       # Remaining space
swap        # 1-2x RAM (or more)
```

**Benefits:**
- Easier backups
- Better quota management
- Filesystem corruption isolation

**2. Regular backups:**

```bash
# Daily: /home
# Weekly: /etc
# Monthly: Full system
```

**3. Monitor disk usage:**

```bash
# Set up monitoring
df -h | mail -s "Disk Usage" admin@example.com

# Cron job
0 6 * * * df -h > /var/log/disk-usage.log
```

**4. Log rotation:**

```bash
# Ensure logrotate is configured
cat /etc/logrotate.conf
ls /etc/logrotate.d/
```

**5. Keep /usr read-only:**

```bash
# In /etc/fstab
/dev/sda2  /usr  ext4  ro  0  1
```

Remount read-write for updates:

```bash
sudo mount -o remount,rw /usr
# Update
sudo mount -o remount,ro /usr
```

### For Developers

**1. Use /usr/local for manual installs:**

```bash
./configure --prefix=/usr/local
make
sudo make install
```

**2. Follow XDG Base Directory:**

```bash
# Config
~/.config/appname/

# Data
~/.local/share/appname/

# Cache
~/.cache/appname/

# Runtime
$XDG_RUNTIME_DIR/appname/
```

**3. Respect filesystem hierarchy:**

- Binaries → `/usr/local/bin`
- Libraries → `/usr/local/lib`
- Docs → `/usr/local/share/doc`
- Configs → `/etc` or `~/.config`

**4. Use standard paths:**

```bash
# Good
/var/log/appname/
/etc/appname/
/usr/local/bin/appname

# Bad
/app/logs/
/app/config/
```

### For Users

**1. Keep home directory organized:**

```bash
~/Documents/
~/Downloads/
~/Pictures/
~/projects/
~/bin/          # Personal scripts
```

**2. Use hidden directories for config:**

```bash
~/.config/
~/.local/
~/.ssh/
```

**3. Regular cleanup:**

```bash
# Clear downloads
rm -rf ~/Downloads/*

# Clear cache
rm -rf ~/.cache/*

# Clear trash
rm -rf ~/.local/share/Trash/*
```

**4. Backup important data:**

```bash
# User data to backup
~/Documents/
~/Pictures/
~/.ssh/
~/.config/
```

---

## 15. Quick Reference Tables

### Essential Directories

| Directory    | Purpose                  | Type          |
| ------------ | ------------------------ | ------------- |
| `/`          | Root directory           | Filesystem    |
| `/bin`       | Essential user binaries  | Binaries      |
| `/boot`      | Boot loader files        | Boot          |
| `/dev`       | Device files             | Virtual       |
| `/etc`       | Configuration files      | Config        |
| `/home`      | User home directories    | User data     |
| `/lib`       | Essential libraries      | Libraries     |
| `/media`     | Removable media          | Mount point   |
| `/mnt`       | Temporary mounts         | Mount point   |
| `/opt`       | Optional software        | Applications  |
| `/proc`      | Process information      | Virtual       |
| `/root`      | Root user home           | User data     |
| `/run`       | Runtime data             | Temporary     |
| `/sbin`      | System binaries          | Binaries      |
| `/srv`       | Service data             | Service data  |
| `/sys`       | System information       | Virtual       |
| `/tmp`       | Temporary files          | Temporary     |
| `/usr`       | User system resources    | System files  |
| `/var`       | Variable data            | Variable data |

### File Types

| Symbol | Type                |
| ------ | ------------------- |
| `-`    | Regular file        |
| `d`    | Directory           |
| `l`    | Symbolic link       |
| `b`    | Block device        |
| `c`    | Character device    |
| `p`    | Named pipe (FIFO)   |
| `s`    | Socket              |

### Permission Numbers

| Number | Binary | Permission |
| ------ | ------ | ---------- |
| 0      | 000    | ---        |
| 1      | 001    | --x        |
| 2      | 010    | -w-        |
| 3      | 011    | -wx        |
| 4      | 100    | r--        |
| 5      | 101    | r-x        |
| 6      | 110    | rw-        |
| 7      | 111    | rwx        |

### Common Commands

| Task                  | Command                          |
| --------------------- | -------------------------------- |
| List directory        | `ls -la`                         |
| Change directory      | `cd /path`                       |
| Print working dir     | `pwd`                            |
| Disk usage            | `df -h`                          |
| Directory size        | `du -sh /path`                   |
| Find file             | `find / -name filename`          |
| Search in files       | `grep -r "text" /path`           |
| File type             | `file filename`                  |
| File permissions      | `ls -l filename`                 |
| Change permissions    | `chmod 755 file`                 |
| Change owner          | `chown user:group file`          |
| Create link           | `ln -s target link`              |
| Mount filesystem      | `mount /dev/sda1 /mnt`           |
| Unmount filesystem    | `umount /mnt`                    |
| Check filesystem      | `fsck /dev/sda1`                 |

---

## 16. Conclusion

The Linux filesystem hierarchy is a well-organized, logical structure designed for:
- **Consistency** across distributions
- **Security** through proper separation
- **Flexibility** for various use cases
- **Maintainability** for system administrators

**Key takeaways:**

1. Everything starts from `/` (root directory)
2. `/usr` contains most programs and libraries
3. `/etc` holds all configuration
4. `/home` is for user data
5. `/var` is for changing data (logs, caches)
6. Virtual filesystems (`/proc`, `/sys`, `/dev`) provide kernel interfaces
7. The usr merge simplified modern systems
8. Understanding the filesystem is essential for effective Linux use

**Continue learning:**
- Experiment in a virtual machine
- Read `man hier` for hierarchy documentation
- Explore files and directories yourself
- Practice with the examples provided

**Remember:** Linux gives you complete control over your system. With that power comes responsibility to understand what you're doing and make informed decisions.

---

**End of Complete Guide**

This guide covers the Linux filesystem hierarchy comprehensively, from basic concepts to advanced topics, suitable for learners at all levels.
