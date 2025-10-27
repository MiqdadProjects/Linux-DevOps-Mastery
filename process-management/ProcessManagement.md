
# ⚙️ Module 07: Process Management in Linux

Welcome back, Linux command-line maestros! 🎉 After securing files in Module 06, it’s time to take control of **processes**—the beating heart of any Linux system. Whether you’re restarting a web server, scheduling backups, or taming runaway processes, mastering process management is a DevOps superpower. 💪 This guide will transform you into a process wrangler, ready to monitor, control, and optimize your system with confidence! 🚀 Let’s get started!

## 📋 Table of Contents
- [Understanding Processes](#understanding-processes)
- [Viewing Processes](#viewing-processes)
- [Process Control](#process-control)
- [Background and Foreground Processes](#background-and-foreground-processes)
- [Process Priority](#process-priority)
- [Systemd and Service Management](#systemd-and-service-management)
- [Job Scheduling](#job-scheduling)
- [Process Monitoring](#process-monitoring)
- [Practice Exercises](#practice-exercises)
- [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)
- [Best Practices](#best-practices)
- [Additional Resources](#additional-resources)
- [Module Completion Checklist](#module-completion-checklist)

---

## 🔄 Understanding Processes

### What is a Process?

A **process** is a running instance of a program, from a simple command like `ls` to a complex service like `nginx`. Each process consumes CPU, memory, and other resources, and Linux manages them efficiently to keep your system humming. 🖥️

### Process Attributes
- **PID**: Unique Process ID.
- **PPID**: Parent Process ID.
- **UID**: User ID of the process owner.
- **State**: Current status (e.g., Running, Sleeping).
- **Priority**: Scheduling priority (nice value).
- **Memory/CPU Usage**: Resources consumed.

### Process Types
1. **Foreground Processes**: Run in the terminal, block input (e.g., `vim config.conf`).
2. **Background Processes**: Run without terminal control (e.g., `nginx &`).
3. **Daemon Processes**: System services running in the background (e.g., `sshd`, `mysqld`).
4. **Zombie Processes**: Terminated but not reaped by parent (`<defunct>` in `ps`).
5. **Orphan Processes**: Parent exited; adopted by `systemd` (PID 1).

### Process States
```
R - Running/Runnable (actively using CPU)
S - Interruptible Sleep (waiting for I/O or event)
D - Uninterruptible Sleep (waiting for disk I/O)
T - Stopped (paused by signal)
Z - Zombie (terminated, awaiting parent cleanup)
X - Dead (rare, should not appear)
```

### Process Hierarchy
```bash
systemd (PID 1)
├── sshd (PID 1234)
│   └── bash (PID 5678)
│       └── vim (PID 9012)
├── nginx (PID 2345)
│   ├── nginx: master (PID 2346)
│   └── nginx: worker (PID 2347)
├── mysql (PID 3456)
└── cron (PID 4567)
```

**Example**:
```bash
# View process hierarchy
pstree
# Output: Shows systemd as root, with children like sshd, nginx
```

**Pro Tip**: PID 1 is always `systemd` (or `init` on older systems), the parent of all processes.

---

## 👁️ Viewing Processes

### Using `ps` Command
The `ps` command displays process information with flexible output formats.

```bash
# Current user's processes
ps
# Output: PID TTY TIME CMD
#         1234 pts/0 00:00:00 bash
#         5678 pts/0 00:00:00 ps

# All processes (BSD style)
ps aux
# Columns: USER, PID, %CPU, %MEM, VSZ, RSS, TTY, STAT, START, TIME, COMMAND

# All processes (Unix style)
ps -ef
# Columns: UID, PID, PPID, C, STIME, TTY, TIME, CMD

# Process tree
ps -ejH
ps axjf
```

**Custom Output**:
```bash
# Select specific columns
ps -eo pid,ppid,user,%cpu,%mem,cmd --sort=-%cpu
# Sort by CPU usage

# Filter by user
ps -u alice

# Find process by name
ps aux | grep nginx
```

**Examples**:
```bash
# Top memory users
ps aux --sort=-%mem | head -10

# Find zombie processes
ps aux | awk '$8=="Z" {print $2, $11}'
# Output: PID and command of zombies

# Process details for PID
ps -p 1234 -o pid,ppid,stat,cmd
```

### Using `top` Command
Interactive, real-time process viewer.

```bash
top
# Output:
# top - 15:08:32 up 5 days, 2:30, 2 users, load average: 0.50, 0.40, 0.35
# Tasks: 245 total, 1 running, 244 sleeping, 0 stopped, 0 zombie
# %Cpu(s): 5.2 us, 2.3 sy, 0.0 ni, 92.0 id, 0.5 wa
# MiB Mem: 7850.5 total, 1234.2 free, 3456.7 used
#   PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
#  1234 alice 20 0 2345678 123456 12345 S 5.2 1.6 1:23.45 firefox
```

**Top Shortcuts**:
- `q`: Quit
- `M`: Sort by memory
- `P`: Sort by CPU
- `k`: Kill process (enter PID)
- `r`: Renice process
- `u`: Filter by user
- `1`: Show per-CPU usage
- `d`: Change refresh interval

**Alternative**: `htop` (more user-friendly, if installed):
```bash
sudo apt install htop  # Ubuntu/Debian
sudo dnf install htop  # RHEL/CentOS
htop
```

### Using `pgrep` and `pidof`
```bash
# Find PIDs by name
pgrep nginx
# Output: 2345 2346 2347

# Show full command
pgrep -a nginx
# Output: 2345 /usr/sbin/nginx -g daemon on;

# Find by user
pgrep -u alice firefox

# Count processes
pgrep -c python

# Get single PID
pidof nginx
```

### Using `pstree`
```bash
# Process tree
pstree -p
# Output: systemd(1)---sshd(1234)---bash(5678)---vim(9012)

# User-specific
pstree alice

# Command arguments
pstree -a
```

**Example**:
```bash
# Monitor top processes in real-time
watch -n 2 'ps aux --sort=-%cpu | head -10'
```

**Pro Tip**: Use `htop` for a colorful, intuitive interface over `top`.

---

## 🎮 Process Control

### Killing Processes
Use `kill`, `killall`, or `pkill` to terminate processes.

#### `kill` Command
```bash
# Graceful termination
kill 1234

# Force kill
kill -9 1234

# Reload configuration
kill -HUP 2345  # Common for nginx, apache

# Common signals
kill -l  # List signals
# 1) SIGHUP  2) SIGINT  9) SIGKILL  15) SIGTERM  19) SIGSTOP

# Examples
sudo kill -9 $(pgrep firefox)  # Force kill firefox
sudo kill -HUP $(cat /var/run/nginx.pid)  # Reload nginx
```

#### `killall` Command
```bash
# Kill by name
killall nginx

# Force kill
killall -9 python

# User-specific
killall -u bob chrome

# Interactive (prompt before killing)
killall -i firefox
```

#### `pkill` Command
```bash
# Kill by pattern
pkill python

# User-specific
pkill -u alice

# By command pattern
pkill -f "python.*server"
```

**Example**:
```bash
# Stop a hung backup script
ps aux | grep backup.sh
# Output: alice 1234 ... /bin/bash ./backup.sh
kill -9 1234
```

**Pro Tip**: Use `SIGTERM` (`-15`) first for graceful shutdown; resort to `SIGKILL` (`-9`) only if necessary.

---

## 🔀 Background and Foreground Processes

### Running in Background
```bash
# Start in background
sleep 100 &

# Check jobs
jobs
# Output: [1]+ Running sleep 100 &

# Run script in background
./backup.sh > backup.log 2>&1 &
```

### Job Control
```bash
# List jobs
jobs -l
# Output: [1]+ 1234 Running sleep 100 &

# Bring to foreground
fg %1

# Suspend foreground job
Ctrl+Z

# Resume in background
bg %1

# Example workflow
vim report.txt
Ctrl+Z  # Suspend
bg      # Run in background
jobs    # Check
fg      # Bring back
```

### Using `nohup`
```bash
# Run after logout
nohup ./long_script.sh &

# Redirect output
nohup python server.py > server.log 2>&1 &

# Check
ps aux | grep nohup
```

### Using `disown`
```bash
# Start and disown
firefox &
disown %1

# Disown all jobs
disown -a
```

### Using `screen` or `tmux`
```bash
# Start screen
screen
./long_script.sh
Ctrl+A, D  # Detach
screen -ls  # List sessions
screen -r  # Reattach

# Start tmux
tmux
./long_script.sh
Ctrl+B, D  # Detach
tmux ls
tmux attach
```

**Example**:
```bash
# Run a backup in screen
screen
./backup.sh
Ctrl+A, D
screen -r  # Resume later
```

**Pro Tip**: Use `tmux` for persistent sessions; it’s more modern than `screen`.

---

## 🎚️ Process Priority

### Nice Values
- Range: -20 (highest priority) to +19 (lowest).
- Default: 0.
- Only root can set negative values.

```bash
# Start with nice
nice -n 10 ./backup.sh  # Low priority
sudo nice -n -5 ./critical.sh  # High priority

# Change running process
renice 15 -p 1234
sudo renice -5 -p 2345

# User processes
renice 10 -u alice
```

**Example**:
```bash
# Lower priority for backup
ps aux | grep backup.sh
# Output: alice 1234 ... ./backup.sh
renice 19 -p 1234
```

**Pro Tip**: Use `chrt` for real-time scheduling (advanced):
```bash
sudo chrt -r -p 20 1234  # Real-time priority
```

---

## 🔧 Systemd and Service Management

Systemd manages services, daemons, and system state.

### Service Commands
```bash
# Start/stop/restart
sudo systemctl start nginx
sudo systemctl stop mysql
sudo systemctl restart apache2

# Reload (no downtime)
sudo systemctl reload nginx

# Enable/disable at boot
sudo systemctl enable docker
sudo systemctl disable mongodb

# Check status
sudo systemctl status sshd
# Output: Shows active, PID, logs
```

### Viewing Services
```bash
# List running services
systemctl list-units --type=service --state=running

# List enabled services
systemctl list-unit-files --type=service --state=enabled

# Service logs
journalctl -u nginx -f  # Follow logs
journalctl -u mysql -n 50  # Last 50 lines
```

### Creating a Service
```bash
sudo vim /etc/systemd/system/webapp.service
# Content:
[Unit]
Description=Web Application
After=network.target

[Service]
Type=simple
User=webapp
WorkingDirectory=/opt/webapp
ExecStart=/opt/webapp/run.sh
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target

# Reload and start
sudo systemctl daemon-reload
sudo systemctl enable webapp
sudo systemctl start webapp
```

**Example**:
```bash
# Restart nginx after config change
sudo nginx -t  # Test config
sudo systemctl reload nginx
sudo journalctl -u nginx -n 20
```

**Pro Tip**: Use `systemctl is-active nginx` to check if a service is running.

---

## ⏰ Job Scheduling

### Cron
Schedules recurring tasks.

**Crontab Format**:
```
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of week (0-7)
│ │ │ └──── Month (1-12)
│ │ └────── Day of month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)
```

```bash
# Edit crontab
crontab -e
# Add:
0 3 * * * /scripts/backup.sh >> /logs/backup.log 2>&1

# List crontab
crontab -l

# Edit for user
sudo crontab -e -u alice
```

**Examples**:
```bash
# Daily backup at 3 AM
0 3 * * * /scripts/backup.sh

# Weekly update (Sunday 4 AM)
0 4 * * 0 apt update && apt upgrade -y

# Every 10 minutes
*/10 * * * * /scripts/monitor.sh

# Special strings
@daily /scripts/cleanup.sh
@reboot /scripts/startup.sh
```

### `at` Command
For one-time jobs.

```bash
# Schedule job
at 22:00
at> /scripts/backup.sh
at> Ctrl+D

# Other formats
at now + 1 hour
at 10:00 tomorrow

# List jobs
atq

# Remove job
atrm 1
```

**Example**:
```bash
# Schedule shutdown
echo "sudo shutdown -h now" | at 23:00
```

**Pro Tip**: Add `MAILTO=admin@example.com` in crontab to email job output.

---

## 📊 Process Monitoring

### Real-time Tools
```bash
# Basic monitoring
top

# Enhanced interface
htop

# Monitor specific PID
top -p 1234
```

### Resource Usage
```bash
# CPU usage
ps aux --sort=-%cpu | head -10
mpstat

# Memory usage
free -h
ps aux --sort=-%mem | head -10

# Disk I/O
iostat
iotop  # Requires sudo

# Network
nethogs eth0
iftop -i eth0
```

### System Load
```bash
uptime
# Output: 15:08:32 up 5 days, load average: 0.50, 0.40, 0.35
# Interpretation: < nproc = good, > nproc = overloaded
nproc  # Check CPU count
```

### Process Limits
```bash
# View limits
ulimit -a
cat /proc/1234/limits

# Set limits
ulimit -n 8192  # Max open files
# Permanent: Edit /etc/security/limits.conf
sudo vim /etc/security/limits.conf
# Add:
alice soft nofile 8192
alice hard nofile 16384
```

**Example**:
```bash
# Monitor high CPU usage
watch -n 2 'ps aux --sort=-%cpu | head -10'
```

**Pro Tip**: Install `sysstat` for advanced monitoring:
```bash
sudo apt install sysstat
sar -u 2  # CPU usage every 2 seconds
```

---

## 🛠️ Practice Exercises

### Exercise 1: Viewing Processes
1. List all processes:
   ```bash
   ps aux | less
   ```
2. Find nginx PIDs:
   ```bash
   pgrep -a nginx
   ```
3. View process tree:
   ```bash
   pstree -p
   ```
4. Monitor with htop:
   ```bash
   htop
   # Press F6 to sort, F9 to kill
   ```

### Exercise 2: Process Control
1. Start a background process:
   ```bash
   sleep 1000 &
   jobs
   ```
2. Find and kill it:
   ```bash
   ps aux | grep sleep
   kill -9 <PID>
   ```

### Exercise 3: Service Management
1. Check and restart SSH:
   ```bash
   sudo systemctl status sshd
   sudo systemctl restart sshd
   journalctl -u sshd -n 20
   ```
2. Enable Docker at boot:
   ```bash
   sudo systemctl enable docker
   ```

### Exercise 4: Cron Job
1. Create a cron job:
   ```bash
   crontab -e
   # Add:
   */2 * * * * date >> /tmp/cron.log
   ```
2. Verify:
   ```bash
   tail -f /tmp/cron.log
   ```
3. Remove:
   ```bash
   crontab -r
   ```

### Exercise 5: Priority
1. Run low-priority process:
   ```bash
   nice -n 15 sleep 1000 &
   ```
2. Check priority:
   ```bash
   ps -p <PID> -o pid,ni,cmd
   ```
3. Adjust priority:
   ```bash
   renice 10 -p <PID>
   ```

---

## 📚 Quick Reference Cheat Sheet

```bash
# Viewing Processes
ps aux                 # All processes
ps -ef                 # Unix style
top                    # Interactive monitor
htop                   # Enhanced monitor
pgrep -a nginx         # Find by name
pstree -p              # Process tree

# Process Control
kill -15 1234          # Graceful kill
kill -9 1234           # Force kill
killall nginx          # Kill by name
pkill -u alice         # Kill user’s processes

# Background/Foreground
command &              # Run in background
Ctrl+Z, bg             # Suspend, resume in background
fg %1                  # Bring to foreground
nohup command &       # Run after logout

# Priority
nice -n 10 command     # Start with low priority
renice 5 -p 1234       # Adjust priority

# Systemd
systemctl start nginx  # Start service
systemctl status nginx # Check status
journalctl -u nginx -f # Follow logs

# Cron
crontab -e             # Edit crontab
*/5 * * * * command    # Every 5 minutes
@daily command         # Daily job
```

---

## 🔐 Best Practices

1. **Graceful Termination**: Use `SIGTERM` (`kill -15`) before `SIGKILL` (`kill -9`).
   ```bash
   kill -15 1234
   ```
2. **Monitor Resource Hogs**:
   ```bash
   watch -n 2 'ps aux --sort=-%cpu | head -5'
   ```
3. **Secure Services**: Run services as non-root users.
   ```bash
   sudo chown webapp:webapp /opt/webapp
   ```
4. **Log Rotation**: Schedule log cleanup with cron.
   ```bash
   0 2 * * * /usr/sbin/logrotate /etc/logrotate.conf
   ```
5. **Limit Resources**: Use `ulimit` or cgroups for resource-intensive processes.
   ```bash
   ulimit -u 500  # Limit user processes
   ```
6. **Automate Backups**:
   ```bash
   0 3 * * * /scripts/backup.sh
   ```

---

## 📖 Additional Resources

- **[Linux Process Management](https://tldp.org/LDP/intro-linux/html/sect_04_01.html)**: TLDP guide.
- **[Red Hat: Systemd](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_system_services_with_systemd)**: Enterprise systemd guide.
- **[CronHowto](https://help.ubuntu.com/community/CronHowto)**: Ubuntu cron tutorial.
- **[Linux Journey: Processes](https://linuxjourney.com/lesson/processes)**: Interactive learning.
- **[Unix & Linux Stack Exchange](https://unix.stackexchange.com/)**: Community Q&A.

**Community Tip**: Join [Reddit r/linuxadmin](https://www.reddit.com/r/linuxadmin/) for process management tips! 🗣️

---

## ✅ Module Completion Checklist

- [ ] Understand process types and states
- [ ] View processes with `ps`, `top`, `htop`, `pstree`
- [ ] Control processes using `kill`, `killall`, `pkill`
- [ ] Manage background/foreground jobs with `jobs`, `fg`, `bg`, `nohup`
- [ ] Adjust priorities with `nice` and `renice`
- [ ] Manage services with `systemctl` and `journalctl`
- [ ] Schedule jobs with `cron` and `at`
- [ ] Monitor resources with `htop`, `iostat`, `nethogs`
- [ ] Complete all practice exercises

---

**Previous Module**: [Module 06: File Permissions](../06-file-permissions/FilePermissions.md)  
**Next Module**: [Module 08: System Monitoring](../08-monitoring/SystemMonitoring.md)

[⬆ Back to Main README](../README.md)