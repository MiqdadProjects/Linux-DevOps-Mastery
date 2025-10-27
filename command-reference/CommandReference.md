# 🛠️ Module 11: Linux Command Reference for DevOps

Welcome to the **Linux Command Reference for DevOps**! 🎉 As a DevOps engineer, you need quick access to the most essential Linux commands to create resources, manage systems, troubleshoot issues, and automate workflows. This module is your go-to cheat sheet, packed with production-ready commands for every DevOps scenario—from spinning up users and containers to debugging network issues and optimizing disks. Let’s supercharge your Linux skills! 🚀

*Updated: October 27, 2025, 12:06 PM CET*

## 📋 Table of Contents
- [Overview](#overview)
- [File and Directory Management](#file-and-directory-management)
- [User and Group Management](#user-and-group-management)
- [Process and Service Management](#process-and-service-management)
- [Networking](#networking)
- [Disk and Storage Management](#disk-and-storage-management)
- [System Monitoring and Logging](#system-monitoring-and-logging)
- [Troubleshooting](#troubleshooting)
- [Automation and Scripting](#automation-and-scripting)
- [Package Management](#package-management)
- [Container and Cloud Tools](#container-and-cloud-tools)
- [Security and Permissions](#security-and-permissions)
- [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)
- [Best Practices](#best-practices)
- [Additional Resources](#additional-resources)
- [Module Completion Checklist](#module-completion-checklist)

---

## 📚 Overview

This module provides a curated list of the most common Linux commands used in DevOps, organized by use case. Whether you’re creating users, troubleshooting connectivity, or automating deployments, these commands are essential for managing Linux systems in production and cloud environments. Each section includes commands for **creating**, **managing**, **troubleshooting**, and **optimizing**, with examples to ensure you’re ready for real-world DevOps tasks.

---

## 📁 File and Directory Management

### Creating and Managing
```bash
# Create directory
mkdir myapp
mkdir -p /path/to/nested/dirs  # Create parent directories

# Create file
touch config.yml
echo "key: value" > config.yml  # Write to file
cat <<EOF > config.yml          # Multi-line file
key1: value1
key2: value2
EOF

# Copy files/directories
cp file.txt /backup/
cp -r /app /backup/app  # Recursive copy

# Move/rename
mv file.txt /new/path/file.txt
mv oldname.txt newname.txt

# Remove files/directories
rm file.txt
rm -r /old/dir  # Recursive delete (careful!)
rm -rf /old/dir  # Force delete (extra careful!)
```

### Searching and Analyzing
```bash
# Find files by name
find / -name "*.log" 2>/dev/null
find /var -type f -name "app*.conf"

# Search file contents
grep "error" /var/log/app.log
grep -r "error" /var/log  # Recursive search
grep -i "error" app.log   # Case-insensitive

# Count lines/words
wc -l access.log  # Lines
wc -w document.txt  # Words

# View file contents
cat file.txt
less file.txt  # Scrollable
tail -f /var/log/app.log  # Follow live updates
head -n 10 file.txt  # First 10 lines
```

**Example**:
```bash
# Create and populate a config file
mkdir -p /app/config
cat <<EOF > /app/config/settings.yml
database: mysql
port: 3306
EOF
cat /app/config/settings.yml
```

**Pro Tip**: Use `find` with `-exec` for bulk operations:
```bash
find /var/log -name "*.log" -exec gzip {} \;
```

---

## 👤 User and Group Management

### Creating and Managing
```bash
# Create user
sudo useradd -m -s /bin/bash devops  # With home dir and shell
sudo passwd devops  # Set password

# Create group
sudo groupadd developers

# Add user to group
sudo usermod -aG developers devops

# Modify user
sudo usermod -L devops  # Lock account
sudo usermod -U devops  # Unlock account
sudo usermod -c "DevOps User" devops  # Set comment

# Delete user
sudo userdel -r devops  # Remove user and home dir
```

### Managing Permissions
```bash
# Set sudo privileges
sudo visudo
# Add: devops ALL=(ALL) NOPASSWD:ALL  # Passwordless sudo

# View users and groups
id devops  # User and group info
getent passwd  # List all users
getent group developers  # Group members
```

**Example**:
```bash
# Create a DevOps user and grant sudo
sudo useradd -m -s /bin/bash devops
sudo usermod -aG sudo devops
id devops
```

**Pro Tip**: Use `/etc/skel` to customize new user home directories:
```bash
sudo cp .bashrc /etc/skel/.bashrc
```

---

## ⚙️ Process and Service Management

### Creating and Managing
```bash
# Start/stop services
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl enable nginx  # Start on boot

# Run a process in background
nohup python3 app.py &
./script.sh &  # Background process

# Kill process
kill 1234  # By PID
killall python3  # By name
pkill -f "app.py"  # By pattern
```

### Monitoring
```bash
# List processes
ps aux  # All processes
ps -ef | grep nginx  # Filter by name
top  # Interactive monitor
htop  # Enhanced interactive monitor

# Check service status
sudo systemctl status nginx
journalctl -u nginx  # Service logs
```

**Example**:
```bash
# Start and monitor a web server
sudo systemctl start nginx
sudo systemctl status nginx
htop
```

**Pro Tip**: Use `systemctl is-active` for automation scripts:
```bash
if systemctl is-active --quiet nginx; then echo "Nginx is running"; fi
```

---

## 🌐 Networking

### Creating and Managing
```bash
# Configure IP (temporary)
sudo ip addr add 192.168.1.100/24 dev eth0
sudo ip route add default via 192.168.1.1

# Netplan (Ubuntu, permanent)
sudo vim /etc/netplan/01-netcfg.yaml
# Example:
network:
  ethernets:
    eth0:
      addresses: [192.168.1.100/24]
      gateway4: 192.168.1.1
sudo netplan apply

# Manage DNS
sudo sh -c 'echo "nameserver 8.8.8.8" > /etc/resolv.conf'

# Start network service
sudo systemctl restart networking  # Ubuntu
sudo nmcli con up eth0  # RHEL/CentOS
```

### Troubleshooting
```bash
# Check connectivity
ping -c 4 8.8.8.8
curl -I https://example.com

# Trace route
traceroute google.com
mtr google.com  # Real-time

# View network interfaces
ip addr show
ip link show

# Check open ports
ss -tulpn
netstat -tulpn  # Alternative

# Capture packets
sudo tcpdump -i eth0 port 80
```

**Example**:
```bash
# Debug a web server
curl -I http://localhost
sudo ss -tulpn | grep :80
sudo tcpdump -i eth0 port 80
```

**Pro Tip**: Use `mtr` for continuous network diagnostics:
```bash
mtr --report google.com
```

---

## 💿 Disk and Storage Management

### Creating and Managing
```bash
# Create partition
sudo parted /dev/sdb mklabel gpt
sudo parted /dev/sdb mkpart primary ext4 0% 100%

# Create filesystem
sudo mkfs.ext4 -L Data /dev/sdb1

# Mount filesystem
sudo mkdir /data
sudo mount /dev/sdb1 /data
echo "UUID=$(sudo blkid -s UUID -o value /dev/sdb1) /data ext4 defaults 0 2" | sudo tee -a /etc/fstab

# Create LVM
sudo pvcreate /dev/sdb1
sudo vgcreate vg_data /dev/sdb1
sudo lvcreate -L 10G -n lv_web vg_data
sudo mkfs.ext4 /dev/vg_data/lv_web
sudo mount /dev/vg_data/lv_web /var/www

# Create RAID
sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
sudo mkfs.ext4 /dev/md0
sudo mount /dev/md0 /mnt/raid
```

### Troubleshooting
```bash
# Check disk usage
df -h
du -sh /var/*

# Find large files
find / -type f -size +100M 2>/dev/null

# Check filesystem
sudo fsck.ext4 -f /dev/sdb1

# Monitor disk health
sudo smartctl -H /dev/sda
```

**Example**:
```bash
# Set up a new disk
sudo parted /dev/sdb mklabel gpt
sudo parted /dev/sdb mkpart primary ext4 0% 100%
sudo mkfs.ext4 /dev/sdb1
sudo mount /dev/sdb1 /mnt
df -h | grep sdb1
```

**Pro Tip**: Use `fstrim` for SSD maintenance:
```bash
sudo fstrim -v /
```

---

## 📊 System Monitoring and Logging

### Monitoring
```bash
# CPU/memory usage
top
htop
vmstat 1

# Disk I/O
iostat -x 1
iotop -o

# Network usage
nload eth0
iftop -i eth0
```

### Logging
```bash
# View logs
journalctl -u nginx
journalctl -f  # Follow live
tail -f /var/log/syslog

# Rotate logs
sudo logrotate -f /etc/logrotate.conf

# Search logs
grep "ERROR" /var/log/app.log
journalctl -p 3  # Error-level logs
```

**Example**:
```bash
# Monitor a service
sudo systemctl start nginx
journalctl -u nginx -f
htop
```

**Pro Tip**: Set up log rotation to manage disk space:
```bash
sudo vim /etc/logrotate.d/myapp
# Add:
/var/log/myapp/*.log {
    daily
    rotate 7
    compress
}
```

---

## 🔍 Troubleshooting

### General
```bash
# Check system status
uptime  # System load
dmesg | tail  # Kernel messages
journalctl -xb  # Boot logs

# Find process by port
lsof -i :80
ss -tulpn | grep :80
```

### Network
```bash
# Test connectivity
ping -c 4 8.8.8.8
nc -zv localhost 80

# DNS lookup
dig google.com
nslookup example.com

# Packet capture
sudo tcpdump -i eth0 host 192.168.1.100
```

### Disk
```bash
# Check disk errors
sudo fsck /dev/sdb1
sudo smartctl -a /dev/sda

# Find space hogs
du -sh /* | sort -hr
```

**Example**:
```bash
# Debug a failed service
sudo systemctl status apache2
journalctl -u apache2 -n 50
lsof -i :80
```

**Pro Tip**: Use `strace` for deep debugging:
```bash
sudo strace -p 1234 -o trace.log
```

---

## 🤖 Automation and Scripting

### Creating Scripts
```bash
# Create a bash script
cat <<EOF > backup.sh
#!/bin/bash
tar -czf /backup/data-$(date +%Y%m%d).tar.gz /data
EOF
chmod +x backup.sh
./backup.sh

# Schedule with cron
crontab -e
# Add: 0 2 * * * /path/to/backup.sh  # Daily at 2 AM
```

### Automation Tools
```bash
# Run commands in parallel
xargs -n 1 -P 4 curl < urls.txt

# Watch command output
watch -n 5 'df -h'

# Automate with Ansible (example)
ansible-playbook -i hosts playbook.yml
```

**Example**:
```bash
# Script to monitor disk usage
cat <<EOF > disk_monitor.sh
#!/bin/bash
df -h | grep /data | awk '{print $5}' | mail -s "Disk Usage" admin@example.com
EOF
chmod +x disk_monitor.sh
crontab -e
# Add: 0 * * * * /path/to/disk_monitor.sh
```

**Pro Tip**: Use `#!/bin/bash -e` to make scripts fail on errors:
```bash
#!/bin/bash -e
```

---

## 📦 Package Management

### Debian/Ubuntu
```bash
# Update and install
sudo apt update
sudo apt install nginx
sudo apt upgrade

# Remove package
sudo apt remove nginx
sudo apt autoremove  # Clean unused dependencies

# Search packages
apt search nginx
```

### RHEL/CentOS
```bash
# Update and install
sudo dnf update
sudo dnf install httpd
sudo dnf upgrade

# Remove package
sudo dnf remove httpd

# Search packages
dnf search httpd
```

**Example**:
```bash
# Install and start a web server
sudo apt install nginx
sudo systemctl start nginx
curl http://localhost
```

**Pro Tip**: Cache packages locally for offline installs:
```bash
sudo apt download nginx
```

---

## 🐳 Container and Cloud Tools

### Docker
```bash
# Install Docker
sudo apt install docker.io
sudo systemctl start docker

# Run container
docker run -d -p 80:80 nginx
docker ps  # List containers

# Manage images
docker pull ubuntu:20.04
docker images
```

### Cloud CLI
```bash
# AWS CLI
aws ec2 describe-instances
aws s3 cp file.txt s3://my-bucket/

# Azure CLI
az vm list
az storage blob upload --file file.txt

# Google Cloud SDK
gcloud compute instances list
```

**Example**:
```bash
# Run a web server in Docker
docker run -d --name web -p 8080:80 nginx
curl http://localhost:8080
```

**Pro Tip**: Use `--restart=always` for Docker containers in production:
```bash
docker run -d --restart=always -p 80:80 nginx
```

---

## 🔒 Security and Permissions

### File Permissions
```bash
# Set permissions
chmod 644 file.txt  # rw-r--r--
chmod -R 755 /app   # Recursive
chown devops:developers file.txt
chown -R devops:developers /app

# Set ACLs
setfacl -m u:devops:rw file.txt
getfacl file.txt
```

### Firewall
```bash
# UFW (Ubuntu)
sudo ufw allow 80/tcp
sudo ufw enable
sudo ufw status

# Firewalld (RHEL/CentOS)
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --reload
```

### SSH Hardening
```bash
# Edit SSH config
sudo vim /etc/ssh/sshd_config
# Set: PermitRootLogin no, Port 2222
sudo systemctl reload sshd

# Key-based auth
ssh-keygen -t rsa -b 4096
ssh-copy-id devops@server
```

**Example**:
```bash
# Secure a directory
sudo chown devops:developers /app
sudo chmod 750 /app
sudo ufw allow 22/tcp
```

**Pro Tip**: Audit permissions regularly:
```bash
find / -perm /6000 -type f  # Find SUID/SGID files
```

---

## 📚 Quick Reference Cheat Sheet

```bash
# File Management
mkdir -p /path/dir  # Create directory
cp -r src dst       # Copy directory
find / -name "*.log"  # Find files
grep -r "error" /var/log  # Search contents

# User Management
sudo useradd -m devops  # Create user
sudo usermod -aG sudo devops  # Add to group
id devops  # View user info

# Process Management
ps aux | grep nginx  # List processes
sudo systemctl start nginx  # Start service
htop  # Monitor processes

# Networking
ip addr show  # View IPs
ping -c 4 8.8.8.8  # Test connectivity
sudo tcpdump -i eth0  # Capture packets

# Disk Management
sudo mkfs.ext4 /dev/sdb1  # Create filesystem
sudo mount /dev/sdb1 /mnt  # Mount
df -h  # Disk usage

# Monitoring
top  # System monitor
journalctl -f  # Live logs
iostat -x 1  # Disk I/O

# Automation
crontab -e  # Schedule tasks
ansible-playbook playbook.yml  # Run Ansible

# Containers
docker run -d -p 80:80 nginx  # Run container
docker ps  # List containers
```

---

## 🔐 Best Practices

1. **Test Safely**:
   Use VMs for risky operations:
   ```bash
   virt-install --name test-vm --ram 2048 --vcpus 2 --disk size=20
   ```

2. **Backup Regularly**:
   Save data before changes:
   ```bash
   tar -czf /backup/data-$(date +%Y%m%d).tar.gz /data
   ```

3. **Use Modern Tools**:
   Prefer `ip`, `ss`, `systemctl`:
   ```bash
   ip addr show
   ss -tulpn
   ```

4. **Automate Repetitive Tasks**:
   Use cron or Ansible for efficiency:
   ```bash
   crontab -e
   # Add: 0 2 * * * /backup.sh
   ```

5. **Secure Systems**:
   Harden SSH and firewalls:
   ```bash
   sudo ufw allow 22/tcp
   sudo chmod 600 /etc/ssh/sshd_config
   ```

6. **Document Everything**:
   Record configurations:
   ```bash
   lsblk > disk_layout.txt
   ip addr show > network_config.txt
   ```

---

## 📖 Additional Resources

- **[Linux Journey](https://linuxjourney.com/)**: Interactive Linux tutorials.
- **[Red Hat Enterprise Linux Docs](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/)**: Enterprise guides.
- **[Ubuntu Server Guide](https://help.ubuntu.com/lts/serverguide/)**: Ubuntu administration.
- **[DevOps Roadmap](https://roadmap.sh/devops)**: DevOps learning path.
- **[Unix & Linux Stack Exchange](https://unix.stackexchange.com/)**: Community Q&A.
- **Communities**: Join [Reddit r/devops](https://www.reddit.com/r/devops/) or [r/linuxadmin](https://www.reddit.com/r/linuxadmin/) for discussions. 🗣️

---

## ✅ Module Completion Checklist

- [ ] Understand commands for file and directory management
- [ ] Master user and group administration
- [ ] Manage processes and services
- [ ] Configure and troubleshoot networking
- [ ] Handle disk and storage tasks
- [ ] Monitor systems and analyze logs
- [ ] Debug issues with troubleshooting tools
- [ ] Automate tasks with scripts and cron
- [ ] Manage packages and containers
- [ ] Secure systems with permissions and firewalls
- [ ] Memorize key commands from the cheat sheet

---

## 🎓 Final Notes

This command reference is your DevOps Swiss Army knife! 🛠️ Use it to streamline tasks, troubleshoot issues, and automate workflows in production. Always test commands in a safe environment (e.g., VMs), back up critical data, and document configurations. You’re now equipped to tackle any Linux DevOps challenge!

**Previous Module**: [Module 10: Disk Management](../10-disk-management/DiskManagement.md)  
**Next Steps**: Apply these commands in your DevOps projects or explore [MiqdadProjects/Linux-DevOps-Mastery](https://github.com/MiqdadProjects/Linux-DevOps-Mastery.git) for more!

[⬆ Back to Main README](../README.md)