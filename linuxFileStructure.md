# Linux Filesystem Structure (FHS) – Complete Guide

This document explains the **Linux filesystem hierarchy**, its directories, and their purposes according to the **Filesystem Hierarchy Standard (FHS)**. It is written for beginners but detailed enough for intermediate users.

---

## 1. Root Directory (`/`)

The **root** is the top-level directory of the Linux filesystem. Every file and folder starts from here.

```text
/
```

Think of `/` as the trunk of a tree.

---

## 2. `/bin` – Essential User Binaries

* Contains **essential command binaries**
* Required for system boot and single-user mode
* Commands usable by all users

Examples:

* `ls`
* `cp`
* `mv`
* `cat`
* `bash`

📌 On modern systems:

```text
/bin -> /usr/bin
```

---

## 3. `/sbin` – System Binaries

* Essential **system administration** commands
* Mostly used by root

Examples:

* `fsck`
* `reboot`
* `ip`
* `mount`

📌 Modern systems:

```text
/sbin -> /usr/sbin
```

---

## 4. `/usr` – User System Resources (Main OS)

Despite the name, `/usr` does **not** mean user home.

It contains the **main operating system software**.

### `/usr/bin`

* Most user commands
* Non-essential for early boot

Examples:

* `git`
* `python`
* `node`
* `vim`

### `/usr/sbin`

* Admin and service binaries

Examples:

* `nginx`
* `sshd`
* `cron`

### `/usr/lib`

* Libraries required by `/usr/bin`

### `/usr/share`

* Architecture-independent data
* Docs, icons, fonts, locales

---

## 5. `/usr/local` – Locally Installed Software

* Software installed **manually by the user/admin**
* Not managed by system package manager
* Safe from OS upgrades

Structure mirrors `/usr`:

```text
/usr/local/bin
/usr/local/lib
/usr/local/share
```

Common use cases:

* Custom scripts
* Manually installed Node, Go, CLI tools

---

## 6. `/lib` and `/lib64` – Essential Libraries

* Shared libraries for `/bin` and `/sbin`
* Required for system boot

Examples:

* `ld-linux.so`
* `libc.so`

📌 Modern systems:

```text
/lib -> /usr/lib
```

---

## 7. `/etc` – Configuration Files

* System-wide configuration
* Text-based files
* No binaries

Examples:

* `/etc/passwd`
* `/etc/fstab`
* `/etc/hosts`
* `/etc/nginx/nginx.conf`

📌 Rule:

> Never put executable programs in `/etc`

---

## 8. `/home` – User Home Directories

* Personal data for users

```text
/home/ajay
/home/user2
```

Contains:

* Documents
* Downloads
* Configs (`.bashrc`, `.profile`)

---

## 9. `/root` – Root User Home

* Home directory of the **root user**
* Separate from `/home`

Reason:

* `/home` may be on another partition
* Root must always be accessible

---

## 10. `/var` – Variable Data

* Files that **change frequently**

Examples:

* Logs: `/var/log`
* Cache: `/var/cache`
* Mail: `/var/mail`
* Spool: `/var/spool`

Common files:

```text
/var/log/syslog
/var/log/auth.log
```

---

## 11. `/tmp` – Temporary Files

* Temporary data
* Deleted on reboot (usually)
* Writable by all users

Permissions:

```text
drwxrwxrwt
```

The `t` = sticky bit

---

## 12. `/dev` – Device Files

* Represents hardware as files

Examples:

* `/dev/sda` → disk
* `/dev/null`
* `/dev/tty`
* `/dev/random`

Managed dynamically by `udev`

---

## 13. `/proc` – Process Information (Virtual FS)

* Not real files
* Kernel-generated

Examples:

* `/proc/cpuinfo`
* `/proc/meminfo`
* `/proc/uptime`

Used for system monitoring

---

## 14. `/sys` – Kernel & Hardware Info

* Interface to kernel objects
* Used by system tools

Examples:

* Power management
* Device info

---

## 15. `/boot` – Bootloader Files

* Required to boot Linux

Contains:

* `vmlinuz`
* `initramfs`
* `grub/`

⚠️ Do not modify casually

---

## 16. `/media` – Removable Media

* Auto-mounted removable devices

Examples:

* USB drives
* External HDDs

---

## 17. `/mnt` – Temporary Mounts

* Manual mounts
* Admin use

Example:

```bash
mount /dev/sdb1 /mnt
```

---

## 18. `/opt` – Optional Software

* Large third-party software

Examples:

* Google Chrome
* Oracle
* Proprietary apps

Structure:

```text
/opt/appname/
```

---

## 19. `/run` – Runtime Data

* Temporary runtime files
* Replaces `/var/run`

Examples:

* PID files
* Sockets

---

## 20. Special Mentions

### Symlinks

```text
/bin  -> /usr/bin
/sbin -> /usr/sbin
/lib  -> /usr/lib
```

### PATH Resolution

Order matters:

```bash
echo $PATH
```

---

## 21. One-Line Summary

| Directory | Purpose              |
| --------- | -------------------- |
| `/`       | Root                 |
| `/bin`    | Essential commands   |
| `/usr`    | Main OS              |
| `/etc`    | Config               |
| `/home`   | User data            |
| `/var`    | Logs & variable data |
| `/dev`    | Devices              |
| `/proc`   | Process info         |
| `/tmp`    | Temporary files      |

---

## 22. Real Questions You Asked (Answered Inside the Filesystem)

This section directly answers **common real-world questions** you asked while learning Linux.

---

### ❓ Why do `/bin` and `/usr/bin` have the same commands?

**Real reason:** historical separation + modern `/usr` merge.

Example:

```bash
ls -l /bin/ls
ls -l /usr/bin/ls
```

Output (simplified):

```text
/bin/ls -> /usr/bin/ls
```

✔ Same binary
✔ Same inode
✔ `/bin` exists only for compatibility

---

### ❓ Does each command exist twice?

❌ No.

There is **only one real binary**:

```text
/usr/bin/ls
```

Everything else is a symlink.

Check:

```bash
stat /bin/ls
stat /usr/bin/ls
```

---

### ❓ Why is `/bin/sh` still special?

* POSIX standard requires `/bin/sh`
* Boot scripts depend on it
* Many installers hardcode it

On Ubuntu:

```bash
/bin/sh -> dash
```

---

### ❓ What breaks if `/usr` is missing?

System will:

* Fail to boot fully
* Drop to emergency shell
* Lose most commands

That is why early boot loads `/usr` first now.

---

## 23. Real-Life Examples (Hands-On)

---

### Example 1: Finding where a command really lives

```bash
which python
whereis python
readlink -f $(which python)
```

Shows:

* PATH resolution
* Actual binary location

---

### Example 2: Custom command overriding system command

```bash
sudo nano /usr/local/bin/hello
```

```bash
#!/bin/bash
echo "Hello from local bin"
```

```bash
chmod +x /usr/local/bin/hello
hello
```

Why it works:

* `/usr/local/bin` appears before `/usr/bin` in `$PATH`

---

### Example 3: Logs growing in `/var`

```bash
ls -lh /var/log
sudo tail -f /var/log/syslog
```

Why logs live here:

* `/var` = variable, growing data

---

### Example 4: Understanding `/proc`

```bash
cat /proc/cpuinfo
cat /proc/meminfo
ls /proc/$$
```

✔ Not real files
✔ Generated by kernel

---

### Example 5: Mounting a USB drive

```bash
lsblk
sudo mount /dev/sdb1 /mnt
```

Temporary mount → `/mnt`
Auto mount → `/media/username`

---

### Example 6: Why root has `/root`

If `/home` is on another disk:

```bash
mount | grep home
```

Root login still works because `/root` is always available.

---

## 24. Common Interview / Practical Questions

---

### Q: Difference between `/opt` and `/usr/local`?

| `/opt`                 | `/usr/local`        |
| ---------------------- | ------------------- |
| Large third-party apps | Locally built tools |
| Vendor-managed         | Admin-managed       |

---

### Q: Can I delete `/tmp`?

✔ Yes (usually safe)
❌ Not `/var/tmp`

---

### Q: Why is everything a file?

* Uniform interface
* Easier scripting
* Device + process control

Example:

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

---

## 25. Final Mental Model

```
Hardware
  ↓
Kernel (/proc, /sys)
  ↓
System binaries (/usr/bin)
  ↓
Configs (/etc)
  ↓
User data (/home)
```

> Linux is simple because it is consistent.

---

✅ End of Document
