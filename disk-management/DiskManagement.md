# 💿 Module 10: Disk Management in Linux

Welcome, Linux storage wizards! 🧙‍♂️ After conquering networking in Module 09, it’s time to master **disk management**—the art of organizing and optimizing storage in Linux. From partitioning disks to configuring LVM and RAID, this module will empower you to manage storage like a pro. Let’s dive into the world of disks, file systems, and performance tuning! 🚀

## 📋 Table of Contents
- [Understanding Disk Storage](#understanding-disk-storage)
- [Viewing Disk Information](#viewing-disk-information)
- [Disk Partitioning](#disk-partitioning)
- [File Systems](#file-systems)
- [Mounting and Unmounting](#mounting-and-unmounting)
- [Logical Volume Management (LVM)](#logical-volume-management-lvm)
- [RAID Configuration](#raid-configuration)
- [Swap Management](#swap-management)
- [Disk Performance and Optimization](#disk-performance-and-optimization)
- [Practice Exercises](#practice-exercises)
- [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)
- [Best Practices](#best-practices)
- [Additional Resources](#additional-resources)
- [Module Completion Checklist](#module-completion-checklist)

---

## 💾 Understanding Disk Storage

### Storage Hierarchy
Visualize how Linux organizes storage:

```
Physical Disk (e.g., /dev/sda)
    └── Partitions (e.g., /dev/sda1, /dev/sda2)
        └── File System (ext4, xfs, etc.)
            └── Mount Point (/, /home, /data)
```

### Device Naming Conventions
Understand how Linux names storage devices:

```bash
# SATA/SCSI/SAS
/dev/sda, /dev/sdb        # Disks
/dev/sda1, /dev/sda2      # Partitions

# NVMe
/dev/nvme0n1              # First NVMe disk
/dev/nvme0n1p1            # Partitions

# Virtual Disks
/dev/vda, /dev/xvda       # Virtio/Xen disks

# LVM
/dev/mapper/vg_data-lv_web # Logical volume
/dev/vg_data/lv_web        # Alternative path

# RAID
/dev/md0, /dev/md1        # Software RAID
```

### Storage Types
Know your options:

- **Block Devices**: HDD, SSD, NVMe (random access, fixed size).
- **Partition Types**:
  - **MBR**: Up to 4 primary or 3 primary + 1 extended (logical partitions).
  - **GPT**: Supports 128+ partitions, modern standard.
- **File Systems**:
  - **ext4**: Reliable, widely used.
  - **xfs**: High-performance, enterprise-grade.
  - **btrfs**: Advanced features (snapshots, compression).
  - **vfat/NTFS**: Cross-platform compatibility.

**Example**:
```bash
# Identify disk type
lsblk -d -o NAME,MODEL
# Output: sda  Samsung SSD 860
```

---

## 🔍 Viewing Disk Information

### List Disks and Partitions
Get a snapshot of your storage.

```bash
# List block devices
lsblk                     # Simple view
lsblk -f                  # Include filesystem, UUID
lsblk -o NAME,SIZE,FSTYPE,MOUNTPOINT  # Custom output
# Output:
# NAME   SIZE FSTYPE MOUNTPOINT
# sda    100G
# ├─sda1  50G ext4   /
# └─sda2  50G xfs    /data

# Detailed disk info
sudo fdisk -l /dev/sda
sudo parted /dev/sda print

# UUIDs and labels
sudo blkid
# Output: /dev/sda1: UUID="abc-123" TYPE="ext4"
```

### Disk Usage
Monitor space consumption.

```bash
# Filesystem usage
df -h                     # Human-readable
df -hT                    # With filesystem type
df -h /data               # Specific mount
df -i                     # Inode usage

# Directory usage
du -sh /var               # Summary
du -h /var | sort -hr | head -10  # Top 10 largest

# Find large files
find / -type f -size +100M -exec ls -lh {} \; 2>/dev/null
```

**Example**:
```bash
# Find largest directories
du -sh /var/* | sort -hr | head -5
```

### Disk Information
Inspect hardware details.

```bash
# Disk details
sudo hdparm -I /dev/sda   # Comprehensive
sudo smartctl -i /dev/sda # Model, serial

# SMART health
sudo apt install smartmontools
sudo smartctl -H /dev/sda
# Output: PASSED (healthy)

# Disk speed test
sudo hdparm -tT /dev/sda
# Output: Timing buffered disk reads: 300 MB/s
```

**Pro Tip**: Use `lsblk -f` to quickly see UUIDs and mount points for `fstab` entries.

---

## 🔧 Disk Partitioning

### Using fdisk (MBR)
Create and manage MBR partitions.

```bash
# Start fdisk
sudo fdisk /dev/sdb

# Commands:
# m: Help
# p: Print table
# n: New partition
# d: Delete partition
# w: Write changes
# q: Quit

# Create partition
sudo fdisk /dev/sdb
# n → p → Enter → Enter → +10G → w

# Update kernel
sudo partprobe /dev/sdb
```

### Using parted (GPT)
Manage GPT partitions for modern systems.

```bash
# Create GPT table
sudo parted /dev/sdb mklabel gpt

# Create partition
sudo parted /dev/sdb mkpart primary ext4 0% 10GB

# Interactive mode
sudo parted /dev/sdb
(parted) mkpart
Partition name? data
File system type? ext4
Start? 0%
End? 50%
(parted) quit
```

### Using gdisk (GPT)
Alternative for GPT partitioning.

```bash
# Start gdisk
sudo gdisk /dev/sdb

# Create partition
# n → Enter → Enter → +10G → 8300 (Linux) → w
```

**Example**:
```bash
# Create two partitions
sudo parted /dev/sdb mklabel gpt
sudo parted /dev/sdb mkpart primary ext4 0% 50%
sudo parted /dev/sdb mkpart primary linux-swap 50% 100%
sudo partprobe /dev/sdb
lsblk /dev/sdb
```

**Pro Tip**: Always use `-a optimal` in `parted` for SSD alignment:
```bash
sudo parted -a optimal /dev/sdb mkpart primary ext4 0% 100%
```

---

## 📁 File Systems

### Creating File Systems
Format partitions for use.

```bash
# ext4
sudo mkfs.ext4 -L Data /dev/sdb1

# xfs
sudo mkfs.xfs -L Data /dev/sdb1

# btrfs
sudo mkfs.btrfs -L Data /dev/sdb1

# FAT32
sudo mkfs.vfat -n USB /dev/sdb1

# Swap
sudo mkswap -L Swap /dev/sdb2
```

### Checking File Systems
Ensure integrity before mounting.

```bash
# Check ext4
sudo umount /dev/sdb1
sudo fsck.ext4 -f /dev/sdb1

# Check xfs
sudo xfs_repair /dev/sdb1

# Schedule checks
sudo tune2fs -c 30 /dev/sdb1  # Every 30 mounts
sudo tune2fs -i 1m /dev/sdb1  # Every month
```

### File System Labels and UUIDs
Use labels or UUIDs for reliable mounting.

```bash
# View UUIDs
sudo blkid
lsblk -o NAME,UUID,LABEL

# Set label
sudo e2label /dev/sdb1 "Data"
sudo xfs_admin -L "Data" /dev/sdb1

# Change UUID
sudo tune2fs -U random /dev/sdb1
```

### Resizing File Systems
Adjust filesystem sizes.

```bash
# Grow ext4
sudo resize2fs /dev/sdb1

# Shrink ext4 (unmount first)
sudo umount /dev/sdb1
sudo e2fsck -f /dev/sdb1
sudo resize2fs /dev/sdb1 50G

# Grow xfs
sudo xfs_growfs /data
```

**Example**:
```bash
# Create and label filesystem
sudo mkfs.ext4 -L Data /dev/sdb1
sudo blkid /dev/sdb1
```

**Pro Tip**: Always run `fsck` before shrinking filesystems to avoid data loss.

---

## 🔗 Mounting and Unmounting

### Manual Mounting
Mount filesystems to access data.

```bash
# Mount
sudo mkdir /data
sudo mount /dev/sdb1 /data
sudo mount -t ext4 /dev/sdb1 /data

# Mount by UUID or label
sudo mount UUID="abc-123" /data
sudo mount LABEL="Data" /data

# Mount read-only
sudo mount -o ro /dev/sdb1 /data
```

### Unmounting
Safely detach filesystems.

```bash
# Unmount
sudo umount /data
sudo umount /dev/sdb1

# Check for processes
sudo lsof +D /data
sudo fuser -m /data

# Force unmount (use cautiously)
sudo umount -f /data
```

### Persistent Mounting (/etc/fstab)
Ensure mounts persist across reboots.

```bash
# Edit fstab
sudo vim /etc/fstab

# Example entries:
UUID=abc-123  /data  ext4  defaults,noatime  0  2
LABEL=Data    /backup  xfs  defaults  0  2
/dev/sdb2     none  swap  sw  0  0
//server/share  /mnt/smb  cifs  credentials=/etc/samba/creds  0  0

# Test fstab
sudo mount -a
sudo findmnt --verify
```

**Example**:
```bash
# Add and test mount
echo "UUID=$(blkid -s UUID -o value /dev/sdb1) /data ext4 defaults 0 2" | sudo tee -a /etc/fstab
sudo mount -a
df -h | grep /data
```

**Pro Tip**: Use UUIDs in `fstab` to avoid issues with changing device names.

---

## 📦 Logical Volume Management (LVM)

### LVM Concepts
Flexible storage management:

```
Physical Volume (PV) → Volume Group (VG) → Logical Volume (LV)
/dev/sdb1 (PV) → vg_data (VG) → lv_web (LV) → /var/www
```

### Creating LVM
Set up a dynamic storage pool.

```bash
# Install LVM
sudo apt install lvm2

# Create PV
sudo pvcreate /dev/sdb1
sudo pvs

# Create VG
sudo vgcreate vg_data /dev/sdb1
sudo vgs

# Create LV
sudo lvcreate -L 10G -n lv_web vg_data
sudo lvs

# Format and mount
sudo mkfs.ext4 /dev/vg_data/lv_web
sudo mkdir /var/www
sudo mount /dev/vg_data/lv_web /var/www
echo "/dev/vg_data/lv_web /var/www ext4 defaults 0 2" | sudo tee -a /etc/fstab
```

### Managing LVM
Resize and manage volumes.

```bash
# Extend VG
sudo pvcreate /dev/sdc1
sudo vgextend vg_data /dev/sdc1

# Extend LV
sudo lvextend -L +5G /dev/vg_data/lv_web
sudo resize2fs /dev/vg_data/lv_web

# Shrink LV (ext4 only)
sudo umount /var/www
sudo e2fsck -f /dev/vg_data/lv_web
sudo resize2fs /dev/vg_data/lv_web 5G
sudo lvreduce -L 5G /dev/vg_data/lv_web
sudo mount /var/www
```

**Example**:
```bash
# Create and extend LVM
sudo lvcreate -l 100%FREE -n lv_data vg_data
sudo mkfs.xfs /dev/vg_data/lv_data
sudo lvextend -l +100%FREE /dev/vg_data/lv_data
sudo xfs_growfs /data
```

**Pro Tip**: Use LVM snapshots for backups:
```bash
sudo lvcreate -L 2G -s -n lv_web_snap /dev/vg_data/lv_web
```

---

## 🔄 RAID Configuration

### Software RAID Levels
Choose the right RAID for your needs:

- **RAID 0**: Speed, no redundancy.
- **RAID 1**: Mirroring, full redundancy.
- **RAID 5**: Parity, needs 3+ disks.
- **RAID 10**: Speed + redundancy, 4+ disks.

### Creating RAID
Set up software RAID with `mdadm`.

```bash
# Install mdadm
sudo apt install mdadm

# Create RAID 1
sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1

# Create filesystem
sudo mkfs.ext4 /dev/md0
sudo mkdir /mnt/raid
sudo mount /dev/md0 /mnt/raid

# Save config
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
sudo update-initramfs -u
echo "/dev/md0 /mnt/raid ext4 defaults 0 2" | sudo tee -a /etc/fstab
```

### Managing RAID
Maintain and monitor RAID arrays.

```bash
# Check status
cat /proc/mdstat
sudo mdadm --detail /dev/md0

# Replace failed disk
sudo mdadm --fail /dev/md0 /dev/sdb1
sudo mdadm --remove /dev/md0 /dev/sdb1
sudo mdadm --add /dev/md0 /dev/sdd1
```

**Example**:
```bash
# Create RAID 5
sudo mdadm --create /dev/md0 --level=5 --raid-devices=3 /dev/sdb1 /dev/sdc1 /dev/sdd1
sudo mkfs.xfs /dev/md0
sudo mount /dev/md0 /mnt/raid
```

**Pro Tip**: Monitor RAID with:
```bash
watch -n 5 'cat /proc/mdstat'
```

---

## 💱 Swap Management

### Creating Swap
Add swap for memory overflow.

```bash
# Swap partition
sudo mkswap /dev/sdb2
sudo swapon /dev/sdb2
echo "UUID=$(blkid -s UUID -o value /dev/sdb2) none swap sw 0 0" | sudo tee -a /etc/fstab

# Swap file
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo "/swapfile none swap sw 0 0" | sudo tee -a /etc/fstab
```

### Managing Swap
Control swap behavior.

```bash
# View swap
swapon --show
free -h

# Disable swap
sudo swapoff /swapfile
sudo swapoff -a

# Adjust swappiness
sudo sysctl vm.swappiness=10
echo "vm.swappiness=10" | sudo tee -a /etc/sysctl.conf
```

**Example**:
```bash
# Create and enable swap file
sudo fallocate -l 1G /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
free -h | grep Swap
```

**Pro Tip**: Set `swappiness=10` for SSDs to reduce wear.

---

## ⚡ Disk Performance and Optimization

### Performance Testing
Measure disk speed.

```bash
# Sequential read/write
sudo hdparm -tT /dev/sda

# Write test
sudo dd if=/dev/zero of=/tmp/testfile bs=1G count=1 oflag=direct

# Random I/O
sudo apt install fio
sudo fio --name=randwrite --rw=randwrite --bs=4k --size=1G --numjobs=4 --runtime=60
```

### I/O Monitoring
Track disk activity.

```bash
# Real-time
iostat -x 1
sudo iotop -o

# Per-process
pidstat -d 1
```

### Optimization Tips
Boost disk performance.

```bash
# Use noatime
echo "UUID=$(blkid -s UUID -o value /dev/sdb1) /data ext4 defaults,noatime 0 2" | sudo tee -a /etc/fstab

# Enable TRIM for SSD
sudo fstrim -v /
echo "UUID=$(blkid -s UUID -o value /dev/sdb1) /data ext4 defaults,discard 0 2" | sudo tee -a /etc/fstab

# Set deadline scheduler
echo deadline | sudo tee /sys/block/sda/queue/scheduler
```

**Example**:
```bash
# Test disk speed
sudo hdparm -tT /dev/sda
# Output: Timing buffered disk reads: 500 MB/s
```

**Pro Tip**: Use `fstrim` weekly on SSDs to maintain performance.

---

## 🛠️ Practice Exercises

### Exercise 1: Disk Information
1. List disks:
   ```bash
   lsblk -f
   sudo fdisk -l
   ```
2. Check usage:
   ```bash
   df -h
   du -sh /var/*
   ```

### Exercise 2: Partitioning
1. Create GPT partition:
   ```bash
   sudo parted /dev/sdb mklabel gpt
   sudo parted /dev/sdb mkpart primary ext4 0% 50%
   ```
2. Format and mount:
   ```bash
   sudo mkfs.ext4 /dev/sdb1
   sudo mkdir /mnt/test
   sudo mount /dev/sdb1 /mnt/test
   df -h | grep sdb1
   ```

### Exercise 3: LVM
1. Set up LVM:
   ```bash
   sudo pvcreate /dev/sdb1
   sudo vgcreate vg_test /dev/sdb1
   sudo lvcreate -L 5G -n lv_test vg_test
   sudo mkfs.ext4 /dev/vg_test/lv_test
   sudo mount /dev/vg_test/lv_test /mnt
   ```
2. Extend LV:
   ```bash
   sudo lvextend -L +2G /dev/vg_test/lv_test
   sudo resize2fs /dev/vg_test/lv_test
   ```

### Exercise 4: Swap
1. Create swap file:
   ```bash
   sudo fallocate -l 1G /swapfile
   sudo chmod 600 /swapfile
   sudo mkswap /swapfile
   sudo swapon /swapfile
   ```
2. Verify:
   ```bash
   swapon --show
   free -h
   ```

### Exercise 5: RAID
1. Create RAID 1:
   ```bash
   sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
   sudo mkfs.ext4 /dev/md0
   sudo mount /dev/md0 /mnt/raid
   ```
2. Check status:
   ```bash
   cat /proc/mdstat
   ```

---

## 📚 Quick Reference Cheat Sheet

```bash
# Disk Info
lsblk -f                  # Disks and filesystems
df -h                     # Disk usage
sudo blkid                # UUIDs
sudo smartctl -H /dev/sda # Health

# Partitioning
sudo parted /dev/sdb mklabel gpt  # Create GPT
sudo fdisk /dev/sdb                # MBR partitioning
sudo partprobe                    # Update kernel

# File Systems
sudo mkfs.ext4 /dev/sdb1  # Create ext4
sudo fsck.ext4 /dev/sdb1  # Check filesystem
sudo resize2fs /dev/sdb1  # Resize ext4

# Mounting
sudo mount /dev/sdb1 /mnt # Mount
sudo umount /mnt          # Unmount
sudo mount -a             # Mount fstab entries

# LVM
sudo pvcreate /dev/sdb1   # Create PV
sudo vgcreate vg_data /dev/sdb1  # Create VG
sudo lvcreate -L 10G -n lv_web vg_data  # Create LV

# RAID
sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
cat /proc/mdstat          # RAID status

# Swap
sudo mkswap /swapfile     # Create swap
sudo swapon /swapfile     # Enable swap
free -h                   # View swap
```

---

## 🔐 Best Practices

1. **Backup First**: Always back up data before disk operations.
   ```bash
   tar -czf /backup/data-$(date +%Y%m%d).tar.gz /data
   ```
2. **Use UUIDs**: Reference disks by UUID in `fstab`.
   ```bash
   echo "UUID=$(blkid -s UUID -o value /dev/sdb1) /data ext4 defaults 0 2" | sudo tee -a /etc/fstab
   ```
3. **Monitor Health**: Enable SMART monitoring.
   ```bash
   sudo smartctl -a /dev/sda
   ```
4. **Test Changes**: Use `mount -a` to verify `fstab`.
   ```bash
   sudo findmnt --verify
   ```
5. **Optimize SSDs**: Use `discard` and `deadline` scheduler.
   ```bash
   echo deadline | sudo tee /sys/block/sda/queue/scheduler
   ```
6. **Document Layout**: Record partition and LVM configurations.
   ```bash
   lsblk > /root/disk_layout.txt
   ```

---

## 📖 Additional Resources

- **[Linux Filesystem Hierarchy](https://tldp.org/LDP/Linux-Filesystem-Hierarchy/html/storage.html)**: TLDP storage guide.
- **[Red Hat: Storage Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_storage_devices)**: Enterprise guide.
- **[Ubuntu: LVM](https://wiki.ubuntu.com/Lvm)**: LVM tutorial.
- **[Linux Journey: Storage](https://linuxjourney.com/lesson/disk-management)**: Interactive learning.
- **[Unix & Linux Stack Exchange](https://unix.stackexchange.com/)**: Community Q&A.

**Community Tip**: Join [Reddit r/linuxadmin](https://www.reddit.com/r/linuxadmin/) for storage tips! 🗣️

---

## ✅ Module Completion Checklist

- [ ] Understand disk naming and storage hierarchy
- [ ] View disk, partition, and usage information
- [ ] Create and manage partitions with `fdisk` and `parted`
- [ ] Create and check ext4, xfs, and other filesystems
- [ ] Mount and unmount filesystems, configure `fstab`
- [ ] Set up and manage LVM volumes
- [ ] Configure software RAID with `mdadm`
- [ ] Create and manage swap space
- [ ] Monitor and optimize disk performance
- [ ] Complete all practice exercises

---

## 🎓 Final Notes
Congratulations on completing the Linux course! 🎉 You’ve mastered disk management, from partitioning to RAID and LVM. Always test commands on non-production systems, back up critical data, and document your configurations. You’re now ready to manage Linux storage like a seasoned sysadmin!

**Previous Module**: [Module 09: Networking](../09-networking/Networking.md)  
**Course Complete!** 🎈

[⬆ Back to Main README](../README.md)