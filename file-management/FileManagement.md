# 📄 Module 04: File Management in Linux

Welcome back, Linux warriors! 🎉 After conquering users and groups in Module 03, it’s time to master **file management**—the art of creating, organizing, searching, and manipulating files in Linux. Whether you’re managing server logs, scripting automation, or securing backups, this module will transform you into a file system maestro! 🥁 Let’s dive into the tools and techniques to make your Linux file operations seamless and powerful! 🚀

## 📋 Table of Contents
- [Basic File Operations](#basic-file-operations)
- [File Viewing and Editing](#file-viewing-and-editing)
- [File Searching](#file-searching)
- [Text Processing](#text-processing)
- [File Compression and Archiving](#file-compression-and-archiving)
- [Links - Symbolic and Hard](#links---symbolic-and-hard)
- [File Attributes](#file-attributes)
- [Practice Exercises](#practice-exercises)
- [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)
- [Additional Resources](#additional-resources)
- [Module Completion Checklist](#module-completion-checklist)

---

## 📂 Basic File Operations

File operations are the bread and butter of Linux administration. Let’s learn how to create, copy, move, and delete files and directories with precision! 🛠️

### Creating Files and Directories

#### Creating Files with `touch`
```bash
# Create an empty file
touch report.txt

# Create multiple files
touch log1.txt log2.txt log3.txt

# Create with specific timestamp
touch -t 202501011200.00 report.txt  # Sets to Jan 1, 2025, 12:00

# Update timestamp of existing file
touch existing_file.txt

# Create only if it doesn’t exist
touch -c newfile.txt

# Create files in bulk with pattern
touch data{1..5}.txt  # Creates data1.txt, data2.txt, ..., data5.txt
```

**Example**:
```bash
# Create a timestamped log file
touch "backup_$(date +%Y%m%d_%H%M%S).log"
ls
# Output: backup_20251027_141530.log
```

#### Creating Directories with `mkdir`
```bash
# Create a single directory
mkdir projects

# Create multiple directories
mkdir src docs tests

# Create nested directories
mkdir -p app/frontend/components

# Create with specific permissions
mkdir -m 750 secure_dir

# Verbose output
mkdir -v logs
# Output: mkdir: created directory 'logs'
```

**Example**:
```bash
# Create a project structure
mkdir -p myapp/{src,tests,docs,bin}
tree myapp
# Output:
# myapp/
# ├── bin
# ├── docs
# ├── src
# └── tests
```

### Copying Files and Directories with `cp`
```bash
# Copy a file
cp config.conf config.bak

# Copy to another directory
cp report.txt /home/user/backups/

# Copy multiple files
cp *.txt /home/user/docs/

# Copy directory recursively
cp -r app/ app_backup/

# Preserve file attributes (permissions, timestamps)
cp -p config.conf config.bak

# Copy only if newer
cp -u data.txt /backups/

# Interactive mode (prompt before overwrite)
cp -i script.sh /scripts/
# Prompt: cp: overwrite '/scripts/script.sh'? y/n

# Force overwrite
cp -f old.conf /etc/nginx/

# Create backup with numbered suffix
cp --backup=numbered config.conf /etc/nginx/
# Creates config.conf.~1~ if exists

# Copy with progress (using rsync)
rsync -ah --progress app/ /backup/app/
```

**Example**:
```bash
# Backup Nginx config with progress
rsync -ah --progress /etc/nginx/ /backup/nginx-$(date +%Y%m%d)/
# Output: Shows progress bar and file sizes
```

### Moving and Renaming Files with `mv`
```bash
# Rename a file
mv draft.txt final.txt

# Move to another directory
mv log.txt /var/log/

# Move multiple files
mv *.log /var/log/archives/

# Move directory
mv src/ app/src/

# Interactive mode
mv -i config.conf /etc/
# Prompt: mv: overwrite '/etc/config.conf'? y/n

# No overwrite
mv -n data.txt /backup/

# Verbose output
mv -v script.sh /scripts/
# Output: renamed 'script.sh' -> '/scripts/script.sh'
```

**Example**:
```bash
# Organize logs by date
mkdir -p /var/log/archives/$(date +%Y%m)
mv access.log /var/log/archives/$(date +%Y%m)/access_$(date +%Y%m%d).log
```

### Deleting Files and Directories with `rm` and `rmdir`
```bash
# Delete a file
rm temp.txt

# Delete multiple files
rm *.tmp

# Interactive deletion
rm -i old.log
# Prompt: rm: remove regular file 'old.log'? y/n

# Force deletion
rm -f stale.conf

# Delete empty directory
rmdir empty_dir/

# Delete directory and contents
rm -r old_project/

# Force delete directory (DANGEROUS!)
rm -rf obsolete_data/

# Verbose deletion
rm -v *.txt
# Output: removed 'file1.txt', removed 'file2.txt'

# Delete files older than 30 days
find /var/log -type f -mtime +30 -delete

# Delete files matching pattern
rm log*.bak
```

**⚠️ Warning**: `rm -rf /` can wipe your entire system! Always double-check paths:
```bash
# Safe practice
ls /path/to/delete
rm -rf /path/to/delete

# Use trash-cli for safer deletion
sudo apt install trash-cli  # Ubuntu/Debian
trash oldfile.txt
trash-list  # View trash
trash-restore  # Restore files
```

**Example**:
```bash
# Safely clean up old logs
find /var/log -name "*.log" -mtime +90 -exec ls -lh {} \;
find /var/log -name "*.log" -mtime +90 -delete
```

**Pro Tip**: Alias `rm` to `rm -i` for safety:
```bash
echo "alias rm='rm -i'" >> ~/.bashrc
source ~/.bashrc
```

---

## 👀 File Viewing and Editing

Viewing and editing files is essential for debugging configs, analyzing logs, and scripting. Let’s explore the tools! 📖

### Viewing File Contents

#### `cat` (Concatenate)
```bash
# Display file content
cat /etc/hosts

# Display with line numbers
cat -n access.log

# Show non-printing characters (e.g., tabs, newlines)
cat -A config.conf

# Concatenate multiple files
cat log1.txt log2.txt > combined.log

# Append to existing file
cat newdata.txt >> existing.log

# Create file interactively (Ctrl+D to save)
cat > notes.txt
Write some notes here
^D
```

**Example**:
```bash
# Combine logs with timestamp
echo "Log dump: $(date)" > combined.log
cat app1.log app2.log >> combined.log
cat -n combined.log
```

#### `less` and `more` (Paginated Viewing)
```bash
# View file with less (better for large files)
less /var/log/syslog

# Less shortcuts:
# Space or f   : Next page
# b            : Previous page
# /pattern     : Search forward
# ?pattern     : Search backward
# n            : Next match
# N            : Previous match
# g            : Go to start
# G            : Go to end
# q            : Quit

# View with more (simpler, less flexible)
more access.log

# Follow file in real-time
less +F /var/log/syslog  # Like tail -f
```

**Example**:
```bash
# Search for errors in logs
less /var/log/syslog
# Type: /error
# Press: n to find next occurrence
```

#### `head` and `tail`
```bash
# View first 10 lines
head config.conf

# View first 20 lines
head -n 20 error.log

# View first 100 bytes
head -c 100 data.txt

# View last 10 lines
tail access.log

# View last 50 lines
tail -n 50 /var/log/syslog

# Follow log in real-time
tail -f /var/log/nginx/access.log

# Follow multiple logs
tail -f app1.log app2.log

# Follow with retry (waits for file creation)
tail -F /var/log/new.log

# Show last 100 lines and follow
tail -n 100 -f /var/log/syslog
```

**Example**:
```bash
# Monitor recent errors
tail -n 50 -f /var/log/syslog | grep -i "error"
```

#### `nl` (Number Lines)
```bash
# Number all lines
nl script.sh

# Number non-empty lines
nl -b t config.conf

# Custom number format (right-justified, 3 digits)
nl -n rz -w 3 data.txt
```

**Example**:
```bash
# Number lines in a script
nl -b t deploy.sh
# Output: Numbers only non-empty lines
```

### Quick File Editing with `sed`
```bash
# Replace text
sed 's/old/new/g' config.conf > new_config.conf

# In-place editing
sed -i 's/server_name localhost/server_name example.com/g' nginx.conf

# Add line at start
sed -i '1i\# Config updated: 2025-10-27' config.conf

# Add line at end
echo "# End of config" | sed -i '$a\' config.conf

# Delete line 5
sed -i '5d' script.sh

# Delete lines matching pattern
sed -i '/# commented/d' config.conf
```

**Example**:
```bash
# Update all instances of "DEBUG" to "INFO" in logs
sed -i 's/DEBUG/INFO/g' app.log
grep "INFO" app.log
```

**Pro Tip**: Use `nano` for quick edits or `vim` for advanced editing (covered in Module 05):
```bash
nano /etc/hosts
```

---

## 🔍 File Searching

Finding files and searching content is a DevOps superpower. Let’s master the tools to locate anything! 🔎

### Using `find`
```bash
# Find files by name
find /home -name "report.txt"

# Case-insensitive search
find /etc -iname "*.conf"

# Find directories
find /var -type d -name "logs"

# Find files only
find / -type f -name "*.log" 2>/dev/null

# Find by size
find /home -size +100M  # Larger than 100MB
find /tmp -size -1M     # Smaller than 1MB
find /data -size 50M    # Exactly 50MB

# Find by modification time
find /var/log -mtime -7   # Modified in last 7 days
find /backup -mtime +30   # Older than 30 days
find /tmp -mmin -60       # Modified in last 60 minutes

# Find by permissions
find /etc -perm 644       # Exactly 644
find /home -perm -u+w     # User writable

# Find by owner
find / -user john -type f 2>/dev/null
find /data -group devteam

# Execute command on found files
find /tmp -name "*.tmp" -exec rm {} \;
find /app -type f -exec chmod 644 {} \+

# Delete matching files
find /tmp -name "*.bak" -delete

# Multiple conditions
find /home -name "*.txt" -size +1M

# OR condition
find /var \( -name "*.log" -o -name "*.bak" \)

# Exclude directory
find / -name "*.conf" -not -path "*/archive/*" 2>/dev/null

# Find empty files
find /tmp -type f -empty

# Find with detailed listing
find /etc -name "*.conf" -ls
```

**Example**:
```bash
# Find and delete old backups
find /backup -name "*.tar.gz" -mtime +90 -exec ls -lh {} \;
find /backup -name "*.tar.gz" -mtime +90 -delete
```

### Using `locate`
```bash
# Update locate database
sudo updatedb

# Find files (fast, database-driven)
locate nginx.conf

# Case-insensitive
locate -i readme.md

# Limit to 10 results
locate -n 10 *.txt

# Count results
locate -c *.log

# Show only existing files
locate -e config.conf

# Use regex
locate -r "\.conf$"
```

**Example**:
```bash
# Quickly find all Nginx configs
locate -r "nginx.*\.conf"
```

### Using `grep` (Search Inside Files)
```bash
# Search for text
grep "error" app.log

# Case-insensitive
grep -i "error" app.log

# Recursive search
grep -r "server_name" /etc/nginx/

# Show line numbers
grep -n "failed" /var/log/syslog

# Show only matching part
grep -o "192\.168\.[0-9]+\.[0-9]+" access.log

# Invert match
grep -v "INFO" app.log

# Count matches
grep -c "error" error.log

# List files with matches
grep -l "error" *.log

# Show context
grep -A 3 "error" app.log  # 3 lines after
grep -B 3 "error" app.log  # 3 lines before
grep -C 3 "error" app.log  # 3 lines before and after

# Multiple patterns
grep -E "error|fail" app.log

# Whole word search
grep -w "root" /etc/passwd
```

**Example**:
```bash
# Find IP addresses in access logs
grep -oE "[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}" /var/log/nginx/access.log | sort | uniq -c
```

### Using `which` and `whereis`
```bash
# Find command location
which python3
# Output: /usr/bin/python3

# Find command, source, and man pages
whereis nginx
# Output: nginx: /usr/sbin/nginx /etc/nginx /usr/share/man/man8/nginx.8.gz
```

**Pro Tip**: Redirect `find` errors to `/dev/null` to clean up output:
```bash
find / -name "*.conf" 2>/dev/null
```

---

## ✂️ Text Processing

Text processing is a DevOps superpower for parsing logs, configs, and data. Let’s wield these tools like a pro! 🧙

### Using `cut`
```bash
# Cut by character position
cut -c 1-10 data.txt
cut -c 5- data.txt  # From 5th character

# Cut by delimiter
cut -d ':' -f 1 /etc/passwd  # First field (usernames)
cut -d ',' -f 1,3 data.csv   # Fields 1 and 3

# Use tab as delimiter
cut -f 1,3 -d $'\t' table.txt
```

**Example**:
```bash
# Extract usernames and UIDs
cut -d ':' -f 1,3 /etc/passwd | sort
# Output: john:1000, alice:1001
```

### Using `sort`
```bash
# Sort alphabetically
sort names.txt

# Sort numerically
sort -n sizes.txt

# Reverse sort
sort -r names.txt

# Sort by column
sort -k 2 data.txt  # Second column
sort -t ':' -k 3 -n /etc/passwd  # Third field, numeric

# Remove duplicates
sort -u names.txt

# Case-insensitive
sort -f names.txt

# Sort by human-readable sizes
du -h /var | sort -h
```

**Example**:
```bash
# Sort users by UID
sort -t ':' -k 3 -n /etc/passwd | cut -d ':' -f 1,3
```

### Using `uniq`
```bash
# Remove duplicates (sort first!)
sort names.txt | uniq

# Count occurrences
sort ips.txt | uniq -c | sort -rn

# Show only duplicates
sort names.txt | uniq -d

# Show unique lines
sort names.txt | uniq -u
```

**Example**:
```bash
# Find top 5 IPs in access log
grep -oE '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' access.log | sort | uniq -c | sort -rn | head -5
```

### Using `wc` (Word Count)
```bash
# Count lines, words, chars
wc report.txt
# Output: 10 50 300 report.txt (lines, words, chars)

# Count lines
wc -l /etc/passwd
# Output: 35 /etc/passwd

# Count files
ls /var/log | wc -l
```

**Example**:
```bash
# Count error occurrences
grep -c "ERROR" /var/log/syslog
```

### Using `sed` (Stream Editor)
```bash
# Replace text
sed 's/old/new/g' config.conf > new.conf

# In-place edit
sed -i 's/DEBUG/INFO/g' app.log

# Delete lines
sed -i '1,5d' temp.txt  # Delete lines 1-5
sed -i '/# comment/d' config.conf

# Print specific lines
sed -n '10,20p' log.txt

# Insert text
sed -i '1i\Generated on: 2025-10-27' report.txt
```

**Example**:
```bash
# Clean up commented lines
sed -i '/^#/d' /etc/nginx/nginx.conf
cat /etc/nginx/nginx.conf
```

### Using `awk`
```bash
# Print specific columns
awk '{print $1, $3}' data.txt

# Custom delimiter
awk -F ':' '{print $1, $3}' /etc/passwd

# Conditional printing
awk '$3 > 1000 {print $1}' /etc/passwd

# Calculate sum
awk '{sum += $1} END {print "Total:", sum}' numbers.txt

# Print with line numbers
awk '{print NR, $0}' log.txt
```

**Example**:
```bash
# Extract users with bash shell
awk -F ':' '$7 ~ /bash/ {print $1}' /etc/passwd
```

**Pro Tip**: Chain commands for powerful processing:
```bash
# Find top 5 largest files
du -h /home | sort -hr | head -5
```

---

## 🗜️ File Compression and Archiving

Compression and archiving save space and simplify file transfers—key for backups and deployments! 🗄️

### Using `tar`
```bash
# Create tar archive
tar -cvf backup.tar /home/user/docs/

# Create compressed tar.gz
tar -czvf backup.tar.gz /home/user/docs/

# Create tar.xz (best compression)
tar -cJvf backup.tar.xz /home/user/docs/

# Extract tar
tar -xvf backup.tar

# Extract to directory
tar -xzvf backup.tar.gz -C /tmp/restore/

# List contents
tar -tzvf backup.tar.gz

# Extract specific file
tar -xzvf backup.tar.gz docs/report.txt

# Backup with timestamp
tar -czvf backup_$(date +%Y%m%d).tar.gz /etc/
```

**Example**:
```bash
# Backup Nginx configs excluding logs
tar -czvf nginx_backup_$(date +%Y%m%d).tar.gz --exclude="*.log" /etc/nginx/
```

### Using `gzip` and `gunzip`
```bash
# Compress file
gzip report.txt  # Creates report.txt.gz

# Keep original
gzip -k log.txt

# Decompress
gunzip report.txt.gz

# View compressed file
zcat access.log.gz | less
```

### Using `zip` and `unzip`
```bash
# Create zip
zip archive.zip *.txt

# Zip directory
zip -r backup.zip /home/user/docs/

# Password-protected zip
zip -e secure.zip sensitive.txt

# Extract zip
unzip backup.zip -d /tmp/restore/
```

**Example**:
```bash
# Create and extract a zip
zip -r project.zip project/
unzip project.zip -d /tmp/project_restore/
```

**Compression Comparison**:
| Tool | Compression | Speed | Use Case |
|------|-------------|-------|----------|
| `gzip` | Moderate | Fast | Quick backups |
| `bzip2` | Better | Medium | General use |
| `xz` | Best | Slow | Maximum compression |

**Pro Tip**: Use `pigz` for faster gzip compression on multi-core systems:
```bash
sudo apt install pigz
pigz large_file.txt
```

---

## 🔗 Links - Symbolic and Hard

Links connect files or directories, saving space or simplifying access. Let’s link it up! 🔗

### Hard Links
```bash
# Create hard link
ln data.txt data_link.txt

# Verify inode
ls -li data.txt data_link.txt
# Output: Same inode number

# Check link count
ls -l data.txt
# Output: Shows link count in second column
```

### Symbolic (Soft) Links
```bash
# Create symbolic link
ln -s /var/www/html website

# Force overwrite
ln -sf /etc/nginx/nginx.conf nginx.conf

# Check link
ls -l website
# Output: website -> /var/www/html
```

**Example**:
```bash
# Link logs for easy access
ln -s /var/log/nginx/access.log ~/access.log
cat ~/access.log
```

**Comparison**:
| Feature | Hard Link | Symbolic Link |
|---------|-----------|---------------|
| Points to | Inode | Path |
| Works if original deleted | Yes | No |
| Links directories | No | Yes |
| Cross file systems | No | Yes |
| Command | `ln` | `ln -s` |

**Pro Tip**: Find broken symlinks:
```bash
find /home -type l ! -exec test -e {} \; -print
```

---

## 🏷️ File Attributes

File attributes control how files can be modified. Perfect for protecting critical data! 🛡️

### Using `lsattr` and `chattr`
```bash
# View attributes
lsattr config.conf

# Make immutable
sudo chattr +i /etc/hosts

# Allow append only
sudo chattr +a /var/log/secure.log

# Remove immutable
sudo chattr -i /etc/hosts
```

**Example**:
```bash
# Protect a critical config
sudo chattr +i /etc/nginx/nginx.conf
echo "test" > /etc/nginx/nginx.conf
# Output: Permission denied
```

### File Timestamps
```bash
# View timestamps
stat report.txt
# Output: Access, Modify, Change times

# Update modification time
touch -m data.txt

# Set specific time
touch -t 202501011200 report.txt
```

**Pro Tip**: Use immutable attributes for log files to prevent tampering:
```bash
sudo chattr +a /var/log/audit.log
```

---

## 🛠️ Practice Exercises

Get hands-on to become a file management pro! 💪

### Exercise 1: Basic File Operations
1. Create a directory structure:
   ```bash
   mkdir -p app/{src,logs,config}
   ```
2. Create files:
   ```bash
   touch app/src/main.py app/config/settings.conf
   ```
3. Copy files:
   ```bash
   cp app/config/settings.conf app/config/settings.bak
   ```
4. Move a file:
   ```bash
   mv app/src/main.py app/src/app.py
   ```
5. Delete a directory:
   ```bash
   rm -rf app/logs
   ```

### Exercise 2: File Viewing
1. View recent log entries:
   ```bash
   tail -n 20 /var/log/syslog
   ```
2. Search for errors:
   ```bash
   less /var/log/syslog
   # Type: /error
   ```
3. Combine files:
   ```bash
   cat app1.log app2.log > combined.log
   ```

### Exercise 3: File Searching
1. Find all `.conf` files:
   ```bash
   find /etc -name "*.conf" 2>/dev/null
   ```
2. Search for "server_name" in Nginx configs:
   ```bash
   grep -r "server_name" /etc/nginx/
   ```
3. Find large files:
   ```bash
   find /home -type f -size +500M -exec ls -lh {} \;
   ```

### Exercise 4: Text Processing
1. Extract usernames:
   ```bash
   cut -d ':' -f 1 /etc/passwd | sort > users.txt
   ```
2. Count unique IPs:
   ```bash
   grep -oE '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' access.log | sort | uniq -c
   ```
3. Replace text:
   ```bash
   sed -i 's/DEBUG/INFO/g' app.log
   ```

### Exercise 5: Compression
1. Archive a directory:
   ```bash
   tar -czvf app_backup_$(date +%Y%m%d).tar.gz app/
   ```
2. Extract to a new location:
   ```bash
   tar -xzvf app_backup_$(date +%Y%m%d).tar.gz -C /tmp/restore/
   ```
3. Create a password-protected zip:
   ```bash
   zip -e secure_backup.zip app/config/*
   ```

---

## 📚 Quick Reference Cheat Sheet

```bash
# Basic Operations
touch file.txt                   # Create file
mkdir -p dir1/dir2              # Create directories
cp -r src dst                   # Copy recursively
mv old new                      # Move/rename
rm -rf dir/                     # Remove recursively

# Viewing
cat file.txt                    # Display file
less file.txt                   # Paginated view
head -n 20 file.txt             # First 20 lines
tail -f /var/log/syslog         # Follow log

# Searching
find /path -name "*.txt"        # Find files
grep -r "pattern" /path         # Search in files
locate filename                 # Fast search

# Text Processing
cut -d ':' -f 1 /etc/passwd     # Cut fields
sort -k 2 file.txt              # Sort by column
uniq -c file.txt                # Count duplicates
sed -i 's/old/new/g' file.txt   # Replace text
awk '{print $1}' file.txt       # Print column

# Compression
tar -czvf archive.tar.gz dir/   # Create gzip archive
zip -r archive.zip dir/         # Create zip
gzip file.txt                   # Compress file

# Links
ln file.txt hardlink.txt        # Hard link
ln -s /path/file symlink.txt    # Symbolic link
```

---

## 📖 Additional Resources

- **[Linux Filesystem Guide](https://tldp.org/LDP/intro-linux/html/sect_03_01.html)**: Deep dive into file operations.
- **[Ubuntu: Working with Files](https://help.ubuntu.com/stable/ubuntu-help/files.html)**: User-friendly file management tips.
- **[Red Hat: File Management](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/using_the_command_line_interface/managing_files_from_the_command_line)**: Enterprise-grade advice.
- **[Linux Journey: Files](https://linuxjourney.com/lesson/file-operations)**: Interactive file management lessons.
- **[OverTheWire Bandit](https://overthewire.org/wargames/bandit/)**: Fun file manipulation challenges.

**Community Tip**: Join [Reddit r/linux](https://www.reddit.com/r/linux/) or [Stack Exchange: Unix & Linux](https://unix.stackexchange.com/) for expert advice! 🗣️

---

## ✅ Module Completion Checklist

- [ ] Master basic file operations (create, copy, move, delete)
- [ ] View and edit files with `cat`, `less`, `sed`, etc.
- [ ] Search files with `find`, `locate`, and `grep`
- [ ] Process text with `cut`, `sort`, `uniq`, `sed`, `awk`
- [ ] Compress and archive files with `tar`, `gzip`, `zip`
- [ ] Understand hard and symbolic links
- [ ] Manage file attributes and timestamps
- [ ] Complete all practice exercises

---

**Previous Module**: [Module 03: User and Group Management](../03-user-management/UserGroupManagement.md)  
**Next Module**: [Module 05: Vi/Vim Shortcuts](../05-vi-shortcuts/VimShortcuts.md)

[⬆ Back to Main README](../README.md)