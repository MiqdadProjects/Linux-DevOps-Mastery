# 📊 Module 08: System Monitoring and Logging

Welcome back, Linux system detectives! 🕵️‍♂️ After mastering process management in Module 07, it’s time to become a pro at **system monitoring and logging**—the key to keeping your Linux systems running smoothly and securely. Whether you’re tracking CPU spikes, hunting down memory leaks, or analyzing logs for security breaches, this module will equip you with the tools to monitor like a DevOps wizard. 🧙‍♂️ Let’s dive into the world of system metrics and logs! 🚀

## 📋 Table of Contents
- [System Resource Monitoring](#system-resource-monitoring)
- [CPU Monitoring](#cpu-monitoring)
- [Memory Monitoring](#memory-monitoring)
- [Disk Monitoring](#disk-monitoring)
- [Network Monitoring](#network-monitoring)
- [Log Management](#log-management)
- [Performance Analysis](#performance-analysis)
- [Alerting and Automation](#alerting-and-automation)
- [Practice Exercises](#practice-exercises)
- [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)
- [Best Practices](#best-practices)
- [Additional Resources](#additional-resources)
- [Module Completion Checklist](#module-completion-checklist)

---

## 🖥️ System Resource Monitoring

### System Overview Commands
Get a quick snapshot of your system’s health with these commands.

```bash
# System uptime and load average
uptime
# Output: 11:20:45 up 5 days, 2:15, 2 users, load average: 0.50, 0.40, 0.35

# Detailed system info
uname -a                    # Kernel, OS, and architecture
hostnamectl                 # Hostname, OS, and version
lscpu                       # CPU details
lsblk                       # Disk and partition layout
lsusb                       # USB devices
lspci                       # PCI devices

# Interactive monitoring
top                         # Real-time process viewer
htop                        # Enhanced, colorful viewer
glances                     # All-in-one monitoring tool
# Install: sudo apt install htop glances

# System statistics
vmstat                      # Memory, CPU, I/O stats
vmstat 2 5                 # 5 samples, 2-second intervals
# Output:
# procs ---memory--- ---swap-- ---io--- -system-- ---cpu---
#  r  b  swpd free buff cache  si so bi bo  in cs us sy id wa
#  1  0   0  234560 52428 456789 0 0 12 34 567 890 15 5 78 2
```

**Pro Tip**: Use `htop` for a user-friendly interface over `top`. Install with:
```bash
sudo apt install htop  # Ubuntu/Debian
sudo dnf install htop  # RHEL/CentOS
```

### System Information Files
Dive into system details with `/proc` files.

```bash
# CPU info
cat /proc/cpuinfo | grep -E 'model name|cpu cores'

# Memory info
cat /proc/meminfo | grep -E 'MemTotal|MemFree|MemAvailable'

# Kernel version
cat /proc/version

# Load average
cat /proc/loadavg
# Output: 0.50 0.40 0.35 2/245 1234
# 1-min, 5-min, 15-min, running/total tasks, last PID

# Uptime (seconds since boot)
cat /proc/uptime

# Kernel boot parameters
cat /proc/cmdline
```

**Example**:
```bash
# Quick system overview
echo "CPU: $(lscpu | grep 'Model name' | awk -F: '{print $2}')"
echo "Memory: $(free -h | grep Mem | awk '{print $2}')"
echo "Uptime: $(uptime -p)"
```

---

## 💻 CPU Monitoring

### CPU Usage Commands
Track CPU performance to spot bottlenecks.

```bash
# Real-time CPU monitoring
top                         # Press '1' for per-CPU stats
htop                        # Press F6 to sort by CPU

# CPU statistics
mpstat                      # Overall CPU usage
mpstat -P ALL 1 5          # Per-CPU, 5 samples
# Output: CPU %usr %sys %iowait %idle
#         all 15.0 5.0  2.0    78.0

# Top CPU consumers
ps aux --sort=-%cpu | head -10

# CPU details
lscpu                       # Architecture, cores, threads
nproc                       # Number of CPU cores

# CPU frequency
watch -n 1 'cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_cur_freq'

# CPU temperature
sensors                     # Requires lm-sensors
sudo apt install lm-sensors
sudo sensors-detect        # Configure sensors
```

**Example**:
```bash
# Monitor CPU in real-time
watch -n 2 'mpstat -P ALL'
```

### CPU Load Analysis
Understand system load to gauge performance.

```bash
# Check load average
uptime
# Output: load average: 0.50, 0.40, 0.35 (1-min, 5-min, 15-min)

# Load guidelines (4-core system):
# < 4.0: Healthy
# 4.0-6.0: Moderate load
# > 6.0: Overloaded

# Calculate load percentage
cores=$(nproc)
load=$(cat /proc/loadavg | awk '{print $1}')
echo "Load: $(echo "scale=2; $load/$cores*100" | bc)%"
```

**Example**:
```bash
# Find CPU-intensive processes
ps -eo pid,user,%cpu,cmd --sort=-%cpu | head -10
```

### CPU Stress Testing
Test CPU performance under load.

```bash
# Install stress tools
sudo apt install stress stress-ng

# Stress 4 CPUs for 30 seconds
stress --cpu 4 --timeout 30
stress-ng --cpu 4 --timeout 30s

# Monitor during stress
watch -n 1 'mpstat -P ALL'
```

**Pro Tip**: High `%iowait` in `mpstat` indicates disk I/O bottlenecks.

---

## 🧠 Memory Monitoring

### Memory Usage Commands
Keep an eye on RAM and swap usage.

```bash
# Memory overview
free -h
# Output:
#               total        used        free      shared  buff/cache   available
# Mem:          7.7Gi       3.4Gi       1.2Gi       456Mi       3.1Gi       4.1Gi
# Swap:         2.0Gi          0B       2.0Gi

# Detailed memory info
cat /proc/meminfo | grep -E 'MemTotal|MemFree|MemAvailable|Buffers|Cached'

# Top memory consumers
ps -eo pid,user,cmd,%mem --sort=-%mem | head -10

# Memory stats
vmstat -s                   # Summary
vmstat -a 1 5              # Active/inactive memory
```

**Memory Terms**:
- **total**: Total physical RAM.
- **used**: Memory in use (includes buffers/cache).
- **free**: Completely unused RAM.
- **available**: Memory available for new processes.
- **buff/cache**: Memory used for buffers and cache (reclaimable).

**Example**:
```bash
# Monitor memory in real-time
watch -n 2 'free -h'
```

### Memory Analysis
Detect memory leaks and swap issues.

```bash
# Check for swap usage
swapon --show
free -h | grep Swap

# Processes using swap
for file in /proc/*/status; do
    awk '/VmSwap|Name/{printf "%-30s %s\n", $2, $3}' $file
done | sort -k2 -nr | head -10

# Check OOM killer events
sudo dmesg | grep -i "out of memory"
journalctl | grep -i "killed process"
```

**Example**:
```bash
# Clear swap (use cautiously)
sudo swapoff -a && sudo swapon -a
```

**Pro Tip**: Low `MemAvailable` with high swap usage indicates memory pressure. Check `/proc/pressure/memory` on modern kernels.

---

## 💾 Disk Monitoring

### Disk Space Commands
Monitor disk usage to prevent outages.

```bash
# Disk space
df -h                       # Human-readable
df -hT                      # Include filesystem type
df -h /data                # Specific mount point

# Directory usage
du -sh /var                 # Summary
du -h --max-depth=1 /var   # One level deep
du -sh /* | sort -rh       # Sort by size

# Find large files
find / -type f -size +100M -exec ls -lh {} \; 2>/dev/null

# File type usage
find /var/log -name "*.log" -exec du -ch {} + | tail -1
```

**Example**:
```bash
# Find largest directories
du -sh /var/* | sort -rh | head -10
```

### Disk I/O Monitoring
Track disk performance bottlenecks.

```bash
# Install sysstat
sudo apt install sysstat

# I/O statistics
iostat -x 1 5              # 5 samples, extended stats
# Output: %util, await, svctm

# Real-time I/O by process
iotop                      # Requires sudo
iotop -o                   # Only active I/O processes

# Per-process I/O
pidstat -d 1               # Disk I/O per process
```

### Disk Health
Monitor drive health with SMART.

```bash
# Install smartmontools
sudo apt install smartmontools

# Check disk health
sudo smartctl -a /dev/sda
sudo smartctl -H /dev/sda  # Quick health check

# Run long test
sudo smartctl -t long /dev/sda
sudo smartctl -l selftest /dev/sda  # View results
```

**Example**:
```bash
# Check all disks
for disk in /dev/sd?; do
    echo "Checking $disk"
    sudo smartctl -H $disk
done
```

### Inode Monitoring
Prevent inode exhaustion.

```bash
# Check inode usage
df -ih

# Find directories with many files
find / -xdev -printf '%h\n' | sort | uniq -c | sort -rn | head -10
```

**Pro Tip**: High `%util` in `iostat` (>80%) or long `await` times indicate disk bottlenecks.

---

## 🌐 Network Monitoring

### Network Statistics
Monitor network interfaces and traffic.

```bash
# Interface info
ip addr show
ip link show

# Connection stats
ss -tulpn                  # Listening ports
ss -s                      # Socket summary

# Bandwidth usage
vnstat -i eth0            # Install: sudo apt install vnstat
vnstat -l                 # Live traffic

# Real-time bandwidth
iftop -i eth0             # Install: sudo apt install iftop

# Per-process bandwidth
nethogs eth0              # Install: sudo apt install nethogs
```

**Example**:
```bash
# Monitor active connections
watch -n 2 'ss -tunap | grep ESTAB'
```

### Network Connectivity
Test network performance and reachability.

```bash
# Ping test
ping -c 4 google.com

# Trace route
mtr google.com            # Install: sudo apt install mtr

# DNS lookup
dig google.com

# Port testing
nc -zv localhost 80       # Check port 80

# Packet capture
sudo tcpdump -i eth0 port 80 -w capture.pcap
```

### Network Performance
Measure bandwidth and latency.

```bash
# Speed test
speedtest-cli             # Install: pip install speedtest-cli
speedtest-cli --simple

# Throughput test
iperf3 -s                 # Server
iperf3 -c server_ip       # Client
```

**Pro Tip**: Use `mtr` for continuous ping/traceroute to diagnose network issues.

---

## 📝 Log Management

### System Log Locations
Know where to find critical logs.

```bash
# Common logs
/var/log/syslog            # Ubuntu/Debian
/var/log/messages          # RHEL/CentOS
/var/log/auth.log          # Authentication (Ubuntu)
/var/log/secure            # Authentication (RHEL)
/var/log/kern.log          # Kernel logs
/var/log/nginx/            # Nginx logs
```

### Viewing Logs
Access and analyze log files.

```bash
# View logs
tail -f /var/log/syslog    # Real-time
less /var/log/syslog       # Paginated
grep "error" /var/log/syslog  # Search for errors

# Systemd logs
journalctl -f              # Real-time
journalctl -u nginx        # Nginx logs
journalctl -b              # Since boot
journalctl --since "1 hour ago"  # Time filter
journalctl -p err          # Errors only
```

**Example**:
```bash
# Find failed SSH logins
sudo grep "Failed password" /var/log/auth.log | tail -10
```

### Log Rotation
Prevent logs from consuming disk space.

```bash
# Check logrotate config
cat /etc/logrotate.conf
ls /etc/logrotate.d/

# Custom logrotate config
sudo vim /etc/logrotate.d/webapp
# Content:
/var/log/webapp/*.log {
    daily
    rotate 14
    compress
    create 0640 webapp webapp
    postrotate
        systemctl reload webapp
    endscript
}

# Test rotation
sudo logrotate -d /etc/logrotate.conf
```

**Example**:
```bash
# Compress old logs
find /var/log -name "*.log" -mtime +30 -exec gzip {} \;
```

**Pro Tip**: Use `journalctl --vacuum-size=500M` to limit systemd log size.

---

## 📈 Performance Analysis

### Performance Tools
Analyze system performance comprehensively.

```bash
# Versatile stats
dstat -cdngy              # CPU, disk, network, paging

# Historical data
sar -u 1 5                # CPU usage
sar -r 1 5                # Memory
sar -d 1 5                # Disk
sar -n DEV 1 5            # Network
```

### Bottleneck Identification
Pinpoint performance issues.

```bash
# CPU bottlenecks
top                       # High %CPU or load average
mpstat -P ALL             # High %usr or %iowait

# Memory bottlenecks
free -h                   # Low MemAvailable
vmstat 1                  # High si/so (swap activity)

# Disk bottlenecks
iostat -x 1               # High %util or await
iotop -o                  # Disk-intensive processes

# Network bottlenecks
iftop                     # High bandwidth usage
ss -s                     # Connection issues
```

**Example**:
```bash
# Identify I/O bottlenecks
watch -n 2 'iostat -x'
```

### Process Analysis
Dive into process-level details.

```bash
# Process stats
pidstat 1                 # Per-process CPU/disk
pidstat -p 1234           # Specific PID

# System calls
strace -c ./script.sh     # Count system calls
strace -p 1234            # Trace running process

# Open files
lsof -p 1234              # Files used by PID
```

**Pro Tip**: Use `sar` for historical performance trends: `sar -f /var/log/sysstat/sa$(date +%d)`.

---

## 🚨 Alerting and Automation

### Simple Alert Scripts
Automate monitoring with alerts.

```bash
# CPU alert
#!/bin/bash
THRESHOLD=80
CPU=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}')
if (( $(echo "$CPU > $THRESHOLD" | bc -l) )); then
    echo "High CPU: ${CPU}% at $(date)" | mail -s "CPU Alert" admin@example.com
fi

# Disk alert
#!/bin/bash
THRESHOLD=85
df -h | grep -vE '^Filesystem|tmpfs' | awk '{print $5,$1}' | while read usage fs; do
    usage=${usage%\%}
    if [ $usage -ge $THRESHOLD ]; then
        echo "Disk $fs at ${usage}% on $(date)" | mail -s "Disk Alert" admin@example.com
    fi
done
```

### Monitoring Script
Create a system health report.

```bash
#!/bin/bash
LOG_FILE="/var/log/system-report.log"
DATE=$(date '+%Y-%m-%d %H:%M:%S')

echo "=== System Report: $DATE ===" >> $LOG_FILE
echo "Uptime: $(uptime)" >> $LOG_FILE
echo -e "\nCPU Usage:" >> $LOG_FILE
mpstat >> $LOG_FILE
echo -e "\nMemory Usage:" >> $LOG_FILE
free -h >> $LOG_FILE
echo -e "\nDisk Usage:" >> $LOG_FILE
df -h >> $LOG_FILE
echo -e "\nTop 5 CPU Processes:" >> $LOG_FILE
ps aux --sort=-%cpu | head -6 >> $LOG_FILE

# Schedule with cron
# 0 * * * * /path/to/monitor.sh
```

### Watchdog Script
Ensure critical services are running.

```bash
#!/bin/bash
SERVICE="nginx"
if ! systemctl is-active --quiet $SERVICE; then
    echo "Restarting $SERVICE at $(date)"
    sudo systemctl restart $SERVICE
    echo "$SERVICE restarted" | mail -s "$SERVICE Down" admin@example.com
fi
```

**Pro Tip**: Use monitoring tools like Prometheus and Grafana for advanced alerting.

---

## 🛠️ Practice Exercises

### Exercise 1: CPU Monitoring
1. Check CPU details:
   ```bash
   lscpu
   cat /proc/cpuinfo | grep "model name" | head -1
   ```
2. Monitor CPU usage:
   ```bash
   htop
   # Sort by CPU (F6), quit (F10)
   ```
3. Find top CPU users:
   ```bash
   ps -eo pid,user,%cpu,cmd --sort=-%cpu | head -10
   ```

### Exercise 2: Memory Monitoring
1. Check memory usage:
   ```bash
   free -h
   watch -n 2 'free -h'
   ```
2. Find memory hogs:
   ```bash
   ps -eo pid,user,%mem,cmd --sort=-%mem | head -10
   ```
3. Check swap usage:
   ```bash
   swapon --show
   ```

### Exercise 3: Disk Monitoring
1. Check disk space:
   ```bash
   df -h
   ```
2. Find large files:
   ```bash
   find /var -type f -size +50M -exec ls -lh {} \;
   ```
3. Monitor I/O:
   ```bash
   iostat -x 1 5
   ```

### Exercise 4: Log Analysis
1. Monitor system logs:
   ```bash
   sudo tail -f /var/log/syslog
   ```
2. Find errors:
   ```bash
   sudo grep -i "error" /var/log/syslog | tail -10
   ```
3. Check service logs:
   ```bash
   journalctl -u nginx -n 50
   ```

### Exercise 5: Alerting
1. Create and test CPU alert:
   ```bash
   vim cpu_alert.sh
   # Add CPU alert script from above
   chmod +x cpu_alert.sh
   ./cpu_alert.sh
   ```

---

## 📚 Quick Reference Cheat Sheet

```bash
# System
uptime                     # Load average
htop                       # Process monitor
vmstat 1                   # System stats

# CPU
mpstat -P ALL             # Per-CPU stats
ps -eo pid,%cpu,cmd --sort=-%cpu  # Top CPU users
lscpu                     # CPU details

# Memory
free -h                   # Memory usage
ps -eo pid,%mem,cmd --sort=-%mem  # Top memory users
vmstat -s                 # Memory stats

# Disk
df -h                     # Disk space
du -sh /*                 # Directory sizes
iostat -x                 # Disk I/O
iotop -o                  # Active I/O processes

# Network
ss -tulpn                 # Listening ports
iftop -i eth0             # Bandwidth usage
nethogs eth0              # Per-process bandwidth

# Logs
tail -f /var/log/syslog   # Real-time logs
journalctl -u nginx -f     # Service logs
grep -i "error" /var/log/*  # Search errors
logrotate -f /etc/logrotate.conf  # Rotate logs
```

---

## 🔐 Best Practices

1. **Regular Monitoring**: Schedule monitoring scripts with cron.
   ```bash
   0 * * * * /path/to/monitor.sh
   ```
2. **Log Rotation**: Configure `logrotate` to prevent disk exhaustion.
   ```bash
   sudo logrotate -f /etc/logrotate.conf
   ```
3. **Alert Thresholds**: Set conservative thresholds (e.g., 80% CPU, 85% disk).
4. **Backup Logs**: Archive logs before deletion.
   ```bash
   tar -czf /backup/logs-$(date +%Y%m%d).tar.gz /var/log
   ```
5. **Secure Logs**: Restrict log file permissions.
   ```bash
   chmod 640 /var/log/syslog
   chown syslog:adm /var/log/syslog
   ```
6. **Use Modern Tools**: Prefer `ss` over `netstat`, `journalctl` for systemd logs.

---

## 📖 Additional Resources

- **[Linux System Monitoring](https://tldp.org/LDP/Linux-Filesystem-Hierarchy/html/monitoring.html)**: TLDP guide.
- **[Red Hat: Performance Monitoring](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/monitoring_and_managing_system_status)**: Enterprise guide.
- **[Ubuntu: Logs](https://help.ubuntu.com/community/LinuxLogFiles)**: Log management tutorial.
- **[Linux Journey: Monitoring](https://linuxjourney.com/lesson/monitoring)**: Interactive learning.
- **[Unix & Linux Stack Exchange](https://unix.stackexchange.com/)**: Community Q&A.

**Community Tip**: Join [Reddit r/sysadmin](https://www.reddit.com/r/sysadmin/) for monitoring tips! 🗣️

---

## ✅ Module Completion Checklist

- [ ] Monitor CPU usage and load average
- [ ] Analyze memory usage and swap activity
- [ ] Check disk space and I/O performance
- [ ] Monitor network traffic and connections
- [ ] Access and analyze system and service logs
- [ ] Configure log rotation with `logrotate`
- [ ] Identify performance bottlenecks with `sar`, `iostat`
- [ ] Create and test monitoring/alert scripts
- [ ] Complete all practice exercises

---

**Previous Module**: [Module 07: Process Management](../07-process-management/ProcessManagement.md)  
**Next Module**: [Module 09: Networking](../09-networking/Networking.md)

[⬆ Back to Main README](../README.md)