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

### Veriy
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


