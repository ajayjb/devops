
# Linux Users, Groups, and Permissions - Complete Guide for Beginners

## Table of Contents
1. [What Are Users and Groups?](#what-are-users-and-groups)
2. [Why Do We Need Users and Groups?](#why-do-we-need-users-and-groups)
3. [Important Files](#important-files)
4. [User Management - Step by Step](#user-management---step-by-step)
5. [Group Management - Step by Step](#group-management---step-by-step)
6. [Command Comparisons - Which One to Use?](#command-comparisons---which-one-to-use)
7. [Understanding File Ownership](#understanding-file-ownership)
8. [Understanding File Permissions](#understanding-file-permissions)
9. [Changing Ownership with chown and chgrp](#changing-ownership-with-chown-and-chgrp)
10. [Changing Permissions with chmod](#changing-permissions-with-chmod)
11. [Real-World Examples](#real-world-examples)
12. [Troubleshooting](#troubleshooting)

---

## What Are Users and Groups?

### What is a User?

A **user** is a person (or program) who can log into your Linux computer. Think of it like a login account on your computer.

**Example**: You might have users named:
- `john` (your friend)
- `alice` (another friend)
- `www-data` (a program that runs web servers)
- `mysql` (a program that runs databases)

Each user has:
- A **username** (the login name like "john")
- A **UID** (User ID - a number the system uses, like 1000)
- A **home directory** (their personal folder, like /home/john)
- A **shell** (the program they use to interact with Linux, like /bin/bash)

### What is a Group?

A **group** is a collection of users. Think of it like a team or club.

**Example**: You might have groups named:
- `developers` - contains john, alice, bob (all developers)
- `sudo` - contains users who can use the "sudo" command (admin command)
- `docker` - contains users who can use Docker

**Why groups exist**:
- Instead of giving permission to each individual user, you can give permission to a group
- All users in that group automatically get that permission
- Much easier to manage!

### Types of Users

**1. System Users** (UID 0-999):
- Used by programs and services
- Examples: `root` (administrator), `www-data` (web server), `mysql` (database)
- Don't have real people logging in with them

**2. Regular Users** (UID 1000+):
- Used by real people
- Examples: `john`, `alice`, `bob`
- Can log in and run programs

---

## Why Do We Need Users and Groups?

### Security

**Imagine your computer is like a house:**
- If everyone has the same key, anyone can go into anyone's room
- With users and groups, each person has their own lock

**On Linux:**
- User `john` can have private files that user `alice` cannot see
- User `alice` cannot accidentally delete user `john`'s important work files
- If one program gets hacked, it can only damage files owned by that program's user

### Organization

**Without users/groups:**
- All files belong to everyone
- Anyone can delete anything
- Mistakes affect everyone

**With users/groups:**
- `john` owns his files
- `alice` owns her files
- A program can only access files it needs
- Mistakes are contained

### Examples

```
Scenario 1: Without users (BAD)
All users can read/write/delete everything
-> One user accidentally deletes all company data!

Scenario 2: With users (GOOD)
john's files: john can read/write, others can only read
alice's files: alice can read/write, others cannot see them
System files: only root can modify
-> john can't accidentally break alice's work
-> Mistakes are limited to one user's area
```

---

## Important Files

When you work with users and groups, Linux stores information in these files:

### 1. `/etc/passwd` - User Accounts

This file stores information about every user on the system.

**Format:**
```
username:password_marker:UID:GID:full_name:home_directory:shell
```

**Example line:**
```
john:x:1000:1000:John Smith:/home/john:/bin/bash
```

**Explanation:**
- `john` = the username
- `x` = password is stored elsewhere (in /etc/shadow)
- `1000` = User ID (the system uses this number instead of the name)
- `1000` = Group ID (john's primary group ID)
- `John Smith` = full name of the person
- `/home/john` = home directory (where user's files are stored)
- `/bin/bash` = the shell (the program used to type commands)

**View this file:**
```bash
cat /etc/passwd
# Shows all users on system
```

### 2. `/etc/shadow` - Encrypted Passwords

This file stores encrypted passwords. Only root can read this file (for security).

**Why it's separate?**
- It's very sensitive information
- Only the root user should be able to read it
- Regular users cannot see password hashes

**View this file (requires root):**
```bash
sudo cat /etc/shadow
# Only works if you use sudo
```

### 3. `/etc/group` - Groups

This file stores information about every group on the system.

**Format:**
```
group_name:password_marker:GID:member_list
```

**Example line:**
```
developers:x:1001:john,alice,bob
```

**Explanation:**
- `developers` = group name
- `x` = group password (rarely used)
- `1001` = Group ID (the system uses this number instead of the name)
- `john,alice,bob` = list of users in this group

**View this file:**
```bash
cat /etc/group
# Shows all groups on system
```

### 4. `/etc/gshadow` - Encrypted Group Passwords

Like /etc/shadow but for groups. Rarely used in modern Linux.

---

## User Management - Step by Step

### Command 1: Creating Users with `useradd`

**What does it do?**
`useradd` creates a new user account on the system. It's like adding a new person to your computer's list of allowed users.

**Basic syntax:**
```bash
sudo useradd [OPTIONS] username
```

**Why `sudo`?**
Only the root user (administrator) can create new users. `sudo` means "run this command as root". You'll need to enter your password.

---

### Example 1: Create a Simple User

**Command:**
```bash
sudo useradd john
```

**What it does:**
- Creates a user named `john`
- Does NOT create a home directory (be careful!)
- Does NOT set a password yet

**Check if it worked:**
```bash
id john
```

**Output might be:**
```
uid=1001(john) gid=1001(john) groups=1001(john)
```

**Explanation of output:**
- `uid=1001(john)` = John's user ID is 1001, username is john
- `gid=1001(john)` = John's primary group ID is 1001 (automatically created)
- `groups=1001(john)` = John is in group 1001 (named john)

---

### Example 2: Create a User WITH Home Directory

**Command:**
```bash
sudo useradd -m alice
```

**What the `-m` flag does:**
- `-m` means "create home directory"
- Creates `/home/alice` folder automatically
- Alice can store her files there

**Check it worked:**
```bash
ls -la /home/alice
```

**Output:**
```
total 8
drwxr-xr-x 2 alice alice 4096 Dec 10 10:30 .
drwxr-xr-x 3 root  root  4096 Dec 10 10:25 ..
```

**Explanation:**
- `drwxr-xr-x` = directory with specific permissions (we'll learn this later)
- `alice alice` = owned by user alice, group alice
- The directory exists and is ready to use!

---

### Example 3: Create User with Specific Shell

**Command:**
```bash
sudo useradd -m -s /bin/bash bob
```

**What the `-s` flag does:**
- `-s` means "set shell"
- `/bin/bash` is the shell program (bash is very common)
- The shell is what appears when you log in (the $ prompt)

**Different shells:**
```
/bin/bash - Very common, powerful (like Windows Command Prompt)
/bin/zsh - Modern, user-friendly
/bin/sh - Basic, minimal
/bin/false - No shell (for programs, not people)
```

**Why would you use /bin/false?**
You create accounts for programs (like web servers) that should NOT allow real login.

---

### Example 4: Create User with Specific Home Directory

**Command:**
```bash
sudo useradd -m -d /opt/myapp appuser
```

**What the `-d` flag does:**
- `-d` means "set home directory"
- `/opt/myapp` is where this user's files will be
- Instead of the default `/home/appuser`

**When would you use this?**
For programs/services that need a special location for their files.

---

### Example 5: Create System User (for programs)

**Command:**
```bash
sudo useradd -r -s /bin/false www-data
```

**What the `-r` flag does:**
- `-r` means "create system user"
- User ID will be in range 0-999 (not 1000+)
- These are for programs, not people

**Full command explanation:**
- `-r` = system user
- `-s /bin/false` = no shell (can't log in)
- `www-data` = user runs the web server program

**Why -s /bin/false?**
- This prevents anyone from logging in as www-data
- The program runs with this user account for security
- If program gets hacked, hacker can only use www-data's permissions

---

### Command 2: Listing Users

**View all users:**
```bash
cat /etc/passwd
```

**Output looks like:**
```
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
john:x:1000:1000::/home/john:/bin/bash
alice:x:1001:1001::/home/alice:/bin/bash
bob:x:1002:1002::/home/bob:/bin/bash
```

**See only human users (UID 1000+):**
```bash
awk -F: '$3 >= 1000 {print $1}' /etc/passwd
```

**Output:**
```
john
alice
bob
```

**Explanation of this command:**
- `awk` = a text processing tool
- `-F:` = use colon as separator (because /etc/passwd uses colons)
- `$3 >= 1000` = only lines where 3rd field is 1000 or bigger
- `{print $1}` = print the 1st field (the username)

---

### Command 3: Get Info About a User

**Command:**
```bash
id john
```

**Output:**
```
uid=1000(john) gid=1000(john) groups=1000(john),4(adm),27(sudo),1001(developers)
```

**Explanation:**
- `uid=1000(john)` = User ID 1000, username john
- `gid=1000(john)` = Primary group ID 1000, group name john
- `groups=1000(john),4(adm),27(sudo),1001(developers)` = All groups john belongs to:
  - 1000(john) - his primary group
  - 4(adm) - administrative group
  - 27(sudo) - can use sudo command
  - 1001(developers) - developer group

---

### Command 4: Modifying Users with `usermod`

**What does it do?**
`usermod` modifies an existing user account. It changes things about the user.

**Basic syntax:**
```bash
sudo usermod [OPTIONS] username
```

---

### Example 1: Add User to a Group

**Command:**
```bash
sudo usermod -aG docker john
```

**What each part means:**
- `usermod` = modify user command
- `-a` = "append" (add without removing existing groups)
- `-G` = supplementary groups (additional groups)
- `docker` = group name to add to
- `john` = which user to modify

**Why -a is important:**
```
WITHOUT -a (WRONG):
sudo usermod -G docker john
-> john is ONLY in docker group now
-> john loses sudo and developers groups!

WITH -a (CORRECT):
sudo usermod -aG docker john
-> john is added to docker group
-> john keeps all other groups too!
```

**Check it worked:**
```bash
groups john
```

**Output:**
```
john : john docker developers sudo
```

---

### Example 2: Add User to Multiple Groups

**Command:**
```bash
sudo usermod -aG sudo,docker,developers,wheel john
```

**What it does:**
- Adds john to 4 groups at once: sudo, docker, developers, wheel

**Check it:**
```bash
id john
```

---

### Example 3: Change User's Shell

**Command:**
```bash
sudo usermod -s /bin/zsh alice
```

**What the `-s` flag does:**
- Changes alice's shell from /bin/bash to /bin/zsh
- Next time alice logs in, she'll get zsh instead of bash

**Verify:**
```bash
grep alice /etc/passwd
```

**Output:**
```
alice:x:1001:1001::/home/alice:/bin/zsh
```

---

### Example 4: Change User's Home Directory

**Command:**
```bash
sudo usermod -d /home/newhome alice
```

**What it does:**
- Changes alice's home directory from /home/alice to /home/newhome
- But DOESN'T move her files!

**To move files too:**
```bash
sudo usermod -m -d /home/newhome alice
```

**What the `-m` flag does:**
- `-m` = "move home directory contents"
- Moves all of alice's files from /home/alice to /home/newhome

---

### Example 5: Lock a User Account

**Command:**
```bash
sudo usermod -L john
```

**What it does:**
- Locks john's account
- john cannot log in anymore
- john's files still exist
- Useful when someone leaves the company

**Check if locked:**
```bash
sudo passwd -S john
```

**Output will show:**
```
john LK 2024-12-10 0 99999 7 -1
   ^
   LK = Locked!
```

**To unlock:**
```bash
sudo usermod -U john
```

---

### Command 5: Set Password with `passwd`

**What does it do?**
`passwd` sets or changes a user's password.

**Change your own password:**
```bash
passwd
```

**Steps:**
1. It asks for your current password (to verify it's you)
2. It asks for new password
3. It asks to confirm new password

**Change another user's password (as root):**
```bash
sudo passwd john
```

**What it does:**
- Sets a new password for john
- john doesn't need to type the old password
- john will need to use the new password to log in

**Expire password (force change on next login):**
```bash
sudo passwd -e john
```

**What it does:**
- john's password is now "expired"
- john MUST change password at next login
- Useful for new employee onboarding

---

### Command 5: Create Users with `adduser` (Interactive Method)

**What does it do?**
`adduser` is a user-friendly version of `useradd`. It asks you questions instead of requiring flags.

**Note:** `adduser` is available on Debian/Ubuntu systems. On Red Hat/CentOS, use `useradd`.

**Basic command:**
```bash
sudo adduser alice
```

**What happens:**
The system prompts you interactively:

```
Adding user 'alice' ...
Adding new group 'alice' (1001) ...
Adding new user 'alice' (1001) with group 'alice' ...
Creating home directory '/home/alice' ...
Copying files from '/etc/skel' ...
New password:                          # You type password here
Retype new password:                   # Confirm password
passwd: password updated successfully
Changing the user information for alice
Enter the new value, or press ENTER for the default
	Full Name []: Alice Smith         # Full name (optional)
	Room Number []:                    # Room number (optional)
	Work Phone []:                     # Work phone (optional)
	Home Phone []:                     # Home phone (optional)
	Other []:                          # Other info (optional)
Is the information correct? [Y/n] y   # Confirm all info
```

**What it creates:**
- User account named alice
- Home directory `/home/alice` (automatically)
- Group named alice (automatically)
- Sets password immediately
- Copies skeleton files from `/etc/skel`

**Step-by-step example:**

```bash
# 1. Run the command
sudo adduser alice

# 2. System asks for password
# You type it (characters won't show for security)
New password: (you type password)
Retype new password: (you type again)

# 3. System asks for user information
Full Name []: Alice Smith
Room Number []:
Work Phone []:
Home Phone []:
Other []:

# 4. System confirms
Is the information correct? [Y/n] y

# Done! alice account is created and ready
```

**What was created:**
```bash
# Check user exists
id alice
# uid=1001(alice) gid=1001(alice) groups=1001(alice)

# Check home directory
ls -ld /home/alice
# drwxr-xr-x alice alice

# Check password is set
sudo passwd -S alice
# alice P 2024-12-10 0 99999 7 -1
#        P = Password is set
```

**Comparison: adduser vs useradd**

```bash
# useradd (low-level, requires flags)
sudo useradd -m -s /bin/bash alice
sudo passwd alice
# More steps, but more control

# adduser (high-level, interactive)
sudo adduser alice
# Fewer steps, asks you questions

# Both create the same result:
# - User alice
# - Home directory /home/alice
# - Group alice
# - Password set
```

**When to use adduser:**
- When you want interactive prompts
- When you want full information collected
- When you're creating accounts manually
- When you're on Ubuntu/Debian
- Easier for beginners

**When to use useradd:**
- When you're writing scripts (adduser is interactive, won't work)
- When you want to specify exact flags
- When you need non-interactive creation
- When you're on Red Hat/CentOS
- More powerful and flexible

---

### Command 6: Delete User with `userdel`

**What does it do?**
`userdel` removes a user account from the system. This is the low-level command.

**Keep home directory:**
```bash
sudo userdel john
```

**What it does:**
- Removes john from /etc/passwd
- john cannot log in anymore
- john's home directory `/home/john` still exists
- Useful if you want to archive the files later

**Delete home directory too:**
```bash
sudo userdel -r john
```

**What the `-r` flag does:**
- `-r` means "remove everything"
- Deletes /home/john and all files in it
- Removes john's mail spool
- Use when you're sure you don't need john's files

**Example:**

```bash
# 1. Check user exists
id john
# uid=1000(john) gid=1000(john) groups=1000(john)

# 2. Remove user (keep home)
sudo userdel john

# 3. Check user is gone
id john
# id: 'john': no such user

# 4. Home directory still exists
ls -ld /home/john
# drwxr-xr-x john john (still here!)

# 5. Later, remove home directory
sudo rm -rf /home/john
```

**Or delete with home directory in one command:**

```bash
# Delete user AND home directory
sudo userdel -r john

# Verify everything is gone
ls -ld /home/john
# ls: cannot access '/home/john': No such file or directory (gone!)
```

---

### Command 7: Delete User with `deluser` (Safer Alternative)

**What does it do?**
`deluser` is a user-friendly, safer version of `userdel`. It has more safety checks and warnings.

**Note:** `deluser` is available on Debian/Ubuntu systems. On Red Hat/CentOS, use `userdel`.

**Remove user (keep home directory):**
```bash
sudo deluser alice
```

**Output:**
```
Removing user 'alice' ...
Warning: group 'alice' has no other members and will be removed.
Done.
```

**What it does:**
- Removes alice from /etc/passwd
- Removes alice's group (if no other members)
- alice's home directory `/home/alice` still exists
- Gives you warnings before deleting
- Safer than userdel (checks for problems)

**Remove user and home directory:**
```bash
sudo deluser --remove-home alice
```

**What the `--remove-home` flag does:**
- Removes alice completely
- Deletes /home/alice
- Deletes all files inside
- Removes alice's group
- Safer than `userdel -r`

**Remove user, home, and all files:**
```bash
sudo deluser --remove-all-files alice
```

**What the `--remove-all-files` flag does:**
- Removes alice completely
- Deletes home directory
- Deletes mail spool
- Removes ALL alice's files system-wide
- Most thorough and dangerous option

**Example step-by-step:**

```bash
# 1. Check user exists
id alice
# uid=1001(alice) gid=1001(alice) groups=1001(alice)

# 2. Remove user safely
sudo deluser alice

# Output shows what it's doing:
# Removing user 'alice' ...
# Warning: group 'alice' has no other members and will be removed.
# Done.

# 3. Verify alice is gone
id alice
# id: 'alice': no such user

# 4. Home directory still exists
ls -ld /home/alice
# drwxr-xr-x 1001 1001 (still exists!)

# 5. If you want to remove home too, use flag:
sudo deluser --remove-home alice
```

**Comparison: deluser vs userdel**

```bash
# userdel (low-level, less safety)
sudo userdel -r john
# Removes user immediately
# No warnings
# Just gone!

# deluser (high-level, safer)
sudo deluser --remove-home john
# Shows warnings
# Checks for problems
# Asks for confirmation (sometimes)
# More careful

# Both do the same thing, but deluser is safer and friendlier
```

**When to use deluser:**
- When you want safety checks
- When you want warnings
- When you're removing accounts manually
- On Debian/Ubuntu systems
- When you're not sure about something
- Easier for beginners

**When to use userdel:**
- When you're writing scripts
- When you need exact control
- When you need non-interactive operation
- When you need all the flags
- More powerful options available

---

## Group Management - Step by Step

### Command 1: Creating Groups with `groupadd`

**What does it do?**
`groupadd` creates a new group on the system. This is the low-level command.

**Basic syntax:**
```bash
sudo groupadd groupname
```

---

### Example 1: Create a Simple Group

**Command:**
```bash
sudo groupadd developers
```

**What it does:**
- Creates a new group called "developers"
- Automatically assigns a GID (Group ID) - probably 1001
- Group is now ready to add users to it

**Verify:**
```bash
cat /etc/group | grep developers
```

**Output:**
```
developers:x:1001:
```

**Explanation:**
- `developers` = group name
- `x` = group password (not used)
- `1001` = Group ID
- (empty) = no members yet

---

### Example 2: Create Group with Specific GID

**Command:**
```bash
sudo groupadd -g 3000 managers
```

**What the `-g` flag does:**
- `-g` means "specify GID"
- Creates group with ID 3000 instead of auto-assignment
- Useful when you want specific IDs for scripting

**When would you use this?**
When you're migrating systems and need exact same GIDs

---

### Example 3: Create System Group

**Command:**
```bash
sudo groupadd -r nginx-users
```

**What the `-r` flag does:**
- `-r` = system group
- GID will be in range 0-999 (for system services)
- Similar to system users

---

### Command 1b: Create Groups with `addgroup` (Interactive Method)

**What does it do?**
`addgroup` is a user-friendly version of `groupadd`. It's simpler and more interactive.

**Note:** `addgroup` is available on Debian/Ubuntu systems. On Red Hat/CentOS, use `groupadd`.

**Basic command:**
```bash
sudo addgroup developers
```

**What happens:**
Creates a group called "developers" with an auto-assigned GID.

**Output:**
```
Adding group 'developers' (GID 1001) ...
Done.
```

**With specific GID:**
```bash
sudo addgroup --gid 3000 managers
```

**What the `--gid` flag does:**
- Creates group with GID 3000
- Same as `groupadd -g 3000 managers`

**As system group:**
```bash
sudo addgroup --system nginx-users
```

**What the `--system` flag does:**
- Creates system group
- GID in range 0-999
- Same as `groupadd -r nginx-users`

**Comparison: addgroup vs groupadd**

```bash
# groupadd (low-level)
sudo groupadd developers
sudo groupadd -g 3000 managers
sudo groupadd -r nginx-users
# More flags, requires exact syntax

# addgroup (high-level, easier)
sudo addgroup developers
sudo addgroup --gid 3000 managers
sudo addgroup --system nginx-users
# Simpler, more readable flags

# Both create the same result
```

**When to use addgroup:**
- When you want simpler syntax
- When you're on Ubuntu/Debian
- When you're creating groups manually
- Easier for beginners

**When to use groupadd:**
- When you're writing scripts
- When you need exact control
- When you're on Red Hat/CentOS
- More portable across Linux systems

---

### Command 2: Listing Groups

**View all groups:**
```bash
cat /etc/group
```

**Output:**
```
root:x:0:
wheel:x:10:
sudo:x:27:john,alice
developers:x:1001:john,bob,charlie
docker:x:999:john,alice
```

**See only human groups (GID 1000+):**
```bash
awk -F: '$3 >= 1000 {print $1}' /etc/group
```

**Output:**
```
developers
docker
```

---

### Command 3: See Groups a User Belongs To

**Command:**
```bash
groups john
```

**Output:**
```
john : john sudo developers docker
```

**Explanation:**
- First "john" = primary group
- Colon separates primary from supplementary
- `john sudo developers docker` = all groups john is in

**More detailed:**
```bash
id john
```

**Output:**
```
uid=1000(john) gid=1000(john) groups=1000(john),27(sudo),1001(developers),999(docker)
```

---

### Command 4: Modifying Groups with `groupmod`

**Change group name:**
```bash
sudo groupmod -n newname developers
```

**What it does:**
- Renames "developers" group to "newname"
- All members stay the same

**Change GID:**
```bash
sudo groupmod -g 3000 developers
```

**What it does:**
- Changes developers' GID from 1001 to 3000
- All members stay the same

---

### Command 5: Delete Group with `groupdel`

**What does it do?**
`groupdel` removes a group from the system. This is the low-level command.

**Command:**
```bash
sudo groupdel developers
```

**What it does:**
- Removes the "developers" group
- Group must not be primary group of any user
- If users have this as primary group, delete them first

**Error if group in use:**
```
groupdel: cannot remove the primary group of user 'john'
```

**Example:**

```bash
# 1. Check group exists
getent group developers
# developers:x:1001:john,bob

# 2. Try to delete
sudo groupdel developers

# 3. If john has developers as primary group, get error:
# groupdel: cannot remove the primary group of user 'john'

# 4. Need to change john's primary group first
sudo usermod -g john john
# (change john's primary group to 'john' group)

# 5. Then delete group
sudo groupdel developers

# 6. Verify it's gone
getent group developers
# (returns nothing - group deleted!)
```

---

### Command 5b: Delete Group with `delgroup` (Safer Alternative)

**What does it do?**
`delgroup` is a user-friendly, safer version of `groupdel`. It has better error handling.

**Note:** `delgroup` is available on Debian/Ubuntu systems. On Red Hat/CentOS, use `groupdel`.

**Basic command:**
```bash
sudo delgroup developers
```

**Output:**
```
Removing group 'developers' ...
Done.
```

**What it does:**
- Removes the "developers" group
- Safer error checking than groupdel
- Warns if group is in use
- Checks for problems before deleting

**Error if group is primary group:**
```
delgroup: group 'developers' is the primary group of another user.
Deleting the group anyway.
```

**Example step-by-step:**

```bash
# 1. Check group exists
getent group developers
# developers:x:1001:john,bob,alice

# 2. Remove group
sudo delgroup developers

# Output:
# Removing group 'developers' ...
# Done.

# 3. Verify it's deleted
getent group developers
# (returns nothing - group deleted!)

# 4. If group is primary group, shows warning:
# delgroup: group 'developers' is the primary group of another user.
# Deleting the group anyway.
```

**With force flag (dangerous):**
```bash
sudo delgroup --force developers
```

**What `--force` does:**
- Deletes group even if it's primary group
- Doesn't ask for confirmation
- DANGEROUS - can break user accounts!
- Use only if you know what you're doing

**Comparison: delgroup vs groupdel**

```bash
# groupdel (low-level)
sudo groupdel developers
# Fails if group is primary group
# No warnings or extra checks
# Direct removal

# delgroup (high-level, safer)
sudo delgroup developers
# Better error messages
# Warns about problems
# More careful

# Both delete the group, but delgroup is safer
```

**When to use delgroup:**
- When you want safety checks
- When you want clear error messages
- When you're on Ubuntu/Debian
- When removing groups manually
- Easier for beginners

**When to use groupdel:**
- When you're writing scripts
- When you need exact control
- When you're on Red Hat/CentOS
- When you need non-interactive operation

---

## Command Comparisons - Which One to Use?

### User Commands Comparison

**Creating Users:**

```
┌─────────────┬──────────────────────────────────────────┐
│ Command     │ Best For                                  │
├─────────────┼──────────────────────────────────────────┤
│ useradd     │ Scripts, automation, exact control       │
│ adduser     │ Manual creation, interactive, beginners  │
└─────────────┴──────────────────────────────────────────┘
```

**Example situation:**
```bash
# You're writing a script to create 100 users?
# Use: useradd (automated, no prompts)

# You're manually creating one user account?
# Use: adduser (asks you questions, easier)
```

**Deleting Users:**

```
┌─────────────┬──────────────────────────────────────────┐
│ Command     │ Best For                                  │
├─────────────┼──────────────────────────────────────────┤
│ userdel     │ Scripts, automation, exact control       │
│ deluser     │ Manual removal, safer, fewer mistakes    │
└─────────────┴──────────────────────────────────────────┘
```

**Example situation:**
```bash
# You need to delete a user right now?
# Use: deluser (safer, shows warnings)

# You're writing a script to remove users?
# Use: userdel (non-interactive, works in scripts)
```

---

### Group Commands Comparison

**Creating Groups:**

```
┌─────────────┬──────────────────────────────────────────┐
│ Command     │ Best For                                  │
├─────────────┼──────────────────────────────────────────┤
│ groupadd    │ Scripts, automation, exact control       │
│ addgroup    │ Manual creation, simpler syntax          │
└─────────────┴──────────────────────────────────────────┘
```

**Example situation:**
```bash
# Creating a group in a script?
# Use: groupadd (works in automated scripts)

# Creating a group manually?
# Use: addgroup (simpler, easier to read)
```

**Deleting Groups:**

```
┌─────────────┬──────────────────────────────────────────┐
│ Command     │ Best For                                  │
├─────────────┼──────────────────────────────────────────┤
│ groupdel    │ Scripts, automation, exact control       │
│ delgroup    │ Manual removal, safer, clearer errors    │
└─────────────┴──────────────────────────────────────────┘
```

**Example situation:**
```bash
# Deleting a group you created?
# Use: delgroup (safer, better error messages)

# Removing multiple groups in a script?
# Use: groupdel (non-interactive)
```

---

### Platform Differences

**On Ubuntu/Debian:**
- `adduser` / `deluser` are preferred (cleaner)
- `groupadd` / `groupdel` still work

**On Red Hat/CentOS/Fedora:**
- Use `useradd`, `userdel`, `groupadd`, `groupdel`
- `adduser` / `deluser` may not be available

**For maximum portability (scripts):**
- Use `useradd`, `userdel`, `groupadd`, `groupdel`
- These work on all Linux systems
- Use exact flags instead of interactive mode

---

### Quick Decision Tree

```
Creating a user?
├─ Writing a script?
│  └─> Use: useradd
├─ Creating manually on Ubuntu/Debian?
│  └─> Use: adduser
└─ Creating manually on CentOS/RHEL?
   └─> Use: useradd

Deleting a user?
├─ Writing a script?
│  └─> Use: userdel
├─ Deleting manually on Ubuntu/Debian?
│  └─> Use: deluser
└─ Deleting manually on CentOS/RHEL?
   └─> Use: userdel

Creating a group?
├─ Writing a script?
│  └─> Use: groupadd
├─ Creating manually on Ubuntu/Debian?
│  └─> Use: addgroup
└─ Creating manually on CentOS/RHEL?
   └─> Use: groupadd

Deleting a group?
├─ Writing a script?
│  └─> Use: groupdel
├─ Deleting manually on Ubuntu/Debian?
│  └─> Use: delgroup
└─ Deleting manually on CentOS/RHEL?
   └─> Use: groupdel
```

---

## Understanding File Ownership

### What is File Ownership?

Every file and directory on Linux has an owner and a group. Think of it like this:

**Physical world:**
```
The file "resume.txt" is owned by John
The group "job_applicants" can also access it
```

**Linux:**
```
The file "resume.txt" is owned by user john
The group "hiring" can also access it
```

---

### Viewing File Ownership

**Command:**
```bash
ls -l /home/john/resume.txt
```

**Output:**
```
-rw-r--r-- 1 john john 2048 Dec 10 10:30 resume.txt
                ^^^^ ^^^^
                user group
```

**Explanation:**
- First part `-rw-r--r--` = permissions (we'll learn this next)
- `john` = file owner (the user)
- `john` = group owner (the group)
- `2048` = file size in bytes
- `Dec 10 10:30` = when file was last modified
- `resume.txt` = filename

**See all files in a directory:**
```bash
ls -l /home/john/
```

**Output:**
```
total 12
-rw-r--r-- 1 john john  2048 Dec 10 10:30 resume.txt
-rw-r--r-- 1 john john  1024 Dec 10 10:31 letter.txt
drwxr-xr-x 2 john john  4096 Dec 10 10:25 Documents
```

**Show numeric UID/GID instead of names:**
```bash
ls -ln /home/john/
```

**Output:**
```
-rw-r--r-- 1 1000 1000  2048 Dec 10 10:30 resume.txt
-rw-r--r-- 1 1000 1000  1024 Dec 10 10:31 letter.txt
```

---

## Understanding File Permissions

### What are Permissions?

Permissions control who can read, write, or execute files. Think of it like:

**Physical world:**
```
A door with a lock:
- Owner has a key (can enter)
- Your friend has a key (can enter)
- Strangers cannot enter (locked out)
```

**Linux files:**
```
A file with permissions:
- Owner can read and write
- Group can read only
- Others cannot access
```

---

### Reading Permission Notation

**Command:**
```bash
ls -l
```

**Output:**
```
-rw-r--r-- 1 john john 2048 Dec 10 10:30 resume.txt
^^^^^^^^^
permissions
```

**Breaking down the permissions:**

```
- r w - r -- r --
^ ^ ^ ^ ^^^ ^^^ ^^^
| | | | |   |   others
| | | | |   group
| | | | owner (execute)
| | | owner (write)
| | owner (read)
| file type (- = regular file, d = directory, l = symlink)
```

**Detailed breakdown:**

```
Position 1: - (file type)
  - = regular file
  d = directory
  l = symbolic link

Positions 2-4: rw- (owner permissions)
  r = read (can view file)
  w = write (can modify file)
  - = no execute

Positions 5-7: r-- (group permissions)
  r = read (can view file)
  - = no write
  - = no execute

Positions 8-10: r-- (others permissions)
  r = read (can view file)
  - = no write
  - = no execute
```

---

### Permission Types

**1. Read (r)**
```
For files: Can view the file's contents
For directories: Can list files in the directory
```

**2. Write (w)**
```
For files: Can modify/edit the file
For directories: Can create/delete files in the directory
```

**3. Execute (x)**
```
For files: Can run the file as a program (like a script or binary)
For directories: Can enter the directory (need this to access files inside)
```

---

### Octal/Numeric Notation

Instead of `rw-r--r--`, you can use numbers:

```
r = 4 (read)
w = 2 (write)
x = 1 (execute)
```

**Adding them up:**
```
7 = 4+2+1 = rwx (read, write, execute)
6 = 4+2   = rw- (read, write)
5 = 4+1   = r-x (read, execute)
4 = 4     = r-- (read only)
3 = 2+1   = -wx (write, execute)
2 = 2     = -w- (write only)
1 = 1     = --x (execute only)
0 = 0     = --- (no permissions)
```

**Full permission codes:**
```
644 = rw-r--r--
  6 = 4+2   = rw- (owner: read, write)
  4 = 4     = r-- (group: read only)
  4 = 4     = r-- (others: read only)

755 = rwxr-xr-x
  7 = 4+2+1 = rwx (owner: full access)
  5 = 4+1   = r-x (group: read, execute)
  5 = 4+1   = r-x (others: read, execute)

700 = rwx------
  7 = 4+2+1 = rwx (owner: full access)
  0 = 0     = --- (group: no access)
  0 = 0     = --- (others: no access)
```

---

### Common Permission Examples

**644 (Very Common for Files)**
```
-rw-r--r--
Owner: read, write (can edit the file)
Group: read only (can view but not edit)
Others: read only (can view but not edit)

Use for: Regular files, documents, logs
Example: resume.txt, config.txt, data.json
```

**755 (Very Common for Directories and Scripts)**
```
-rwxr-xr-x
Owner: read, write, execute (full control)
Group: read, execute (can enter and view)
Others: read, execute (can enter and view)

Use for: Executable files, scripts, directories
Example: /usr/bin/python, /var/www/html, deploy.sh
```

**700 (Private)**
```
-rwx------
Owner: read, write, execute (full control)
Group: no access
Others: no access

Use for: Private files, sensitive data
Example: private.key, .ssh/config, secret.txt
```

**600 (Very Private)**
```
-rw-------
Owner: read, write
Group: no access
Others: no access

Use for: Private files (no execute needed)
Example: id_rsa (SSH key), .bashrc, passwords.txt
```

**777 (Everyone Can Do Everything - DANGEROUS)**
```
-rwxrwxrwx
Owner: read, write, execute
Group: read, write, execute
Others: read, write, execute

DANGER: Anyone can modify or delete!
Use for: /tmp (temporary files only)
Never use for: Your important files!
```

---

## Changing Ownership with chown and chgrp

### Command 1: `chown` - Change Owner

**What does it do?**
`chown` changes the owner (and optionally the group) of a file or directory.

**Basic syntax:**
```bash
sudo chown owner file
sudo chown owner:group file
```

---

### Example 1: Change File Owner

**Command:**
```bash
sudo chown john resume.txt
```

**What it does:**
- Changes owner of resume.txt from current owner to john
- Group stays the same

**Before:**
```bash
ls -l resume.txt
# -rw-r--r-- 1 alice alice 2048 Dec 10 10:30 resume.txt
#              alice is owner
```

**After:**
```bash
ls -l resume.txt
# -rw-r--r-- 1 john alice 2048 Dec 10 10:30 resume.txt
#            john is now owner
```

---

### Example 2: Change Owner and Group

**Command:**
```bash
sudo chown john:developers resume.txt
```

**What it does:**
- Changes owner to john
- Changes group to developers

**Before:**
```bash
ls -l resume.txt
# -rw-r--r-- 1 alice alice 2048 Dec 10 10:30 resume.txt
```

**After:**
```bash
ls -l resume.txt
# -rw-r--r-- 1 john developers 2048 Dec 10 10:30 resume.txt
```

---

### Example 3: Change Group Only

**Command:**
```bash
sudo chown :developers resume.txt
```

**What the colon means:**
- `:` alone means "change group, not owner"

**Before:**
```bash
ls -l resume.txt
# -rw-r--r-- 1 john john 2048 Dec 10 10:30 resume.txt
```

**After:**
```bash
ls -l resume.txt
# -rw-r--r-- 1 john developers 2048 Dec 10 10:30 resume.txt
```

---

### Example 4: Change Ownership Recursively (Directories)

**Command:**
```bash
sudo chown -R john:developers /home/john
```

**What the `-R` flag does:**
- `-R` means "recursive" (apply to directory and all contents)
- Changes ownership of /home/john AND all files inside
- Changes ownership of /home/john/Documents AND all files inside that
- And so on...

**Why you need this:**
```
Without -R:
sudo chown john /home/john
-> Only /home/john directory changes
-> All files inside still have old owner
-> john can enter directory but can't access his own files!

With -R:
sudo chown -R john /home/john
-> /home/john changes
-> All files inside /home/john change
-> john can access everything
```

**Before:**
```bash
ls -lR /home/john/
# total 12
# -rw-r--r-- 1 alice alice 2048 Dec 10 10:30 resume.txt
# -rw-r--r-- 1 alice alice 1024 Dec 10 10:31 letter.txt
# drwxr-xr-x 2 alice alice 4096 Dec 10 10:25 Documents
#   -rw-r--r-- 1 alice alice 512 Dec 10 10:26 notes.txt
```

**After:**
```bash
ls -lR /home/john/
# total 12
# -rw-r--r-- 1 john developers 2048 Dec 10 10:30 resume.txt
# -rw-r--r-- 1 john developers 1024 Dec 10 10:31 letter.txt
# drwxr-xr-x 2 john developers 4096 Dec 10 10:25 Documents
#   -rw-r--r-- 1 john developers 512 Dec 10 10:26 notes.txt
```

---

### Example 5: Verbose Output (See What Changed)

**Command:**
```bash
sudo chown -v john file.txt
```

**Output:**
```
changed ownership of 'file.txt' from alice to john
```

**Useful for:**
- Seeing exactly what was changed
- Documenting what you did
- Verifying the command worked

**For directories with -R:**
```bash
sudo chown -R -v john:developers /home/john
```

**Output:**
```
changed ownership of '/home/john' from alice to john
changed ownership of '/home/john/resume.txt' from alice to john
changed ownership of '/home/john/Documents' from alice to john
changed ownership of '/home/john/Documents/notes.txt' from alice to john
```

---

### Command 2: `chgrp` - Change Group

**What does it do?**
`chgrp` changes only the group of a file (not the owner).

**Basic syntax:**
```bash
sudo chgrp groupname file
```

**Note:** You can do the same with `chown` using `:groupname`, but `chgrp` is simpler if you only want to change the group.

---

### Example 1: Change File Group

**Command:**
```bash
sudo chgrp developers resume.txt
```

**What it does:**
- Changes group of resume.txt to developers
- Owner stays the same

**Before:**
```bash
ls -l resume.txt
# -rw-r--r-- 1 john john 2048 Dec 10 10:30 resume.txt
#            john owns it, john group
```

**After:**
```bash
ls -l resume.txt
# -rw-r--r-- 1 john developers 2048 Dec 10 10:30 resume.txt
#            john owns it, developers group
```

---

### Example 2: Change Group Recursively

**Command:**
```bash
sudo chgrp -R developers /var/www
```

**What it does:**
- Changes group of /var/www to developers
- Changes group of all files inside /var/www to developers
- Changes group of all subdirectories to developers

---

### When to use `chown` vs `chgrp`

**Use `chown` when:**
- You need to change owner
- You need to change both owner and group at the same time

**Use `chgrp` when:**
- You only need to change the group
- You want simpler, clearer commands

**Both are equivalent:**
```bash
# These do the same thing:
sudo chown :developers file.txt
sudo chgrp developers file.txt
```

---

## Changing Permissions with chmod

### Command: `chmod` - Change Mode

**What does it do?**
`chmod` changes the permissions (who can read, write, execute) of a file or directory.

**Two ways to use it:**
1. Symbolic method: `chmod u+rw file` (human-readable)
2. Numeric method: `chmod 644 file` (using numbers)

---

### Symbolic Method (Easier to Learn)

**Basic syntax:**
```bash
chmod who+permission file
chmod who-permission file
chmod who=permission file
```

**"who" can be:**
```
u = user (owner)
g = group
o = others
a = all (owner, group, others)
```

**"permission" can be:**
```
r = read
w = write
x = execute
```

**"operation" can be:**
```
+ = add permission
- = remove permission
= = set exactly (remove all others)
```

---

### Example 1: Add Execute Permission for Owner

**Command:**
```bash
chmod u+x script.sh
```

**What it does:**
- Adds execute permission for owner (user)
- Owner can now run the script

**Before:**
```bash
ls -l script.sh
# -rw-r--r-- 1 john john 512 Dec 10 10:30 script.sh
#  ^^^
#  rw- means owner can read/write but NOT execute
```

**After:**
```bash
ls -l script.sh
# -rwxr--r-- 1 john john 512 Dec 10 10:30 script.sh
#  ^^^
#  rwx means owner can read/write/execute now
```

---

### Example 2: Remove Write Permission from Others

**Command:**
```bash
chmod o-w file.txt
```

**What it does:**
- Removes write permission from others
- Others can no longer modify the file

**Before:**
```bash
ls -l file.txt
# -rw-rw-rw- 1 john john 1024 Dec 10 10:30 file.txt
#         ^^
#         rw means others can read and write
```

**After:**
```bash
ls -l file.txt
# -rw-rw-r-- 1 john john 1024 Dec 10 10:30 file.txt
#         ^^
#         r- means others can read but not write
```

---

### Example 3: Add Read for Group

**Command:**
```bash
chmod g+r file.txt
```

**What it does:**
- Adds read permission for group

---

### Example 4: Set Exact Permissions

**Command:**
```bash
chmod u=rwx,g=rx,o= file.txt
```

**What it does:**
- `u=rwx` - Owner: read, write, execute (remove others)
- `g=rx` - Group: read, execute (remove write)
- `o=` - Others: nothing (remove all)

**Before:**
```bash
ls -l file.txt
# -rw-rw-rw- 1 john john 1024 Dec 10 10:30 file.txt
```

**After:**
```bash
ls -l file.txt
# -rwxr-x--- 1 john john 1024 Dec 10 10:30 file.txt
#  ^^^^^^^^
#  u=rwx, g=rx, o=nothing
```

**Explanation:**
- `=` means "set to exactly this, remove anything else"
- `rwx` = read, write, execute
- `rx` = read, execute (no write)
- (empty) = nothing

---

### Example 5: Make Script Executable

**Command:**
```bash
chmod +x script.sh
```

**What it does:**
- Adds execute permission for owner, group, and others
- Everyone can run this script

**Before:**
```bash
ls -l script.sh
# -rw-r--r-- 1 john john 512 Dec 10 10:30 script.sh
```

**After:**
```bash
ls -l script.sh
# -rwxr-xr-x 1 john john 512 Dec 10 10:30 script.sh
#  ^^^^^^
#  everyone can execute now
```

---

### Example 6: Remove Execute from All

**Command:**
```bash
chmod -x script.sh
```

**What it does:**
- Removes execute permission from everyone
- Nobody can run the script anymore

---

### Numeric Method (Faster)

**Basic syntax:**
```bash
chmod 755 file
```

**How it works:**
```
755
^^^
|||
||+-- Others permissions
|+--- Group permissions
+----- Owner permissions

4 = read
2 = write
1 = execute
```

**Common combinations:**
```
0 = 0       = --- (no permissions)
1 = 1       = --x (execute only)
2 = 2       = -w- (write only)
3 = 2+1     = -wx (write, execute)
4 = 4       = r-- (read only)
5 = 4+1     = r-x (read, execute)
6 = 4+2     = rw- (read, write)
7 = 4+2+1   = rwx (read, write, execute)
```

---

### Example 1: Set 644 (Common for Files)

**Command:**
```bash
chmod 644 file.txt
```

**What it means:**
```
6 = 4+2 = rw- (owner: read, write)
4 = 4   = r-- (group: read only)
4 = 4   = r-- (others: read only)
```

**Result:**
```bash
ls -l file.txt
# -rw-r--r-- 1 john john 1024 Dec 10 10:30 file.txt
```

**When to use:** Regular files you want to share (read-only)

---

### Example 2: Set 755 (Common for Directories/Scripts)

**Command:**
```bash
chmod 755 script.sh
```

**What it means:**
```
7 = 4+2+1 = rwx (owner: read, write, execute)
5 = 4+1   = r-x (group: read, execute)
5 = 4+1   = r-x (others: read, execute)
```

**Result:**
```bash
ls -l script.sh
# -rwxr-xr-x 1 john john 512 Dec 10 10:30 script.sh
```

**When to use:** Executable files, directories you want people to access

---

### Example 3: Set 700 (Private)

**Command:**
```bash
chmod 700 private.txt
```

**What it means:**
```
7 = 4+2+1 = rwx (owner: read, write, execute)
0 = 0     = --- (group: nothing)
0 = 0     = --- (others: nothing)
```

**Result:**
```bash
ls -l private.txt
# -rwx------ 1 john john 1024 Dec 10 10:30 private.txt
```

**When to use:** Very private files (only owner access)

---

### Example 4: Set 600 (Private, No Execute)

**Command:**
```bash
chmod 600 id_rsa
```

**What it means:**
```
6 = 4+2 = rw- (owner: read, write)
0 = 0   = --- (group: nothing)
0 = 0   = --- (others: nothing)
```

**Result:**
```bash
ls -l id_rsa
# -rw------- 1 john john 1024 Dec 10 10:30 id_rsa
```

**When to use:** Private files like SSH keys

---

### Example 5: Recursive Permissions (Directories)

**Command:**
```bash
chmod -R 755 /var/www
```

**What it does:**
- Changes /var/www to 755
- Changes all files inside to 755
- Changes all subdirectories to 755

**Before:**
```bash
ls -lR /var/www/
# drwxrwxrwx (wrong)
#   -rw-rw-rw- (wrong)
#   -rw-rw-rw- (wrong)
#   drwxrwxrwx (wrong)
```

**After:**
```bash
ls -lR /var/www/
# drwxr-xr-x (correct)
#   -rwxr-xr-x (all executable - might not want this!)
#   -rwxr-xr-x
#   drwxr-xr-x
```

**Better approach (only change files and directories separately):**

```bash
# Change all FILES to 644 (read-only)
find /var/www -type f -exec chmod 644 {} \;

# Change all DIRECTORIES to 755 (enter and read)
find /var/www -type d -exec chmod 755 {} \;
```

---

### Verbose Output (See What Changed)

**Command:**
```bash
chmod -v 755 file.txt
```

**Output:**
```
mode of 'file.txt' changed from 0644 (rw-r--r--) to 0755 (rwxr-xr-x)
```

---

### Special Permissions (Advanced)

**SUID (Set-User-ID) - 4xxx**

```bash
# Makes file run as owner (not as the person who runs it)
chmod 4755 program
```

**Example in real life:**
- `passwd` program runs as root even when you run it
- Allows you to change your own password (which updates /etc/shadow)
- Without SUID, regular users couldn't modify /etc/shadow

**View SUID files:**
```bash
ls -l /usr/bin/passwd
# -rwsr-xr-x (notice the 's' instead of 'x')
```

---

**SGID (Set-Group-ID) - 2xxx**

```bash
# On files: runs as group owner
# On directories: new files inherit directory's group
chmod 2755 directory
```

**Example:**
```bash
# Create shared directory for developers
chmod 2770 /shared/project

# Any file created in /shared/project automatically belongs to 'developers' group
# Even if john created it, group is 'developers' (not 'john')
```

**View SGID:**
```bash
ls -l /shared/project
# drwxrws--- (notice the 's' in group execute position)
```

---

**Sticky Bit - 1xxx**

```bash
# On directories: only owner can delete files
chmod 1777 /tmp
```

**Example:**
- `/tmp` is world-writable (everyone can create files)
- But only the owner of each file can delete it
- Prevents one person from deleting another's temp files

**View sticky bit:**
```bash
ls -ld /tmp
# drwxrwxrwt (notice the 't' in others execute position)
```

---

## Real-World Examples

### Scenario 1: Web Server Directory Setup

**Goal:** Setup a web directory where www-data (web server) owns files, but developers can edit

**Steps:**

```bash
# 1. Create the directory
sudo mkdir -p /var/www/myapp

# 2. Change ownership to web server user
sudo chown www-data:www-data /var/www/myapp

# 3. Set permissions: owner full, group read/write, others none
sudo chmod 770 /var/www/myapp

# 4. Set SGID so new files inherit group
sudo chmod g+s /var/www/myapp

# 5. Create developers group if needed
sudo groupadd -f developers

# 6. Add developers to the group
sudo usermod -aG www-data alice
sudo usermod -aG www-data bob

# 7. Verify
ls -ld /var/www/myapp
# drwxrws--- www-data developers (SGID bit visible as 's')

getent group www-data
# www-data:x:33:alice,bob
```

**Result:**
- www-data owns the directory
- developers group can read, write, execute
- Any file created automatically belongs to developers group
- Others cannot access

---

### Scenario 2: Create New Employee Account

**Goal:** Set up account for new developer john

**Steps:**

```bash
# 1. Create user with home directory and bash shell
sudo useradd -m -s /bin/bash john

# 2. Set initial password
sudo passwd john
# System asks for new password twice

# 3. Add to developer groups
sudo usermod -aG sudo john        # Can use sudo
sudo usermod -aG developers john  # Joins developers group
sudo usermod -aG docker john      # Can use docker

# 4. Verify
id john
# uid=1001(john) gid=1001(john) groups=1001(john),27(sudo),1001(developers),999(docker)

groups john
# john : john sudo developers docker

# 5. Verify home directory
ls -ld /home/john
# drwxr-xr-x john john (properly owned)

# 6. Expire password so john must change it on first login
sudo passwd -e john
```

**Result:**
- john has account and can log in
- john can use sudo (administrative privileges)
- john can use docker
- john must change password on first login
- john's files are in /home/john

---

### Scenario 3: Create Shared Project Directory

**Goal:** Create directory for team to collaborate on project

**Steps:**

```bash
# 1. Create group for project
sudo groupadd webdev

# 2. Create project directory
sudo mkdir -p /opt/projects/website

# 3. Set owner and group
sudo chown :webdev /opt/projects/website
# (keep existing owner, change group to webdev)

# 4. Set permissions: owner and group can do everything, others nothing
sudo chmod 770 /opt/projects/website

# 5. Set SGID for group inheritance
sudo chmod g+s /opt/projects/website

# 6. Add team members to group
sudo usermod -aG webdev john
sudo usermod -aG webdev alice
sudo usermod -aG webdev bob

# 7. Verify
ls -ld /opt/projects/website
# drwxrws--- (group owner webdev, SGID bit set)

getent group webdev
# webdev:x:1002:john,alice,bob

# 8. Create a test file
touch /opt/projects/website/test.txt

# 9. Check that it inherited the group
ls -l /opt/projects/website/test.txt
# -rw-r--r-- ? ? webdev (group is webdev, not john!)
```

**Result:**
- Directory owned by webdev group
- john, alice, bob can all read/write/execute
- Any file they create belongs to webdev group automatically
- Easy to manage permissions for whole team

---

### Scenario 4: Secure SSH Key Setup

**Goal:** Create SSH key with proper permissions

**Steps:**

```bash
# 1. Create .ssh directory
mkdir -p ~/.ssh

# 2. Create SSH key
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""
# Creates id_rsa (private key) and id_rsa.pub (public key)

# 3. Set permissions on directory
chmod 700 ~/.ssh
# Only owner can read/write/execute

# 4. Set permissions on private key
chmod 600 ~/.ssh/id_rsa
# Only owner can read/write (no execute needed)

# 5. Set permissions on public key (can be shared)
chmod 644 ~/.ssh/id_rsa.pub
# Owner can read/write, group/others can read

# 6. Verify
ls -la ~/.ssh/
# drwx------ (only owner access)
#   -rw------- id_rsa (private, owner only)
#   -rw-r--r-- id_rsa.pub (public)
```

**Result:**
- Private key is protected (only you can read)
- Public key can be shared
- Directory is protected
- SSH will work properly

---

### Scenario 5: Fix Permissions on Existing Directory

**Goal:** Repair messed-up permissions on /var/www

**Steps:**

```bash
# 1. Find problems
find /var/www -type f -perm /022
# Shows all files writable by group or others (bad!)

# 2. Fix file permissions (644 = rw-r--r--)
find /var/www -type f -exec chmod 644 {} \;

# 3. Fix directory permissions (755 = rwxr-xr-x)
find /var/www -type d -exec chmod 755 {} \;

# 4. Fix ownership
sudo chown -R www-data:www-data /var/www

# 5. Verify
ls -lR /var/www | head -20
# Should now show 644 for files, 755 for directories

find /var/www -type f -perm /022
# Should return nothing (no problems!)
```

**Result:**
- All files have safe permissions
- All directories have safe permissions
- Everything is owned by www-data
- Web server works correctly

---

## Troubleshooting

### Problem 1: "Permission Denied" Error

**Command fails with:**
```
Permission denied
```

**How to diagnose:**

```bash
# Check who owns the file
ls -l filename

# Check your groups
groups

# Check what permissions you have
# Compare file's permissions with your user/group
```

**Example:**
```bash
ls -l report.txt
# -rw-r----- 1 alice developers 1024 Dec 10 10:30 report.txt
#  ^^^^^^^^^^
#  You can only read (you're not owner or in developers group)

groups
# john (you're in john group, not developers)
```

**Solution:**
```bash
# Ask admin to add you to the group
sudo usermod -aG developers john

# Or ask admin to change permissions
sudo chmod g+w report.txt
```

---

### Problem 2: Can't Execute Script

**You get:**
```
bash: ./script.sh: Permission denied
```

**How to diagnose:**

```bash
ls -l script.sh
# -rw-r--r-- 1 alice alice 512 Dec 10 10:30 script.sh
#  ^^^ (owner is alice, you're john - can't execute!)
# Also no 'x' permission for anyone
```

**Solution:**

```bash
# If you own it:
chmod +x script.sh

# If alice owns it:
# Ask alice to do: chmod +x script.sh
# Or ask admin to do: sudo chmod +x script.sh
```

---

### Problem 3: Can't Access Directory

**You get:**
```
bash: cd: /home/alice: Permission denied
```

**How to diagnose:**

```bash
ls -ld /home/alice
# drw------- (alice only)
#   ^
#   no 'x' for group/others
```

**Explanation:**
- Directories need `x` permission to enter
- You don't have execute permission on /home/alice

**Solution:**

```bash
# If you own it:
chmod o+x /home/alice  # Add execute for others

# If alice owns it:
# Ask alice: chmod o+x /home/alice
# Or ask admin: sudo chmod o+x /home/alice
```

---

### Problem 4: Files Don't Inherit Expected Group

**You create file, but group is wrong:**

```bash
# You're in webdev group
# You create file in /opt/project
touch /opt/project/file.txt

ls -l /opt/project/file.txt
# -rw-r--r-- john john (should be webdev group!)
#          ^^^^
```

**How to fix:**

```bash
# Check directory SGID
ls -ld /opt/project
# drwxr-xr-x john john (no 's' = no SGID)

# Set SGID
sudo chmod g+s /opt/project

# Verify
ls -ld /opt/project
# drwxr-sr-x (there's the 's'!)

# Now new files inherit group
touch /opt/project/file2.txt
ls -l /opt/project/file2.txt
# -rw-r--r-- john developers (correct!)
```

---

### Problem 5: User Left - Cleanup

**An employee left and you want to clean up:**

```bash
# 1. Lock the account
sudo usermod -L alice

# 2. Remove from groups
sudo usermod -G "" alice  # Empty supplementary groups

# 3. Backup their files (optional)
sudo tar -czf /backup/alice-2024-12.tar.gz /home/alice

# 4. Delete their files and account
sudo userdel -r alice

# 5. Remove their files from group-owned directories
find /opt/projects -user alice -exec chown john {} \;

# 6. Verify
id alice
# Should say 'id: 'alice': no such user
```

---

### Problem 6: Wrong Owner on Files

**Files are owned by wrong user:**

```bash
ls -l /home/john/
# -rw-r--r-- root root (root is owner - wrong!)
```

**Fix:**

```bash
# Change owner
sudo chown john /home/john/file.txt

# Change owner and group
sudo chown john:john /home/john/file.txt

# Fix entire directory
sudo chown -R john:john /home/john

# Verify
ls -l /home/john/
# -rw-r--r-- john john (correct!)
```

---

## Quick Command Reference

### User Commands (Low-Level - useradd/userdel)

**Use in scripts and for automation:**

```bash
# Create user (low-level)
sudo useradd -m -s /bin/bash username

# Create user with specific UID
sudo useradd -m -u 1500 username

# Create system user (for services)
sudo useradd -r -s /bin/false serviceuser

# Change password
sudo passwd username

# Modify user
sudo usermod -aG groupname username

# Delete user (keep home)
sudo userdel username

# Delete user (remove home)
sudo userdel -r username

# List users
cat /etc/passwd
getent passwd username

# Get user info
id username
```

### User Commands (High-Level - adduser/deluser)

**Use for manual creation on Ubuntu/Debian:**

```bash
# Create user (interactive, asks questions)
sudo adduser username

# Delete user (safe, shows warnings)
sudo deluser username

# Delete user and home
sudo deluser --remove-home username

# Delete user and all files
sudo deluser --remove-all-files username
```

---

### Group Commands (Low-Level - groupadd/groupdel)

**Use in scripts and for automation:**

```bash
# Create group
sudo groupadd groupname

# Create group with specific GID
sudo groupadd -g 3000 groupname

# Create system group
sudo groupadd -r groupname

# Add user to group
sudo usermod -aG groupname username

# Delete group
sudo groupdel groupname

# List groups
cat /etc/group
getent group groupname

# View groups for user
groups username
id username
```

### Group Commands (High-Level - addgroup/delgroup)

**Use for manual creation on Ubuntu/Debian:**

```bash
# Create group (simpler syntax)
sudo addgroup groupname

# Create group with specific GID
sudo addgroup --gid 3000 groupname

# Create system group
sudo addgroup --system groupname

# Delete group (safe, shows warnings)
sudo delgroup groupname

# Delete group (force removal)
sudo delgroup --force groupname
```

---

### Permission Commands

```bash
# Change owner
sudo chown user file
sudo chown user:group file

# Change group
sudo chgrp group file

# Change permissions (symbolic)
chmod u+x file       # Add execute for owner
chmod g-w file       # Remove write from group
chmod o= file        # Remove all from others

# Change permissions (numeric)
chmod 644 file       # rw-r--r--
chmod 755 file       # rwxr-xr-x
chmod 700 file       # rwx------

# Recursive
chmod -R 755 directory
chown -R user:group directory

# View permissions
ls -l file
ls -ld directory
```

---

## Common Permission Cheat Sheet

| Permission | Octal | Symbolic | Use Case |
|-----------|-------|----------|----------|
| rw-r--r-- | 644 | Regular file (readable by all) |
| rwxr-xr-x | 755 | Script or directory |
| rwx------ | 700 | Private file |
| rw------- | 600 | Secret file (like SSH key) |
| rwxrwxrwx | 777 | DANGER - use only for /tmp! |
| rwxrws--- | 2770 | Shared directory with SGID |
| -rwsr-xr-x | 4755 | SUID binary (runs as owner) |

---

## Summary

### Key Takeaways

1. **Users** are login accounts. Each has:
   - Username
   - UID (numeric ID)
   - Home directory
   - Shell
   - Groups they belong to

2. **Groups** are collections of users. Useful for:
   - Managing permissions easily
   - Organizing teams
   - Sharing files/directories

3. **File Ownership** determines who owns each file:
   - User (owner)
   - Group (group owner)
   - Others (everyone else)

4. **Permissions** control what you can do:
   - Read (r = 4): view contents
   - Write (w = 2): modify contents
   - Execute (x = 1): run file or enter directory

5. **Commands**:
   - `useradd/usermod` - manage users
   - `groupadd/groupmod` - manage groups
   - `chown` - change owner
   - `chgrp` - change group
   - `chmod` - change permissions

6. **Safe Defaults**:
   - User files: 644 (owner rw, others read only)
   - Directories: 755 (owner rwx, others rx)
   - Private files: 600 (owner rw only)
   - SSH keys: 600 (owner rw only)

### Next Steps

1. Practice creating users and groups
2. Experiment with permissions on test files
3. Understand ownership in your system
4. Read about sudo and administrative access
5. Learn about special permissions (SUID, SGID, sticky bit)

---

## Glossary

### Key Concepts
**UID** - User ID, numeric identifier for a user
**GID** - Group ID, numeric identifier for a group
**Home Directory** - /home/username, where user stores files
**Shell** - /bin/bash, the command interpreter
**sudo** - Run command as root (administrator)
**Recursive (-R)** - Apply to directory and all contents
**Supplementary Group** - Additional groups a user belongs to
**Primary Group** - User's main group
**SUID** - Set-User-ID, makes file run as owner
**SGID** - Set-Group-ID, makes file run as group
**Sticky Bit** - Only owner can delete file in directory

### User Commands
**useradd** - Low-level tool to create users (use in scripts)
**adduser** - High-level tool to create users interactively (use for manual creation on Ubuntu/Debian)
**userdel** - Low-level tool to delete users (use in scripts)
**deluser** - High-level tool to delete users safely (use for manual deletion on Ubuntu/Debian)
**usermod** - Modify existing user accounts
**passwd** - Change password or password settings

### Group Commands
**groupadd** - Low-level tool to create groups (use in scripts)
**addgroup** - High-level tool to create groups (use for manual creation on Ubuntu/Debian)
**groupdel** - Low-level tool to delete groups (use in scripts)
**delgroup** - High-level tool to delete groups safely (use for manual deletion on Ubuntu/Debian)
**groupmod** - Modify existing groups

### Permission Commands
**chown** - Change file/directory owner and group
**chgrp** - Change file/directory group only
**chmod** - Change file/directory permissions
**ls -l** - List files with permissions and ownership
**stat** - Show detailed file information including permissions

### Files
**/etc/passwd** - User account information file
**/etc/shadow** - Encrypted password file
**/etc/group** - Group information file
**/etc/gshadow** - Encrypted group password file
**/home** - Directory where user home directories are stored
**/etc/skel** - Skeleton files copied to new user home directories
