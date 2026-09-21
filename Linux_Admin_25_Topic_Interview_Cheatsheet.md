# Linux Admin Interview — 25-Topic Master Cheatsheet

**Source:** `Linux_Admin_Interview_Preparation_Topics_1-25_EXACT(1).docx`  
**Purpose:** Interview revision + technical-round command reference.  
**Coverage:** All 25 topics in the source document, including definitions, commands, distinctions, scenarios, traps, and practical tasks.

---

# 0. MASTER INTERVIEW MAP

## The 25 topics

1. Linux Command Line & Shell Usage
2. Linux File System
3. Vim / Text Editors
4. Users and Groups
5. File Permissions & Security
6. Process Management
7. System & Service Management
8. Task Scheduling
9. Privilege Delegation
10. Remote Administration
11. Storage Management Fundamentals
12. Logical Volume Manager (LVM)
13. Btrfs
14. Network Configuration
15. Software Management
16. System Logging & Monitoring
17. Bootloader, Installation, Time Synchronization & Basic Troubleshooting
18. Advanced System Administration
19. Encryption & Security
20. Shell Scripting (Bash)
21. Hardware & Drivers
22. Advanced Networking
23. Advanced Storage Administration
24. Centralized Authentication
25. Advanced Software Management

## Core command families

```text
Navigation:
pwd  ls  cd  mkdir  rmdir  touch  cp  mv  rm

File/text:
cat  less  head  tail  file  grep  find

Vim:
vim  i  Esc  :w  :q  :wq  :q!  dd  yy  p  u  /pattern

Users/groups:
useradd  usermod  userdel  groupadd  groupdel  groups  id  passwd

Permissions:
ls -l  chmod  chown  chgrp  getfacl  setfacl

Processes:
ps  ps aux  top  kill  killall  jobs  fg  bg  nice  renice

Services/systemd:
systemctl status/start/stop/restart/reload/enable/disable
systemctl enable --now
systemctl get-default
systemctl set-default
systemctl list-units

Scheduling:
crontab -e  crontab -l  crontab -r
at
systemd timers

Privilege:
su  su -  sudo  sudo -l  visudo

Remote:
ssh  ssh -i
ss -tuln
ping

Storage:
lsblk  fdisk  parted  mount  umount  df -h  du -sh

LVM:
pvcreate  pvs
vgcreate  vgs
lvcreate  lvs
lvextend
resize2fs

Btrfs:
btrfs subvolume create/list/snapshot/delete

Network:
ip addr  ip link  ip route
nmcli device status
nmcli connection show
nmcli device show
nmcli connection modify
nmcli connection up
hostnamectl
nslookup
ss -tuln

Packages:
rpm
yum
zypper

Logging:
dmesg
journalctl
journalctl -f
journalctl -b
journalctl -u <service>
logrotate
df -h
du -sh /var/log/*

Time:
chronyc tracking
chronyc sources
systemctl status chronyd

Security/crypto:
openssl version
gpg --full-generate-key
gpg --list-keys
gpg --list-secret-keys
gpg --export --armor
gpg --import
gpg --encrypt
gpg --decrypt

Hardware:
lscpu  free -h  lspci  lsusb  lsblk
lshw  lsmod  modprobe  modinfo  dmesg

Advanced networking:
ip netns
ip -6 addr
ip -6 route
ping -6

Advanced storage/auth/repos:
iSCSI concepts
multipath concepts
PAM  /etc/pam.d/
SSSD  systemctl status sssd
createrepo
libzypp
RMT
```

---

# 1. LINUX COMMAND LINE & SHELL USAGE

## 1.1 Bash fundamentals

**Shell:** command-line interface used to interact with the operating system.

**Bash:** Bourne Again Shell; commonly used Linux shell.

Bash is used to:

- execute commands
- manage files/directories
- run programs
- work with variables
- create scripts
- automate administration

**Interview answer:**  
“Bash is a command-line shell used in Linux to interact with the operating system. It allows administrators to execute commands, manage files, control processes, and automate tasks using shell scripts.”

## 1.2 Command syntax

```text
command [options] [arguments]
```

Example:

```bash
ls -l /home
```

- `ls` = command
- `-l` = option
- `/home` = argument

Another:

```bash
cp file1.txt /backup/
```

**Option vs argument:**  
Option modifies command behavior; argument identifies the object/input the command acts on.

## 1.3 Getting help

```bash
man ls
ls --help
info ls
```

- `--help` = quick syntax/usage
- `man` = manual/documentation
- `info` = detailed Info documentation where available

## 1.4 Essential commands

```bash
pwd
ls
ls -l
ls -la
cd /etc
cd ..
cd ~
mkdir test
touch test.txt
cp test.txt backup.txt
mv test.txt /tmp/
mv old.txt new.txt
rm test.txt
rmdir test
cat file.txt
less file.txt
head file.txt
tail file.txt
file test.txt
```

## 1.5 Redirection

```bash
command > file
command >> file
command < file
```

- `>` = redirect output and overwrite
- `>>` = append output
- `<` = use file as command input

**Trap:** `>` can destroy existing file contents.

## 1.6 Pipes

```bash
ls -l | less
ps aux | grep ssh
```

A pipe sends standard output of one command to standard input of another.

## 1.7 grep

```bash
grep "error" logfile.txt
grep -i "error" logfile.txt
grep -r "error" /var/log/
```

Use `grep` to search text/patterns in files or command output.

## 1.8 find

```bash
find /home -name "test.txt"
find /var/log -name "*.log"
find /home -type d
find /home -type f
```

Use `find` to search for files/directories based on criteria.

## 1.9 grep vs find

```text
find = search for files/directories
grep = search for text/patterns
```

## 1.10 Log investigation scenario

```bash
ls /var/log
grep -i "error" /var/log/<logfile>
less /var/log/<logfile>
```

Flow:

```text
Identify log → search with grep → inspect with less
```

## 1.11 Must-know interview questions

- What is Bash?
- What is the command structure?
- Option vs argument?
- `>` vs `>>`?
- What is a pipe?
- What is grep?
- What is find?
- grep vs find?
- How do you get help?
- How do you show hidden files?
- How do you find the current directory?

## 1.12 Technical-round mini test

```bash
pwd
ls -la
cd /etc
cd ..
mkdir test
touch test/file.txt
cp test/file.txt test/file_backup.txt
mv test/file_backup.txt test/backup.txt
rm test/file.txt
rmdir test
```

---

# 2. LINUX FILE SYSTEM

## 2.1 Linux filesystem model

Linux uses a hierarchical filesystem beginning at:

```text
/
```

`/` = root directory of the entire filesystem.

Linux presents storage as one unified tree rather than the Windows-style C:/D: model.

## 2.2 FHS — Filesystem Hierarchy Standard

Know the purpose of the major directories:

```text
/       = filesystem root
/home   = regular users' home directories
/etc    = system-wide configuration
/var    = variable/changing data
/var/log= system/application logs
/tmp    = temporary files
/usr    = user-space programs and related files
/bin    = essential command binaries
/sbin   = system administration commands
/dev    = device files
/proc   = process/system information
/boot   = boot-related files
/root   = root user's home directory
```

## 2.3 `/` vs `/root`

```text
/      = root of filesystem
/root  = home directory of root user
```

Common interview trap.

## 2.4 File types

The source explicitly focuses on:

- regular file
- directory
- link
- device

Typical `ls -l` first character:

```text
- = regular file
d = directory
l = symbolic link
```

Device interfaces are commonly represented under:

```text
/dev
```

Use:

```bash
file filename
ls -l filename
```

## 2.5 Navigation

```bash
pwd
ls
ls -l
ls -la
cd /etc
cd ..
cd ~
cd /
```

Special paths:

```text
.   = current directory
..  = parent directory
```

## 2.6 Absolute vs relative path

**Absolute:**

```text
/home/kartik/file.txt
```

Starts at `/`.

**Relative:**

```text
./file.txt
documents/file.txt
```

Interpreted from the current working directory.

## 2.7 File handling

```bash
touch test.txt
mkdir test
cp test.txt backup.txt
mv test.txt /tmp/
mv old.txt new.txt
rm test.txt
rmdir test
```

## 2.8 Hidden files

Files beginning with `.` are hidden from normal `ls`.

Example:

```text
.bashrc
```

Show them:

```bash
ls -la
```

## 2.9 Scenario flow

Need logs?

```bash
/var/log
```

Need configuration?

```bash
/etc
```

Need normal user home?

```bash
/home
```

Need root user's home?

```bash
/root
```

## 2.10 Practical test

```bash
pwd
cd /etc
ls -la
cd ..
mkdir test
touch test/file.txt
cp test/file.txt test/file_backup.txt
mv test/file_backup.txt test/backup.txt
rm test/file.txt
rmdir test
```

---

# 3. VIM / TEXT EDITORS

## 3.1 What is Vim?

Vim = Vi Improved.

A command-line editor used to create/modify text and configuration files, especially when working directly from a Linux terminal.

Open:

```bash
vim test.txt
```

## 3.2 Three major modes

```text
Normal/Command = navigation + editing commands
Insert          = type/modify text
Visual          = select text
```

Enter insert:

```text
i
```

Return to normal:

```text
Esc
```

Visual mode:

```text
v
```

## 3.3 Save/exit

From normal mode:

```text
:w    = save
:q    = quit
:wq   = save + quit
:q!   = quit without saving
ZZ    = save + quit
```

## 3.4 Navigation

```text
h = left
j = down
k = up
l = right
0 = beginning of line
$ = end of line
:number = jump to line
```

Example:

```text
:25
```

## 3.5 Editing

```text
x     = delete character
dd    = delete line
yy    = copy line
p     = paste
u     = undo
Ctrl+r= redo
```

## 3.6 Search

```text
/pattern
```

Then:

```text
n = next match
```

Example:

```text
/Listen
```

## 3.7 Basic workflow

```bash
vim /path/to/config
```

Then:

```text
i
edit text
Esc
:wq
```

## 3.8 Practical test

```text
vim test.conf
i
Linux Admin
Esc
:wq
vim test.conf
/Linux
:q
```

## 3.9 Configuration/customization

Vim can be customized through configuration files such as user-level/editor configuration.

For this source, the interview focus is the concept of customization, not memorizing a large `.vimrc`.

## 3.10 Advanced editing — interview level

Know that Vim supports:

- efficient navigation
- text selection
- search
- copy/paste
- multi-line editing
- undo/redo

## 3.11 Interview trap

Do not claim advanced Vim expertise if you cannot demonstrate it.

Good practical answer:

“I understand the basic Vim workflow, including the main modes, navigation, editing, searching, and saving/exiting files.”

---

# 4. USERS AND GROUPS

## 4.1 User

A Linux user is an account used to access/interact with the system.

Identified by:

```text
UID = User ID
```

## 4.2 Group

A group is a collection of users used to simplify access and permission management.

Identified by:

```text
GID = Group ID
```

## 4.3 User vs group

```text
User  → individual account → UID
Group → collection of users → GID
```

Users can own files; groups help control access for multiple users.

## 4.4 `/etc/passwd`

Contains general user account information.

Typical structure:

```text
username:x:UID:GID:comment:home:shell
```

Example:

```text
kartik:x:1001:1001:Kartik:/home/kartik:/bin/bash
```

Know:

- username
- UID
- GID
- comment/info
- home directory
- login shell

## 4.5 `/etc/shadow`

Contains password-related authentication information.

It is more restricted because it contains sensitive authentication data.

## 4.6 passwd vs shadow

```text
/etc/passwd  → account information
/etc/shadow  → password/authentication information
```

## 4.7 User management

Create:

```bash
useradd testuser
```

Modify:

```bash
usermod -s /bin/bash testuser
usermod -d /home/newhome testuser
```

Delete:

```bash
userdel testuser
userdel -r testuser
```

Set/change password:

```bash
passwd testuser
```

Check account:

```bash
id testuser
```

## 4.8 Groups

Create:

```bash
groupadd developers
```

Delete:

```bash
groupdel developers
```

Add supplementary group:

```bash
usermod -aG developers testuser
```

Check memberships:

```bash
groups testuser
id testuser
```

**Critical trap:** `-aG` means append supplementary groups. Omitting `-a` can replace existing supplementary-group membership.

## 4.9 Primary vs supplementary group

```text
Primary group       = main/default group of the account
Supplementary group = additional memberships
```

## 4.10 Practical scenarios

Create user:

```bash
useradd testuser
id testuser
grep '^testuser:' /etc/passwd
```

Create group + add user:

```bash
groupadd admins
usermod -aG admins testuser
id testuser
groups testuser
```

Delete user:

```bash
userdel testuser
userdel -r testuser
```

## 4.11 Permission troubleshooting using identity

If a user cannot access a file:

```bash
id username
ls -l filename
```

Check:

1. user identity
2. groups
3. ownership
4. permissions
5. relevant access configuration

## 4.12 Graphical identity management

The source requires awareness that user/group management can also be performed with graphical identity-management tools where available. No particular GUI tool is mandated by the source.

## 4.13 High-probability questions

- UID vs GID?
- `/etc/passwd`?
- `/etc/shadow`?
- passwd vs shadow?
- Create/modify/delete user?
- Create group?
- Add user to group?
- Primary vs supplementary group?
- How to check groups?
- How to set password?

---

# 5. FILE PERMISSIONS & SECURITY

## 5.1 Basic permissions

```text
r = read
w = write
x = execute
```

Applied to:

```text
u = user/owner
g = group
o = others
```

## 5.2 `ls -l`

Example:

```text
-rwxr-xr--
```

Breakdown:

```text
-     = regular file
rwx   = owner
r-x   = group
r--   = others
```

## 5.3 Meaning on files

```text
r = read contents
w = modify contents
x = execute file when appropriate
```

## 5.4 Meaning on directories

```text
r = list directory contents
w = create/delete/rename entries, subject to relevant permissions
x = traverse/access directory
```

**Very important:** A user can have read permission on a directory but still fail to access an entry because directory `x` (traverse) permission is missing.

## 5.5 Octal permissions

```text
r = 4
w = 2
x = 1
```

Examples:

```text
rwx = 7
rw- = 6
r-x = 5
r-- = 4
-w- = 2
--x = 1
--- = 0
```

## 5.6 755

```text
755 = rwxr-xr-x
```

Owner:

```text
rwx
```

Group:

```text
r-x
```

Others:

```text
r-x
```

## 5.7 644

```text
644 = rw-r--r--
```

Owner:

```text
rw-
```

Group:

```text
r--
```

Others:

```text
r--
```

## 5.8 chmod

Numeric:

```bash
chmod 755 script.sh
chmod 644 file.txt
```

## 5.9 Symbolic mode

Operators:

```text
+ = add
- = remove
= = set exactly
```

Examples:

```bash
chmod u+x script.sh
chmod g-w file.txt
chmod o+r file.txt
chmod u=rwx,g=rx,o=r file.txt
```

## 5.10 chmod vs chown vs chgrp

```text
chmod = permissions
chown = ownership (owner/group)
chgrp = group ownership
```

Examples:

```bash
chown kartik file.txt
chown kartik:admins file.txt
chgrp admins file.txt
```

Inspect:

```bash
ls -l
```

## 5.11 SUID

SUID = Set User ID.

For an executable, it can cause execution with the file owner's privileges.

Pattern:

```text
-rwsr-xr-x
```

`s` in owner execute position = SUID.

## 5.12 SGID

SGID = Set Group ID.

For executable:

- can run with file group's privileges.

For directory:

- newly created files/directories inherit the directory's group.

## 5.13 Sticky bit

Important for shared directories.

It restricts users from deleting/renaming files owned by other users in that directory.

Classic example:

```text
/tmp
```

Pattern:

```text
drwxrwxrwt
```

`t` = sticky bit.

## 5.14 ACL

ACL = Access Control List.

Purpose: provide more granular permissions than standard owner/group/others.

Example scenario:

```text
Owner   → rw
Group   → r
Others  → no access
User B  → additionally needs rw
```

Use ACL rather than changing the basic group structure.

## 5.15 ACL commands

View:

```bash
getfacl file.txt
```

Grant user:

```bash
setfacl -m u:user2:rw file.txt
```

Verify:

```bash
getfacl file.txt
```

## 5.16 Permission-denied troubleshooting

Check:

```bash
ls -l file.txt
id username
getfacl file.txt
```

Investigate:

1. permissions
2. owner/group
3. user group membership
4. ACL
5. directory traversal (`x`)

## 5.17 Technical-round task

```bash
touch test.txt
ls -l test.txt
chmod 644 test.txt
chmod u+x test.txt
chown testuser test.txt
chgrp admins test.txt
getfacl test.txt
setfacl -m u:user2:rw test.txt
getfacl test.txt
```

## 5.18 High-probability questions

- What are r/w/x?
- Who do permissions apply to?
- What does 755 mean?
- What does 644 mean?
- chmod?
- chown?
- chgrp?
- SUID?
- SGID?
- Sticky bit?
- ACL?
- getfacl vs setfacl?
- Why would you use ACL?

---

# 6. PROCESS MANAGEMENT

## 6.1 Process

A process = running instance of a program.

```text
Program = executable code on disk
Process = running instance of that program
```

Every running process has a PID.

## 6.2 ps

Snapshot:

```bash
ps
ps aux
```

## 6.3 top

Dynamic/continuously updating process/resource view:

```bash
top
```

Can show:

- CPU usage
- memory usage
- PID
- processes/activity

## 6.4 ps vs top

```text
ps  = snapshot
top = live/dynamic view
```

## 6.5 Process lifecycle

Conceptual:

```text
Created → Running → Waiting/Sleeping → Running → Terminated
```

## 6.6 Signals and kill

`kill` sends a signal.

```bash
kill PID
kill -9 PID
```

Important:

- `kill` is not synonymous with “force kill”.
- Signal matters.
- `kill -9` is forceful and should not automatically be the first response.

## 6.7 killall

Targets by process name:

```bash
killall process_name
```

Comparison:

```text
kill    = normally PID-based
killall = process-name-based
```

## 6.8 Foreground/background

Foreground:

- occupies current terminal interaction.

Background:

```bash
command &
```

Example:

```bash
sleep 100 &
```

Job commands:

```bash
jobs
fg
bg
```

## 6.9 Nice / renice

Niceness affects scheduling priority.

Basic rule:

```text
Higher nice value → lower scheduling priority
Lower nice value  → higher scheduling priority
Default commonly 0
```

Start process with niceness:

```bash
nice -n 10 command
```

Change running process:

```bash
renice 10 -p 1234
```

Comparison:

```text
nice   = start process with nice value
renice = change nice value of existing process
```

## 6.10 Process troubleshooting

Slow server:

```bash
top
ps aux
```

Unresponsive process:

```bash
ps aux
kill PID
# only if necessary:
kill -9 PID
```

Find SSH:

```bash
ps aux | grep ssh
```

High CPU:

```text
top → identify PID → investigate → decide action
```

## 6.11 Technical-round commands

```bash
ps
ps aux
top
ps aux | grep process_name
kill PID
kill -9 PID
command &
jobs
fg
bg
nice -n 10 command
renice 10 -p PID
```

---

# 7. SYSTEM & SERVICE MANAGEMENT

## 7.1 systemd

systemd is a system and service manager.

Responsibilities in the source:

- manage services
- initialize system
- manage startup
- manage dependencies
- manage targets

## 7.2 systemctl

CLI tool for interacting with systemd.

```bash
systemctl status ssh
```

## 7.3 Core service commands

```bash
systemctl status <service>
systemctl start <service>
systemctl stop <service>
systemctl restart <service>
systemctl reload <service>
systemctl enable <service>
systemctl disable <service>
systemctl enable --now <service>
```

## 7.4 Reload vs restart

```text
reload  = ask service to reread config without full stop/start, if supported
restart = actually restart service
```

Do not assume every service supports reload.

## 7.5 Start vs enable

```text
start  = affect service now
enable = configure automatic startup at boot
```

`enable` does not necessarily start it immediately.

For both:

```bash
systemctl enable --now ssh
```

## 7.6 Stop vs disable

```text
stop    = stop running service now
disable = prevent normal automatic startup at boot
```

## 7.7 Boot process

```text
Firmware
  ↓
Bootloader
  ↓
Linux kernel
  ↓
systemd
  ↓
Services/targets
  ↓
Usable system
```

## 7.8 Targets

A target groups units to represent a system state.

Know:

```text
multi-user.target
graphical.target
```

Check default:

```bash
systemctl get-default
```

Set default:

```bash
systemctl set-default multi-user.target
```

List active units:

```bash
systemctl list-units
```

## 7.9 Service failure scenario

First:

```bash
systemctl status <service>
```

Then investigate logs/configuration.

The source explicitly connects this to:

```bash
journalctl -u <service>
```

Avoid blind restarts before checking the state/error.

## 7.10 Technical-round tasks

```bash
systemctl status ssh
systemctl start ssh
systemctl stop ssh
systemctl restart ssh
systemctl enable ssh
systemctl disable ssh
systemctl get-default
```

---

# 8. TASK SCHEDULING

## 8.1 Scheduling map

```text
cron / crontab  → recurring jobs
at               → one-time jobs
systemd timer    → systemd-integrated scheduled jobs
```

## 8.2 crontab

Edit:

```bash
crontab -e
```

List:

```bash
crontab -l
```

Remove:

```bash
crontab -r
```

## 8.3 Five cron fields

```text
minute hour day-of-month month day-of-week command
```

Example:

```text
0 2 * * * /path/to/script.sh
```

Means 2:00 AM every day.

## 8.4 Common examples

Every minute:

```text
* * * * * command
```

Every hour:

```text
0 * * * * command
```

Every day at 2 AM:

```text
0 2 * * * command
```

Every Sunday at 3 AM:

```text
0 3 * * 0 command
```

Every midnight:

```text
0 0 * * * /path/to/script.sh
```

## 8.5 `at`

One-time scheduling:

```bash
at 14:00
```

Then enter command(s), finish input as appropriate.

Concept:

```text
cron = recurring
at   = one-time
```

## 8.6 systemd timers

Relationship:

```text
timer unit
   ↓
service unit
   ↓
command/application
```

The timer defines when a systemd-managed service/task is triggered.

## 8.7 Cron vs systemd timer

```text
cron         = traditional recurring scheduler
systemd timer = scheduler integrated with systemd
```

## 8.8 Interview scenarios

Recurring weekly cleanup:

```text
cron or systemd timer
```

One-time tonight:

```text
at
```

Systemd-managed scheduled service:

```text
systemd timer
```

---

# 9. PRIVILEGE DELEGATION

## 9.1 Core idea

Privilege delegation = allow users to perform needed administrative operations without automatically giving unrestricted administrative access.

Main mechanisms:

```text
su
sudo
/etc/sudoers
```

## 9.2 UID/GID

```text
UID = identifies user
GID = identifies group
```

Check:

```bash
id username
```

## 9.3 su

Switch user:

```bash
su username
```

Login-style switch:

```bash
su -
```

The source highlights:

```text
su     = switch user
su -   = login-style shell/environment
```

## 9.4 sudo

Run a specific command with elevated privileges:

```bash
sudo systemctl restart ssh
```

## 9.5 su vs sudo

```text
su    = switch to another user/account
sudo  = execute authorized command with elevated privileges
```

Controlled command-level elevation is the key security idea.

## 9.6 `/etc/sudoers`

Defines sudo authorization rules.

Conceptually:

```text
Who
 ↓
may execute what
 ↓
on which host
 ↓
as which user
```

## 9.7 visudo

Use:

```bash
visudo
```

Reason:

- safely edit sudoers
- syntax-check configuration

Do not casually edit `/etc/sudoers` with a normal editor.

## 9.8 Least privilege

Give the user only the minimum rights required for the task.

Example:

If a user only needs to restart one service, do not give unrestricted root access.

## 9.9 Secure privilege escalation flow

```text
Identify task
 ↓
Determine required privilege
 ↓
Grant minimum necessary access
 ↓
Use sudo where appropriate
 ↓
Avoid unnecessary full root access
```

## 9.10 Useful commands

```bash
id username
su username
su -
sudo command
sudo -l
visudo
```

## 9.11 Common trap questions

**Does sudo make the user permanently root?**  
No. It normally elevates the specified command.

**Can every user use sudo?**  
No. The user must be authorized by sudo/system policy.

---

# 10. REMOTE ADMINISTRATION

## 10.1 SSH

SSH = Secure Shell.

Used for secure remote command-line access/admin.

Common port:

```text
22
```

Connect:

```bash
ssh username@server_ip
```

Example:

```bash
ssh admin@192.168.1.10
```

## 10.2 SSH key-based authentication

Uses:

```text
Public key + Private key
```

Concept:

```text
Client private key  → authentication → Server public key
```

Common public-key location on server:

```text
~/.ssh/authorized_keys
```

Security:

```text
Private key = protect
Public key  = can be shared/configured
```

## 10.3 SSH using a specific key

```bash
ssh -i ~/.ssh/id_rsa admin@192.168.1.10
```

`-i` = specify private key file.

## 10.4 SSH service

Depending on distribution:

```bash
systemctl status ssh
systemctl status sshd
```

## 10.5 RDP and VNC

```text
SSH = secure command-line remote administration
RDP = graphical remote desktop protocol
VNC = graphical remote access
```

## 10.6 Secure remote-access practices

- protect private SSH keys
- use strong authentication
- prefer key-based authentication where appropriate
- avoid unnecessary exposure
- restrict remote access where practical
- maintain remote-access software/configuration

## 10.7 SSH troubleshooting

Flow:

```text
Network connectivity
 ↓
Server reachable?
 ↓
SSH service running?
 ↓
Correct port/listening?
 ↓
Authentication problem?
 ↓
Firewall/access-control issue?
```

Useful:

```bash
ping server_ip
systemctl status ssh
systemctl status sshd
ss -tuln
```

---

# 11. STORAGE MANAGEMENT FUNDAMENTALS

## 11.1 Disk vs partition

```text
Disk      = physical/virtual storage device
Partition = section of disk
```

Example:

```text
Disk
├── Partition 1
├── Partition 2
└── Partition 3
```

## 11.2 Filesystem

Defines how files/directories are organized and stored.

Source specifically covers:

```text
ext4
XFS
```

Both are Linux filesystems.

## 11.3 See disks/partitions

```bash
lsblk
```

## 11.4 Partitioning tools

```bash
fdisk /dev/sdb
parted /dev/sdb
```

Used to create/delete/modify partitions.

Be careful: wrong operations can impact data.

## 11.5 Mounting

```bash
mount /dev/sdb1 /data
```

Concept:

```text
device/filesystem
      ↓
 mount point
      ↓
 accessible data
```

Unmount:

```bash
umount /data
umount /dev/sdb1
```

Why can `umount` fail?

- filesystem is busy
- processes are using it

## 11.6 df vs du

```text
df -h = filesystem-level usage
du    = file/directory usage
```

Example:

```bash
df -h
du -sh /var/log
du -sh /var/log/*
```

## 11.7 Full filesystem troubleshooting

```text
df -h
  ↓
identify full filesystem
  ↓
du -sh relevant directories/files
  ↓
investigate logs/application data
  ↓
fix cause carefully
```

Do not delete blindly.

## 11.8 Partition vs filesystem vs mount point

```text
Partition  = section of disk
Filesystem = data organization structure
Mount point= directory through which mounted filesystem is accessed
```

Example:

```text
/dev/sdb1 → ext4 → /data
```

## 11.9 New-disk workflow

```text
Identify disk
 ↓
Partition if needed
 ↓
Create filesystem
 ↓
Create mount point
 ↓
Mount
 ↓
Verify
```

Example after `/dev/sdb1` exists:

```bash
mkdir /data
mount /dev/sdb1 /data
lsblk
df -h
```

---

# 12. LVM — LOGICAL VOLUME MANAGER

## 12.1 Architecture

```text
Disk / Partition
      ↓
     PV
      ↓
     VG
      ↓
     LV
      ↓
Filesystem
      ↓
Mount point
```

## 12.2 PV

PV = Physical Volume.

Prepare disk/partition for LVM:

```bash
pvcreate /dev/sdb1
```

Inspect:

```bash
pvs
```

## 12.3 VG

VG = Volume Group.

Storage pool from one or more PVs:

```bash
vgcreate vgdata /dev/sdb1
```

Inspect:

```bash
vgs
```

## 12.4 LV

LV = Logical Volume.

Create:

```bash
lvcreate -L 10G -n lvdata vgdata
```

Inspect:

```bash
lvs
```

## 12.5 Complete creation flow

```bash
pvcreate /dev/sdb1
vgcreate vgdata /dev/sdb1
lvcreate -L 10G -n lvdata vgdata
mkfs.ext4 /dev/vgdata/lvdata
mkdir /data
mount /dev/vgdata/lvdata /data
```

## 12.6 Why LVM?

Main concept:

- flexible logical storage management
- storage pool abstraction
- logical volumes can be resized more flexibly than fixed partitions

## 12.7 Resize an LV

Extend:

```bash
lvextend -L +5G /dev/vgdata/lvdata
```

For ext4, filesystem resize example:

```bash
resize2fs /dev/vgdata/lvdata
```

**Critical distinction:**

```text
LV size increase ≠ automatically the same as filesystem size increase
```

The filesystem may also need resizing depending on filesystem and operation.

## 12.8 LVM snapshot

A point-in-time view of an LV.

Useful for certain backup/testing/recovery workflows.

But:

```text
snapshot ≠ automatically an independent backup
```

## 12.9 Inspect

```bash
pvs
vgs
lvs
```

## 12.10 YaST

Source connects YaST to graphical/system administration in SUSE environments.

Know:

```text
CLI → command-based administration
YaST → graphical/SUSE-oriented administration
```

## 12.11 LVM full filesystem scenario

```bash
df -h
lvs
vgs
```

If the VG has free space:

```text
extend LV
 ↓
resize filesystem appropriately
```

---

# 13. BTRFS

## 13.1 What is Btrfs?

Linux filesystem with features including:

- subvolumes
- snapshots

## 13.2 Subvolume

A separately manageable filesystem tree inside a Btrfs filesystem.

Create:

```bash
btrfs subvolume create /data/subvol1
```

List:

```bash
btrfs subvolume list /
```

## 13.3 Snapshot

A point-in-time view of a subvolume.

Create:

```bash
btrfs subvolume snapshot /data/subvol1 /data/snapshot1
```

Read-only:

```bash
btrfs subvolume snapshot -r /data/subvol1 /data/snapshot1
```

Delete:

```bash
btrfs subvolume delete /data/snapshot1
```

## 13.4 Snapshot vs backup

```text
Snapshot = point-in-time filesystem state
Backup   = independent copy intended for recovery
```

Do not claim a snapshot is automatically a complete backup.

## 13.5 Btrfs vs LVM snapshot

```text
Btrfs snapshot → Btrfs subvolume/filesystem
LVM snapshot   → LVM logical volume
```

## 13.6 Rollback scenario

```text
List snapshots
 ↓
identify appropriate point-in-time state
 ↓
use snapshot management/recovery workflow
```

Core command:

```bash
btrfs subvolume list /
```

---

# 14. NETWORK CONFIGURATION

## 14.1 IP basics

Know:

- IP address
- subnet mask/CIDR
- default gateway
- DNS
- interface
- IPv4
- static IP vs DHCP

Example:

```text
IP      192.168.1.10
Mask    255.255.255.0
Gateway 192.168.1.1
DNS     8.8.8.8
```

## 14.2 Static vs DHCP

```text
DHCP   = automatically assigned configuration
Static = administrator manually sets configuration
```

The source notes predictable addressing makes static configuration common for servers.

## 14.3 NetworkManager

Status:

```bash
systemctl status NetworkManager
```

Start:

```bash
systemctl start NetworkManager
```

Enable:

```bash
systemctl enable NetworkManager
```

## 14.4 nmcli

Device status:

```bash
nmcli device status
```

Connections:

```bash
nmcli connection show
```

Connection details:

```bash
nmcli connection show "connection-name"
```

Device details:

```bash
nmcli device show
```

## 14.5 Static IP example

```bash
nmcli connection modify "Wired connection 1" \
ipv4.addresses 192.168.1.100/24 \
ipv4.gateway 192.168.1.1 \
ipv4.dns 8.8.8.8 \
ipv4.method manual

nmcli connection up "Wired connection 1"
ip addr
```

## 14.6 Hostname

```bash
hostname
hostnamectl
hostnamectl set-hostname server01
```

## 14.7 DNS

DNS translates names to IP addresses.

Useful checks:

```bash
nmcli device show
```

Potential file:

```text
/etc/resolv.conf
```

Test:

```bash
nslookup google.com
```

## 14.8 Network troubleshooting

Interface/IP:

```bash
ip addr
```

Routes:

```bash
ip route
```

Connectivity:

```bash
ping 8.8.8.8
```

DNS:

```bash
ping google.com
nslookup google.com
```

Listening sockets:

```bash
ss -tuln
```

Flow:

```text
Interface
 ↓
IP
 ↓
Gateway
 ↓
Routing
 ↓
Ping gateway
 ↓
Ping external IP
 ↓
DNS
 ↓
Firewall
```

Key scenario:

**Can ping `8.8.8.8` but not `google.com`?**  
Likely DNS resolution issue.

---

# 15. SOFTWARE MANAGEMENT

## 15.1 RPM

RPM manages individual RPM packages.

Install:

```bash
rpm -ivh package.rpm
```

Upgrade/install:

```bash
rpm -Uvh package.rpm
```

Remove:

```bash
rpm -e package-name
```

Query installed:

```bash
rpm -q package-name
```

List all installed:

```bash
rpm -qa
```

Package info:

```bash
rpm -qi package-name
```

Files installed:

```bash
rpm -ql package-name
```

## 15.2 YUM

Install:

```bash
yum install nginx
```

Remove:

```bash
yum remove nginx
```

Update:

```bash
yum update
```

Search:

```bash
yum search nginx
```

Info:

```bash
yum info nginx
```

Installed:

```bash
yum list installed
```

## 15.3 Zypper

Common in SUSE/openSUSE.

```bash
zypper install nginx
zypper remove nginx
zypper refresh
zypper update
zypper search nginx
zypper info nginx
```

## 15.4 RPM vs YUM/Zypper

```text
RPM       = direct individual-package management
YUM/Zypper= repository-based higher-level package management + dependency handling
```

## 15.5 Repository

Managed source containing:

- packages
- package metadata

Conceptual:

```text
Repository
 ↓
Package manager
 ↓
Package + metadata
 ↓
Dependency resolution
 ↓
Install/update
```

## 15.6 Dependencies

Applications can require other packages/libraries.

YUM/Zypper can resolve dependencies automatically.

Direct RPM can fail if dependencies are missing.

## 15.7 YUM server/client

Architecture:

```text
YUM Server
  ↓
Repository of RPMs + metadata
  ↓ network
Clients
```

Client points to repository configuration and downloads packages/metadata.

## 15.8 Graphical package management

GUI package managers support the same conceptual workflow:

```text
Search → select → resolve dependencies → install/remove/update
```

---

# 16. SYSTEM LOGGING & MONITORING

## 16.1 Purpose of logs

Use logs to understand:

- what happened
- when it happened
- what service/process was involved
- whether errors occurred

Common information:

- authentication events
- service failures
- kernel messages
- system events
- application errors

## 16.2 `/var/log`

Major location for log files.

Examples can include:

```text
/var/log/messages
/var/log/secure
/var/log/syslog
```

Exact files depend on distribution/configuration.

## 16.3 Kernel logs

```bash
dmesg
dmesg | tail
```

Useful for:

- hardware problems
- drivers
- boot issues
- device problems
- kernel errors

## 16.4 Syslog

Traditional Linux logging mechanism/framework for collecting/storing system/application messages.

## 16.5 journald

systemd-journald is systemd's logging service.

Main tool:

```bash
journalctl
```

## 16.6 journalctl

All:

```bash
journalctl
```

Recent/end:

```bash
journalctl -e
```

Follow live:

```bash
journalctl -f
```

Specific service:

```bash
journalctl -u ssh
journalctl -u NetworkManager
```

Current boot:

```bash
journalctl -b
```

## 16.7 Log rotation

Logs continuously grow.

`logrotate` helps:

- control log size
- prevent excessive disk usage
- retain historical logs for defined periods
- archive/compress old logs

## 16.8 SOSReport / supportconfig

These tools collect system/support information such as:

- logs
- configuration
- diagnostic data
- system information

Used for troubleshooting/support.

## 16.9 Service failure troubleshooting

```text
systemctl status <service>
        ↓
journalctl -u <service>
        ↓
/var/log relevant logs
        ↓
dmesg if hardware/kernel-related
        ↓
identify root cause
```

## 16.10 Logs filling disk

```bash
df -h
du -sh /var/log/*
```

Investigate excessive growth or missing log rotation.

## 16.11 journalctl vs /var/log

```text
/var/log    = traditional/stored log files
journalctl  = query systemd journal
```

---

# 17. BOOTLOADER, INSTALLATION, TIME SYNC & TROUBLESHOOTING

## 17.1 GRUB2

GRUB2 = bootloader that loads the Linux kernel and initial boot environment.

Boot flow:

```text
BIOS/UEFI
 ↓
GRUB2
 ↓
Linux kernel
 ↓
initramfs
 ↓
systemd
 ↓
services/login
```

GRUB2 can:

- provide boot menu
- load kernel
- load initramfs
- pass kernel parameters
- select boot entries

## 17.2 Why GRUB matters

Incorrect GRUB configuration/boot parameters can prevent proper boot.

For this source: understand basics, not deep customization.

## 17.3 AutoYaST vs Anaconda

```text
AutoYaST = automated SUSE installation/configuration
Anaconda = installation framework used by Red Hat-family distributions
```

Why automate?

```text
Manual repeated installs → slow + inconsistent
Automated deployment    → faster + consistent
```

## 17.4 Chrony

Chrony is used for time synchronization.

Useful commands:

```bash
systemctl status chronyd
chronyc tracking
chronyc sources
```

Time matters for:

- logs
- authentication
- certificates
- distributed systems
- troubleshooting

## 17.5 Incorrect server time

Check:

```bash
systemctl status chronyd
chronyc tracking
chronyc sources
```

Investigate:

- service
- configured sources
- synchronization state

## 17.6 Basic monitoring

CPU/process:

```bash
top
ps aux
```

Memory:

```bash
free -h
```

Disk:

```bash
df -h
du -sh /var/log/*
```

Network:

```bash
ip addr
ip route
ss -tuln
```

Connectivity:

```bash
ping <gateway>
```

## 17.7 Structured troubleshooting

```text
Identify symptom
 ↓
Check system/service status
 ↓
Check logs
 ↓
Check CPU/RAM/disk
 ↓
Check network if relevant
 ↓
Identify root cause
 ↓
Apply fix
 ↓
Verify
```

Slow server:

```bash
top
free -h
df -h
ps aux
```

Service problem:

```bash
systemctl status <service>
journalctl -u <service>
```

Network problem:

```bash
ip addr
ip route
ping <gateway>
ss -tuln
```

---

# 18. ADVANCED SYSTEM ADMINISTRATION

## 18.1 Security management

Linux security administration involves controlling:

- user access
- file permissions
- authentication/authorization
- network/service exposure
- security-related configuration

Can be managed via CLI and available graphical tools.

## 18.2 Snapper

Snapper manages filesystem snapshots, especially Btrfs snapshots.

Concept:

```text
Btrfs
 ↓
Snapper
 ↓
create/manage snapshots
 ↓
rollback/recovery
```

Important:

```text
snapshot ≠ independent backup
```

## 18.3 Shared libraries

Reusable compiled code used by multiple programs.

Common pattern:

```text
libsomething.so
```

`.so` generally indicates shared object library.

Concept:

```text
Program A ─┐
Program B ─┼→ Shared Library
Program C ─┘
```

## 18.4 System health

Monitor:

- CPU
- RAM
- disk
- processes
- network
- services
- logs

Commands:

```bash
top
free -h
df -h
ps aux
ip addr
ss -tuln
```

## 18.5 System optimization

Source emphasizes **methodology**, not blind tuning.

```text
Measure current state
 ↓
Identify bottleneck
 ↓
Apply targeted change
 ↓
Measure again
 ↓
Verify improvement
```

For slow server ask:

- CPU high?
- RAM exhausted?
- disk full?
- high I/O?
- problematic process?
- network issue?

## 18.6 cgroups

cgroups = control groups.

Used to organize/process-control and limit resource usage.

Resources in source:

- CPU
- memory
- I/O

Concept:

```text
Processes
 ↓
cgroup
 ↓
resource control/limits
```

Scenario: service consumes too much CPU → identify service/process → consider cgroup resource controls.

## 18.7 Critical comparisons

```text
Snapshot    = point-in-time state
Backup      = separate copy

Monitoring  = observe system
Optimization= change system to improve behavior

Application = uses functionality
Shared lib  = provides reusable functionality

Snapper     = snapshot management
Btrfs       = filesystem
cgroups     = resource control
```

---

# 19. ENCRYPTION & SECURITY

## 19.1 TLS

TLS = Transport Layer Security.

Provides:

```text
Confidentiality
Integrity
Authentication
```

SSL = older protocol family; TLS is the modern successor.

## 19.2 HTTPS

```text
HTTP + TLS = HTTPS
```

HTTPS = HTTP communication protected by TLS.

## 19.3 TLS certificate

Used as part of server authentication.

Concept:

```text
Server
 ↓ certificate
Client verifies identity
 ↓
secure TLS communication
```

Certificate is associated with server identity and contains a public key.

## 19.4 OpenSSL

OpenSSL = toolkit for cryptographic/TLS-related tasks.

Source lists uses:

- keys
- certificates
- encryption/decryption
- hashing
- TLS testing

Check version:

```bash
openssl version
```

## 19.5 Symmetric vs asymmetric

Symmetric:

```text
one shared secret key
```

Asymmetric:

```text
public key + private key
```

Remember:

```text
Symmetric = one shared secret
Asymmetric = key pair
```

## 19.6 GPG

GPG = GNU Privacy Guard.

Used for:

- encryption
- decryption
- cryptographic key management
- key distribution

Generate key:

```bash
gpg --full-generate-key
```

List public:

```bash
gpg --list-keys
```

List secret/private:

```bash
gpg --list-secret-keys
```

## 19.7 GPG secure file-sharing workflow

```text
Bob creates key pair
 ↓
Bob shares public key
 ↓
Alice imports Bob's public key
 ↓
Alice encrypts file using Bob's public key
 ↓
Alice sends encrypted file
 ↓
Bob decrypts with Bob's private key
```

Encrypt:

```bash
gpg --encrypt --recipient bob@example.com file.txt
```

Decrypt:

```bash
gpg --decrypt file.txt.gpg
```

Export public key:

```bash
gpg --export --armor bob@example.com > bob-public.asc
```

Import:

```bash
gpg --import bob-public.asc
```

## 19.8 Key security

```text
Public key  = share
Private key = protect
```

If a private key is compromised, operations associated with that key may be compromised depending on its protection/usage.

## 19.9 OpenSSL vs GPG

Do not describe them as interchangeable.

Source's simple memory:

```text
TLS      → secure network communication
OpenSSL  → cryptographic/TLS toolkit
GPG      → encryption and OpenPGP key management
```

---

# 20. SHELL SCRIPTING (BASH)

## 20.1 Shebang

Typical first line:

```bash
#!/bin/bash
```

Specifies interpreter.

## 20.2 Variables

Correct:

```bash
name="Kartik"
echo "$name"
```

Incorrect:

```bash
name = "Kartik"
```

Bash variable assignments do not allow spaces around `=`.

## 20.3 Commands in scripts

Scripts can call normal commands:

```bash
hostname
date
df -h
free -h
```

## 20.4 Command substitution

```bash
hostname=$(hostname)
current_date=$(date)
```

Store command output in variable.

## 20.5 User input

```bash
read name
echo "Hello $name"
```

Or:

```bash
read -p "Enter username: " username
echo "You entered: $username"
```

## 20.6 if

```bash
if [ condition ]; then
    command
fi
```

Example:

```bash
if [ -f /etc/passwd ]; then
    echo "File exists"
fi
```

## 20.7 if-else

```bash
if [ -f /etc/passwd ]; then
    echo "File exists"
else
    echo "File does not exist"
fi
```

## 20.8 if-elif-else

```bash
if [ condition1 ]; then
    command
elif [ condition2 ]; then
    command
else
    command
fi
```

Source example concept: checking disk usage thresholds.

## 20.9 Loops

for:

```bash
for user in alice bob charlie
do
    echo "$user"
done
```

while:

```bash
count=1
while [ "$count" -le 5 ]
do
    echo "$count"
    count=$((count + 1))
done
```

Uses:

- multiple users
- files
- servers
- services
- repeated values

## 20.10 Arrays

Create:

```bash
servers=("server1" "server2" "server3")
```

One element:

```bash
echo "${servers[0]}"
```

All:

```bash
echo "${servers[@]}"
```

Count:

```bash
echo "${#servers[@]}"
```

Loop:

```bash
for server in "${servers[@]}"
do
    echo "$server"
done
```

## 20.11 Functions

```bash
check_disk() {
    df -h
}

check_disk
```

Example:

```bash
show_system_info() {
    hostname
    uptime
    free -h
    df -h
}

show_system_info
```

Why functions?

- organization
- reuse

## 20.12 File tests

```bash
[ -f file.txt ]      # file exists
[ -d /var/log ]       # directory exists
[ -r file.txt ]       # readable
[ -w file.txt ]       # writable
[ -x script.sh ]      # executable
```

## 20.13 Numeric comparisons

```text
-eq = equal
-ne = not equal
-gt = greater than
-lt = less than
```

## 20.14 File comparisons

```text
-ef = same file
-nt = file1 newer than file2
-ot = file1 older than file2
```

## 20.15 Practical admin script

Check service:

```bash
#!/bin/bash

service="sshd"

if systemctl is-active --quiet "$service"; then
    echo "$service is running"
else
    echo "$service is not running"
fi
```

Check file:

```bash
#!/bin/bash

file="/etc/passwd"

if [ -f "$file" ]; then
    echo "$file exists"
else
    echo "$file does not exist"
fi
```

Check `/var/log`:

```bash
#!/bin/bash

if [ -d /var/log ]; then
    echo "/var/log exists"
else
    echo "/var/log does not exist"
fi
```

User input:

```bash
#!/bin/bash

read -p "Enter username: " username
echo "Username: $username"
```

Multiple servers:

```bash
servers=("server1" "server2" "server3")

for server in "${servers[@]}"
do
    echo "Checking $server"
done
```

## 20.16 Command options in scripts

Normal options still apply:

```bash
ls -l
```

Understand command + option use inside scripts.

---

# 21. HARDWARE & DRIVERS

## 21.1 Hardware information commands

CPU:

```bash
lscpu
```

Memory:

```bash
free -h
cat /proc/meminfo
```

PCI:

```bash
lspci
lspci | grep -i network
```

USB:

```bash
lsusb
```

Storage/block:

```bash
lsblk
```

Detailed hardware:

```bash
sudo lshw
sudo lshw -short
```

## 21.2 Driver concept

A driver is the software interface between Linux kernel and hardware.

Concept:

```text
Application
 ↓
Linux kernel
 ↓
Driver
 ↓
Hardware
```

## 21.3 Kernel modules

Many drivers are implemented as kernel modules.

List:

```bash
lsmod
```

Load:

```bash
sudo modprobe module_name
```

Remove:

```bash
sudo modprobe -r module_name
```

Information:

```bash
modinfo module_name
```

## 21.4 Driver troubleshooting

Flow:

```text
Identify hardware
 ↓
Check if Linux detects it
 ↓
Identify driver/module
 ↓
Check module loaded
 ↓
Check kernel messages
 ↓
Investigate driver
```

Useful:

```bash
lspci
lsusb
lsmod
modinfo <module>
dmesg
ip link
```

## 21.5 Network adapter example

```bash
lspci | grep -i network
ip link
lsmod
dmesg | grep -i network
```

Questions to answer:

```text
Hardware detected?
Driver available?
Driver loaded?
Interface created?
```

---

# 22. ADVANCED NETWORKING

## 22.1 Linux bridge

Software-based Layer 2 device.

Concept:

```text
Interface 1 ─┐
             ├─ Bridge
Interface 2 ─┘
```

Think:

```text
Bridge ≈ software Layer 2 switching
```

Useful in virtual networking/VM scenarios.

## 22.2 veth pair

Virtual Ethernet pair behaves like a software cable:

```text
veth0 <======> veth1
```

Traffic entering one end can emerge at the other.

Useful for:

- namespaces
- containers
- virtual networking

## 22.3 VLAN

VLAN = Virtual Local Area Network.

Provides logical network separation over shared physical infrastructure.

Know:

- VLAN ID
- 802.1Q tagging
- access port
- trunk port

For this source, focus on concept/Linux usage rather than deep enterprise switching.

## 22.4 Network namespace

Provides isolated network stack with its own:

- interfaces
- routes
- IP addresses
- network configuration

## 22.5 veth + namespace relationship

```text
Namespace isolation → network namespace
Virtual connection   → veth pair
Layer 2 connection   → bridge
```

A veth pair can connect namespaces or a namespace to host networking.

## 22.6 IPv6

IPv4:

```text
32-bit
```

IPv6:

```text
128-bit
```

Example:

```text
192.168.1.10
2001:db8::10
```

Know:

- hexadecimal notation
- addressing
- assignment
- routing
- interface configuration

## 22.7 IPv6 commands

```bash
ip -6 addr
ip -6 route
ping -6 <IPv6-address>
```

## 22.8 Namespace command concept

```bash
ip netns
```

## 22.9 Troubleshooting

VLAN interface not communicating:

```text
Interface exists?
 ↓
Correct VLAN configuration?
 ↓
Correct IP?
 ↓
Correct route?
 ↓
Connectivity test
 ↓
Relevant network configuration
```

Namespace issue:

```text
Namespace exists?
 ↓
Interface exists?
 ↓
veth connected?
 ↓
Correct IP?
 ↓
Routes?
 ↓
Bridge/routing?
```

## 22.10 Memory line

```text
Bridge   = Layer 2 connectivity
veth     = virtual Ethernet link
VLAN     = logical network separation
Namespace= network isolation
IPv6     = 128-bit IP
```

---

# 23. ADVANCED STORAGE ADMINISTRATION

## 23.1 iSCSI

iSCSI = Internet Small Computer Systems Interface.

Provides remote block storage over IP.

Concept:

```text
Linux server
   ↓
IP network
   ↓
iSCSI storage
```

Server can see remote storage as a block device.

## 23.2 Target vs initiator

```text
Initiator = client/server that accesses storage
Target    = system that provides storage
```

Memory:

```text
Initiator → requests/accesses
Target    → provides
```

## 23.3 iSCSI workflow

Target:

```text
Create/provide storage
 ↓
Configure target
 ↓
Make storage available
```

Initiator:

```text
Configure initiator
 ↓
Discover target
 ↓
Authenticate if required
 ↓
Login
 ↓
Linux detects block device
 ↓
Use storage
```

## 23.4 Discovery vs login

```text
Discovery = find available targets
Login     = establish session to selected target
```

## 23.5 iSCSI troubleshooting

If disk is not visible:

```text
Network connectivity
 ↓
Target reachable?
 ↓
Discovery works?
 ↓
Login/session established?
 ↓
Block device visible?
 ↓
Storage configuration correct?
```

Useful:

```bash
ip addr
ip route
lsblk
```

Also inspect system logs.

## 23.6 Multipath I/O

Provides multiple paths between server and storage.

Purpose:

- redundancy
- availability
- path failure tolerance

Concept:

```text
Server
 ├── Path 1 ──┐
 └── Path 2 ──┤→ Storage
```

If one path fails, another can continue.

## 23.7 Device Mapper Multipath

Linux device-mapper can combine multiple underlying paths into a logical multipath device.

Concept:

```text
Path 1 \
        → Multipath logical device
Path 2 /
```

## 23.8 Multipath vs RAID

```text
Multipath = path redundancy
RAID      = disk/data-layout redundancy
```

They solve different problems.

## 23.9 iSCSI unavailable disk scenario

Check:

1. network
2. target reachability
3. iSCSI session/login
4. `lsblk`
5. logs

Core answer:

“iSCSI provides remote block storage over IP; the initiator connects to the target. Multipathing provides multiple paths for redundancy and availability.”

---

# 24. CENTRALIZED AUTHENTICATION

## 24.1 PAM

PAM = Pluggable Authentication Modules.

Provides a common authentication framework.

Concept:

```text
Application/Login
 ↓
PAM
 ↓
Authentication mechanism
 ↓
User authenticated
```

## 24.2 PAM configuration

Common directory:

```text
/etc/pam.d/
```

Important trap:

> PAM is an authentication framework; it does not itself store user accounts.

## 24.3 Authentication vs authorization

```text
Authentication = Who are you?
Authorization  = What are you allowed to do?
```

## 24.4 SSSD

SSSD = System Security Services Daemon.

Used for integration with centralized identity/authentication sources.

Concept:

```text
Linux client
 ↓
SSSD
 ↓
Central identity service
```

Useful in multi-server organizations.

## 24.5 PAM vs SSSD

```text
PAM  = authentication framework
SSSD = centralized identity/authentication integration
```

They can work together.

## 24.6 PAM + SSSD relationship

Simplified:

```text
User
 ↓
Linux application/login
 ↓
PAM
 ↓
SSSD
 ↓
Central identity source
 ↓
Authentication / identity information
```

## 24.7 Central-login troubleshooting

If a centralized user cannot log in:

```text
Determine local vs centralized problem
 ↓
Check PAM configuration
 ↓
Check SSSD
 ↓
Check identity-service connectivity
 ↓
Check logs
```

Service:

```bash
systemctl status sssd
```

## 24.8 Why centralized authentication?

Compared with local accounts on every server, central identity can simplify:

- identity management
- consistent access
- multi-server administration

---

# 25. ADVANCED SOFTWARE MANAGEMENT

## 25.1 Software repository

Managed location containing:

- software packages
- package metadata

Concept:

```text
Repository
 ↓
Metadata
 ↓
Package manager
 ↓
Client
 ↓
Install/update
```

## 25.2 Why local/internal repository?

Possible administrative benefits:

- centralized package availability
- controlled software sources
- consistent package versions
- fewer repeated external downloads

Architecture:

```text
Upstream repository
       ↓
Internal repository
   ↓   ↓   ↓
Client1 Client2 Client3
```

## 25.3 createrepo

`createrepo` is used to generate repository metadata for RPM packages.

Important:

```text
createrepo ≠ package builder
createrepo = repository metadata generator
```

Workflow:

```text
RPM packages
 ↓
createrepo
 ↓
repository metadata
 ↓
clients can use repository
```

## 25.4 Repository management workflow

```text
Add packages
 ↓
Generate/update metadata
 ↓
Publish repository
 ↓
Configure clients
 ↓
Test installation/update
```

## 25.5 libzypp

Package-management library used in the SUSE ecosystem.

Relationship:

```text
Administrator
 ↓
Zypper
 ↓
libzypp
 ↓
Repository/package handling
```

Memory:

```text
Zypper = package-management CLI
libzypp = underlying package-management library
```

## 25.6 RMT

RMT = Repository Mirroring Tool.

Purpose:

- mirror repositories
- provide internal repository source
- support client/update management

Architecture:

```text
Upstream SUSE repositories
          ↓
         RMT
          ↓
  Internal mirror
   ↓     ↓     ↓
 C1     C2    C3
```

## 25.7 Why RMT?

Instead of each client directly downloading from upstream:

```text
Internet
 ↓
RMT
 ↓
Internal clients
```

This gives a controlled internal source and can reduce repeated external downloads.

## 25.8 RMT client management

Concept:

```text
RMT server
 ↓
registered clients
 ↓
available repositories
 ↓
software updates
```

## 25.9 Repository use vs repository mirroring

```text
Normal repository use:
Client → Repository

Mirroring:
Upstream Repository → Mirror Server → Clients
```

## 25.10 Final comparison

```text
Repository   = packages + metadata source
createrepo   = creates repository metadata
libzypp      = SUSE package-management library
RMT          = repository mirroring + client/update management
```

---

# RAPID-FIRE COMPARISONS

## Linux essentials

```text
grep vs find
grep = text/pattern search
find = file/directory search
```

```text
> vs >>
>  = overwrite
>> = append
```

```text
/ vs /root
/     = filesystem root
/root = root user's home
```

```text
absolute vs relative
absolute = begins at /
relative = relative to current directory
```

## Users and permissions

```text
UID vs GID
UID = user
GID = group
```

```text
passwd vs shadow
passwd = account information
shadow = password/authentication information
```

```text
chmod vs chown vs chgrp
chmod = permissions
chown = owner/group ownership
chgrp = group ownership
```

```text
normal permissions vs ACL
normal = owner/group/others
ACL    = additional granular users/groups
```

```text
SUID vs SGID vs sticky
SUID   = file owner privilege for executable
SGID   = group privilege for executable + directory group inheritance
sticky = restrict shared-directory deletion/rename of other users' files
```

## Processes

```text
program vs process
program = code/executable
process = running instance
```

```text
ps vs top
ps  = snapshot
top = dynamic
```

```text
kill vs killall
kill    = PID
killall = process name
```

```text
nice vs renice
nice   = start with niceness
renice = change running process niceness
```

```text
foreground vs background
foreground = active terminal
background = runs without occupying terminal
```

## Services

```text
start vs enable
start  = now
enable = boot
```

```text
stop vs disable
stop    = stop now
disable = prevent normal boot start
```

```text
reload vs restart
reload  = reread config if supported
restart = full service restart
```

## Scheduling

```text
cron = recurring
at   = one-time
systemd timer = systemd-integrated scheduling
```

## Privilege

```text
su vs sudo
su   = switch user
sudo = execute authorized command with elevated privilege
```

```text
sudo vs root shell
sudo      = controlled command-level elevation
su - root = broad administrative shell
```

## Remote access

```text
SSH = secure CLI remote administration
RDP = graphical remote desktop
VNC = graphical remote access
```

## Storage

```text
Disk = physical/virtual device
Partition = disk section
Filesystem = data organization
Mount point = directory exposing mounted filesystem
```

```text
df vs du
df = filesystem usage
du = file/directory usage
```

```text
PV → VG → LV
PV = physical volume
VG = volume group/storage pool
LV = logical volume
```

```text
LVM snapshot vs Btrfs snapshot
LVM snapshot = LV-level
Btrfs snapshot = subvolume/filesystem-level
```

```text
snapshot vs backup
snapshot = point-in-time state
backup = independent copy
```

## Networking

```text
Bridge = Layer 2 connectivity
VLAN   = logical network separation
veth   = virtual Ethernet link
namespace = isolated network stack
```

```text
IPv4 = 32-bit
IPv6 = 128-bit
```

```text
Initiator vs Target
Initiator = connects/accesses iSCSI storage
Target    = provides storage
```

```text
Multipath vs RAID
Multipath = path redundancy
RAID      = disk/data redundancy
```

## Authentication/security

```text
Authentication = Who are you?
Authorization  = What are you allowed to do?
```

```text
PAM  = authentication framework
SSSD = centralized identity/authentication integration
```

```text
Symmetric = one shared secret
Asymmetric = public + private key
```

```text
TLS      = secure network communication protocol
OpenSSL  = cryptographic/TLS toolkit
GPG      = encryption/OpenPGP key management
```

## Package management

```text
RPM = individual package management
YUM/Zypper = repository-based higher-level package management + dependencies
```

```text
Repository = packages + metadata
createrepo = generates repository metadata
libzypp    = SUSE package-management library
RMT        = repository mirroring/client-update infrastructure
```

---

# TROUBLESHOOTING FLOWS TO MEMORIZE

## 1. Service not working

```text
systemctl status <service>
        ↓
journalctl -u <service>
        ↓
configuration/error details
        ↓
/var/log if relevant
        ↓
dmesg if kernel/hardware related
        ↓
fix
        ↓
verify
```

## 2. Server is slow

```text
top
 ↓
CPU?
 ↓
free -h
 ↓
RAM?
 ↓
df -h
 ↓
disk space?
 ↓
ps aux
 ↓
problem process?
 ↓
logs
 ↓
network if needed
```

## 3. Disk is full

```text
df -h
 ↓
which filesystem?
 ↓
du -sh <large directories>
 ↓
check /var/log
 ↓
check application data
 ↓
investigate log rotation
 ↓
clean/fix cause carefully
```

## 4. Network cannot access Internet

```text
ip addr
 ↓
IP valid?
 ↓
ip route
 ↓
gateway exists?
 ↓
ping gateway
 ↓
ping 8.8.8.8
 ↓
DNS: nslookup google.com
 ↓
ss -tuln / firewall if needed
```

## 5. SSH failure

```text
ping server
 ↓
systemctl status ssh/sshd
 ↓
ss -tuln
 ↓
correct port/listening?
 ↓
authentication
 ↓
keys/authorized_keys
 ↓
firewall/access control
```

## 6. Permission denied

```text
id username
 ↓
ls -l file
 ↓
owner/group?
 ↓
group membership?
 ↓
getfacl file
 ↓
directory x/traversal?
 ↓
fix permissions/ownership/ACL
```

## 7. User creation + group

```bash
useradd testuser
passwd testuser
groupadd admins
usermod -aG admins testuser
id testuser
groups testuser
```

## 8. New disk

```text
lsblk
 ↓
partition if needed
 ↓
mkfs.*
 ↓
mkdir /data
 ↓
mount
 ↓
df -h
```

## 9. LVM storage expansion

```text
df -h
 ↓
lvs
 ↓
vgs
 ↓
VG has free space?
 ↓
lvextend
 ↓
filesystem resize if required
 ↓
df -h
```

## 10. Incorrect time

```bash
systemctl status chronyd
chronyc tracking
chronyc sources
```

## 11. Hardware/driver problem

```text
lspci / lsusb
 ↓
device detected?
 ↓
lsmod
 ↓
modinfo
 ↓
dmesg
 ↓
ip link if networking
```

## 12. iSCSI disk unavailable

```text
Network
 ↓
Target reachable
 ↓
Discovery
 ↓
Login/session
 ↓
lsblk
 ↓
logs
```

## 13. Centralized login failure

```text
local vs central
 ↓
/etc/pam.d/
 ↓
systemctl status sssd
 ↓
identity-service connectivity
 ↓
logs
```

## 14. Btrfs rollback

```text
list subvolumes/snapshots
 ↓
identify correct point in time
 ↓
snapshot/rollback workflow
 ↓
verify
```

---

# TECHNICAL-ROUND COMMAND DRILL

## Drill 1 — Filesystem

```bash
pwd
ls -la
cd /etc
cd ..
mkdir test
touch test/file.txt
cp test/file.txt test/backup.txt
mv test/backup.txt test/renamed.txt
rm test/file.txt
```

## Drill 2 — Users/groups

```bash
useradd testuser
passwd testuser
groupadd admins
usermod -aG admins testuser
id testuser
groups testuser
grep '^testuser:' /etc/passwd
userdel testuser
```

## Drill 3 — Permissions/ACL

```bash
touch test.txt
ls -l test.txt
chmod 644 test.txt
chmod u+x test.txt
chown testuser test.txt
chgrp admins test.txt
getfacl test.txt
setfacl -m u:user2:rw test.txt
getfacl test.txt
```

## Drill 4 — Services

```bash
systemctl status ssh
systemctl start ssh
systemctl stop ssh
systemctl restart ssh
systemctl enable ssh
systemctl disable ssh
systemctl enable --now ssh
systemctl get-default
```

## Drill 5 — Processes

```bash
ps
ps aux
top
ps aux | grep ssh
kill PID
jobs
fg
bg
nice -n 10 command
renice 10 -p PID
```

## Drill 6 — Scheduling

```bash
crontab -l
crontab -e
```

Example:

```text
0 2 * * * /path/to/script.sh
```

One-time:

```bash
at 23:00
```

## Drill 7 — SSH

```bash
ssh admin@192.168.1.10
ssh -i ~/.ssh/id_rsa admin@192.168.1.10
ss -tuln
```

## Drill 8 — Storage

```bash
lsblk
df -h
du -sh /var/log
fdisk /dev/sdb
parted /dev/sdb
mount /dev/sdb1 /data
umount /data
```

## Drill 9 — LVM

```bash
pvcreate /dev/sdb1
pvs
vgcreate vgdata /dev/sdb1
vgs
lvcreate -L 10G -n lvdata vgdata
lvs
mkfs.ext4 /dev/vgdata/lvdata
mkdir /data
mount /dev/vgdata/lvdata /data
lvextend -L +5G /dev/vgdata/lvdata
resize2fs /dev/vgdata/lvdata
```

## Drill 10 — Btrfs

```bash
btrfs subvolume create /data/subvol1
btrfs subvolume list /
btrfs subvolume snapshot /data/subvol1 /data/snapshot1
btrfs subvolume snapshot -r /data/subvol1 /data/snapshot1
btrfs subvolume delete /data/snapshot1
```

## Drill 11 — Networking

```bash
ip addr
ip route
ping 8.8.8.8
nslookup google.com
ss -tuln
nmcli device status
nmcli connection show
nmcli device show
hostnamectl
```

## Drill 12 — Logs

```bash
dmesg
dmesg | tail
journalctl
journalctl -f
journalctl -b
journalctl -u ssh
df -h
du -sh /var/log/*
```

## Drill 13 — Time

```bash
systemctl status chronyd
chronyc tracking
chronyc sources
```

## Drill 14 — Hardware

```bash
lscpu
free -h
cat /proc/meminfo
lspci
lsusb
lsblk
sudo lshw -short
lsmod
modprobe module_name
modinfo module_name
dmesg
```

## Drill 15 — Crypto

```bash
openssl version
gpg --full-generate-key
gpg --list-keys
gpg --list-secret-keys
gpg --export --armor bob@example.com > bob-public.asc
gpg --import bob-public.asc
gpg --encrypt --recipient bob@example.com file.txt
gpg --decrypt file.txt.gpg
```

---

# LAST-MINUTE MEMORY SHEET

```text
Bash            = Linux command shell
/               = filesystem root
/root           = root user's home
/etc            = configuration
/var            = changing data
/var/log        = logs
/home           = normal-user homes
/dev            = device files
/proc           = process/system information
/boot           = boot files

UID             = user identifier
GID             = group identifier
passwd          = account data
shadow          = password/auth data

rwx             = 4/2/1
755             = rwxr-xr-x
644             = rw-r--r--
chmod           = permissions
chown           = ownership
chgrp           = group ownership
ACL             = granular extra access
SUID            = owner privilege for executable
SGID            = group privilege + directory inheritance
sticky          = shared-dir deletion protection

ps              = process snapshot
top             = live processes
kill            = send signal by PID
killall         = by name
nice            = start priority
renice          = change running priority

systemd         = service/system manager
systemctl       = systemd CLI
start           = now
enable          = boot
stop            = now
disable         = boot configuration
reload          = reread config (if supported)
restart         = restart service
target          = system state

cron            = recurring
at              = one-time
systemd timer   = systemd scheduling

su              = switch user
sudo            = authorized elevated command
visudo          = safe sudoers editing
least privilege = minimum required access

SSH             = secure remote CLI
RDP/VNC         = graphical remote access
22              = common SSH port
authorized_keys = server-side public-key list

Disk            = storage device
Partition       = disk section
Filesystem      = data organization
Mount point     = directory for mounted filesystem
df              = filesystem usage
du              = directory/file usage

PV → VG → LV
LVM             = flexible storage management
Btrfs           = filesystem with subvolumes/snapshots
snapshot        = point-in-time state
backup          = independent copy

NetworkManager  = network service
nmcli           = NetworkManager CLI
ip addr         = addresses
ip route        = routes
ping            = reachability test
nslookup        = DNS lookup
ss              = socket/listening check

RPM             = individual RPM package
YUM/Zypper      = repository-based package management
repository      = packages + metadata

journalctl      = systemd journal query
dmesg           = kernel messages
logrotate       = rotate/manage logs

GRUB2           = bootloader
AutoYaST        = SUSE automated installation
Anaconda        = Red Hat-family installation framework
chrony          = time synchronization

Snapper         = filesystem snapshot management
.so             = shared object library
cgroups         = resource control

TLS             = secure network communication
HTTPS           = HTTP over TLS
OpenSSL         = crypto/TLS toolkit
GPG             = OpenPGP encryption/key management
symmetric       = shared secret
asymmetric      = public/private

#!/bin/bash     = Bash shebang
$var            = variable expansion
$(command)      = command substitution
read            = input
if/elif/else    = decisions
for/while       = loops
array           = multiple values
function        = reusable logic

lscpu           = CPU info
free            = memory
lspci           = PCI devices
lsusb           = USB devices
lsblk           = block devices
lshw            = hardware details
lsmod           = loaded modules
modprobe        = load/remove module
modinfo         = module information

bridge          = Layer 2
veth            = virtual Ethernet link
VLAN            = logical network separation
namespace       = network isolation
IPv4            = 32-bit
IPv6            = 128-bit

iSCSI           = block storage over IP
initiator       = storage client
target          = storage provider
discovery       = find target
login           = connect/session
multipath       = multiple storage paths
RAID            = disk/data redundancy

PAM             = authentication framework
SSSD            = centralized identity integration
authentication  = who are you?
authorization   = what can you do?

createrepo      = RPM repository metadata
libzypp         = SUSE package-management library
RMT             = repository mirroring/client-update management
```

---

# FINAL INTERVIEW RULE

When answering scenario questions, prefer this pattern:

```text
1. Identify the symptom
2. Run the most relevant check
3. Inspect status/logs/data
4. Isolate the cause
5. Apply the least-risk appropriate fix
6. Verify the result
```

Avoid answers such as:

```text
“Just restart it.”
“Just use root.”
“Just delete the files.”
“Use kill -9 immediately.”
“Change random settings.”
```

A strong Linux Admin answer shows:

```text
Understand → Check → Diagnose → Fix → Verify
```

