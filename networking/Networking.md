# 🌐 Module 09: Linux Networking Fundamentals

Hello, Linux network ninjas! 🥷 After mastering system monitoring in Module 08, it’s time to conquer **Linux networking**—the backbone of connectivity in your systems! Whether you’re configuring IPs, securing SSH, or troubleshooting connectivity, this module will transform you into a networking pro. 🌐 Let’s dive into the world of packets, interfaces, and firewalls with confidence! 🚀

## 📋 Table of Contents
- [Network Basics](#network-basics)
- [Network Configuration](#network-configuration)
- [DNS Configuration](#dns-configuration)
- [Network Troubleshooting](#network-troubleshooting)
- [Firewall Management](#firewall-management)
- [SSH Configuration](#ssh-configuration)
- [Network Services](#network-services)
- [Advanced Networking](#advanced-networking)
- [Practice Exercises](#practice-exercises)
- [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)
- [Best Practices](#best-practices)
- [Additional Resources](#additional-resources)
- [Module Completion Checklist](#module-completion-checklist)

---

## 🔌 Network Basics

### Understanding Network Interfaces
Network interfaces connect your system to the world. Let’s explore them!

```bash
# List network interfaces
ip link show               # Modern command
ip addr show              # Show IP addresses
ip a                      # Short form
ifconfig                  # Legacy (use with caution)

# Common interface names:
# - eth0, eth1: Traditional Ethernet
# - ens33, enp0s3: Predictable names (modern)
# - wlan0, wlp3s0: Wireless
# - lo: Loopback (127.0.0.1)

# View specific interface
ip addr show eth0
# Output: Shows IP, MAC, and status
```

**Example**:
```bash
# Check interface details
ip link show eth0
# Output: 2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
#         link/ether 00:0c:29:xx:xx:xx brd ff:ff:ff:ff:ff:ff
```

### Interface Status
Manage and inspect interface states.

```bash
# Check status
ip link show eth0

# Status flags explained:
# - UP: Interface is enabled
# - LOWER_UP: Physical link is active
# - BROADCAST: Supports broadcast
# - MULTICAST: Supports multicast

# Enable/disable interface
sudo ip link set eth0 up
sudo ip link set eth0 down
```

**Example**:
```bash
# Restart interface
sudo ip link set eth0 down && sudo ip link set eth0 up
```

### IP Addressing
View and manage IP addresses.

```bash
# Show IPs
ip addr show
hostname -I               # All IPs
hostname -i               # Primary IP

# Filter by protocol
ip -4 addr show           # IPv4 only
ip -6 addr show           # IPv6 only

# Get external IP
curl ifconfig.me
curl icanhazip.com
dig +short myip.opendns.com @resolver1.opendns.com
```

**Example**:
```bash
# Verify external connectivity
curl ifconfig.me
# Output: Your public IP (e.g., 203.0.113.1)
```

### MAC Addresses
Manage hardware addresses for interfaces.

```bash
# View MAC
ip link show eth0 | grep link/ether
cat /sys/class/net/eth0/address

# Change MAC (temporary)
sudo ip link set dev eth0 down
sudo ip link set dev eth0 address 00:11:22:33:44:55
sudo ip link set dev eth0 up
```

**Pro Tip**: Use `ip` over `ifconfig` for modern systems, as `ifconfig` is deprecated in many distributions.

---

## ⚙️ Network Configuration

### Temporary IP Configuration
Make quick, non-persistent changes.

```bash
# Add IP
sudo ip addr add 192.168.1.100/24 dev eth0

# Remove IP
sudo ip addr del 192.168.1.100/24 dev eth0

# Set default gateway
sudo ip route add default via 192.168.1.1

# Add static route
sudo ip route add 10.0.0.0/8 via 192.168.1.1
```

**Example**:
```bash
# Add and verify IP
sudo ip addr add 192.168.1.100/24 dev eth0
ip addr show eth0
```

### Permanent Configuration - Ubuntu/Debian (Netplan)
Use Netplan for modern Ubuntu/Debian systems.

```bash
# Edit Netplan config
sudo vim /etc/netplan/01-netcfg.yaml

# Static IP example
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      addresses:
        - 192.168.1.100/24
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
      mtu: 1500

# Apply and test
sudo netplan apply
sudo netplan try  # Reverts if fails
```

### Permanent Configuration - RHEL/CentOS (NetworkManager)
Use NetworkManager for Red Hat-based systems.

```bash
# Edit interface config
sudo vim /etc/sysconfig/network-scripts/ifcfg-eth0

# Static IP example
TYPE=Ethernet
BOOTPROTO=none
NAME=eth0
DEVICE=eth0
ONBOOT=yes
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
DNS2=8.8.4.4

# Apply changes
sudo nmcli con reload
sudo nmcli con up eth0

# Verify
nmcli con show
nmcli device status
```

### DHCP Configuration
Automate IP assignment with DHCP.

```bash
# Request DHCP lease
sudo dhclient eth0
sudo dhclient -r eth0  # Release lease

# Netplan DHCP example
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: yes
sudo netplan apply
```

### NetworkManager CLI
Manage networks dynamically with `nmcli`.

```bash
# List connections
nmcli con show

# Add static IP
nmcli con add con-name my-eth0 type ethernet ifname eth0 ipv4.method manual ipv4.addresses 192.168.1.100/24 ipv4.gateway 192.168.1.1 ipv4.dns "8.8.8.8,8.8.4.4"

# Activate connection
nmcli con up my-eth0
```

**Example**:
```bash
# Create and activate connection
nmcli con add con-name home-net type ethernet ifname eth0 ipv4.method manual ipv4.addresses 192.168.1.200/24
nmcli con up home-net
```

**Pro Tip**: Use `nmcli con show --active` to verify active connections.

---

## 🌍 DNS Configuration

### Checking DNS
Verify and test DNS resolution.

```bash
# View DNS servers
cat /etc/resolv.conf
nmcli dev show | grep DNS

# DNS lookup
nslookup google.com
dig google.com
host google.com

# Test resolution
ping -c 4 google.com
dig +short google.com
```

**Example**:
```bash
# Quick DNS test
dig +short google.com
# Output: Lists Google’s IP addresses
```

### Configuring DNS
Set DNS servers for reliable name resolution.

```bash
# Temporary DNS change
sudo sh -c 'echo "nameserver 8.8.8.8" > /etc/resolv.conf'

# Permanent DNS (Netplan)
sudo vim /etc/netplan/01-netcfg.yaml
# Add:
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
sudo netplan apply

# Permanent DNS (RHEL/CentOS)
sudo vim /etc/sysconfig/network-scripts/ifcfg-eth0
# Add:
DNS1=8.8.8.8
DNS2=8.8.4.4
sudo nmcli con reload
```

### Local DNS Cache
Improve performance with a local cache.

```bash
# Install dnsmasq
sudo apt install dnsmasq
sudo systemctl enable dnsmasq
sudo systemctl start dnsmasq

# Add local hosts
sudo vim /etc/hosts
# Example:
192.168.1.10 server1.local
192.168.1.11 server2.local
```

**Example**:
```bash
# Test local DNS
ping server1.local
```

**Pro Tip**: Use `systemd-resolved` for modern DNS caching:
```bash
sudo systemctl status systemd-resolved
journalctl -u systemd-resolved -f
```

---

## 🔍 Network Troubleshooting

### Basic Connectivity
Diagnose network issues quickly.

```bash
# Test connectivity
ping -c 4 8.8.8.8
ping -c 4 google.com

# Trace route
traceroute google.com
mtr google.com  # Real-time traceroute
```

### Network Connections
Inspect active connections and ports.

```bash
# View listening ports
ss -tulpn
# Alternative: netstat -tulpn

# Check specific port
nc -zv localhost 80
telnet localhost 22
```

### Packet Analysis
Capture and analyze network traffic.

```bash
# Capture packets
sudo tcpdump -i eth0
sudo tcpdump -i eth0 port 80 -w capture.pcap

# Analyze with Wireshark
wireshark capture.pcap  # GUI tool
sudo tcpdump -r capture.pcap  # CLI
```

### Network Statistics
Monitor network performance.

```bash
# Interface stats
ip -s link show eth0
netstat -i

# Protocol stats
ss -s
netstat -s
```

**Example**:
```bash
# Debug HTTP connectivity
nc -zv server1.local 80
sudo tcpdump -i eth0 port 80
```

**Pro Tip**: Use `mtr` for continuous ping/traceroute to diagnose packet loss:
```bash
mtr --report google.com
```

---

## 🛡️ Firewall Management

### UFW (Uncomplicated Firewall - Ubuntu)
Simplify firewall management.

```bash
# Enable UFW
sudo ufw enable

# Allow ports
sudo ufw allow 22/tcp  # SSH
sudo ufw allow 80/tcp  # HTTP
sudo ufw allow 443/tcp  # HTTPS

# Deny port
sudo ufw deny 23/tcp  # Telnet

# Allow specific IP
sudo ufw allow from 192.168.1.100 to any port 22

# Check status
sudo ufw status numbered
# Output: Lists rules with numbers

# Delete rule
sudo ufw delete 2  # Delete rule #2
```

### Firewalld (RHEL/CentOS)
Dynamic firewall management.

```bash
# Start and enable
sudo systemctl start firewalld
sudo systemctl enable firewalld

# Add rules
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --permanent --add-service=ssh

# Reload
sudo firewall-cmd --reload

# List rules
sudo firewall-cmd --list-all
```

### IPTables (Advanced)
Low-level firewall control.

```bash
# View rules
sudo iptables -L -v -n --line-numbers

# Allow port
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Block IP
sudo iptables -A INPUT -s 192.168.1.200 -j DROP

# Save rules
sudo sh -c 'iptables-save > /etc/iptables/rules.v4'
```

**Example**:
```bash
# Secure web server with UFW
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
sudo ufw status
```

**Pro Tip**: Always test firewall rules and ensure SSH (port 22) is allowed to avoid lockout:
```bash
sudo ufw allow 22/tcp
```

---

## 🔐 SSH Configuration

### SSH Server Setup
Secure remote access with SSH.

```bash
# Install and enable
sudo apt install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh

# Check status
sudo systemctl status ssh
```

### SSH Configuration
Harden your SSH server.

```bash
# Edit config
sudo vim /etc/ssh/sshd_config

# Recommended settings:
Port 2222                 # Custom port
PermitRootLogin no        # Disable root login
AllowUsers alice bob      # Restrict users
PasswordAuthentication no # Require key-based auth
MaxAuthTries 3            # Limit login attempts

# Reload SSH
sudo systemctl reload sshd
```

### SSH Client
Connect and transfer files securely.

```bash
# Connect
ssh alice@192.168.1.100
ssh -p 2222 alice@server1.local

# Copy files
scp file.txt alice@192.168.1.100:/home/alice/
scp -r /data alice@server1.local:/backup/

# Key-based authentication
ssh-keygen -t rsa -b 4096
ssh-copy-id alice@192.168.1.100
```

**Example**:
```bash
# Set up key-based SSH
ssh-keygen -t rsa -b 4096 -C "alice@server1"
ssh-copy-id -i ~/.ssh/id_rsa.pub alice@server1.local
ssh alice@server1.local
```

**Pro Tip**: Use `~/.ssh/config` for shortcuts:
```bash
Host server1
    HostName 192.168.1.100
    User alice
    Port 2222
    IdentityFile ~/.ssh/id_rsa
# Connect: ssh server1
```

---

## 🌐 Network Services

### Common Network Services
Manage essential services.

```bash
# Web servers
sudo systemctl start nginx
sudo systemctl start apache2

# DNS server
sudo systemctl start named  # BIND
sudo systemctl start dnsmasq

# DHCP server
sudo systemctl start isc-dhcp-server

# FTP server
sudo systemctl start vsftpd
```

### Service Logs
Monitor service health.

```bash
# View logs
journalctl -u nginx -f
journalctl -u named -n 50
```

### Network Time Protocol (NTP)
Keep time in sync.

```bash
# Install and enable
sudo apt install chrony
sudo systemctl enable chronyd
sudo systemctl start chronyd

# Check status
chronyc tracking
chronyc sources
```

**Example**:
```bash
# Verify nginx
sudo systemctl start nginx
curl http://localhost
journalctl -u nginx -n 20
```

**Pro Tip**: Monitor NTP drift with `chronyc tracking` to ensure accurate time sync.

---

## 🔧 Advanced Networking

### Bonding Interfaces
Combine interfaces for redundancy or performance.

```bash
# Ubuntu (Netplan)
sudo vim /etc/netplan/01-netcfg.yaml
# Example:
network:
  version: 2
  renderer: networkd
  bonds:
    bond0:
      interfaces: [eth0, eth1]
      addresses: [192.168.1.100/24]
      gateway4: 192.168.1.1
      parameters:
        mode: 802.3ad
        mii-monitor-interval: 100
sudo netplan apply

# RHEL/CentOS
sudo vim /etc/sysconfig/network-scripts/ifcfg-bond0
# Example:
DEVICE=bond0
TYPE=Bond
BONDING_MASTER=yes
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
BONDING_OPTS="mode=4 miimon=100"
sudo nmcli con up bond0
```

### VLAN Configuration
Segment networks with VLANs.

```bash
# Install VLAN tools
sudo apt install vlan

# Add VLAN
sudo vconfig add eth0 10
sudo ip addr add 192.168.10.100/24 dev eth0.10
sudo ip link set eth0.10 up

# Permanent VLAN (Netplan)
sudo vim /etc/netplan/01-netcfg.yaml
# Add:
  vlans:
    vlan10:
      id: 10
      link: eth0
      addresses: [192.168.10.100/24]
sudo netplan apply
```

### Bridge Configuration
Create network bridges for VMs or containers.

```bash
# Create bridge
sudo brctl addbr br0
sudo brctl addif br0 eth0
sudo ip addr add 192.168.1.100/24 dev br0
sudo ip link set br0 up

# Permanent bridge (Netplan)
sudo vim /etc/netplan/01-netcfg.yaml
# Add:
  bridges:
    br0:
      interfaces: [eth0]
      addresses: [192.168.1.100/24]
      gateway4: 192.168.1.1
sudo netplan apply
```

**Example**:
```bash
# Verify bonding
cat /proc/net/bonding/bond0
```

**Pro Tip**: Use `mode=4` (802.3ad) for link aggregation in bonding to maximize throughput.

---

## 🛠️ Practice Exercises

### Exercise 1: Network Interfaces
1. List interfaces:
   ```bash
   ip link show
   ip addr show
   ```
2. Toggle interface:
   ```bash
   sudo ip link set eth0 down
   sudo ip link set eth0 up
   ip link show eth0
   ```

### Exercise 2: IP Configuration
1. Set temporary IP:
   ```bash
   sudo ip addr add 192.168.1.200/24 dev eth0
   ip addr show eth0
   ```
2. Configure permanent IP:
   ```bash
   sudo vim /etc/netplan/01-netcfg.yaml
   # Add static IP (see above)
   sudo netplan apply
   ```

### Exercise 3: DNS
1. Check DNS:
   ```bash
   cat /etc/resolv.conf
   dig google.com
   ```
2. Add Google DNS:
   ```bash
   sudo sh -c 'echo "nameserver 8.8.8.8" >> /etc/resolv.conf'
   ping -c 4 google.com
   ```

### Exercise 4: Firewall
1. Configure UFW:
   ```bash
   sudo ufw allow 22/tcp
   sudo ufw allow 80/tcp
   sudo ufw enable
   sudo ufw status
   ```
2. Test connectivity:
   ```bash
   nc -zv localhost 80
   ```

### Exercise 5: SSH
1. Harden SSH:
   ```bash
   sudo vim /etc/ssh/sshd_config
   # Set: PermitRootLogin no, Port 2222
   sudo systemctl reload sshd
   ```
2. Set up key-based auth:
   ```bash
   ssh-keygen -t rsa -b 4096
   ssh-copy-id -p 2222 alice@192.168.1.100
   ssh -p 2222 alice@192.168.1.100
   ```

---

## 📚 Quick Reference Cheat Sheet

```bash
# Interfaces
ip link show              # List interfaces
ip addr show              # IP addresses
ip link set eth0 up       # Enable interface

# IP Configuration
ip addr add 192.168.1.100/24 dev eth0  # Add IP
ip route add default via 192.168.1.1   # Set gateway
nmcli con show                        # NetworkManager

# DNS
cat /etc/resolv.conf      # View DNS
dig google.com            # DNS lookup
nslookup google.com       # Alternative lookup

# Troubleshooting
ping -c 4 8.8.8.8         # Test connectivity
mtr google.com            # Traceroute
ss -tulpn                 # Listening ports
tcpdump -i eth0 port 80   # Packet capture

# Firewall
ufw allow 22/tcp          # Allow SSH
firewall-cmd --add-port=80/tcp  # Firewalld
iptables -L -v -n         # View iptables

# SSH
ssh alice@192.168.1.100   # Connect
scp file.txt alice@server:/  # Copy file
ssh-keygen -t rsa -b 4096 # Generate key
```

---

## 🔐 Best Practices

1. **Use Modern Tools**: Prefer `ip` and `ss` over `ifconfig` and `netstat`.
   ```bash
   ip addr show
   ss -tulpn
   ```
2. **Secure SSH**: Disable root login and use key-based authentication.
   ```bash
   sudo vim /etc/ssh/sshd_config
   # Set: PermitRootLogin no, PasswordAuthentication no
   ```
3. **Firewall First**: Always allow SSH before enabling firewalls.
   ```bash
   sudo ufw allow 22/tcp
   ```
4. **Backup Configs**: Save network configs before changes.
   ```bash
   sudo cp /etc/netplan/01-netcfg.yaml /etc/netplan/01-netcfg.yaml.bak
   ```
5. **Monitor Services**: Check logs for service issues.
   ```bash
   journalctl -u nginx -f
   ```
6. **Test Changes**: Use `netplan try` or `nmcli con reload` to validate configs.

---

## 📖 Additional Resources

- **[Linux Networking Guide](https://tldp.org/HOWTO/Networking-Overview-HOWTO.html)**: TLDP networking overview.
- **[Red Hat Networking](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_networking)**: Enterprise guide.
- **[Ubuntu Netplan](https://netplan.io/)**: Official Netplan documentation.
- **[Linux Journey: Networking](https://linuxjourney.com/lesson/networking-basics)**: Interactive learning.
- **[Unix & Linux Stack Exchange](https://unix.stackexchange.com/)**: Community Q&A.

**Community Tip**: Join [Reddit r/linuxadmin](https://www.reddit.com/r/linuxadmin/) for networking tips! 🗣️

---

## ✅ Module Completion Checklist

- [ ] Understand network interfaces and IP addressing
- [ ] Configure temporary and permanent network settings
- [ ] Set up and test DNS resolution
- [ ] Troubleshoot connectivity with `ping`, `mtr`, `tcpdump`
- [ ] Manage firewall rules with UFW, Firewalld, or iptables
- [ ] Secure SSH with key-based authentication
- [ ] Manage network services like nginx and chrony
- [ ] Explore advanced configurations (bonding, VLANs)
- [ ] Complete all practice exercises

---

**Previous Module**: [Module 08: System Monitoring](../08-system-monitoring/SystemMonitoring.md)  
**Next Module**: [Module 10: Package Management](../10-package-management/PackageManagement.md)

[⬆ Back to Main README](../README.md)