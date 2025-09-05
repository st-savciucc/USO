# Useful Linux Commands

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
