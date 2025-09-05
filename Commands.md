# Useful Linux Commands

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
