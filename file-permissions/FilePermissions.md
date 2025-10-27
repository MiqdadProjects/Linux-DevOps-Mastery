# 🔐 Module 06: File Permissions and Ownership

Welcome back, Linux security guardians! 🎉 After conquering Vi/Vim in Module 05, it’s time to lock down your system with **file permissions and ownership**—the cornerstone of Linux security. Whether you’re securing SSH keys, configuring web servers, or managing team projects, mastering permissions is a DevOps superpower. 💪 This guide will transform you into a permissions pro, ensuring your files and directories are safe and accessible only to the right users! 🚀 Let’s dive in!

## 📋 Table of Contents
- [Understanding Linux Permissions](#understanding-linux-permissions)
- [Permission Types](#permission-types)
- [Reading Permissions](#reading-permissions)
- [Changing Permissions - chmod](#changing-permissions---chmod)
- [Changing Ownership - chown](#changing-ownership---chown)
- [Special Permissions](#special-permissions)
- [Default Permissions - umask](#default-permissions---umask)
- [Access Control Lists (ACLs)](#access-control-lists-acls)
- [Practice Exercises](#practice-exercises)
- [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)
- [Security Best Practices](#security-best-practices)
- [Additional Resources](#additional-resources)
- [Module Completion Checklist](#module-completion-checklist)

---

## 🔑 Understanding Linux Permissions

Linux file permissions are the gatekeepers of your system, controlling who can **read**, **write**, or **execute** files and directories. They’re critical for:

- **Security**: Protect sensitive data like `/etc/shadow` or SSH keys. 🔒
- **Multi-user Systems**: Isolate user data in `/home`. 🧑‍💼
- **DevOps**: Secure configs (e.g., `/etc/nginx/nginx.conf`) and scripts. 🛠️
- **System Integrity**: Prevent unauthorized changes to critical files. 🛡️

### Permission Model
Linux uses a **three-tier model**:
1. **Owner (User)**: The file’s creator or assigned owner.
2. **Group**: Users in the file’s assigned group.
3. **Others (World)**: Everyone else on the system.

Each tier has three permissions:
- **Read (r)**: View file contents or list directory contents.
- **Write (w)**: Modify files or create/delete files in directories.
- **Execute (x)**: Run files as programs or enter directories.

**Example**:
```bash
ls -l secret.txt
# Output: -rw-r----- 1 alice developers 1024 Oct 27 14:53 secret.txt
# Owner (alice): rw- (read, write)
# Group (developers): r-- (read only)
# Others: --- (no access)
```

---

## 📊 Permission Types

### For Files
| Permission | Symbol | Octal | Meaning |
|------------|--------|-------|---------|
| Read | r | 4 | View file contents (`cat`, `less`) |
| Write | w | 2 | Modify file contents (`vim`, `echo`) |
| Execute | x | 1 | Run file as a program (e.g., `./script.sh`) |
| None | - | 0 | No access |

### For Directories
| Permission | Symbol | Octal | Meaning |
|------------|--------|-------|---------|
| Read | r | 4 | List directory contents (`ls`) |
| Write | w | 2 | Create/delete files in directory |
| Execute | x | 1 | Enter directory (`cd`) or access files within |
| None | - | 0 | No access |

### Permission Combinations
```bash
0   ---   No permissions
1   --x   Execute only (e.g., `cd` into directory)
2   -w-   Write only (rare)
3   -wx   Write and execute
4   r--   Read only
5   r-x   Read and execute
6   rw-   Read and write
7   rwx   Full access
```

**Pro Tip**: Directories need `x` to allow `cd`, even if `r` is set for listing contents.

---

## 👁️ Reading Permissions

### Using `ls -l`
```bash
ls -l
# Output:
# -rw-r--r-- 1 alice developers 1024 Oct 27 14:53 file.txt
# drwxr-xr-x 2 bob devops 4096 Oct 27 14:53 project/
```

### Permission String Breakdown
```
-rw-r--r-- 1 alice developers 1024 Oct 27 14:53 file.txt
│          │ │     │         │    │           │
│          │ │     │         │    │           └─ Filename
│          │ │     │         │    └───────────── Timestamp
│          │ │     │         └────────────────── Size (bytes)
│          │ │     └──────────────────────────── Group
│          │ └────────────────────────────────── Owner
│          └──────────────────────────────────── Hard links
└─────────────────────────────────────────────── Permissions
```

**Permission Details**:
```
-rw-r--r--
│ │  │  │
│ │  │  └── Others: r-- (read only)
│ │  └───── Group: r-- (read only)
│ └──────── Owner: rw- (read, write)
└─────────── Type: - (file)
```

### File Types
```
-   Regular file
d   Directory
l   Symbolic link
b   Block device (e.g., /dev/sda)
c   Character device (e.g., /dev/tty)
p   Named pipe
s   Socket
```

**Examples**:
```bash
# View a config file
ls -l /etc/passwd
# Output: -rw-r--r-- 1 root root 2847 Oct 27 14:53 /etc/passwd

# View a directory
ls -ld /var/log
# Output: drwxr-xr-x 12 root adm 4096 Oct 27 14:53 /var/log

# View hidden files
ls -la ~/
# Output: Shows .bashrc, .ssh/, etc.

# Human-readable sizes
ls -lh /var
# Output: Shows sizes in KB, MB, etc.
```

**Example**:
```bash
# Analyze permissions
ls -l backup.sh
# Output: -rwxr-xr-x 1 alice devops 512 Oct 27 14:53 backup.sh
# Owner: rwx (full access)
# Group: r-x (read, execute)
# Others: r-x (read, execute)
```

**Pro Tip**: Use `stat` for detailed permission info:
```bash
stat /etc/passwd
# Output: Includes access, modify times, and more
```

---

## ⚙️ Changing Permissions - chmod

The `chmod` command modifies file and directory permissions using **symbolic** or **octal** methods.

### Symbolic Method
**Syntax**: `chmod [who][operator][permissions] file`

- **Who**: `u` (user), `g` (group), `o` (others), `a` (all)
- **Operator**: `+` (add), `-` (remove), `=` (set exact)
- **Permissions**: `r` (read), `w` (write), `x` (execute)

```bash
# Add execute for owner
chmod u+x script.sh

# Remove write for group and others
chmod go-w config.conf

# Set exact permissions
chmod u=rw,g=r,o= private.txt  # Owner: rw, Group: r, Others: none

# Add read for all
chmod a+r public.txt

# Recursive for directory
chmod -R u+rwx project/
```

**Example**:
```bash
# Make a script executable
chmod +x deploy.sh
ls -l deploy.sh
# Output: -rwxr-xr-x ... deploy.sh
```

### Octal Method
**Syntax**: `chmod [octal] file`

- Read = 4, Write = 2, Execute = 1
- Add values for each tier (owner, group, others)

```bash
# Common patterns
chmod 755 script.sh      # rwxr-xr-x (scripts, directories)
chmod 644 file.txt      # rw-r--r-- (files, configs)
chmod 600 private.key   # rw------- (sensitive files)
chmod 700 private_dir/  # rwx------ (private directories)

# Calculate: rwxr-xr--
# Owner: rwx = 4+2+1 = 7
# Group: r-x = 4+0+1 = 5
# Others: r-- = 4+0+0 = 4
# Result: 754
chmod 754 file.txt
```

**Example**:
```bash
# Secure SSH keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
ls -la ~/.ssh/
```

**Real-World Scenarios**:
```bash
# Web server files
chmod 644 /var/www/html/index.html
chmod 755 /var/www/html

# Database configs
chmod 640 /etc/mysql/my.cnf
sudo chown mysql:mysql /etc/mysql/my.cnf

# Log files
chmod 640 /var/log/nginx/access.log
sudo chown nginx:adm /var/log/nginx/access.log
```

**Pro Tip**: Use `--reference` to copy permissions:
```bash
chmod --reference=template.conf new.conf
```

---

## 👤 Changing Ownership - chown

The `chown` command changes file or directory ownership, while `chgrp` changes group ownership.

### Changing Owner
```bash
# Change owner
sudo chown alice file.txt

# Recursive
sudo chown -R alice /home/alice/docs/

# Verbose
sudo chown -v bob project.conf
# Output: changed ownership of 'project.conf' to bob
```

### Changing Group
```bash
# Using chown
sudo chown :developers file.txt

# Using chgrp
sudo chgrp developers file.txt

# Recursive
sudo chgrp -R devops /project/
```

### Changing Both
```bash
sudo chown alice:developers project.conf
sudo chown -R nginx:nginx /var/www/html/
```

**Examples**:
```bash
# Web server setup
sudo chown -R www-data:www-data /var/www/html/
chmod -R 755 /var/www/html/

# Database directory
sudo chown -R postgres:postgres /var/lib/postgresql/
chmod -R 700 /var/lib/postgresql/

# Copy ownership
sudo chown --reference=/etc/passwd newfile.txt
```

**Pro Tip**: Use `--from` to change only specific owners:
```bash
sudo chown --from=olduser newuser file.txt
```

---

## 🔒 Special Permissions

Special permissions add advanced control, often used for shared resources or privileged operations.

### SUID (Set User ID) - 4000
Runs executables with the **owner’s permissions**.

```bash
# Set SUID
chmod u+s program
chmod 4755 program

# View
ls -l /usr/bin/passwd
# Output: -rwsr-xr-x ... passwd (s = SUID)
```

**Example**:
```bash
# Allow users to change passwords
ls -l /usr/bin/passwd
# Output: -rwsr-xr-x 1 root root ... passwd
```

### SGID (Set Group ID) - 2000
- **Files**: Run with group’s permissions.
- **Directories**: New files inherit the directory’s group.

```bash
# Set SGID
chmod g+s shared_dir
chmod 2755 shared_dir

# Example: Team project directory
sudo mkdir /team
sudo chgrp developers /team
chmod 2775 /team
touch /team/file.txt
ls -l /team/file.txt
# Output: Group is developers
```

### Sticky Bit - 1000
Only file owners can delete files in a directory, even if others have write access.

```bash
# Set sticky bit
chmod +t public_dir
chmod 1777 public_dir

# View
ls -ld /tmp
# Output: drwxrwxrwt ... /tmp (t = sticky bit)
```

**Example**:
```bash
# Create public directory
sudo mkdir /public
sudo chmod 1777 /public
# Users can create files, but only owners can delete
```

### Special Permissions Summary
| Permission | Octal | Symbol | Use Case |
|------------|-------|--------|----------|
| SUID | 4000 | u+s | Run as owner (e.g., `passwd`) |
| SGID | 2000 | g+s | Inherit group (shared dirs) |
| Sticky Bit | 1000 | +t | Protect files in shared dirs (e.g., `/tmp`) |

```bash
# Combine
chmod 6755 script.sh  # SUID + SGID + rwxr-xr-x
chmod 1777 shared/    # Sticky + rwxrwxrwx
```

**Pro Tip**: Audit SUID/SGID files for security:
```bash
find / -perm -4000 -type f 2>/dev/null  # SUID files
find / -perm -2000 -type f 2>/dev/null  # SGID files
```

---

## 🎭 Default Permissions - umask

The `umask` command sets default permissions for new files and directories.

### How umask Works
- **Default permissions**:
  - Files: 666 (rw-rw-rw-)
  - Directories: 777 (rwxrwxrwx)
- **umask** subtracts permissions from defaults.

```bash
# View umask
umask
# Output: 0022
umask -S
# Output: u=rwx,g=rx,o=rx

# Calculate: umask 0022
# Files: 666 - 022 = 644 (rw-r--r--)
# Dirs: 777 - 022 = 755 (rwxr-xr-x)
```

### Common umask Values
| umask | Files | Directories | Use Case |
|-------|-------|-------------|----------|
| 0022 | 644 | 755 | Default for root |
| 0002 | 664 | 775 | Team collaboration |
| 0077 | 600 | 700 | Private files |
| 0027 | 640 | 750 | Group read-only |

```bash
# Set umask
umask 0027
touch test.txt
mkdir test_dir
ls -l
# Output:
# -rw-r----- ... test.txt
# drwxr-x--- ... test_dir
```

**Permanent umask**:
```bash
# Add to ~/.bashrc
echo "umask 0027" >> ~/.bashrc
source ~/.bashrc

# System-wide
sudo sh -c 'echo "umask 0022" >> /etc/profile'
```

**Example**:
```bash
# Test team collaboration
umask 0002
mkdir shared
touch shared/file.txt
ls -ld shared shared/file.txt
# Output:
# drwxrwxr-x ... shared
# -rw-rw-r-- ... shared/file.txt
```

---

## 📋 Access Control Lists (ACLs)

ACLs provide fine-grained permissions beyond the standard model, ideal for complex access scenarios.

### Checking ACL Support
```bash
# Check filesystem support
mount | grep acl
# Output: /dev/sda1 on / type ext4 (rw,relatime,acl)

# Install ACL tools
sudo apt install acl  # Ubuntu/Debian
sudo dnf install acl  # RHEL/CentOS
```

### Viewing ACLs
```bash
getfacl project.conf
# Output:
# file: project.conf
# owner: alice
# group: developers
# user::rw-
# user:bob:rwx
# group::r--
# mask::rwx
# other::r--
```

### Setting ACLs
```bash
# Grant specific user permissions
setfacl -m u:bob:rwx project.conf

# Grant group permissions
setfacl -m g:devops:rw data.txt

# Default ACL for new files
setfacl -d -m u:bob:rwx /project/

# Recursive
setfacl -R -m u:alice:rwx /shared/

# Remove ACL
setfacl -x u:bob project.conf

# Remove all ACLs
setfacl -b project.conf
```

**Example**:
```bash
# Grant web developer access
sudo setfacl -R -m u:webdev:rwx /var/www/html/
sudo setfacl -R -d -m u:webdev:rwx /var/www/html/
ls -l /var/www/html/
# Output: Files show + (ACL indicator)
```

**Pro Tip**: Check for ACLs with `ls -l` (look for `+`):
```bash
ls -l file.txt
# Output: -rw-r--r--+ ... file.txt
```

---

## 🛠️ Practice Exercises

### Exercise 1: Basic Permissions
1. Create and set permissions:
   ```bash
   touch doc.txt
   chmod 644 doc.txt
   ls -l doc.txt
   # Verify: -rw-r--r--
   ```
2. Make executable:
   ```bash
   chmod u+x doc.txt
   ls -l doc.txt
   # Verify: -rwxr--r--
   ```
3. Set restrictive:
   ```bash
   chmod 600 doc.txt
   ls -l doc.txt
   # Verify: -rw-------
   ```

### Exercise 2: Ownership
1. Create file and change ownership:
   ```bash
   touch app.conf
   sudo chown alice:developers app.conf
   ls -l app.conf
   ```
2. Recursive ownership:
   ```bash
   mkdir -p /app/logs
   sudo chown -R nginx:nginx /app/
   ls -ld /app /app/logs
   ```

### Exercise 3: Special Permissions
1. Set SGID for team directory:
   ```bash
   mkdir /team
   sudo chgrp developers /team
   chmod 2775 /team
   touch /team/file.txt
   ls -l /team/file.txt
   # Verify group: developers
   ```
2. Set sticky bit:
   ```bash
   mkdir /public
   chmod 1777 /public
   ls -ld /public
   # Verify: drwxrwxrwt
   ```

### Exercise 4: umask
1. Test umask:
   ```bash
   umask 0027
   touch test.txt
   mkdir test_dir
   ls -l test.txt test_dir
   # Verify: -rw-r----- and drwxr-x---
   ```
2. Set permanent umask:
   ```bash
   echo "umask 0027" >> ~/.bashrc
   source ~/.bashrc
   ```

### Exercise 5: ACLs
1. Grant specific access:
   ```bash
   touch report.txt
   setfacl -m u:bob:rw report.txt
   getfacl report.txt
   ```
2. Set default ACL:
   ```bash
   mkdir /shared
   setfacl -d -m u:alice:rwx /shared
   touch /shared/newfile.txt
   getfacl /shared/newfile.txt
   ```

---

## 📚 Quick Reference Cheat Sheet

```bash
# View Permissions
ls -l file.txt              # File permissions
ls -ld dir/                 # Directory permissions
stat file.txt               # Detailed info

# chmod (Symbolic)
chmod u+x script.sh         # Add execute for owner
chmod go-w file.txt         # Remove write for group/others
chmod u=rw,g=r,o= file.txt  # Set exact permissions

# chmod (Octal)
chmod 755 script.sh         # rwxr-xr-x
chmod 644 file.txt          # rw-r--r--
chmod 600 private.txt       # rw-------

# chown
sudo chown alice file.txt   # Change owner
sudo chown alice:dev file.txt  # Owner and group
sudo chown -R nginx:nginx dir/ # Recursive

# Special Permissions
chmod 4755 script.sh        # SUID + rwxr-xr-x
chmod 2755 dir/             # SGID + rwxr-xr-x
chmod 1777 dir/             # Sticky + rwxrwxrwx

# umask
umask 0022                  # Default for root
umask 0002                  # Team collaboration
umask 0077                  # Private

# ACLs
getfacl file.txt            # View ACL
setfacl -m u:alice:rwx file.txt  # Set ACL
setfacl -b file.txt         # Remove all ACLs
```

---

## 🔐 Security Best Practices

1. **Least Privilege**: Grant only necessary permissions.
   ```bash
   chmod 600 ~/.ssh/id_rsa
   chmod 700 /home/user/private/
   ```
2. **Avoid 777**: Never use world-writable permissions.
   ```bash
   # Bad practice
   chmod 777 file.txt
   # Instead, use groups
   chgrp devteam file.txt
   chmod 770 file.txt
   ```
3. **Protect Sensitive Files**:
   ```bash
   chmod 600 /etc/shadow
   chmod 644 /etc/passwd
   chmod 600 ~/.netrc
   ```
4. **Audit Special Permissions**:
   ```bash
   find / -perm -4000 -type f 2>/dev/null  # SUID
   find / -perm -2000 -type f 2>/dev/null  # SGID
   find / -perm -0777 2>/dev/null  # World-writable
   ```
5. **Use ACLs for Complex Access**:
   ```bash
   setfacl -m u:contractor:rwx /project/temp/
   ```
6. **Regular Backups**:
   ```bash
   tar -czf /backup/etc-$(date +%Y%m%d).tar.gz /etc/
   chmod 600 /backup/etc-$(date +%Y%m%d).tar.gz
   ```

**Pro Tip**: Use `ls -lZ` to check SELinux contexts if enabled:
```bash
ls -lZ /var/www/html/
```

---

## 📖 Additional Resources

- **[Linux Filesystem Security](https://tldp.org/LDP/Linux-Filesystem-Hierarchy/html/security.html)**: Deep dive into permissions.
- **[Red Hat: Managing Permissions](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/using_the_command_line_interface/managing_file_permissions)**: Enterprise-grade guide.
- **[Ubuntu: Permissions](https://help.ubuntu.com/community/FilePermissions)**: User-friendly tutorial.
- **[Linux Journey: Permissions](https://linuxjourney.com/lesson/file-permissions)**: Interactive learning.
- **[Unix & Linux Stack Exchange](https://unix.stackexchange.com/)**: Community for permission-related questions.

**Community Tip**: Join [Reddit r/linuxadmin](https://www.reddit.com/r/linuxadmin/) for expert security tips! 🗣️

---

## ✅ Module Completion Checklist

- [ ] Understand the Linux permission model (Owner, Group, Others)
- [ ] Interpret permission strings (`ls -l`)
- [ ] Use `chmod` (symbolic and octal) to set permissions
- [ ] Use `chown` and `chgrp` to manage ownership
- [ ] Apply special permissions (SUID, SGID, Sticky Bit)
- [ ] Configure `umask` for default permissions
- [ ] Implement ACLs for fine-grained access
- [ ] Follow security best practices
- [ ] Complete all practice exercises

---

**Previous Module**: [Module 05: Vi/Vim Editor](../05-vi-shortcuts/VimShortcuts.md)  
**Next Module**: [Module 07: Process Management](../07-process-management/ProcessManagement.md)

[⬆ Back to Main README](../README.md)