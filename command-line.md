# Command Line - TryHackMe

## Date Completed: February 10, 2026

## Module Overview
This module covered essential Windows command-line interface (CLI) operations, focusing on system information gathering, network utilities, file system navigation, and process monitoring - all critical skills for cybersecurity professionals.

## Learning Objectives
- Master Windows Command Prompt (cmd.exe) operations
- Gather system and network information using built-in tools
- Navigate and manage the Windows file system via CLI
- Monitor running processes and network connections
- Apply command-line skills to security operations and incident response

---

## Commands Learned & Mastered

### System Information Commands

#### `systeminfo`
Displays detailed configuration information about the computer and its operating system.
```cmd
systeminfo
```

**Output includes:**
- OS Name and Version
- System Manufacturer and Model
- Processor information
- BIOS Version
- Windows Directory
- Total/Available Physical Memory
- Installed Hotfixes (patches)
- Network Adapters

**Security relevance:**
- Identify OS version for vulnerability research
- Check patch level (missing updates = vulnerabilities)
- Gather system info during reconnaissance phase
- Document system configuration during incident response

**Example use case:** When investigating a compromised system, `systeminfo` quickly tells you what OS version and patches are installed, helping identify potential exploit vectors.

---

#### `ver`
Displays the Windows version number.
```cmd
ver
```

**Output example:**
```
Microsoft Windows [Version 10.0.19045.3803]
```

**Why it matters:**
- Quick version check without full systeminfo output
- Useful in scripts to determine OS version
- Helps identify end-of-life systems (security risk)

---

#### `set`
Displays, sets, or removes environment variables.
```cmd
set                    # Display all environment variables
set PATH               # Display specific variable
set VARIABLE=value     # Set new variable
```

**Key environment variables:**
- `PATH` - Directories searched for executable files
- `USERNAME` - Current user
- `COMPUTERNAME` - System name
- `USERPROFILE` - User's home directory
- `TEMP` / `TMP` - Temporary file locations

**Security relevance:**
- Attackers check PATH for writable directories (privilege escalation)
- Environment variables may contain sensitive info
- `TEMP` directories often used for malware staging
- Understanding user context is critical during investigations

---

### Network Utilities

#### `ipconfig`
Displays TCP/IP network configuration.
```cmd
ipconfig               # Basic IP info
ipconfig /all          # Detailed configuration
ipconfig /release      # Release DHCP lease
ipconfig /renew        # Renew DHCP lease
ipconfig /flushdns     # Clear DNS cache
ipconfig /displaydns   # Show DNS cache
```

**Key information shown:**
- IP Address
- Subnet Mask
- Default Gateway
- DNS Servers
- DHCP Status
- MAC Address (Physical Address)

**Security applications:**
- Identify network configuration during reconnaissance
- Check DNS servers (could be malicious)
- Verify proper network settings during incident response
- MAC address for device identification

**Example:** During an incident, `ipconfig /all` helps determine if a system is on the expected network or if DNS has been hijacked to a malicious server.

---

#### `ping`
Tests network connectivity to another host.
```cmd
ping [IP/hostname]
ping -t [IP]           # Continuous ping
ping -n 10 [IP]        # Send 10 packets
ping -a [IP]           # Resolve hostname
```

**What it does:**
- Sends ICMP Echo Request packets
- Measures round-trip time (latency)
- Shows packet loss percentage
- Verifies network connectivity

**Security uses:**
- Verify target is reachable before scanning
- Check if firewall blocks ICMP
- Test network connectivity during troubleshooting
- Simple availability check

**From Pre Security:** This directly applies ICMP protocol knowledge from the networking modules!

---

#### `tracert`
Traces the route packets take to reach a destination.
```cmd
tracert [IP/hostname]
tracert -h 15 [IP]     # Limit hops to 15
```

**What it shows:**
- Each router (hop) along the path
- Round-trip time for each hop
- Where packets are being delayed or dropped

**Security applications:**
- Identify network path and potential monitoring points
- Detect routing anomalies
- Troubleshoot connectivity issues
- Map network topology during reconnaissance

**Example:** If connections to a server are slow, `tracert` reveals which network segment is causing delays.

---

#### `nslookup`
Queries DNS servers to obtain domain name or IP address mapping.
```cmd
nslookup [domain]
nslookup [IP]          # Reverse DNS lookup
nslookup [domain] [DNS-server]  # Query specific DNS server
```

**Interactive mode:**
```cmd
nslookup
> server 8.8.8.8       # Use Google's DNS
> google.com           # Query domain
> set type=mx          # Query mail servers
> exit
```

**Security relevance:**
- Verify DNS resolution is correct (detect DNS poisoning)
- Identify mail servers for reconnaissance
- Check if domain resolves to expected IP
- DNS information gathering during OSINT

**Example:** `nslookup suspicious-domain.com` helps verify if a phishing email's domain resolves to a legitimate mail server or a malicious host.

---

#### `netstat`
Displays active network connections and listening ports.
```cmd
netstat                # Show active connections
netstat -a             # All connections and listening ports
netstat -n             # Numerical addresses (no DNS resolution)
netstat -o             # Show owning process ID (PID)
netstat -ano           # All connections with PID (most useful!)
netstat -b             # Show executable name (requires admin)
```

**What it reveals:**
- Local and remote IP addresses
- Port numbers
- Connection state (ESTABLISHED, LISTENING, TIME_WAIT)
- Process ID responsible for each connection

**CRITICAL for SOC analysts:**
```cmd
netstat -ano
```

**Example output:**
```
Proto  Local Address      Foreign Address    State       PID
TCP    192.168.1.10:445   0.0.0.0:0          LISTENING   4
TCP    192.168.1.10:3389  203.0.113.50:52341 ESTABLISHED 1234
```

**Security applications:**
- Detect suspicious outbound connections (C2 communication)
- Identify which process opened a connection
- Check for unexpected listening ports (backdoors)
- Verify legitimate services are running

**Real scenario:** If malware is beaconing to a command-and-control server, `netstat -ano` reveals the suspicious connection and the PID of the malicious process.

---

### File System Commands

#### `cd`
Change directory.
```cmd
cd [directory]         # Change to specified directory
cd ..                  # Go up one level
cd \                   # Go to root of current drive
cd %userprofile%       # Go to user's home directory
```

**Common paths:**
```cmd
cd C:\Windows\System32
cd %appdata%           # Application data folder
cd %temp%              # Temporary files
```

**Security relevance:**
- Navigate to common malware locations (%temp%, %appdata%)
- Access system directories during investigation
- Check startup folders for persistence

---

#### `tree`
Displays directory structure in a tree format.
```cmd
tree                   # Show tree from current directory
tree /F                # Include files
tree /A                # Use ASCII characters
tree C:\Path /F        # Specific directory with files
```

**Example output:**
```
C:\USERS\ADMIN
├───Desktop
├───Documents
│   └───Projects
├───Downloads
└───Pictures
```

**Security uses:**
- Visualize directory structure quickly
- Identify unusual folder hierarchies
- Document file system layout during investigation
- Spot hidden directories

---

#### `mkdir` / `md`
Create new directory.
```cmd
mkdir [directory-name]
mkdir "Folder Name With Spaces"
md C:\Path\NewFolder
```

**Security application:**
- Create directories for investigation files
- Organize evidence during incident response
- Set up analysis workspace

---

#### `rmdir` / `rd`
Remove directory.
```cmd
rmdir [directory]
rmdir /S [directory]   # Remove directory and all contents
rmdir /Q [directory]   # Quiet mode (no confirmation)
```

**Use carefully!** `/S` deletes everything inside.

---

### Process Monitoring

#### `tasklist`
Lists all running processes.
```cmd
tasklist               # Show all processes
tasklist /V            # Verbose (includes window titles)
tasklist /FI "STATUS eq RUNNING"  # Filter running processes
tasklist /FI "PID eq 1234"        # Filter by specific PID
```

**Key information shown:**
- Image Name (process name)
- PID (Process ID)
- Session Name
- Session Number
- Memory Usage

**Security applications:**
- Identify suspicious processes
- Check for malware process names
- Correlate with netstat output (PID matching)
- Monitor resource-heavy processes

**Example for investigation:**
```cmd
netstat -ano | findstr "ESTABLISHED"
# See PID 5678 connected to suspicious IP
tasklist | findstr "5678"
# Identify which process (e.g., malware.exe)
```

**Common malware indicators:**
- Misspelled legitimate process names (svchost.exe vs svch0st.exe)
- Unusual processes running from %temp% or %appdata%
- High memory usage from unknown processes
- Processes with random names (asdf1234.exe)

---

## What I Learned

### Technical Skills Developed

**1. System Enumeration:**
- Quickly gather system information without GUI
- Identify OS version, patches, and configuration
- Check environment variables for sensitive data

**2. Network Analysis:**
- Diagnose connectivity issues
- Map network paths with tracert
- Verify DNS resolution
- Monitor active connections in real-time

**3. Process Investigation:**
- List all running processes
- Match processes to network connections
- Identify resource usage

**4. File System Navigation:**
- Navigate directories efficiently via keyboard
- Visualize folder structures
- Create investigation workspaces

### Security Perspective

**How attackers use these commands:**
1. **Reconnaissance:** `systeminfo`, `ipconfig`, `set` to understand target environment
2. **Network mapping:** `ping`, `tracert`, `nslookup` to map internal networks
3. **Persistence check:** `netstat` to see if security tools are monitoring them
4. **Process hiding:** `tasklist` to identify security products to evade

**How defenders (SOC analysts) use these commands:**
1. **Incident response:** Quick system assessment without GUI
2. **Threat hunting:** `netstat -ano` and `tasklist` to find malicious connections/processes
3. **Forensics:** Document system state during investigation
4. **Troubleshooting:** Diagnose network/system issues rapidly

**Key insight:** The exact same commands serve both offensive and defensive purposes. Intent and authorization make the difference.

---

## Practical Applications

### Scenario 1: Investigating Suspicious Network Activity

**Alert:** Unusual outbound connection detected

**Investigation steps:**
```cmd
# 1. Check all active connections
netstat -ano

# 2. Identify suspicious connection (e.g., to unknown IP)
# Note the PID (e.g., 2468)

# 3. Find which process is responsible
tasklist | findstr "2468"

# 4. Gather system context
systeminfo > system_info.txt

# 5. Check DNS for suspicious domains
nslookup [suspicious-IP]
```

---

### Scenario 2: System Compromise Assessment

**Task:** Determine if system is compromised

**Enumeration commands:**
```cmd
# System information
systeminfo
ver
set

# Network configuration
ipconfig /all
ipconfig /displaydns

# Active connections (look for C2 communication)
netstat -ano

# Running processes (look for malware)
tasklist /V

# Directory exploration (check common malware locations)
cd %temp%
tree /F
cd %appdata%
tree /F
```

---

### Scenario 3: Network Troubleshooting

**Issue:** Cannot reach internal server

**Diagnostic steps:**
```cmd
# 1. Check own IP configuration
ipconfig /all

# 2. Test connectivity
ping [server-IP]

# 3. Trace route to identify where packets drop
tracert [server-IP]

# 4. Verify DNS resolution
nslookup [server-hostname]

# 5. Check if anything is listening on expected port
netstat -ano | findstr "[port-number]"
```

---

## Connection to Previous Learning

### From Pre Security - Networking:
- **ICMP protocol:** `ping` uses ICMP Echo Request/Reply (learned in Pre Security)
- **DNS:** `nslookup` applies DNS concepts from networking modules
- **TCP/IP:** `netstat` shows TCP connections and ports in action
- **IP addressing:** `ipconfig` displays IP configuration knowledge applied practically

### From Windows Fundamentals:
- **File system:** `cd`, `tree`, `mkdir`, `rmdir` navigate Windows directory structure
- **Processes:** `tasklist` shows Windows processes in action
- **Environment:** `set` reveals Windows environment variables

### From Active Directory:
- Commands like `set` show domain information
- `systeminfo` reveals if system is domain-joined
- Network commands help investigate AD authentication issues

---

## Challenges & Solutions

### Challenge 1: Remembering command syntax
**Solution:** Created a cheat sheet with most-used commands and their flags

### Challenge 2: Understanding netstat output
**Solution:** Practiced interpreting connection states (ESTABLISHED, LISTENING, TIME_WAIT)

### Challenge 3: Matching PIDs between netstat and tasklist
**Solution:** 
```cmd
# Find process for specific connection
netstat -ano | findstr "suspicious-IP"
# Get PID from output
tasklist | findstr "[PID]"
```

---

## Commands Cheat Sheet

### Quick Reference - Most Important for Security:
```cmd
# System Information
systeminfo              # Full system details
ver                     # Windows version
set                     # Environment variables

# Network Diagnostics
ipconfig /all           # Detailed network config
ping [target]           # Connectivity test
tracert [target]        # Route tracing
nslookup [domain]       # DNS lookup

# Security Monitoring
netstat -ano            # All connections with PIDs
tasklist                # Running processes

# File System
cd [path]               # Navigate
tree /F                 # Visualize structure
mkdir [name]            # Create directory
```

---

## Next Steps

### Immediate Practice:
1. Use these commands daily instead of GUI when possible
2. Practice investigating mock incidents
3. Create scripts combining multiple commands

### Security-Specific Learning:
1. Learn PowerShell (more powerful than cmd)
2. Study common attack commands for detection
3. Practice correlating netstat + tasklist outputs
4. Learn Windows Event Log analysis (next level)

### Career Development:
1. Build personal command reference guide
2. Practice rapid system enumeration
3. Simulate incident response scenarios
4. Document investigation workflows

---

## SOC Analyst Relevance

### Why These Commands Matter:

**Daily SOC Operations:**
- Quick system checks during alert triage
- Network connection monitoring for C2 detection
- Process analysis for malware identification
- Incident documentation and evidence collection

**Incident Response:**
When responding to security incidents, I'll use:
```cmd
systeminfo              # Document system state
ipconfig /all           # Network configuration
netstat -ano            # Active connections
tasklist                # Running processes
nslookup [IOC]          # Investigate indicators
```

**Threat Hunting:**
Proactively searching for threats using:
- `netstat -ano` to find unusual connections
- `tasklist` to identify suspicious processes
- `nslookup` to investigate suspicious domains
- `tracert` to map adversary infrastructure

---

## Real-World Impact

### Why Command Line > GUI for Security:

**1. Speed:**
- `netstat -ano` faster than opening Resource Monitor
- `systeminfo` faster than clicking through System Properties

**2. Remote Access:**
- SSH/RDP sessions often CLI-only
- Some systems don't have GUI (servers)

**3. Automation:**
- Commands can be scripted
- Batch processing for multiple systems

**4. Precision:**
- Exact control over what you're checking
- Can filter and parse output programmatically

**5. Documentation:**
- Command output easily saved to files
- Reproducible investigations

---

## Reflection

This module fundamentally changed how I interact with Windows systems. Before, I relied on GUI tools for everything. Now I understand that command-line proficiency is essential for cybersecurity work.

The most valuable realization: these aren't "hacking tools" - they're built into every Windows system. Both attackers and defenders use them. What matters is knowing how to use them effectively and recognizing their use by others.

For my SOC analyst career path, mastering these commands is foundational. Every investigation starts with rapid enumeration using `systeminfo`, `netstat`, and `tasklist`. Understanding output from these tools will be part of my daily workflow.

The combination of system information gathering, network analysis, and process monitoring gives me a complete picture of a Windows system's state - exactly what's needed for security operations.

**Key takeaway:** GUI is convenient, CLI is powerful. Security professionals need both, but live in the terminal.

---

## Resources

- Windows Command-Line Reference: `help [command]`
- Command syntax help: `[command] /?`
- TryHackMe Command Line Room

---

## Commands Summary Table

| Command | Purpose | Key Flag | Security Use |
|---------|---------|----------|--------------|
| systeminfo | System details | - | Identify OS/patches |
| ver | Windows version | - | Quick version check |
| set | Environment variables | - | Check PATH, find sensitive data |
| ipconfig | Network config | /all | Full network details |
| ping | Test connectivity | -t | Verify host is up |
| tracert | Trace route | - | Map network path |
| nslookup | DNS lookup | - | Verify DNS, find mail servers |
| netstat | Network connections | -ano | Find suspicious connections + PIDs |
| tasklist | Running processes | /V | Identify malware processes |
| cd | Change directory | - | Navigate file system |
| tree | Directory structure | /F | Visualize folders/files |
| mkdir | Create directory | - | Organize investigation files |
| rmdir | Remove directory | /S | Clean up (careful!) |

**Most critical for security:** `netstat -ano`, `tasklist`, `systeminfo`, `ipconfig /all`
