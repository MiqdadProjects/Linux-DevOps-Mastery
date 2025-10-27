# 📂 Module 02: Linux Folder Structure & File System

Hey there, Linux adventurers! 🚀 Building on Module 01, where you got your Linux basics locked in, we're now exploring the backbone of Linux—the **file system**. Imagine it as the organized blueprint of a bustling city, where every folder has a purpose and every file knows its place. This module will equip you with the knowledge to navigate, manage, and optimize the Linux file system like a pro DevOps engineer. We'll add plenty of hands-on examples, professional tips, and DevOps-relevant details to make your learning journey exciting and practical. Let's map it out! 🗺️

## 📋 Table of Contents
- [Linux File System Hierarchy](#linux-file-system-hierarchy)
- [Important Directories Explained](#important-directories-explained)
- [File System Types](#file-system-types)
- [Navigating the File System](#navigating-the-file-system)
- [Understanding Paths](#understanding-paths)
- [Practice Exercises](#practice-exercises)
- [Additional Resources](#additional-resources)
- [Module Completion Checklist](#module-completion-checklist)

---

## 🗂️ Linux File System Hierarchy

Unlike Windows with its separate drives (C:\, D:\), Linux uses a **single-rooted hierarchical file system** starting from `/` (the root directory). Everything in Linux is treated as a file—including directories, hardware devices, and even processes! This unified approach makes Linux efficient for automation, scripting, and large-scale deployments in DevOps environments.

### File System Hierarchy Standard (FHS)

The File System Hierarchy Standard (FHS) is the blueprint that standardizes directory structures across Linux distros, ensuring consistency. Here's a visual overview of the key directories under `/`:

```
/  (Root Directory - The starting point of everything!)
├── bin/       → Essential user command binaries (ls, cp, mv - everyday tools)
├── boot/      → Boot loader files and kernel (vmlinuz, initrd - startup essentials)
├── dev/       → Device files (sda for disks, tty for terminals)
├── etc/       → System-wide configuration files (passwd, hosts - settings hub)
├── home/      → User home directories (your personal workspace)
├── lib/       → Essential shared libraries (dynamic links for programs)
├── media/     → Mount points for removable media (USB drives, CDs)
├── mnt/       → Temporary mount points (for manual mounts)
├── opt/       → Optional application software (third-party apps)
├── proc/      → Virtual files for processes and kernel info (cpuinfo, meminfo)
├── root/      → Root user's home directory (admin's personal space)
├── run/       → Runtime data (temporary system files, sockets)
├── sbin/      → System administration binaries (fdisk, reboot - admin tools)
├── srv/       → Data for services (web servers, FTP data)
├── sys/       → Kernel and system information (virtual filesystem)
├── tmp/       → Temporary files (cleared on reboot)
├── usr/       → User programs and data (bin, lib for additional software)
└── var/       → Variable data (logs, databases, caches - dynamic content)
```

**Pro Tip**: In DevOps, understanding FHS helps when building Docker images or Kubernetes pods—knowing where configs (`/etc`) and logs (`/var/log`) go ensures portable and reliable setups.

**Try it Out**: Visualize the hierarchy:
```bash
# Install tree if needed (Ubuntu: sudo apt install tree)
tree / -L 1
# Output: Shows top-level directories
```

**DevOps Relevance**: When troubleshooting containerized apps, you'll often mount volumes to `/var` for persistent logs or `/etc` for configs.

---

## 📁 Important Directories Explained

Let's break down the most crucial directories you'll use in DevOps workflows. Each one has specific roles, and I'll include professional use cases, examples, and commands to help you apply them immediately! 🔧

### 1. `/` (Root Directory)
The foundation of the file system—everything branches from here. It's read-only for normal users to prevent accidental damage.

**Professional Use Case**: In production servers, root (`/`) is often on a separate partition for easier backups and recovery.
**Example**:
```bash
# List root contents with permissions
ls -l /
# Output: drwxr-xr-x 2 root root 4096 Oct 27 10:00 bin
```

### 2. `/bin` (Essential User Binaries)
Essential commands for basic operations, available even in single-user mode.

**Common Commands**: `ls` (list), `cp` (copy), `mv` (move), `rm` (remove), `cat` (concatenate), `echo` (print), `bash` (shell), `grep` (search).

**Professional Use Case**: DevOps scripts often use `/bin` tools for portability across distros.
**Examples**:
```bash
# Find command location
which grep
# Output: /bin/grep

# Use grep to search in a file
echo "Hello Linux" > test.txt
grep "Linux" test.txt
# Output: Hello Linux

# Count binaries
ls /bin | wc -l
# Output: e.g., 100+
```

### 3. `/sbin` (System Binaries)
Admin tools for system maintenance, usually requiring root privileges.

**Common Commands**: `ip` (network config), `iptables` (firewall), `fdisk` (partitioning), `mkfs` (format disks), `shutdown` (power off), `reboot` (restart).

**Professional Use Case**: In DevOps, use `/sbin` tools for infrastructure automation, like scripting firewall rules in CI/CD pipelines.
**Examples**:
```bash
# List network-related tools
ls /sbin | grep -i "net"
# Output: ip, ifconfig, netstat, etc.

# Check network interfaces
sudo /sbin/ip link show
# Output: 1: lo (loopback), 2: eth0, etc.

# Reboot system (careful!)
sudo /sbin/reboot
```

### 4. `/etc` (Configuration Files)
Central hub for system and application configs—editable text files for customization.

**Key Files**: `/etc/passwd` (users), `/etc/group` (groups), `/etc/shadow` (passwords), `/etc/hosts` (host mappings), `/etc/hostname` (system name), `/etc/resolv.conf` (DNS), `/etc/fstab` (mounts), `/etc/ssh/sshd_config` (SSH), `/etc/nginx/nginx.conf` (Nginx).

**Professional Use Case**: In DevOps, config management tools like Ansible target `/etc` for automated deployments.
**Examples**:
```bash
# Edit hosts file (as root)
sudo vim /etc/hosts
# Add: 127.0.0.1 localhost

# View cron jobs
cat /etc/crontab
# Output: System-wide cron schedule

# Search for config files
ls /etc | grep "conf"
# Output: resolv.conf, sshd_config, etc.

# Backup a config file
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
sudo systemctl restart sshd
```

### 5. `/home` (User Home Directories)
Personal spaces for users to store files, configs, and data.

**Structure**:
```
/home/
├── bob/     → /home/bob/.bashrc (shell config), /home/bob/.ssh (keys), /home/bob/projects
├── alice/   → User-specific files
```

**Professional Use Case**: In multi-user DevOps setups, `/home` stores SSH keys and scripts for CI/CD access.
**Examples**:
```bash
# List hidden files in home
ls -la ~
# Output: .bashrc, .profile, .ssh

# Create a project directory
mkdir ~/devops-project
cd ~/devops-project
pwd
# Output: /home/youruser/devops-project

# Share a file between users (requires permissions)
cp ~/report.txt /home/alice/
```

### 6. `/var` (Variable Data)
Dynamic data that changes during runtime, like logs and databases.

**Key Subdirectories**: `/var/log` (logs), `/var/www` (web files), `/var/cache` (caches), `/var/lib` (databases), `/var/tmp` (persistent temps).

**Professional Use Case**: DevOps monitoring tools like Prometheus scrape `/var/log` for metrics and alerts.
**Examples**:
```bash
# Tail system logs
sudo tail -f /var/log/syslog  # Ubuntu
sudo tail -f /var/log/messages  # CentOS

# Check log size
du -sh /var/log
# Output: e.g., 100M

# View Apache logs (if installed)
sudo less /var/log/apache2/access.log

# Clear old logs (professional rotation)
sudo logrotate -f /etc/logrotate.conf
```

### 7. `/tmp` (Temporary Files)
For short-lived temporary files, often cleared on reboot.

**Professional Use Case**: DevOps scripts use `/tmp` for intermediate files in pipelines.
**Examples**:
```bash
# Create and list temp file
touch /tmp/tempdata.txt
ls /tmp

# Check tmp size
df -h /tmp
# Output: tmpfs 2.0G 0 2.0G 0% /tmp

# Secure temp files (mode 1777)
ls -ld /tmp
# Output: drwxrwxrwt (sticky bit for security)
```

### 8. `/usr` (User Programs and Data)
Non-essential user software and libraries.

**Key Subdirectories**: `/usr/bin` (extra binaries), `/usr/lib` (libraries), `/usr/local` (custom installs).

**Professional Use Case**: Install custom tools in `/usr/local` for container builds.
**Examples**:
```bash
# List user binaries
ls /usr/bin | head -5

# Install to /usr/local (common for manual compiles)
./configure --prefix=/usr/local
make install
```

### 9. `/dev` (Device Files)
Represents hardware as files for interaction.

**Examples**: `/dev/sda` (disk), `/dev/null` (discard output), `/dev/random` (random data), `/dev/zero` (zeroes).

**Professional Use Case**: In DevOps, script disk operations like backups using `/dev` devices.
**Examples**:
```bash
# List block devices
lsblk -f
# Output: sda ext4 /

# Use /dev/null to discard output
ls > /dev/null

# Generate random password
head -c 12 /dev/urandom | base64
```

### 10. `/proc` (Process Information)
Virtual filesystem for real-time system and process data.

**Key Files**: `/proc/cpuinfo` (CPU), `/proc/meminfo` (memory), `/proc/<pid>` (process details).

**Professional Use Case**: Monitoring tools like `top` read `/proc` for metrics in CI/CD health checks.
**Examples**:
```bash
# CPU details
cat /proc/cpuinfo | grep "model name"

# Memory usage
free -h  # Reads from /proc/meminfo

# Process info (e.g., PID 1)
ls /proc/1
cat /proc/1/status
```

**Pro Tip**: For DevOps, script system monitoring using `/proc`:
```bash
#!/bin/bash
# Save as monitor.sh
cpu_cores=$(grep -c ^processor /proc/cpuinfo)
echo "CPU Cores: $cpu_cores"
mem_total=$(awk '/MemTotal/ {print $2}' /proc/meminfo)
echo "Total Memory: $mem_total kB"
```

---

## 💾 File System Types

Linux supports a variety of file systems, each optimized for performance, reliability, or size. Choosing the right one is crucial for DevOps tasks like storage optimization in cloud environments.

### Common File System Types
1. **ext4 (Extended File System 4)**:
   - Default for Ubuntu/Debian
   - Supports up to 1EB volumes, journaling for crash recovery
   - **Use Case**: General servers, reliable for databases
   - **Example**:
     ```bash
     # Format a disk as ext4
     sudo mkfs.ext4 /dev/sdb1
     ```

2. **XFS**:
   - High-performance, scalable
   - Supports 8EB volumes, excellent for large files
   - **Use Case**: Media streaming, big data (Hadoop)
   - **Example**:
     ```bash
     sudo mkfs.xfs /dev/sdb1
     xfs_info /dev/sdb1
     ```

3. **Btrfs (B-Tree File System)**:
   - Features snapshots, compression, RAID
   - Supports 16EB volumes
   - **Use Case**: Backups, container storage (Docker volumes)
   - **Example**:
     ```bash
     sudo mkfs.btrfs /dev/sdb1
     btrfs subvolume create /mnt/myvolume
     ```

4. **FAT32/NTFS**:
   - Cross-platform (Linux/Windows)
   - FAT32: 4GB max file size; NTFS: Windows native
   - **Use Case**: Shared USB drives, dual-boot systems
   - **Example**:
     ```bash
     sudo mount -t ntfs /dev/sdb1 /mnt/ntfs
     ```

5. **tmpfs**:
   - Memory-based, fast but volatile
   - Size limited by RAM
   - **Use Case**: Temporary data in scripts, RAM disks
   - **Example**:
     ```bash
     mount -t tmpfs tmpfs /mnt/ramdisk
     ```

6. **ZFS**:
   - Advanced with pooling, snapshots, deduplication
   - **Use Case**: Storage servers, NAS
   - **Example** (requires installation):
     ```bash
     zpool create mypool /dev/sdb
     zfs create mypool/data
     ```

**Check File Systems**:
```bash
# Mounted file systems
df -hT
# All supported types
cat /proc/filesystems
```

**DevOps Pro Tip**: For cloud instances (e.g., AWS EBS), ext4 or XFS are go-tos for performance. Use `lsblk` to inspect disks before formatting!

---

## 🧭 Navigating the File System

Navigation is the foundation of Linux proficiency—let's make it fun and efficient with more commands and examples! 🕹️

### Key Commands
- `pwd`: Current directory
- `ls`: List contents (`ls -l` for details, `ls -a` for hidden)
- `cd`: Change directory (`cd -` to previous)
- `mkdir -p`: Create directories recursively
- `rmdir`: Remove empty directories
- `touch`: Create/update files
- `cp -r`: Copy recursively
- `mv`: Move/rename
- `rm -rf`: Remove recursively (dangerous—use with caution!)
- `find`: Search files
- `tree`: Directory tree view
- `du -sh`: Disk usage
- `df -h`: File system usage

**Examples**:
```bash
# Print working directory
pwd
# Output: /home/user

# List with human-readable sizes
ls -lh

# Create nested directories
mkdir -p project/scripts/utils

# Copy directory
cp -r project /tmp/project-backup

# Find files modified in last 7 days
find ~ -type f -mtime -7

# Check directory size
du -sh ~
# Output: 2.5G /home/user

# View file system usage
df -h
# Output: /dev/sda1 100G 20G 80G 20% /

# Remove directory (careful!)
rm -rf /tmp/olddata
```

**Pro Tip**: Use wildcards (`*`) for patterns:
```bash
ls *.txt  # All .txt files
rm log*.old  # Remove files starting with "log" ending with ".old"
```

**DevOps Relevance**: In CI/CD pipelines, use these commands to manage artifacts, logs, and deployments.

---

## 🛤️ Understanding Paths

Paths are the GPS coordinates of the file system. Mastering them saves time and prevents errors! 📍

### 1. **Absolute Paths**
Full path from root (`/`).

**Example**:
```bash
# Edit a config with absolute path
sudo vim /etc/nginx/nginx.conf
```

### 2. **Relative Paths**
From current location.

**Example**:
```bash
# If in /home/user/documents
cd ../projects  # Moves to /home/user/projects
cat ../../etc/hosts  # Accesses /etc/hosts
```

**Special Symbols**:
- `.`: Current directory (`./script.sh` to run in current dir)
- `..`: Parent directory
- `~`: Home directory (`~/docs`)
- `/`: Root

**Examples**:
```bash
# Run script in current dir
./myscript.sh

# Move up two levels
cd ../..

# Expand home path
echo ~
# Output: /home/user

# Create file in parent
touch ../report.txt
```

**Pro Tip**: Use `$PATH` environment variable for executables:
```bash
echo $PATH
# Output: /usr/local/bin:/usr/bin:/bin

# Add to PATH
export PATH=$PATH:/home/user/scripts
```

**DevOps Tip**: In scripts, use absolute paths for reliability:
```bash
#!/bin/bash
LOG_FILE="/var/log/myapp.log"
echo "Error" >> "$LOG_FILE"
```

---

## 🛠️ Practice Exercises

Time to roll up your sleeves! These exercises will turn theory into muscle memory. Complete them in your Linux environment for the best results. 💥

### Exercise 1: Explore the Hierarchy
1. List root with permissions:
   ```bash
   ls -l /
   ```
2. Count subdirectories in `/etc`:
   ```bash
   ls -l /etc | grep ^d | wc -l
   ```

### Exercise 2: Work with Directories
1. Create a nested structure:
   ```bash
   mkdir -p /tmp/devops/config/logs
   ```
2. Create files:
   ```bash
   touch /tmp/devops/config/app.conf
   echo "Log entry" > /tmp/devops/logs/error.log
   ```
3. Copy the structure:
   ```bash
   cp -r /tmp/devops ~/devops-backup
   ```
4. Verify:
   ```bash
   tree ~/devops-backup
   ```

### Exercise 3: Configuration Files
1. Backup and edit hostname:
   ```bash
   sudo cp /etc/hostname /etc/hostname.bak
   sudo echo "devops-server" > /etc/hostname
   ```
2. View SSH config:
   ```bash
   grep "Port" /etc/ssh/sshd_config
   # Output: Port 22
   ```
3. List cron jobs:
   ```bash
   ls /etc/cron.d/
   ```

### Exercise 4: Navigation Challenge
1. Navigate to `/var/log` and search for auth logs:
   ```bash
   cd /var/log
   find . -name "*auth*"
   ```
2. Use relative path to go back:
   ```bash
   cd ../../home/$USER
   pwd
   ```
3. Use `tree` to view /etc:
   ```bash
   tree /etc -L 1
   ```

### Exercise 5: File System Types
1. Format a virtual disk (careful, use loop device):
   ```bash
   sudo dd if=/dev/zero of=/tmp/testdisk.img bs=1M count=100
   sudo mkfs.ext4 /tmp/testdisk.img
   ```
2. Mount and check:
   ```bash
   sudo mkdir /mnt/test
   sudo mount /tmp/testdisk.img /mnt/test
   df -hT /mnt/test
   # Output: ext4
   sudo umount /mnt/test
   ```

---

## 📚 Additional Resources

- **[Filesystem Hierarchy Standard (FHS)](https://refspecs.linuxfoundation.org/FHS_3.0/fhs/index.html)**: Official standard reference.
- **[Linux Filesystem Explained](https://www.linux.com/training-tutorials/linux-filesystem-explained/)**: Beginner-friendly breakdown.
- **[Ubuntu Filesystem Guide](https://help.ubuntu.com/community/LinuxFilesystemTreeOverview)**: Distro-specific insights.
- **[OverTheWire Bandit](https://overthewire.org/wargames/bandit/)**: Game-like challenges for navigation skills.
- **[Linux Journey: Filesystem](https://linuxjourney.com/lesson/filesystem-hierarchy)**: Interactive tutorials with quizzes.
- **[The Art of Command Line](https://github.com/jlevy/the-art-of-command-line)**: Advanced navigation tips.

**Community Tip**: Share your file system scripts on [GitHub](https://github.com/search?q=linux+filesystem) or discuss on [Reddit r/linuxadmin](https://www.reddit.com/r/linuxadmin/) for feedback! 👥

---

## ✅ Module Completion Checklist

- [ ] Master the Linux file system hierarchy and FHS
- [ ] Understand key directories with examples and use cases
- [ ] Learn common file system types and how to check them
- [ ] Practice navigation commands with hands-on examples
- [ ] Grasp absolute and relative paths with special symbols
- [ ] Complete all practice exercises

---

**Next Module**: [Module 03: Linux Commands & Shell Scripting](../03-linux-commands-shell-scripting/README.md)

[⬆ Back to Main README](../README.md)