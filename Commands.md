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
``` ✅
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

## 3. Taking a Screenshot to Clipboard

**Command:**

```bash
gnome-screenshot -a -f /tmp/ss.png && xclip -selection clipboard -t image/png -i /tmp/ss.png
```

* **`-a`**: Allows selecting a region with the mouse.
* **`-f /tmp/ss.png`**: Specifies the output file path.
* **`xclip -selection clipboard -t image/png -i /tmp/ss.png`**: Copies the saved image into the system clipboard.

After running this command, you can paste (Ctrl+V) the screenshot directly into any application that supports image pasting.
