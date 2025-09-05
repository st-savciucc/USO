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