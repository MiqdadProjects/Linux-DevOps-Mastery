# 👥 Module 03: User and Group Management

Welcome back, Linux adventurers! 🎉 In Module 02, you mastered the Linux file system like a pro. Now, it’s time to take control of **users and groups**—the keys to managing access and permissions in Linux. Whether you’re setting up a secure server or collaborating on a DevOps project, this module will empower you to create, manage, and secure user accounts with confidence! 🔐 Let’s dive in and become Linux permission ninjas! 🥷

## 📋 Table of Contents
- [Understanding Linux Users](#understanding-linux-users)
- [User Account Files](#user-account-files)
- [User Management Commands](#user-management-commands)
- [Group Management](#group-management)
- [Password Management](#password-management)
- [Sudo and Root Access](#sudo-and-root-access)
- [SSH Key Authentication](#ssh-key-authentication)
- [Practice Exercises](#practice-exercises)
- [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)
- [Additional Resources](#additional-resources)
- [Module Completion Checklist](#module-completion-checklist)

---

## 👤 Understanding Linux Users

Linux is a **multi-user system**, meaning multiple users can work simultaneously, each with their own permissions and environment. Think of users as tenants in a secure building, each with their own key (password) and apartment (home directory). Let’s break it down! 🏠

### Types of Users

1. **Root User (Superuser)** 👑
   - **Username**: `root`
   - **UID**: `0`
   - **Home**: `/root`
   - **Privileges**: Full system access, can do *anything* (use with caution!)
   - **Use Case**: System administration, critical updates

2. **System Users** 🤖
   - **UID**: 1–999 (typically)
   - **Purpose**: Run services (e.g., `www-data` for web servers, `mysql` for databases)
   - **Login**: Usually no shell (`/usr/sbin/nologin`)
   - **Example**: `daemon`, `postfix`

3. **Regular Users** 😎
   - **UID**: 1000+ (Ubuntu/Debian, RHEL/CentOS 7+)
   - **Purpose**: Everyday users for development, DevOps tasks
   - **Privileges**: Limited, controlled by groups and `sudo`
   - **Example**: `john`, `alice`

**Try it**:
```bash
# Check your username
whoami
# Example Output: john

# View user details
id
# Output: uid=1000(john) gid=1000(john) groups=1000(john),27(sudo)

# List all logged-in users
who
# Output: john pts/0 2025-10-27 14:00 (192.168.1.100)

# Detailed session info
w
# Output: Shows user, TTY, login time, and running processes
```

**Pro Tip**: Use `id username` to check any user’s details, even if they’re not logged in! 🔍

---

## 📄 User Account Files

Linux stores user and group information in specific files under `/etc`. These are the blueprints for access control. Let’s explore them! 📚

### 1. `/etc/passwd` - User Account Information

Contains user metadata (not passwords, despite the name).

**Format**: `username:x:UID:GID:comment:home:shell`

**Example Entry**:
```bash
john:x:1000:1000:John Doe:/home/john:/bin/bash
```

**Field Breakdown**:
- `john`: Username
- `x`: Password placeholder (actual password in `/etc/shadow`)
- `1000`: User ID (UID)
- `1000`: Primary Group ID (GID)
- `John Doe`: Comment/Full name (GECOS field)
- `/home/john`: Home directory
- `/bin/bash`: Login shell

**Examples**:
```bash
# View all users
cat /etc/passwd

# Find a specific user
grep john /etc/passwd
# Output: john:x:1000:1000:John Doe:/home/john:/bin/bash

# Count total users
wc -l /etc/passwd
# Output: 35 /etc/passwd

# List usernames only
cut -d: -f1 /etc/passwd | sort
# Output: alice, bob, john, root, etc.

# Find users with bash shell
grep "/bin/bash" /etc/passwd
```

### 2. `/etc/shadow` - Encrypted Passwords

Stores encrypted passwords and password policies (root access required).

**Format**: `username:password:last_change:min:max:warn:inactive:expire`

**Example Entry**:
```bash
john:$6$rounds=5000$salt$hash:19500:7:90:7:::
```

**Field Breakdown**:
- `john`: Username
- `$6$...`: Encrypted password (SHA-512)
- `19500`: Days since Jan 1, 1970, when password was last changed
- `7`: Minimum days before password change
- `90`: Maximum days password is valid
- `7`: Warning days before expiration
- Empty: Inactive days after expiration
- Empty: Account expiration date

**Examples**:
```bash
# View shadow file (requires root)
sudo cat /etc/shadow

# Check password aging for a user
sudo chage -l john
# Output: Last password change, min/max days, etc.

# View encrypted passwords (root only)
sudo cut -d: -f2 /etc/shadow
```

### 3. `/etc/group` - Group Information

Defines groups and their members.

**Format**: `groupname:x:GID:members`

**Example Entry**:
```bash
developers:x:1001:john,alice,bob
```

**Examples**:
```bash
# View all groups
cat /etc/group

# Find a specific group
grep developers /etc/group
# Output: developers:x:1001:john,alice,bob

# List group names
cut -d: -f1 /etc/group | sort
# Output: adm, developers, docker, sudo, etc.

# Check user’s groups
groups john
# Output: john : john sudo developers
```

### 4. `/etc/gshadow` - Group Passwords

Stores encrypted group passwords (rarely used).

**Example**:
```bash
# View gshadow (requires root)
sudo cat /etc/gshadow
# Output: developers:!::john,alice,bob
```

**Pro Tip**: Always back up `/etc/passwd`, `/etc/shadow`, and `/etc/group` before editing:
```bash
sudo cp /etc/passwd /etc/passwd.bak
sudo cp /etc/shadow /etc/shadow.bak
sudo cp /etc/group /etc/group.bak
```

---

## 🔧 User Management Commands

Managing users is a core DevOps skill. Let’s master the commands to create, modify, and delete users! 🛠️

### Creating Users

#### Method 1: `useradd` (Low-level, customizable)
```bash
# Basic user creation
sudo useradd john

# Create user with home directory
sudo useradd -m john

# Specify shell
sudo useradd -m -s /bin/bash john

# Set custom UID
sudo useradd -m -u 1050 john

# Add comment
sudo useradd -m -c "John Doe, DevOps Engineer" john

# Assign primary group
sudo useradd -m -g developers john

# Add to secondary groups
sudo useradd -m -G developers,docker,sudo john

# Full example
sudo useradd -m -s /bin/bash -c "John Doe" -u 1050 -G sudo,developers john
sudo passwd john
```

**Common `useradd` Options**:
- `-m`: Create home directory
- `-d /path`: Specify home directory
- `-s /shell`: Set login shell
- `-u UID`: Set user ID
- `-g group`: Set primary group
- `-G groups`: Set secondary groups
- `-c comment`: Add user description
- `-e YYYY-MM-DD`: Set account expiration

#### Method 2: `adduser` (Interactive, Ubuntu/Debian)
```bash
# Interactive user creation
sudo adduser alice
# Prompts for password, full name, and sets up home directory
```

**Example**:
```bash
# Create a user with adduser
sudo adduser devops
# Follow prompts to set password and details
# Verify
id devops
ls -ld /home/devops
```

### Modifying Users

```bash
# Change username
sudo usermod -l newjohn john

# Move home directory
sudo usermod -d /newhome/john -m john

# Change shell to zsh
sudo usermod -s /bin/zsh john

# Add to group
sudo usermod -aG docker john
# Note: -a (append) prevents overwriting existing groups

# Add to multiple groups
sudo usermod -aG docker,developers,sudo john

# Change UID
sudo usermod -u 1100 john

# Lock account
sudo usermod -L john

# Unlock account
sudo usermod -U john

# Set account expiration
sudo usermod -e 2025-12-31 john
```

**Example**:
```bash
# Add user to sudo group
sudo usermod -aG sudo alice
groups alice
# Output: alice : alice sudo developers
```

### Deleting Users

```bash
# Delete user (keep home directory)
sudo userdel john

# Delete user and home directory
sudo userdel -r john

# Force delete, including if logged in
sudo userdel -rf john
```

**Example**:
```bash
# Delete user and verify
sudo userdel -r devops
ls /home/devops
# Output: ls: cannot access '/home/devops': No such file or directory
```

### Viewing User Information

```bash
# Check if user exists
id john
# Output: uid=1000(john) gid=1000(john) groups=1000(john),27(sudo)

# View user details
finger john
# Output: Login, Name, Directory, Shell, Last login

# Check last login
lastlog | grep john

# Get user’s passwd entry
getent passwd john
# Output: john:x:1000:1000:John Doe:/home/john:/bin/bash

# Check password status
sudo passwd -S john
# Output: john P 10/27/2025 7 90 7 -1
```

**Pro Tip**: Use `getent` for reliable user lookups, as it checks both local files and network databases (e.g., LDAP).

---

## 👥 Group Management

Groups simplify permission management by grouping users with similar access needs. Think of groups as teams with shared access to resources! 🤝

### Creating Groups

```bash
# Create a group
sudo groupadd developers

# Create group with specific GID
sudo groupadd -g 1050 devops

# Force creation (override conflicts)
sudo groupadd -f developers
```

**Example**:
```bash
# Create and verify group
sudo groupadd devteam
grep devteam /etc/group
# Output: devteam:x:1002:
```

### Modifying Groups

```bash
# Rename group
sudo groupmod -n newdevteam devteam

# Change group ID
sudo groupmod -g 1100 devteam

# Add user to group
sudo usermod -aG devteam john

# Add multiple users
sudo gpasswd -M john,alice,bob devteam

# Remove user from group
sudo gpasswd -d alice devteam
```

**Example**:
```bash
# Add users to group and verify
sudo gpasswd -M john,alice devteam
getent group devteam
# Output: devteam:x:1002:john,alice
```

### Deleting Groups

```bash
# Delete a group
sudo groupdel devteam
```

**Example**:
```bash
# Delete and verify
sudo groupdel devteam
grep devteam /etc/group
# Output: (nothing, group deleted)
```

### Viewing Groups

```bash
# List all groups
cat /etc/group | sort

# View current user’s groups
groups
# Output: john sudo developers

# View specific user’s groups
groups alice
# Output: alice : alice sudo

# View group members
getent group developers
# Output: developers:x:1001:john,alice,bob
```

**Pro Tip**: Use groups to manage permissions for shared resources (e.g., a `/var/www/html` directory for web developers):
```bash
sudo chgrp -R developers /var/www/html
sudo chmod -R g+rw /var/www/html
```

---

## 🔐 Password Management

Secure password management is critical for Linux security. Let’s lock down those accounts! 🔒

### Setting and Changing Passwords

```bash
# Set password for user
sudo passwd john
# Prompt: Enter new UNIX password:

# Change your own password
passwd
# Prompt: Enter current password, then new password

# Generate random password
openssl rand -base64 12
# Output: kJ9xPqW3mL4nZ7v8

# Set password non-interactively
echo "john:SecurePass123!" | sudo chpasswd
```

**Example**:
```bash
# Set a random password for a user
PASS=$(openssl rand -base64 12)
echo "alice:$PASS" | sudo chpasswd
echo "Alice's password: $PASS"
```

### Password Aging

Control how often passwords must be changed.

```bash
# View password aging info
sudo chage -l john
# Output: Last change, min/max days, etc.

# Set minimum days before change
sudo chage -m 7 john

# Set maximum password validity
sudo chage -M 90 john

# Set warning period
sudo chage -W 7 john

# Set inactive period after expiration
sudo chage -I 30 john

# Set account expiration
sudo chage -E 2025-12-31 john

# Force password change on next login
sudo chage -d 0 john

# Interactive aging setup
sudo chage john
```

**Example**:
```bash
# Force password change at login
sudo chage -d 0 alice
# Alice must change password on next login
```

### Password Policies

Edit `/etc/login.defs` for system-wide password settings.

**Example**:
```bash
# View password policies
grep "^PASS" /etc/login.defs
# Output:
# PASS_MAX_DAYS 90
# PASS_MIN_DAYS 7
# PASS_MIN_LEN 8
# PASS_WARN_AGE 7

# Edit policies (backup first)
sudo cp /etc/login.defs /etc/login.defs.bak
sudo nano /etc/login.defs
```

### Lock/Unlock Accounts

```bash
# Lock account
sudo passwd -l john
# or
sudo usermod -L john

# Unlock account
sudo passwd -u john
# or
sudo usermod -U john

# Check lock status
sudo passwd -S john
# Output: john L (locked) or john P (password set)
```

**Example**:
```bash
# Lock and verify
sudo passwd -l devops
sudo passwd -S devops
# Output: devops L 10/27/2025 7 90 7 -1
```

**Pro Tip**: Use strong passwords (12+ characters, mixed case, numbers, symbols) and enforce policies via `/etc/login.defs` for production systems.

---

## 🔑 Sudo and Root Access

The `sudo` command lets authorized users run commands as root or another user. It’s your superpower for system administration, but use it wisely! ⚡

### Configuring `sudo`

The `/etc/sudoers` file defines who can use `sudo`. Always edit it with `visudo` to avoid syntax errors.

```bash
# Edit sudoers file
sudo visudo
```

**Example `sudoers` Entries**:
```bash
# Allow user full access
john ALL=(ALL:ALL) ALL

# Allow without password
john ALL=(ALL) NOPASSWD: ALL

# Allow specific commands
john ALL=(ALL) /usr/bin/apt,/usr/bin/systemctl

# Allow group access
%developers ALL=(ALL:ALL) ALL

# Allow specific command without password
john ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart nginx
```

**Add User to Sudo Group**:
```bash
# Ubuntu/Debian
sudo usermod -aG sudo john

# RHEL/CentOS
sudo usermod -aG wheel john
```

### Using `sudo`

```bash
# Run command as root
sudo apt update

# Run as specific user
sudo -u alice whoami
# Output: alice

# Switch to root shell
sudo su -
# or
sudo -i

# Run last command with sudo
sudo !!

# Edit file as root
sudo nano /etc/hosts

# Check sudo privileges
sudo -l
# Output: Lists allowed commands

# Validate sudoers syntax
sudo visudo -c
# Output: /etc/sudoers: parsed OK
```

**Example**:
```bash
# Restart Nginx with sudo
sudo systemctl restart nginx
sudo systemctl status nginx
```

### Sudo Best Practices

1. **Use `visudo`**: Prevents syntax errors in `/etc/sudoers`.
2. **Minimal Privileges**: Grant only necessary permissions:
   ```bash
   john ALL=(ALL) /usr/bin/systemctl restart nginx
   ```
3. **Use Groups**: Manage permissions via groups (`sudo`, `wheel`).
4. **Log Sudo Usage**: Enable logging in `/etc/sudoers`:
   ```bash
   Defaults logfile="/var/log/sudo.log"
   ```
5. **Require Passwords for Sensitive Actions**: Avoid blanket `NOPASSWD`.

**Pro Tip**: Create aliases for frequent `sudo` commands:
```bash
echo "alias restart-nginx='sudo systemctl restart nginx'" >> ~/.bashrc
source ~/.bashrc
```

---

## 🔐 SSH Key Authentication

SSH keys provide secure, passwordless access to servers—perfect for automation and DevOps workflows! 🔑

### Generating SSH Keys

```bash
# Generate Ed25519 key (recommended, more secure)
ssh-keygen -t ed25519 -C "john@devops-lab" -f ~/.ssh/id_ed25519
# Prompts for passphrase (optional)

# Generate RSA key (4096-bit)
ssh-keygen -t rsa -b 4096 -C "john@devops-lab" -f ~/.ssh/id_rsa

# Output files:
# ~/.ssh/id_ed25519     (private key - KEEP SECRET)
# ~/.ssh/id_ed25519.pub (public key - shareable)
```

**Example**:
```bash
# Generate key with no passphrase
ssh-keygen -t ed25519 -C "john@devops-lab" -N "" -f ~/.ssh/my_key
ls ~/.ssh/
# Output: my_key  my_key.pub
```

### Setting Up SSH Key Authentication

```bash
# Copy public key to remote server
ssh-copy-id -i ~/.ssh/my_key.pub john@remote-server
# Prompts for password

# Manual method
cat ~/.ssh/my_key.pub | ssh john@remote-server "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"

# Set permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/my_key
chmod 644 ~/.ssh/my_key.pub
```

**Example**:
```bash
# Copy key and test
ssh-copy-id -i ~/.ssh/id_ed25519.pub john@192.168.1.100
ssh john@192.168.1.100
# Should connect without password
```

### Using SSH Keys

```bash
# Connect with specific key
ssh -i ~/.ssh/my_key john@remote-server

# Configure SSH for easy access
nano ~/.ssh/config
```

**Example `~/.ssh/config`**:
```bash
Host devserver
    HostName 192.168.1.100
    User john
    IdentityFile ~/.ssh/my_key
    Port 22
```

```bash
# Connect using alias
ssh devserver
```

### SSH Security

Harden your SSH server for production use.

```bash
# Edit SSH config
sudo nano /etc/ssh/sshd_config

# Recommended settings
PasswordAuthentication no
PubkeyAuthentication yes
PermitRootLogin no
Port 2222  # Change default port

# Restart SSH service
sudo systemctl restart sshd
sudo systemctl status sshd
```

**Pro Tip**: Test SSH config before restarting:
```bash
sudo sshd -t
# Output: (none if valid, errors if invalid)
```

---

## 🛠️ Practice Exercises

Get hands-on to master user and group management! 💪

### Exercise 1: User Creation and Deletion
1. Create a user `devops` with a home directory and Bash shell:
   ```bash
   sudo useradd -m -s /bin/bash devops
   sudo passwd devops
   ```
2. Verify creation:
   ```bash
   id devops
   ls -ld /home/devops
   ```
3. Delete the user and home directory:
   ```bash
   sudo userdel -r devops
   ls /home/devops
   # Output: ls: cannot access '/home/devops': No such file or directory
   ```

### Exercise 2: Group Management
1. Create a group `devteam`:
   ```bash
   sudo groupadd devteam
   ```
2. Create two users and add to `devteam`:
   ```bash
   sudo useradd -m alice
   sudo useradd -m bob
   sudo usermod -aG devteam alice
   sudo usermod -aG devteam bob
   ```
3. Verify group membership:
   ```bash
   getent group devteam
   # Output: devteam:x:1002:alice,bob
   ```
4. Remove `alice` from `devteam`:
   ```bash
   sudo gpasswd -d alice devteam
   getent group devteam
   # Output: devteam:x:1002:bob
   ```

### Exercise 3: Sudo Configuration
1. Add a user to the `sudo` group:
   ```bash
   sudo usermod -aG sudo bob
   ```
2. Test sudo access:
   ```bash
   sudo -u bob sudo -l
   ```
3. Allow `bob` to restart Nginx without a password:
   ```bash
   sudo visudo
   # Add: bob ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart nginx
   ```
4. Test the command:
   ```bash
   sudo -u bob systemctl restart nginx
   ```

### Exercise 4: SSH Key Setup
1. Generate an Ed25519 key:
   ```bash
   ssh-keygen -t ed25519 -C "test@devops-lab" -N "" -f ~/.ssh/test_key
   ```
2. Copy the key to a remote server:
   ```bash
   ssh-copy-id -i ~/.ssh/test_key.pub john@192.168.1.100
   ```
3. Connect using the key:
   ```bash
   ssh -i ~/.ssh/test_key john@192.168.1.100
   ```
4. Harden SSH:
   ```bash
   sudo nano /etc/ssh/sshd_config
   # Set: PasswordAuthentication no
   sudo systemctl restart sshd
   ```

### Exercise 5: Password Management
1. Set a password policy for a user:
   ```bash
   sudo chage -M 90 -m 7 -W 7 alice
   ```
2. Verify the policy:
   ```bash
   sudo chage -l alice
   ```
3. Force password change on next login:
   ```bash
   sudo chage -d 0 alice
   ```

---

## 📚 Quick Reference Cheat Sheet

```bash
# User Management
sudo useradd -m -s /bin/bash user  # Create user with home
sudo passwd user                   # Set password
sudo usermod -aG group user       # Add to group
sudo userdel -r user              # Delete user

# Group Management
sudo groupadd group               # Create group
sudo groupmod -n new old          # Rename group
sudo groupdel group               # Delete group
getent group group                # View group members

# Password Management
sudo passwd user                  # Change password
sudo chage -l user                # View password aging
sudo passwd -l user               # Lock account
sudo chage -E YYYY-MM-DD user     # Set expiration

# Sudo
sudo visudo                       # Edit sudoers
sudo -l                           # List privileges
sudo -u user command              # Run as user
sudo su -                         # Switch to root

# SSH Keys
ssh-keygen -t ed25519             # Generate key
ssh-copy-id user@host             # Copy key
ssh -i key user@host              # Connect with key
sudo systemctl restart sshd       # Restart SSH
```

---

## 📖 Additional Resources

- **[Linux Documentation Project: Users](https://tldp.org/LDP/intro-linux/html/sect_03_01.html)**: Guide to user management.
- **[Ubuntu: User Management](https://help.ubuntu.com/stable/ubuntu-help/user-accounts.html)**: Ubuntu-specific tips.
- **[Red Hat: Managing Users](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_users_and_groups)**: Enterprise-grade advice.
- **[OpenSSH Manual](https://www.openssh.com/manual.html)**: SSH configuration details.
- **[Linux Journey: Users](https://linuxjourney.com/lesson/users-and-groups)**: Interactive user management lessons.
- **[OverTheWire Bandit](https://overthewire.org/wargames/bandit/)**: Fun SSH and user challenges.

**Community Tip**: Join [Reddit r/linuxadmin](https://www.reddit.com/r/linuxadmin/) or [Stack Exchange: Unix & Linux](https://unix.stackexchange.com/) for expert advice! 🗣️

---

## ✅ Module Completion Checklist

- [ ] Understand Linux user types (root, system, regular)
- [ ] Master user account files (`/etc/passwd`, `/etc/shadow`, `/etc/group`)
- [ ] Create, modify, and delete users with `useradd`, `usermod`, `userdel`
- [ ] Manage groups with `groupadd`, `groupmod`, `groupdel`
- [ ] Configure password policies with `chage` and `/etc/login.defs`
- [ ] Set up and use `sudo` for privileged access
- [ ] Implement SSH key authentication and secure SSH
- [ ] Complete all practice exercises

---

**Previous Module**: [Module 02: Linux Folder Structure & File System](../02-folder-structure/LinuxFolderStructure.md)  
**Next Module**: [Module 04: File Management](../04-file-management/FileManagement.md)

[⬆ Back to Main README](../README.md)