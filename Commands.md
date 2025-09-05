# Useful Linux Commands

---


# 📑 Cuprins

- [Navigarea în sistemul de fișiere & linkuri (mkdir, touch, ln, ln -s)](#creating-a-symbolic-link)
- [Crearea unui utilizator nou](#2-creating-a-new-user)
- [Procese și monitorizare (ps, top, kill)](#-process-fields--options-in-ps)
- [Lucrul cu dd, /dev/zero, /dev/urandom](#-working-with-dd-devzero-and-devurandom)
- [Utilizatori, grupuri & permisiuni](#-working-with-users-groups--permissions-in-ubuntu)
- [Arhivare & compresie (tar, zip, gzip, bzip2, xz)](#-archiving--compression-in-ubuntu-tar-zip-gzip-bzip2-xz)
- [Rețelistică de bază (ip, ping, curl, ip route)](#-networking-basics-in-ubuntu-ip-ping-curl-ip-route)
- [Servicii (systemd)](#-services-systemd--7-useful-examples)
- [Pachete (APT/Dpkg)](#-packages-aptdpkg--7-useful-examples)
- [Comenzi de lucru cu fișiere (sort, seq, cat, uniq)](#-sort--sortarea-textului)
- [awk – rezumat complex](#-awk--rezumat-complex)
- [grep – rezumat complex](#-grep--rezumat-complex)
- [SSH – rezumat complex](#-ssh--rezumat-complex)
- [SSH – conectare fără parolă (chei SSH)](#-ssh--conectare-fără-parolă)
- [Git Essentials (init, add, commit, reset, branch)](#-git-essentials--init-add-commit-reset-branch)
- [Exemple utile (scripturi și comenzi practice)](#-exemple-utile)



---


# 🔑 `ls` — Essential Options (Summary)

* **Basic listing**

  ```bash
  ls
  ls -1
  ```

  → show files; with `-1`, one entry per line.

* **Long / detailed listing**

  ```bash
  ls -l
  ls -lh
  ```

  → `-l` shows permissions, owner, group, size, timestamp;
  → `-h` makes sizes human-readable (KB, MB, GB).

* **Hidden files**

  ```bash
  ls -a   # include . and ..
  ls -A   # include hidden files, exclude . and ..
  ```

* **Show directory itself, not its contents**

  ```bash
  ls -ld /etc
  ```

* **Sorting options**

  ```bash
  ls -lt   # by modification time (newest first)
  ls -lS   # by file size (largest first)
  ls -lX   # by extension
  ls -lr   # reverse order
  ```

* **Visual classification**

  ```bash
  ls -F   # add / to dirs, * to executables, @ to symlinks
  ls -p   # add / only to directories
  ```

* **Recursive listing**

  ```bash
  ls -R
  ```

* **Extra info**

  ```bash
  ls -li   # show inode numbers
  ls -ln   # show numeric UID/GID
  ```
---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---


## 1. Creating a Symbolic Link

### Hard Link (brief)

**What**: Another filename pointing to the same inode/data.  
**Pros**: Survives original deletion, super fast.  
**Limits**: Files only (no dirs), same filesystem/partition.

```bash
# create
ln path/to/source.txt path/to/hardlink.txt

# verify (same inode = same number in 1st column)
ls -li path/to/source.txt path/to/hardlink.txt
```

---

### Creating a Symbolic Link

**What**: A small file that stores the path to the target (like a shortcut).  
**Pros**: Can link to dirs, across filesystems.  
**Cons**: Breaks if the path changes (becomes “dangling”).

### Basic syntax
```bash
# file -> symlink
ln -s path/to/target path/to/link
```

### Verification
````bash
ls -l nume_link
# afișează: link -> /cale/catre/target
````

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---
## 2. Creating a New User

**Command:**

```bash
sudo useradd -m -p $(openssl passwd -1 password) username
```

* **`sudo`**: Runs the command with superuser privileges.
* **`useradd`**: The command to add a new user.
* **`-m`**: Creates the user's home directory (`/home/username`).
* **`-p $(openssl passwd -1 password)`**: Sets the user's password. `openssl passwd -1 password` generates an encrypted password string using MD5.
* **`username`**: The login name of the new user.

### Verifying User Creation

1. **Check if the user exists:**

   ```bash
   getent passwd username
   ```
2. **Check home directory:**

   ```bash
   ls -ld /home/username
   ```
3. **Verify password:**

   * Switch to the new user:

     ```bash
     su - username
     ```
   * If you can log in without errors, the password is set correctly.
   * Alternatively, check password status:

     ```bash
     sudo passwd -S username
     ```

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

## 📌 Process Fields & Options in `ps`

### 🔹 Process Fields

* **PID** – Process ID.
* **PPID** – Parent Process ID.
* **CMD** – Executed command (may be truncated).
* **ARGS / COMMAND** – Full command with arguments.
* **COMM** – Only the executable name.
* **USER** – Effective user name.
* **UID** – Effective user ID (numeric).
* **RUSER / RUID** – Real user name / ID.
* **EUSER / EUID** – Effective user name / ID.
* **GID / EGID** – (Effective) group ID.
* **RGROUP / RGID** – Real group (name / ID).
* **TTY** – Associated terminal (e.g. pts/0), or `?` if none.
* **STAT** – Process state (R, S, D, T, Z + flags).
* **PRI** – Priority.
* **NI** – Niceness value (−20…19).
* **%CPU / PCPU** – CPU usage percentage.
* **%MEM / PMEM** – Memory usage percentage.
* **VSZ** – Virtual memory size (KB).
* **RSS** – Resident Set Size (RAM usage, KB).
* **ETIME** – Elapsed running time (HH\:MM\:SS).
* **TIME / CPUTIME** – Total consumed CPU time.
* **START / LSTART** – Start time (short / full).
* **NLWP** – Number of threads (lightweight processes).
* **SID** – Session ID.
* **PGID** – Process Group ID.
* **WCHAN** – Kernel function where process sleeps.

---

### 🔹 Examples

```bash
# Processes of user "student", showing PID, PPID, full args, state, %CPU and %MEM
ps -u student -o pid,ppid,args,stat,%cpu,%mem --sort=ppid

# Show PID, elapsed time, memory usage, and command for a given PID
ps -o pid,etime,vsz,rss,cmd -p <PID>

# Show PID, long start time, and command
ps -o pid,lstart,cmd -p <PID>
```

---

### 🔹 Common Options (briefly)

* **`-e`** → Show *all processes* (same as `-A`).
* **`-o`** → Customize *output format* (choose columns).
* **`-u`** → Show processes for a *specific user*.
* **`-f`** → Full-format listing (includes PPID, UID, time, etc).

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

# 🔑 Working with `dd`, `/dev/zero`, and `/dev/urandom`

The `dd` command in Linux is a powerful low-level copying tool. It is commonly used to generate test files, overwrite existing ones, clone or wipe disks, and even create bootable drives.

---

## 1. Create a file filled with zeros

```bash
dd if=/dev/zero of=zero.bin bs=1M count=32
```

* `if=/dev/zero` → input file is `/dev/zero`, which outputs continuous zero bytes
* `of=zero.bin` → output file will be named `zero.bin`
* `bs=1M` → block size is 1 MB
* `count=32` → copy 32 blocks → total size 32 MB

---

## 2. Create a file filled with random data

```bash
dd if=/dev/urandom of=random.bin bs=1M count=10
```

* `if=/dev/urandom` → input source is `/dev/urandom`, which produces random bytes
* `of=random.bin` → output file is `random.bin`
* `bs=1M` → block size = 1 MB
* `count=10` → copy 10 blocks → total size 10 MB

---

## 3. Overwrite the beginning of a file

```bash
dd if=random.bin of=zero.bin conv=notrunc
```

* `if=random.bin` → read from `random.bin`
* `of=zero.bin` → write to `zero.bin`
* `conv=notrunc` → do not truncate the output file, only overwrite the beginning

---

## 4. Copy only part of a file

```bash
dd if=random.bin of=part.bin bs=1M count=5
```

* `bs=1M` → block size = 1 MB
* `count=5` → copy only 5 blocks (first 5 MB of the file)

---

## 5. Skip data in the input

```bash
dd if=random.bin of=segment.bin bs=1M skip=2 count=3
```

* `skip=2` → skip the first 2 blocks (2 MB) from input file
* `count=3` → then copy 3 blocks (3 MB)
* Output file will contain bytes from positions 2–5 MB of the input

---

## 6. Write at an offset in the output

```bash
dd if=random.bin of=zero.bin bs=1M count=1 seek=10 conv=notrunc
```

* `count=1` → copy only 1 block (1 MB)
* `seek=10` → skip 10 blocks in the output before writing (start writing at offset 10 MB)
* `conv=notrunc` → do not truncate `zero.bin`

---

## 7. Useful real-world examples

**a) Show progress while creating a file of zeros**

```bash
dd if=/dev/zero of=testfile bs=1M count=100 status=progress
```

* `status=progress` → display live progress info

---

**b) Create a bootable USB drive from an ISO**

```bash
dd if=ubuntu.iso of=/dev/sdb bs=4M status=progress && sync
```

* `if=ubuntu.iso` → input file is the ISO image
* `of=/dev/sdb` → output device (USB drive – must be correct!)
* `bs=4M` → larger block size for faster writing
* `sync` → flushes data to disk to ensure integrity

---

**c) Wipe a disk with random data (security)**

```bash
dd if=/dev/urandom of=/dev/sdX bs=1M status=progress
```

* `if=/dev/urandom` → input is random data
* `of=/dev/sdX` → output is the disk (⚠️ destroys all data)
* `status=progress` → shows progress while writing

---

👉 **In summary:**

* `/dev/zero` → produces infinite zeros (useful for test/wipe).
* `/dev/urandom` → produces random bytes (useful for tests and security).
* `dd` arguments:

  * `if=` input file/device
  * `of=` output file/device
  * `bs=` block size
  * `count=` number of blocks
  * `skip=` skip blocks from input
  * `seek=` skip blocks in output before writing
  * `conv=notrunc` → don’t cut the output file
  * `status=progress` → show progress

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

# 🔑 Working with Users, Groups & Permissions in Ubuntu

Managing users, groups, and file permissions is central to system administration. Below are **typical exam-style tasks**, with commands and brief descriptions.

---

## 1. Create a new user with a home directory

```bash
sudo useradd -m andrei
```

* `-m` → create home directory (`/home/andrei`)

---

## 2. Create a new user with custom home and shell

```bash
sudo useradd -m -d /srv/andrei -s /bin/bash andrei
```

* `-m` → create home directory
* `-d /srv/andrei` → custom home path
* `-s /bin/bash` → default shell set to bash

---

## 3. Create a user and set the password immediately

```bash
sudo useradd -m ana
echo 'ana:MyPass123' | sudo chpasswd
```

* `chpasswd` → read `username:password` from stdin
* This sets password during user creation

---

## 4. Create a user and set password interactively

```bash
sudo passwd ana
```

* `passwd` → change user password
* If run without username, it changes **your own password**
* If run with username, it changes **that user’s password** (requires `sudo`)

---

## 5. Force user to change password at first login

```bash
sudo passwd -e ana
```

* `-e` (expire) → password will expire immediately
* User must set a new password at next login

---

## 6. Lock or unlock a user’s password

```bash
sudo passwd -l ana   # lock
sudo passwd -u ana   # unlock
```

* `-l` → lock account (disable password login)
* `-u` → unlock account

---

## 7. Set password validity rules

```bash
sudo chage -M 30 ana
```

* `chage` → manage password aging
* `-M 30` → maximum password age = 30 days

---

## 8. Add a user to an existing group

```bash
sudo usermod -aG umb ana
```

* `-aG umb` → append (`-a`) user to group(s) (`-G`)

---

## 9. Add multiple users to the same group

```bash
sudo usermod -aG umb ana
sudo usermod -aG umb ion
```

👉 Typical exam case: add two or more users to the same group

---

## 10. Delete a user

```bash
sudo userdel -r ana
```

* `userdel` → remove a user
* `-r` → remove user’s home directory and mail spool

---

## 11. Change ownership of a file

```bash
sudo chown ana file.txt
```

* Sets `ana` as owner of `file.txt`

**Change both owner and group:**

```bash
sudo chown ana:umb file.txt
```

* Sets owner = `ana`, group = `umb`

---

## 12. Change only group of a file

```bash
sudo chgrp umb file.txt
```

* Changes group ownership only

---

## 13. Change permissions numerically

```bash
chmod 640 file.txt
```

* `640` = owner=read/write (6), group=read (4), others=none (0)

---

## 14. Change permissions symbolically

```bash
chmod u+x,g-w,o-r file.txt
```

* `u+x` → add execute to owner
* `g-w` → remove write from group
* `o-r` → remove read from others

---

## 15. Recursive ownership change

```bash
sudo chown -R ana:umb /project
```

* `-R` → apply to all subfiles/subdirs in `/project`

---

## 16. Check permissions and groups

```bash
ls -l file.txt
groups ana
id ana
```

* `ls -l` → shows owner, group, and permissions of files
* `groups` → lists groups a user belongs to
* `id` → shows UID, GID, and groups

---

👉 **In summary for passwords:**

* `passwd user` → set/change password interactively
* `echo 'user:pass' | chpasswd` → set password in scripts/one-liners
* `passwd -e user` → force password reset at next login
* `passwd -l user` / `-u user` → lock/unlock password login
* `chage -M days user` → enforce password expiration policy

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

# 🔑 Archiving & Compression in Ubuntu (tar, zip, gzip, bzip2, xz)

Archiving and compression are used to package files and reduce their size. The most common tools in Linux are `tar`, `zip`, and `gzip` (plus `bzip2` and `xz` for stronger compression).

---

## 1. Create a `.tar` archive (without compression)

```bash
tar -cf backup.tar Documents/
```

* `tar` → archive utility
* `-c` → create archive
* `-f backup.tar` → filename of archive (`backup.tar`)
* `Documents/` → folder to include

---

## 2. Extract a `.tar` archive

```bash
tar -xf backup.tar
```

* `-x` → extract files
* `-f` → file to extract (`backup.tar`)

---

## 3. Create a compressed `.tar.gz` archive

```bash
tar -czf project.tar.gz project/
```

* `-c` → create archive
* `-z` → compress with gzip
* `-f project.tar.gz` → output archive file
* `project/` → source folder

---

## 4. Extract a `.tar.gz` archive

```bash
tar -xzf project.tar.gz
```

* `-x` → extract
* `-z` → use gzip decompression
* `-f project.tar.gz` → file to extract

---

## 5. Create a `.tar.bz2` archive (bzip2 compression)

```bash
tar -cjf archive.tar.bz2 folder/
```

* `-j` → compress with bzip2 (better compression, slower)

---

## 6. Create a `.tar.xz` archive (xz compression)

```bash
tar -cJf archive.tar.xz folder/
```

* `-J` → compress with xz (strong compression, slower than gzip)

---

## 7. List contents of a tar archive

```bash
tar -tf project.tar.gz
```

* `-t` → list contents
* `-f` → file to check

---

## 8. Create a `.zip` archive

```bash
zip -r files.zip project/
```

* `zip` → create zip archive
* `-r` → recursive (include subdirectories)
* `files.zip` → archive name
* `project/` → folder to archive

---

## 9. Extract a `.zip` archive

```bash
unzip files.zip
```

* `unzip` → extract zip archive

---

## 10. Create a password-protected `.zip`

```bash
zip -r -P Secret123 secure.zip project/
```

* `-P Secret123` → set password = `Secret123`
  ⚠️ Not very secure (password visible in history) → prefer `zipcloak` or `gpg`

---

## 11. Compress a single file with `gzip`

```bash
gzip file.txt
```

* Compresses `file.txt` → creates `file.txt.gz` and removes the original

**Decompress back:**

```bash
gunzip file.txt.gz
```

* Restores the original file

---

## 12. Keep original file when compressing

```bash
gzip -c file.txt > file.txt.gz
```

* `-c` → write output to stdout (redirect to file)
* Leaves the original file untouched

---

## 13. Stronger compression with `bzip2`

```bash
bzip2 file.txt
bunzip2 file.txt.bz2
```

* `bzip2` → compresses into `.bz2`
* `bunzip2` → decompress

---

## 14. Stronger compression with `xz`

```bash
xz file.txt
unxz file.txt.xz
```

* `xz` → compresses into `.xz`
* `unxz` → decompress

---

## 15. Check compression ratios

```bash
ls -lh file.txt file.txt.gz file.txt.bz2 file.txt.xz
```

* `ls -lh` → compare file sizes easily

---

👉 **In summary:**

* `tar` → bundles multiple files into one archive (can combine with gzip/bzip2/xz for compression)
* `zip` / `unzip` → widely used, supports password (less secure)
* `gzip` / `bzip2` / `xz` → compress single files with different strength/speed trade-offs

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

# 🔑 Processes & Monitoring in Ubuntu (ps, top, kill)

Linux treats each running program as a **process**. System admins often need to list processes, inspect CPU/memory usage, and terminate processes. The three main tools are **`ps`**, **`top`**, and **`kill`**.

---

## 🔹 1. Working with `ps`

`ps` (process status) lists information about running processes.

### a) List processes for the current user

```bash
ps
```

* Default: shows PID, TTY, TIME, CMD for your own processes.

---

### b) List all processes in the system

```bash
ps -e
```

* `-e` → show every process

---

### c) Full format with extra details

```bash
ps -ef
```

* `-f` → full format (UID, PID, PPID, start time, etc.)
* `-ef` → common on many Linux distributions (SysV style)

---

### d) Display specific columns

```bash
ps -u student -o pid,ppid,cmd,%cpu,%mem --sort=ppid
```

* `-u student` → processes of user `student`
* `-o` → output format: PID, parent PID, command, CPU%, MEM%
* `--sort=ppid` → sort processes by parent PID

---

### e) Show processes with a controlling terminal

```bash
ps -eo pid,tty,cmd --tty | sort -k1,1nr
```

* `-e` → all processes
* `-o` → select output columns (PID, TTY, CMD)
* `--tty` → only processes attached to a terminal
* `sort -k1,1nr` → sort by PID in descending order

---

### f) Display only processes of root user

```bash
ps -U root -o pid,cmd,etime --sort=etime
```

* `-U root` → filter by real user ID (root)
* `etime` → elapsed time since start
* `--sort=etime` → sort by run time

---

### g) Count how many processes a user has

```bash
ps -u student --no-headers | wc -l
```

* `--no-headers` → remove header row
* `wc -l` → count lines = number of processes

---

---

## 🔹 2. Monitoring with `top` (and `htop`)

`top` shows real-time process activity (interactive).

### a) Basic usage

```bash
top
```

* Displays processes with CPU and memory usage updated live

---

### b) Sort processes by memory

Inside `top`:

* Press **Shift + M** → sort by memory usage

---

### c) Sort processes by CPU

Inside `top`:

* Press **Shift + P** → sort by CPU usage

---

### d) Show only processes of a specific user

```bash
top -u student
```

* `-u student` → filter processes of user `student`

---

### e) Limit number of processes shown

```bash
top -n 1 -b | head -20
```

* `-n 1` → refresh only once (non-interactive)
* `-b` → batch mode (output suitable for scripts)
* `head -20` → show first 20 lines

---

### f) Alternative: `htop`

```bash
htop
```

* Colorful, user-friendly alternative to `top`
* Allows scrolling, searching (`/`), killing processes (`F9`), and filtering by user (`u`).

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

## 🔹 3. Killing processes (`kill`, `killall`, `pkill`)

Sometimes a process hangs or misbehaves → you must terminate it.

### a) Kill a process by PID

```bash
kill 1234
```

* `kill` → send a signal (default: `SIGTERM`)
* `1234` → PID of the process

---

### b) Kill a process forcefully

```bash
kill -9 1234
```

* `-9` → `SIGKILL` (force kill, cannot be ignored)
* Use only if normal kill does not work

---

### c) Kill all processes by name

```bash
killall firefox
```

* `killall` → kill by process name
* `firefox` → kills all processes named firefox

---

### d) Use `pkill` with pattern matching

```bash
pkill -u student bash
```

* `pkill` → kill based on pattern
* `-u student` → only for user `student`
* `bash` → matches processes named bash

---

### e) Send other signals (not just kill)

```bash
kill -STOP 1234   # pause process
kill -CONT 1234   # resume process
```

* `-STOP` → suspend process
* `-CONT` → continue a stopped process

---

---

## 🔹 Practical exam-style tasks

1. **List processes of current user sorted by memory usage**

```bash
ps -u $(whoami) -o pid,cmd,%mem --sort=-%mem
```

2. **Find and kill the process using the most CPU**

```bash
ps -eo pid,cmd,%cpu --sort=-%cpu | head -2
kill <PID>
```

3. **Check how many unique users have running processes**

```bash
ps -eo user= | sort -u | wc -l
```

4. **Suspend and resume a process**

```bash
kill -STOP <PID>
kill -CONT <PID>
```

---

👉 **In summary:**

* `ps` → snapshot of processes (static list, can filter, sort, format output)
* `top/htop` → dynamic real-time monitoring
* `kill/killall/pkill` → control processes (terminate, pause, resume)

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

# 🔑 Networking Basics in Ubuntu (ip, ping, curl, ip route)

Networking tools help inspect interfaces, test connectivity, and view routing information.

---

## 🔹 1. The `ip` command

The modern replacement for `ifconfig`. Used for showing/managing network interfaces, addresses, and routes.

### a) Show all network interfaces and addresses

```bash
ip addr show
```

* `addr` → show IP addresses
* Shows IPv4, IPv6, broadcast, MAC

---

### b) Show interfaces in brief form

```bash
ip -br a
```

* `-br` → brief mode (clean, table-like output)

---

### c) Show info for one interface

```bash
ip addr show dev ens3
```

* `dev ens3` → limit output to interface `ens3`

---

### d) Show only IPv4 addresses of an interface

```bash
ip -4 -o addr show dev ens3 | awk '{print $4}'
```

* `-4` → only IPv4
* `-o` → one-line per address
* `awk '{print $4}'` → extract `IP/mask`

---

### e) Add or delete an IP address

```bash
sudo ip addr add 192.168.1.100/24 dev ens3
sudo ip addr del 192.168.1.100/24 dev ens3
```

* `addr add/del` → add or delete address on interface
* `/24` → subnet mask

---

---

## 🔹 2. The `ping` command

Tests connectivity to another host using ICMP packets.

### a) Basic ping to a host

```bash
ping -c 4 google.com
```

* `-c 4` → send 4 packets only

---

### b) Ping with timeout

```bash
ping -w 5 8.8.8.8
```

* `-w 5` → stop after 5 seconds regardless of replies

---

### c) Ping until stopped manually

```bash
ping 1.1.1.1
```

* Continuous ping (press `Ctrl+C` to stop)

---

### d) Ping multiple hosts in a script

```bash
for h in 8.8.8.8 1.1.1.1 google.com; do
  ping -c1 $h &>/dev/null && echo "$h is up" || echo "$h is down"
done
```

* Quick connectivity check for a list of hosts

---

---

## 🔹 3. The `curl` command

Transfers data from or to a server (HTTP, HTTPS, FTP, etc.).

### a) Fetch a webpage

```bash
curl https://example.com
```

* Outputs the raw HTML page to terminal

---

### b) Save output to a file

```bash
curl -o page.html https://example.com
```

* `-o page.html` → save output to `page.html`

---

### c) Silent mode (no progress info)

```bash
curl -s https://example.com
```

* `-s` → silent (don’t show progress bar or errors)

---

### d) Get your public IP

```bash
curl -s ifconfig.me
```

* Queries service `ifconfig.me` to return your external IP

---

### e) Download a file

```bash
curl -O https://example.com/file.zip
```

* `-O` → save with same name as remote file

---

### f) Send a POST request with data

```bash
curl -X POST -d "user=test&pass=123" https://example.com/login
```

* `-X POST` → use POST method
* `-d` → data to send

---

---

## 🔹 4. The `ip route` command

Shows and manages the kernel’s routing table.

### a) Show default route

```bash
ip route show default
```

* Shows which gateway is used for internet traffic

---

### b) Show full routing table

```bash
ip route show
```

* Lists all known routes (local, default, connected networks)

---

### c) Add a new route

```bash
sudo ip route add 192.168.2.0/24 via 192.168.1.1
```

* Route traffic to `192.168.2.0/24` via gateway `192.168.1.1`

---

### d) Delete a route

```bash
sudo ip route del 192.168.2.0/24
```

* Removes the previously added route

---

### e) Show route to a specific host

```bash
ip route get 8.8.8.8
```

* Displays which interface/gateway will be used to reach `8.8.8.8`

---

---

## 🔹 Practical exam-style tasks

1. **Find and display only the IPv4 address of interface `ens3`**

```bash
ip -4 -o addr show dev ens3 | awk '{print $4}'
```

2. **Check if gateway is reachable**

```bash
ping -c 3 $(ip route show default | awk '{print $3}')
```

3. **Get the public IP of your VM**

```bash
curl -s ifconfig.me
```

4. **Display number of routes in routing table**

```bash
ip route | wc -l
```

---

👉 **In summary:**

* `ip` → inspect & configure interfaces/addresses
* `ping` → test connectivity (ICMP echo)
* `curl` → fetch web resources or public IP
* `ip route` → view/modify routing table

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

# 🔹 Networking — 7 useful examples

**Show all IP addresses and interfaces**

```bash
ip addr show
```

**Check connectivity with ping**

```bash
ping -c 4 google.com
```

**Display the routing table**

```bash
ip route show
```

**Test an HTTP request (headers only)**

```bash
curl -I https://example.com
```

**Check open TCP/UDP ports (using ss)**

```bash
ss -tuln
```

**Show public IP address (via curl)**

```bash
curl https://ifconfig.me
```

**Bring a network interface down/up**

```bash
sudo ip link set eth0 down
sudo ip link set eth0 up
```

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

# 🔑 Git Essentials — (init, add, commit, reset, branch)

## 1) `git init` — initialize a repository

**a) Init într-un director existent (proiect deja creat)**

```bash
cd ~/projects/myapp
git init
git status
```

* `git init` → creează `.git/` (repo gol).
* `git status` → vezi ce fișiere sunt „untracked”.

**b) Init cu nume de branch inițial explicit (unele sisteme cer `main`)**

```bash
git init --initial-branch=main
```

* `--initial-branch=main` → setează default branch la `main`.

**c) Convert un folder în repo și ignoră build-urile**

```bash
git init
printf "node_modules/\n*.log\nbuild/\n" > .gitignore
git add .gitignore
git commit -m "chore: add basic .gitignore"
```

---

## 2) `git add` — stage changes

**a) Adaugă un singur fișier**

```bash
git add app.py
```

**b) Adaugă toate fișierele noi și modificările (inclusiv untracked)**

```bash
git add -A
```

* `-A` → echivalent cu `--all`, include adăugări/ștergeri/modificări.

**c) Adaugă doar fișierele deja urmărite (tracked)**

```bash
git add -u
```

* `-u` → actualizează doar fișierele urmărite (bun pt „update fără fișiere noi”).

**d) Adaugă după un pattern**

```bash
git add "*.sh"
git add docs/*.md
```

**e) Stage interactiv (selectezi hunks)**

```bash
git add -p
```

* `-p` → îți arată „hunk”-uri; alegi ce intră în commit.

**f) Include tot dintr-un subfolder, exclude ceva**

```bash
git add dir/
git reset HEAD dir/bigfile.bin   # scoate din stage acel fișier
```

---

## 3) `git commit` — snapshot with message

**a) Commit standard**

```bash
git commit -m "feat: add login form"
```

**b) Commit și cu body (mesaj pe 2 linii)**

```bash
git commit -m "feat: import CSV" -m "Handles quoted fields and BOM."
```

* al doilea `-m` → corpul mesajului.

**c) Commit toate modificările urmărite, într-o singură mișcare**

```bash
git commit -am "fix: adjust timeouts"
```

* `-a` → adaugă automat modificările fișierelor *tracked* (nu și untracked).

**d) Amend — ai uitat un fișier**

```bash
git add missing.cfg
git commit --amend -m "feat: add login form (include cfg)"
```

* `--amend` → rescrie ultimul commit (nu face asta după ce ai „pushed”, decât cu grijă).

**e) Commit cu autor explicit (exerciții scripting)**

```bash
git commit --author="John Doe <john@ex.com>" -m "docs: add README"
```

**f) Commit gol (marchezi un eveniment)**

```bash
git commit --allow-empty -m "chore: trigger CI"
```

**g) Afișează fișierele schimbate în ultimul commit (util în subiecte)**

```bash
git show --name-only --oneline
```

---

## 4) `git reset` — move HEAD / unstage / discard

> Gândește-te așa: **soft** = doar HEAD se mută; **mixed** (implicit) = scoate din stage; **hard** = aruncă și din working tree.

**a) „Undo last commit”, păstrând modificările în stage (squash-ready)**

```bash
git reset --soft HEAD~1
```

* folosit ca pas înainte de a combina commits.

**b) Scoate din stage, păstrează în working tree (corecție după `git add`)**

```bash
git reset HEAD
# sau pentru un fișier anume:
git reset HEAD path/to/file
```

* (fără flag) = `--mixed`.

**c) Revine la ultimul commit, aruncând schimbările locale (atenție!)**

```bash
git reset --hard HEAD
```

**d) Resetează un fișier la versiunea din ultimul commit**

```bash
git checkout -- app.py          # varianta veche
git restore --source=HEAD app.py  # varianta modernă
```

**e) Combină ultimele 2 commits (pattern foarte des întâlnit)**

```bash
git reset --soft HEAD~2
git commit -m "feat: combine last two commits"
```

**f) Mută branch-ul la un anumit commit (repoziționezi HEAD + branch)**

```bash
git reset --hard <commit-hash>
```

---

## 5) `git branch` — create / rename / delete / list

**a) Creează și comută pe un branch nou**

```bash
git switch -c feature/auth
# sau: git checkout -b feature/auth
```

**b) Creează un branch dintr-un commit specific**

```bash
git branch hotfix-conn 7e1d0f3
git switch hotfix-conn
```

**c) Redenumește branch-ul curent**

```bash
git branch -m teme
# forțează dacă există deja: git branch -M teme
```

**d) Șterge un branch local**

```bash
git branch -d old-branch     # șterge doar dacă e fully merged
git branch -D old-branch     # forțează (atenție!)
```

**e) Listează branch-urile cu ultimul commit scurt**

```bash
git branch -vv
```

**f) Setează upstream pentru un branch nou (pregătit pentru push)**

```bash
git push -u origin feature/auth
```

* `-u` → memorează upstream, ca mai târziu `git push`/`pull` fără argumente.

**g) Schimbă numele unui branch local și actualizează remote**

```bash
git branch -m oldname newname
git push origin -u newname
git push origin --delete oldname
```

---

## 🎯 Exam-style drills (pe temele cerute)

**1) Init + 3 commits „add: File <N>” apoi combine ultimele două într-unul**

```bash
git init --initial-branch=main
printf "a\n" > f1.txt; git add f1.txt; git commit -m "add: File 1"
printf "b\n" > f2.txt; git add f2.txt; git commit -m "add: File 2"
printf "c\n" > f3.txt; git add f3.txt; git commit -m "add: File 3"
git reset --soft HEAD~2
git commit -m "add: Files 2 3"
```

**2) Staging selectiv + amend**

```bash
printf "x\n" > app.py
printf "y\n" > notes.txt
git add -p app.py             # doar anumite hunk-uri din app.py
git commit -m "feat: partial add in app.py"
git add notes.txt             # ai uitat notes.txt
git commit --amend -m "feat: partial add in app.py + notes"
```

**3) Unstage rapid tot, dar păstrează modificările**

```bash
git reset HEAD .
```

**4) Creează branch dintr-un commit anterior și lucrează acolo**

```bash
git log --oneline            # găsește <hash>
git branch exp/<hash> <hash>
git switch exp/<hash>
```

**5) Redenumește branch curent și setează upstream nou**

```bash
git branch -m tema1
git push -u origin tema1
```

**6) Aruncă toate modificările locale și revino la ultimul commit**

```bash
git reset --hard HEAD
```

**7) Listă scurtă a branch-urilor cu ultimul commit (verificare rapidă)**

```bash
git branch -vv
```

---

## Tips & gotchas

* `git status` des și `git log --oneline --graph --decorate` pentru context rapid.
* `--hard` elimină modificări locale — folosește-l doar când ești sigur.
* După `--amend`/`reset` peste ceva deja împins pe remote, ai nevoie de `git push --force-with-lease`.

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

# 🔹 Services (systemd) — 7 useful examples

**Check status of a service**

```bash
systemctl status ssh
```

**Start / Stop / Restart a service**

```bash
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
```

**Enable / Disable autostart at boot**

```bash
sudo systemctl enable nginx
sudo systemctl disable nginx
```

**Check if a service is active or enabled**

```bash
systemctl is-active cron
systemctl is-enabled cron
```

**View recent logs for a service**

```bash
journalctl -u nginx -n 50 --no-pager
```

**List only running services**

```bash
systemctl list-units --type=service --state=running
```

**Mask / Unmask a service (prevent manual start)**

```bash
sudo systemctl mask telnet
sudo systemctl unmask telnet
```

---

# 🔹 Packages (APT/Dpkg) — 7 useful examples

**Update package index**

```bash
sudo apt update
```

**Upgrade installed packages**

```bash
sudo apt upgrade
```

**Install a package**

```bash
sudo apt install curl
```

**Remove a package (with configs)**

```bash
sudo apt remove --purge nginx
```

**Search for a package by keyword**

```bash
apt search openssh
```

**Check if a package is installed**

```bash
dpkg -l | grep python3
```

**List files installed by a package**

```bash
dpkg -L curl
```

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

# 🔹 `sort` – Sortarea textului

**Sortare alfabetică (A → Z)**

```bash
sort file.txt
```

**Sortare inversă (Z → A)**

```bash
sort -r file.txt
```

**Sortare numerică**

```bash
sort -n numbers.txt
```

**Sortare numerică descrescătoare**

```bash
sort -nr numbers.txt
```

**Sortare după coloană**

```bash
sort -k2 file.txt          # după coloana a 2-a
sort -k3,3 file.txt        # strict după coloana a 3-a
sort -t$'\t' -k2 music.txt # după col 2 când delimitatorul e TAB
```

👉 **Rezumat**:

* alfabetic: `sort`
* invers: `sort -r`
* numeric: `sort -n`
* numeric invers: `sort -nr`
* după coloană: `sort -kN` (cu `-t` dacă delimitator ≠ spațiu).

---

# 🔹 `seq` – Generarea secvențelor de numere

**Teorie**
`seq` afișează o listă de numere.
Format:

```bash
seq [START] [STEP] END
```

**Exemple**

```bash
seq 5           # 1 2 3 4 5
seq 2 6         # 2 3 4 5 6
seq 10 2 20     # 10 12 14 16 18 20
seq -3 3        # -3 -2 -1 0 1 2 3
seq -w 07 12    # 07 08 09 10 11 12
seq -s, 1 5     # 1,2,3,4,5
seq -f "Num=%02g" 1 3
```

---

# 🔹 `cat` – Afișare și concatenare fișiere

**Teorie**
`cat` afișează conținutul fișierelor sau le lipește.

**Exemple**

```bash
cat file.txt
cat file1.txt file2.txt > all.txt
cat -n file.txt   # numerotează toate liniile
cat -b file.txt   # numerotează doar liniile cu text
cat -s file.txt   # elimină liniile goale consecutive
cat -E file.txt   # marchează sfârșitul liniei cu $
```

---

# 🔹 `uniq` – Eliminarea liniilor duplicate

**Teorie**
`uniq` elimină duplicatele **consecutive** (de obicei folosit cu `sort`).

**Exemple**
Fișier `names.txt`:

```
ana
ana
bogdan
ion
ion
ion
maria
```

```bash
uniq names.txt        # elimină duplicatele consecutive
sort names.txt | uniq # elimină toate duplicatele (sortare + uniq)
uniq -c names.txt     # arată și numărul de apariții
uniq -d names.txt     # doar liniile duplicate
uniq -u names.txt     # doar liniile unice
uniq -i names.txt     # ignoră litere mari/mici
```

---

**Combinare cu alte comenzi**

```bash
ps -ef | grep firefox   # procese cu „firefox”
ls -l | grep ".txt"     # doar fișiere .txt
```

---

# 🔹 Rezumat pentru examen

* **`sort`** → sortare (alfabetică, numerică, invers, pe coloane)
* **`seq`** → generează liste de numere (intervale, pași, formate)
* **`cat`** → afișează/unește fișiere, numerotare linii, elimină spații
* **`uniq`** → elimină duplicate, afișează contorizare, doar unice/duplicate

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

# 🔹 `awk` – Rezumat complex

## 1. Teorie

* **Ce este `awk`?**
  `awk` este un limbaj de procesare a textului.
  Funcționează astfel: citește fișiere **linie cu linie**, împarte fiecare linie în **câmpuri (fields)** (implicit separate prin spații/tab) și aplică instrucțiuni.

* **Structura generală**

  ```bash
  awk 'pattern { action }' fisier
  ```

  * **pattern** = condiția (ex: `$3 == "Hip hop"`)
  * **action** = ce să facă (ex: `print $1`)
  * Dacă lipsește `pattern`, se aplică pentru toate liniile.
  * Dacă lipsește `action`, implicit este `print $0` (afișează linia întreagă).

---

## 2. Variabile built-in importante

* `$1, $2, …` → câmpurile (coloanele)
* `$0` → întreaga linie
* `NF` → numărul de câmpuri din linie
* `NR` → numărul liniei curente
* `FNR` → numărul liniei curente din fișier (diferit dacă procesezi mai multe fișiere)
* `FS` → field separator (delimitator la input, implicit spațiu/tab)
* `OFS` → output field separator (implicit spațiu)
* `RS` → record separator (delimitator de linii, implicit newline)
* `ORS` → output record separator (implicit newline)

---

## 3. Opțiuni și argumente importante

* `-F` → setează delimitatorul câmpurilor.

  ```bash
  awk -F: '{print $1}' /etc/passwd
  ```
* `-v` → definește variabile din linia de comandă.

  ```bash
  awk -v limit=100 '$3 > limit {print $1, $3}' file.txt
  ```
* `BEGIN {}` → cod rulat înainte de a citi fișierul.
* `END {}` → cod rulat după ce fișierul a fost procesat.

---

## 4. Exemple practice (20)

### 1. Prima coloană din fișier

```bash
awk '{print $1}' music.txt
```

### 2. Mai multe coloane

```bash
awk '{print $1, "-", $2}' music.txt
```

### 3. Numerotarea liniilor

```bash
awk '{print NR, $0}' music.txt
```

### 4. Ultima coloană

```bash
awk '{print $NF}' music.txt
```

### 5. Număr total de linii

```bash
awk 'END {print "Total linii:", NR}' music.txt
```

### 6. Număr total de câmpuri din fișier

```bash
awk '{c += NF} END {print "Total câmpuri:", c}' music.txt
```

### 7. Linii cu mai mult de 3 câmpuri

```bash
awk 'NF > 3' music.txt
```

### 8. Linii care conțin un anumit text

```bash
awk '/Hip hop/ {print $1, $2}' music.txt
```

### 9. Filtrare pe bază de condiție numerică

```bash
awk '$3 == "Hip hop" {print $1, $2}' music.txt
```

### 10. Schimbare delimitator output

```bash
awk -F"\t" 'BEGIN {OFS=" | "} {print $1, $2, $3}' music.txt
```

### 11. Contorizare linii care îndeplinesc condiții

```bash
awk '$3=="Hip hop" {c++} END {print "Albume hip hop:", c}' music.txt
```

### 12. Suma valorilor dintr-o coloană

Fișier `numere.txt`:

```bash
awk '{s+=$1} END {print "Suma =", s}' numere.txt
```

### 13. Media aritmetică

```bash
awk '{s+=$1; c++} END {print "Media =", s/c}' numere.txt
```

### 14. Minim și maxim

```bash
awk 'NR==1 {min=max=$1} $1<min {min=$1} $1>max {max=$1} END {print "Min=",min,"Max=",max}' numere.txt
```

### 15. Print doar anumite linii (ex. primele 5)

```bash
awk 'NR <= 5 {print}' music.txt
```

### 16. Print linii pare

```bash
awk 'NR % 2 == 0' music.txt
```

### 17. Print linii impare

```bash
awk 'NR % 2 == 1' music.txt
```

### 18. Arată linia și câte câmpuri are

```bash
awk '{print "Linia:", NR, "are", NF, "câmpuri"}' music.txt
```

### 19. Formatare tabel

```bash
awk '{printf "%-15s %-20s\n", $1, $2}' music.txt
```

### 20. Înlocuiri simple (simulare „replace”)

```bash
awk '{gsub("Hip hop", "Rap"); print}' music.txt
```

---

## 5. Rezumat

* **Coloane:** `$1, $2, $NF`
* **Linie completă:** `$0`
* **Număr linie:** `NR`
* **Număr câmpuri:** `NF`
* **Separatoare:** `-F`, `FS`, `OFS`
* **Condiții:** `$3=="Hip hop"`, `NR>5`, `NF<4`
* **Blocuri speciale:** `BEGIN {}`, `END {}`

👉 `awk` = cel mai puternic utilitar de lucru pe fișiere text structurate.

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

# 🔹 `grep` – Rezumat complex

## 1. Teorie

* **Ce este `grep`?**
  `grep` (*Global Regular Expression Print*) caută și afișează liniile dintr-un fișier sau output care se potrivesc cu un șablon (pattern).
* Funcționează cu text simplu sau expresii regulate.
* Implicit: afișează **întreaga linie** care conține pattern-ul.

**Sintaxă generală:**

```bash
grep [OPȚIUNI] "pattern" fisier
```

---

## 2. Opțiuni importante

* `-i` → ignoră diferența între litere mari/mici
* `-n` → afișează numărul liniei
* `-c` → afișează doar numărul de linii care se potrivesc
* `-v` → inversează (afișează liniile care **nu** conțin pattern-ul)
* `-w` → caută doar cuvinte întregi
* `-r` → caută recursiv în directoare
* `-l` → afișează doar numele fișierelor care conțin potriviri
* `-L` → afișează doar fișierele care **nu** au potriviri
* `-E` → folosește expresii regulate extinse (echivalent `egrep`)
* `-o` → afișează doar partea care se potrivește, nu linia întreagă
* `--color=auto` → evidențiază potrivirile

---

## 3. Exemple practice (20)

### 1. Căutare simplă

```bash
grep "error" logfile.txt
```

### 2. Căutare case-insensitive

```bash
grep -i "error" logfile.txt
```

### 3. Afișează cu număr de linie

```bash
grep -n "main" program.c
```

### 4. Afișează doar numărul de potriviri

```bash
grep -c "student" users.txt
```

### 5. Exclude un pattern

```bash
grep -v "root" /etc/passwd
```

### 6. Potrivire cuvânt întreg

```bash
grep -w "cat" animals.txt
```

### 7. Căutare recursivă în directoare

```bash
grep -r "TODO" src/
```

### 8. Afișează doar fișierele care conțin pattern-ul

```bash
grep -l "main" *.c
```

### 9. Afișează fișierele care NU conțin pattern-ul

```bash
grep -L "main" *.c
```

### 10. Afișează doar potrivirea (nu întreaga linie)

```bash
grep -o "error" logfile.txt
```

### 11. Evidențiere potriviri

```bash
grep --color=auto "error" logfile.txt
```

### 12. Linii care încep cu o literă

```bash
grep "^[A-Z]" file.txt
```

### 13. Linii care se termină cu `.txt`

```bash
grep "txt$" filelist.txt
```

### 14. Expresii regulate extinse

```bash
grep -E "dog|cat" pets.txt
```

### 15. Numere pe linie

```bash
grep "^[0-9]" data.txt
```

### 16. Potriviri multiple (regex)

```bash
grep -E "(error|warning)" logfile.txt
```

### 17. Găsește linii goale

```bash
grep "^$" file.txt
```

### 18. Găsește linii care NU sunt goale

```bash
grep -v "^$" file.txt
```

### 19. Combinație cu alte comenzi (pipe)

```bash
ps -ef | grep firefox
ls -l | grep ".txt"
```

### 20. Căutare într-o arhivă de loguri

```bash
zgrep "error" logfile.gz
```

---

## 4. Rezumat

* **Căutare simplă:** `grep "pattern" file`
* **Căutare insensibilă la caz:** `grep -i`
* **Număr linii:** `grep -c`
* **Linii fără potriviri:** `grep -v`
* **Recursiv:** `grep -r`
* **Doar potrivirea:** `grep -o`
* **Regex extins:** `grep -E`
* **Fișiere cu/ fără potriviri:** `-l` / `-L`

👉 `grep` = cel mai rapid mod de a filtra textul, mai ales împreună cu alte comenzi prin `|`.

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

# 🔹 `ssh` – Rezumat complex

## 1. Teorie

* **Ce este `ssh`?**
  `ssh` (*Secure Shell*) este protocolul standard pentru conectare securizată la servere și echipamente remote.
  Oferă: autentificare sigură, criptare a traficului și tuneluri securizate.

* **Componente**:

  * **ssh client** – comanda pe care o rulezi din terminal.
  * **sshd** – serviciul (daemon) care rulează pe server și acceptă conexiuni.

* **Sintaxă generală**:

  ```bash
  ssh [OPȚIUNI] user@host [command]
  ```

---

## 2. Opțiuni importante

* `-p` → specifică portul (implicit 22)
* `-i` → folosește un fișier cheie privată pentru autentificare
* `-L` → forward port local (tunel local)
* `-R` → forward port remote (tunel invers)
* `-N` → nu execută comandă, doar ține conexiunea (folosit la tuneluri)
* `-T` → dezactivează alocarea unui pseudo-terminal (folosit la scripturi)
* `-v` → verbose/debug (mai multe `-v` = mai detaliat)
* `-X` / `-Y` → activează forwarding grafic (X11 forwarding)

---

## 3. Exemple practice (20)

### 1. Conectare simplă

```bash
ssh user@server
```

### 2. Specificare port

```bash
ssh -p 2222 user@server
```

### 3. Conectare și executarea unei comenzi remote

```bash
ssh user@server "uptime"
```

### 4. Conectare cu key file specific

```bash
ssh -i ~/.ssh/id_rsa user@server
```

### 5. Debugging conexiune

```bash
ssh -v user@server
```

### 6. Copiere key publică pe server (autentificare fără parolă)

```bash
ssh-copy-id user@server
```

### 7. Forward port local (tunel local)

```bash
ssh -L 8080:localhost:80 user@server
```

→ accesezi `http://localhost:8080` și vezi portul 80 al serverului.

### 8. Forward port remote (tunel invers)

```bash
ssh -R 9090:localhost:22 user@server
```

→ serverul poate accesa portul 9090 pentru a se conecta la SSH-ul tău local.

### 9. Doar tunel (fără shell)

```bash
ssh -N -L 8080:localhost:80 user@server
```

### 10. Conectare fără terminal interactiv (script)

```bash
ssh -T git@github.com
```

### 11. Rulare comandă ca root prin sudo

```bash
ssh user@server "sudo systemctl restart nginx"
```

### 12. Scurtături prin config

Fișier `~/.ssh/config`:

```
Host myserver
  HostName 192.168.1.50
  User student
  Port 2222
```

Conectare:

```bash
ssh myserver
```

### 13. Proxy prin alt server (jump host)

```bash
ssh -J user@gateway user@internal
```

### 14. Forward grafic (X11)

```bash
ssh -X user@server
xclock
```

### 15. Transfer fișier prin `scp`

```bash
scp file.txt user@server:/home/user/
```

### 16. Transfer recursiv director prin `scp`

```bash
scp -r project/ user@server:/home/user/
```

### 17. Copiere fișiere cu progres (folosind `rsync` prin SSH)

```bash
rsync -avz -e ssh project/ user@server:/home/user/project/
```

### 18. Test rapid dacă portul SSH este deschis

```bash
ssh -p 22 -o ConnectTimeout=5 user@server "exit"
```

### 19. Evitare verificare host key (⚠️ nesigur)

```bash
ssh -o StrictHostKeyChecking=no user@server
```

### 20. Multiplexare conexiuni (reutilizare)

```bash
ssh -M -S /tmp/sshsocket user@server
ssh -S /tmp/sshsocket user@server "uptime"
ssh -S /tmp/sshsocket -O exit user@server
```

---

## 4. Rezumat

* **Conectare simplă:** `ssh user@host`
* **Port custom:** `-p`
* **Autentificare cu key:** `-i`
* **Tunel local / remote:** `-L` / `-R`
* **Fără shell:** `-N`
* **Forward grafic:** `-X`
* **Debugging:** `-v`
* **Copiere fișiere:** `scp`, `rsync`

👉 `ssh` = instrumentul de bază pentru administrare la distanță, tuneluri securizate și transfer fișiere.

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

# 🔹 `ssh` – Conectare fără parolă

## 1. Teorie

* În loc să tastezi parola de fiecare dată, poți folosi **chei SSH**.
* O cheie SSH are două părți:

  * **private key** (rămâne pe client, de ex. `~/.ssh/id_rsa`)
  * **public key** (pusă pe server, în `~/.ssh/authorized_keys`).
* Odată configurat, te autentifici doar cu cheia, fără parolă.

---

## 2. Pași de configurare

### 1. Generează o pereche de chei

```bash
ssh-keygen -t rsa -b 4096 -C "student@myhost"
```

* `-t rsa` → tipul cheii
* `-b 4096` → lungime (mai sigur)
* `-C` → comentariu (opțional, apare în fișier)

Cheia privată: `~/.ssh/id_rsa`
Cheia publică: `~/.ssh/id_rsa.pub`

---

### 2. Copiază cheia publică pe server

```bash
ssh-copy-id user@server
```

sau manual:

```bash
cat ~/.ssh/id_rsa.pub | ssh user@server "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

---

### 3. Conectează-te fără parolă

```bash
ssh user@server
```

---

## 3. Exemple practice

**1. Generare cheie nouă fără parolă suplimentară**

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -N ""
```

(`-N ""` → fără passphrase pe cheie, conectare directă)

**2. Copiere rapidă cheie**

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@server
```

**3. Verificare pe server (cheia instalată)**

```bash
cat ~/.ssh/authorized_keys
```

**4. Conectare folosind cheia explicit**

```bash
ssh -i ~/.ssh/id_ed25519 user@server
```

**5. Definire shortcut în `~/.ssh/config`**

```
Host myserver
  HostName 192.168.1.100
  User student
  IdentityFile ~/.ssh/id_ed25519
```

Conectare rapidă:

```bash
ssh myserver
```

---

👉 **Rezumat pentru examene:**

* `ssh-keygen` → generezi cheie privată/publică
* `ssh-copy-id user@server` → instalezi cheia pe server
* `ssh user@server` → te conectezi fără parolă
* `~/.ssh/config` → scurtături și configurări personalizate

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---

# 📌 Exemple utile

---

## 🔹 Fișier cu 2500 de linii

**Pe scurt:**

```bash
mkdir -p ciocolata
yes "There is nothing better than a friend, unless chocolate" | head -n 2500 > ciocolata/neagra
```

**Verificare:**

```bash
wc -l ciocolata/neagra   # trebuie să arate 2500
```

---

## 🔹 Script simplu (2500 linii)

```bash
#!/bin/bash
# Generează fișierul ciocolata/neagra cu 2500 de linii

mkdir -p ciocolata
for i in $(seq 2500); do
  echo "There is nothing better than a friend, unless chocolate"
done > ciocolata/neagra

echo "Fișierul a fost creat cu $(wc -l < ciocolata/neagra) linii."
```

**Rulare:**

```bash
chmod +x script.sh
./script.sh
```

---

## 🔹 Creare fișiere numerotate (one-liner)

```bash
for i in $(seq 1 10); do echo "Hello world $i" > file_$i.txt; done
```

🔎 Explicație:

* `seq 1 10` → numere 1…10
* `file_$i.txt` → creează `file_1.txt … file_10.txt`
* conținut: „Hello world X”

**Copiere fișier existent de 10 ori:**

```bash
for i in $(seq 1 10); do cp original.txt copy_$i.txt; done
```

---

## 🔹 Script `find_files.sh` (a) – fișiere, dimensiune, owner

```bash
#!/bin/bash
# list files with name, size, owner

if [ $# -lt 1 ]; then
  echo "Usage: $0 <directory>"
  exit 1
fi

DIR="$1"

find "$DIR" -type f -exec stat -c "File: %n | Size: %s bytes | Owner: %U" {} \;
```

---

## 🔹 (b) Doar fișiere > 1 KB

```bash
find "$DIR" -type f -size +1k -exec stat -c "File: %n | Size: %s bytes | Owner: %U" {} \;
```

---

## 🔹 (c) Interval de dimensiuni

```bash
find "$DIR" -type f -size +"$MIN"c -size -"$MAX"c \
  -exec stat -c "File: %n | Size: %s bytes | Owner: %U" {} \;
```

🔎 Parametri:

* `MIN` = dimensiune minimă (bytes)
* `MAX` = dimensiune maximă (bytes)

**Exemplu:**

```bash
./find_files.sh /etc 1024 2048
```

---

## 🔹 (d) Script – `ls` cu probabilitate 1/3

```bash
#!/bin/bash
# ls cu probabilitate de 1/3

if [ $((RANDOM % 3)) -eq 0 ]; then
  ls
else
  echo "Nu se execută ls."
fi
```

---

## 🔹 (b) One-liner – dimensiune cache L2

```bash
lscpu | grep "L2 cache" | awk '{print $3}'
```

sau:

```bash
lscpu | awk '/L2 cache/ {print $NF}'
```

🔎 Afișează strict dimensiunea cache L2 (ex: `256K`).

---

## 🔹 AWK — printează 2 coloane (artist + album) din fișier TAB-delimitat `music.txt`



```bash
awk -F'\t' '{print "Artist:", $1, "| Album:", $2}' music.txt
```

### 1) GREP + AWK — filtrează după „Warner Bros” și printează coloanele 1 și 2

```bash
grep -F "Warner Bros" music.txt | awk -F'\t' '{print $1, $2}'
```

### 2) CUT — afișează **doar partea până la primul punct** din fiecare linie (ex. `practice_text.txt`)

```bash
cut -d'.' -f1 practice_text.txt
```

Foarte bună întrebare 👌

În exemplul de mai sus:

```bash
awk -F'\t' '{print "Artist:", $1, "| Album:", $2}' music.txt
```

* **`-F`** = *field separator* (separator de câmpuri)
* Implicit, `awk` împarte fiecare linie după **spațiu/tab**.
* Dacă specifici `-F'\t'`, îi spui lui `awk` că **delimitatorul e TAB**.

🔎 Exemple:

1. Dacă ai fișier delimitat prin **virgulă (CSV)**:

```bash
awk -F',' '{print $1, $2}' fisier.csv
```

2. Dacă e delimitat prin **două puncte** (ca `/etc/passwd`):

```bash
awk -F':' '{print $1, $3}' /etc/passwd
```

---

# ⬆️ [**Înapoi la Cuprins**](#-cuprins)

---
