# 🚀 Module 01: Getting Started with Linux

Welcome to the exciting world of Linux! 🎉 Whether you're a DevOps newbie or a seasoned pro looking to refresh your skills, this module is your launchpad into mastering Linux, the backbone of modern cloud and server infrastructure. Let's dive into the fundamentals, set up your environment, and get hands-on with Linux!

## 📋 Table of Contents
- [Linux Fundamentals](#linux-fundamentals)
- [Linux Architecture](#linux-architecture)
- [Linux Distributions](#linux-distributions)
- [Setting Up Your Environment](#setting-up-your-environment)
- [Package Managers](#package-managers)
- [Practice Exercises](#practice-exercises)
- [Additional Resources](#additional-resources)
- [Module Completion Checklist](#module-completion-checklist)

---

## 🐧 Linux Fundamentals

### What is Linux?

Linux is a **free, open-source operating system kernel** created by Linus Torvalds in 1991. It's the heart of countless operating systems (called *distributions*) that power servers, desktops, smartphones, IoT devices, and even supercomputers. Think of Linux as the Swiss Army knife of operating systems—versatile, reliable, and endlessly customizable! 🛠️

### Why Linux for DevOps? 🤔

Linux is the go-to choice for DevOps engineers because it’s:
1. **Open Source**: Free to use, modify, and share—perfect for experimentation!
2. **Rock-Solid Stability**: Servers running Linux can stay up for *years* without a reboot.
3. **Top-Notch Security**: Regular patches and robust permissions keep systems secure.
4. **Automation-Friendly**: Command-line tools like `bash`, `awk`, and `sed` make automation a breeze.
5. **Massive Community**: Millions of users, forums, and docs to help you troubleshoot.
6. **Industry Standard**: Powers most cloud platforms (AWS, Azure, GCP) and container systems (Docker, Kubernetes).

**Fun Fact**: Over 90% of the world’s top 500 supercomputers run Linux! 🚀

### Key Linux Features

- **Multi-user**: Multiple users can work on the same system simultaneously.
- **Multitasking**: Run multiple processes in parallel without breaking a sweat.
- **Portability**: Works on everything from Raspberry Pis to massive servers.
- **Security**: Fine-grained user permissions and security modules like SELinux.
- **Networking**: Built-in tools for robust network management.
- **Shell**: A powerful command-line interface for scripting and control.

**Example**: Check your Linux version with:
```bash
cat /etc/os-release
```

---

## 🏗️ Linux Architecture

Linux is like a well-organized city, with different layers working together to keep things running smoothly. Here’s a breakdown of its architecture:

### 1. Hardware Layer
The physical stuff: CPU, RAM, storage, network cards, and peripherals.

### 2. Kernel
The kernel is the *core* of Linux, acting as the bridge between hardware and software. It handles:
- **Process Management**: Starts, schedules, and stops processes.
- **Memory Management**: Allocates and frees memory for apps.
- **Device Drivers**: Communicates with hardware like disks and GPUs.
- **File System Management**: Organizes files and directories.
- **Network Stack**: Manages network connections (TCP/IP, etc.).

**Example**: Check your kernel version:
```bash
uname -r
```

### 3. Shell
The shell is your command-line playground, translating your commands into actions. Popular shells include:
- **Bash** (Bourne Again Shell): The default for most distros, versatile and widely used.
- **Zsh**: Packed with features like auto-completion and themes (try Oh My Zsh!).
- **Fish**: User-friendly with colorful prompts and suggestions.
- **Sh**: The classic Bourne Shell, lightweight but limited.

**Try it**: See your current shell:
```bash
echo $SHELL
```

### 4. System Utilities
Core tools like `ls`, `cat`, `grep`, and `systemd` for system management.

### 5. Applications
User-installed software, from web browsers to DevOps tools like Docker and Ansible.

**Visual Overview**:
```
[Applications] ↔ [System Utilities] ↔ [Shell] ↔ [Kernel] ↔ [Hardware]
```

---

## 🎯 Linux Distributions

A Linux distribution (distro) bundles the Linux kernel with software, tools, and a package manager to create a complete OS. Each distro has its own flavor, tailored for specific use cases.

### Popular Linux Distributions for DevOps

#### 1. **Ubuntu**
- **Best for**: Beginners, cloud servers, desktops
- **Package Manager**: APT (`apt`, `apt-get`)
- **Release Cycle**: Long Term Support (LTS) every 2 years (e.g., Ubuntu 22.04 LTS, 24.04 LTS)
- **Use Cases**: AWS/GCP/Azure, web servers, development
- **Example**:
  ```bash
  # Install Nginx on Ubuntu
  sudo apt update
  sudo apt install nginx
  ```

#### 2. **CentOS Stream / Rocky Linux / AlmaLinux**
- **Best for**: Enterprise servers, production
- **Package Manager**: DNF (replaces YUM)
- **Release Cycle**: Rolling updates (CentOS Stream) or stable (Rocky/Alma)
- **Use Cases**: Enterprise apps, stable production environments
- **Example**:
  ```bash
  # Install Apache on Rocky Linux
  sudo dnf install httpd
  ```

#### 3. **Red Hat Enterprise Linux (RHEL)**
- **Best for**: Mission-critical enterprise systems
- **Package Manager**: DNF
- **Support**: Commercial support with SLAs
- **Use Cases**: Banking, healthcare, large-scale deployments

#### 4. **Debian**
- **Best for**: Stable, lightweight servers
- **Package Manager**: APT
- **Philosophy**: Free software purity
- **Use Cases**: Long-term server deployments
- **Example**:
  ```bash
  # Check Debian version
  cat /etc/debian_version
  ```

#### 5. **Amazon Linux 2023**
- **Best for**: AWS cloud environments
- **Package Manager**: DNF
- **Optimized**: For AWS EC2, ECS, and Lambda
- **Use Cases**: Cloud-native workloads
- **Example**:
  ```bash
  # Update Amazon Linux
  sudo dnf update -y
  ```

#### 6. **Fedora**
- **Best for**: Cutting-edge features, developers
- **Package Manager**: DNF
- **Release Cycle**: Frequent updates (~every 6 months)
- **Use Cases**: Testing new tech, development
- **Example**:
  ```bash
  # Install Docker on Fedora
  sudo dnf install docker
  ```

#### 7. **Alpine Linux**
- **Best for**: Containers, minimal systems
- **Package Manager**: APK
- **Size**: Extremely lightweight (~5MB)
- **Use Cases**: Docker images, Kubernetes

### Choosing the Right Distro

| Use Case | Recommended Distro |
|----------|-------------------|
| Cloud Servers (AWS) | Amazon Linux 2023, Ubuntu |
| Cloud Servers (Azure/GCP) | Ubuntu, RHEL |
| Enterprise Production | RHEL, Rocky Linux |
| Development/Testing | Ubuntu, Fedora |
| Learning Linux | Ubuntu, Debian |
| Containers | Alpine, Ubuntu |

**Pro Tip**: Start with **Ubuntu** for its beginner-friendly interface and vast community support. For containerized workloads, try **Alpine** for its tiny footprint! 🐳

---

## 💻 Setting Up Your Environment

Ready to get hands-on? Here are four ways to set up a Linux environment for practice, each with its own perks. Pick what suits you best! 🚀

### Option 1: Docker Container (Quick & Isolated) 🐳

**Why?** Instant setup, no system changes, perfect for testing.

```bash
# Pull Ubuntu 22.04 image
docker pull ubuntu:22.04

# Run an interactive container
docker run -it --name linux-lab \
  --hostname devops-lab \
  -v $(pwd)/data:/data \
  ubuntu:22.04 /bin/bash

# Inside container, install tools
apt update
apt install -y vim curl wget git sudo
```

**Tips**:
- Use `-v` to persist data outside the container.
- Run `docker rm linux-lab` to clean up after practice.

### Option 2: Windows Subsystem for Linux (WSL) 🪟

**Why?** Native Linux on Windows without a VM.

```powershell
# Open PowerShell as Administrator
# Install WSL
wsl --install

# Install Ubuntu 22.04
wsl --install -d Ubuntu-22.04

# Check installed distros
wsl --list --all

# Set WSL 2 as default
wsl --set-default-version 2
```

**Tips**:
- Access Windows files in `/mnt/c/`.
- Update WSL regularly: `wsl --update`.

### Option 3: Virtual Machine (VM) 💾

**Why?** Full Linux experience with GUI support.

**Steps (Using VirtualBox)**:
1. Download [VirtualBox](https://www.virtualbox.org/).
2. Download Ubuntu Server ISO from [ubuntu.com](https://ubuntu.com/download/server).
3. Create a VM:
   - Type: Linux
   - Version: Ubuntu (64-bit)
   - RAM: 4GB (2GB minimum)
   - Disk: 25GB dynamic
   - Network: NAT or Bridged
4. Install Ubuntu via the ISO.

**Example VM Setup**:
```
Name: LinuxLab
CPU: 2 cores
RAM: 4GB
Disk: 25GB VDI
Network: NAT
```

### Option 4: Cloud-Based Linux Server ☁️

**Why?** Real-world cloud experience, great for DevOps practice.

**AWS EC2 (Free Tier)**:
```bash
# Launch an EC2 instance
aws ec2 run-instances \
  --image-id ami-0c55b159cbfafe1f0 \
  --instance-type t2.micro \
  --key-name my-key-pair \
  --security-groups my-sg

# Connect via SSH
ssh -i "my-key.pem" ubuntu@ec2-xx-xx-xx-xx.compute.amazonaws.com
```

**DigitalOcean Droplet**:
1. Sign up at [DigitalOcean](https://www.digitalocean.com/).
2. Create a Droplet with Ubuntu 22.04.
3. SSH into the Droplet: `ssh root@<droplet-ip>`.

**Pro Tip**: Use the AWS Free Tier or DigitalOcean’s $200 free credit to practice without costs! 💸

---

## 📦 Package Managers

Package managers are your best friends for installing, updating, and removing software. They make managing dependencies a breeze! Let’s explore the two most common ones for DevOps.

### APT (Advanced Package Tool) - Ubuntu/Debian

**Key Commands**:
```bash
# Update package index
sudo apt update

# Upgrade installed packages
sudo apt upgrade -y

# Install a package
sudo apt install nginx

# Install multiple packages
sudo apt install nginx mysql-server php -y

# Remove a package (keep configs)
sudo apt remove nginx

# Remove with configs
sudo apt purge nginx

# Clean unused packages
sudo apt autoremove

# Search for packages
apt search nginx

# Get package details
apt show nginx

# List installed packages
apt list --installed
```

**Key Files**:
- **Sources**: `/etc/apt/sources.list`, `/etc/apt/sources.list.d/`
- **Cache**: `/var/cache/apt/archives/`

**Example**:
```bash
# Install and start Nginx
sudo apt update
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl status nginx
```

### DNF (Dandified YUM) - RHEL/CentOS/Fedora/Amazon Linux

**Key Commands**:
```bash
# Check for updates
sudo dnf check-update

# Upgrade all packages
sudo dnf upgrade -y

# Install a package
sudo dnf install httpd

# Remove a package
sudo dnf remove httpd

# Search for packages
dnf search httpd

# Get package details
dnf info httpd

# List installed packages
dnf list installed

# Clean cache
sudo dnf clean all
```

**Key Files**:
- **Repos**: `/etc/yum.repos.d/`
- **Cache**: `/var/cache/dnf/`

**Example**:
```bash
# Install and enable Apache
sudo dnf install httpd -y
sudo systemctl enable httpd
sudo systemctl start httpd
```

### Package Manager Comparison

| Task | Ubuntu (APT) | RHEL/CentOS (DNF) |
|------|--------------|-------------------|
| Update List | `apt update` | `dnf check-update` |
| Upgrade All | `apt upgrade` | `dnf upgrade` |
| Install | `apt install pkg` | `dnf install pkg` |
| Remove | `apt remove pkg` | `dnf remove pkg` |
| Search | `apt search pkg` | `dnf search pkg` |
| Clean | `apt autoremove` | `dnf autoremove` |

### Best Practices

1. **Always Update First**:
   ```bash
   sudo apt update && sudo apt install package-name -y
   ```

2. **Keep Systems Updated**:
   ```bash
   # Ubuntu
   sudo apt update && sudo apt upgrade -y

   # CentOS/RHEL
   sudo dnf upgrade -y
   ```

3. **Clean Up Regularly**:
   ```bash
   # Ubuntu
   sudo apt autoremove && sudo apt autoclean

   # CentOS/RHEL
   sudo dnf autoremove && sudo dnf clean all
   ```

4. **Enable Auto-Updates for Security** (Ubuntu):
   ```bash
   sudo apt install unattended-upgrades
   sudo dpkg-reconfigure --priority=low unattended-upgrades
   ```

**Pro Tip**: Use `alias` to simplify commands:
```bash
echo "alias update='sudo apt update && sudo apt upgrade -y'" >> ~/.bashrc
source ~/.bashrc
```

---

## 🛠️ Practice Exercises

Get hands-on with these exercises to solidify your Linux skills! 💪

### Exercise 1: Environment Setup
1. Set up a Linux environment (Docker, WSL, VM, or cloud).
2. Verify the distro:
   ```bash
   cat /etc/os-release
   ```
3. Check the kernel:
   ```bash
   uname -r
   ```

### Exercise 2: Package Management
1. Update the package list:
   ```bash
   sudo apt update  # or sudo dnf check-update
   ```
2. Install `htop` and `tree`:
   ```bash
   sudo apt install htop tree -y  # or sudo dnf install htop tree -y
   ```
3. Verify installation:
   ```bash
   which htop tree
   ```
4. Remove `tree` and confirm:
   ```bash
   sudo apt remove tree
   which tree  # Should return nothing
   ```

### Exercise 3: Explore the System
1. Identify your shell:
   ```bash
   echo $SHELL
   ```
2. View system info:
   ```bash
   hostnamectl
   ```
3. Check uptime:
   ```bash
   uptime
   ```
4. List logged-in users:
   ```bash
   who
   ```

### Exercise 4: Automation Warm-Up
Create a simple script to update and clean your system:
```bash
#!/bin/bash
# save as update-system.sh
sudo apt update && sudo apt upgrade -y && sudo apt autoremove
chmod +x update-system.sh
./update-system.sh
```

---

## 📚 Additional Resources

- **[Linux Documentation Project](https://www.tldp.org/)**: Comprehensive Linux guides.
- **[Ubuntu Documentation](https://help.ubuntu.com/)**: Official Ubuntu resources.
- **[Red Hat Documentation](https://access.redhat.com/documentation/)**: Enterprise-grade tutorials.
- **[Arch Linux Wiki](https://wiki.archlinux.org/)**: In-depth, distro-agnostic knowledge.
- **[Linux Journey](https://linuxjourney.com/)**: Interactive learning for beginners.
- **[OverTheWire Wargames](https://overthewire.org/wargames/)**: Fun, hands-on Linux challenges.

**Community Tip**: Join forums like [Reddit r/linux](https://www.reddit.com/r/linux/) or [Ask Ubuntu](https://askubuntu.com/) for peer support! 🗣️

---

## ✅ Module Completion Checklist

- [ ] Understand Linux and its role in DevOps
- [ ] Explore Linux architecture layers
- [ ] Learn about major Linux distributions
- [ ] Set up a Linux environment (Docker, WSL, VM, or cloud)
- [ ] Master package management with APT or DNF
- [ ] Complete all practice exercises

---

**Next Module**: [Module 02: Linux File System](../02-file-system/README.md)

[⬆ Back to Main README](../README.md)