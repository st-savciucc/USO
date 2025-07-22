# Useful Linux Commands

## 1. Creating a Symbolic Link

**Command:**

```bash
ln -s /path/to/source /path/to/destination
```

* **`/path/to/source`**: The original file or directory you want to link to.
* **`/path/to/destination`**: The name and location of the symbolic link that will be created.

When you run this command, `destination` will point to `source`. If you list `destination`, you'll see something like:

```bash
$ ls -l destination
destination -> /path/to/source
```

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
