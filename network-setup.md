# Network Setup Documentation

## Date: February 5, 2026

## Objective
Configure an isolated lab network to practice networking concepts from TryHackMe Pre Security path.

## Pre Security Concepts Applied
- **Network Isolation:** Using host-only adapter prevents lab traffic from reaching home network
- **Private IP Addressing:** Using 192.168.56.0/24 range (Class C private network)
- **Subnet Understanding:** All machines on same /24 subnet can communicate
- **OSI Model:** Demonstrated Layer 3 (IP) and ICMP protocol

## Network Configuration

### Host-only Network Details
- Network Name:VirtualBox Host-Only Ethernet Adapter
- Network Address: 192.168.56.0/24
- Gateway: 192.168.56.1

### Virtual Machine IP Assignments

#### Kali Linux
- IP Address:192.168.56.101
- Netmask: 255.255.255.0
- Purpose: Network analysis workstation

Command Used
```bash
ip addr
```

Screenshot:

<img width="915" height="314" alt="Screenshot 2026-02-05 204132" src="https://github.com/user-attachments/assets/a70c95b2-90c8-4c53-9ac4-4736ad83b915" />


---

#### Ubuntu Server
- IP Address: 192.168.56.102
- Netmask: 255.255.255.0
- Purpose: Linux practice target

Command Used:
```bash
ip addr
```

Screenshot:
<img width="729" height="365" alt="Screenshot 2026-02-05 210712" src="https://github.com/user-attachments/assets/fd0bcf1f-325f-41f0-bfe1-606685243394" />

---

#### Metasploitable 2
- IP Address: 192.168.56.103
- Netmask: 255.255.255.0
- Purpose:Vulnerable practice target
- Default Login:msfadmin / msfadmin

Command Used:
```bash
ifconfig
```

Screenshot: <img width="717" height="400" alt="Screenshot 2026-02-05 205811" src="https://github.com/user-attachments/assets/a8bdf0f9-6ee2-4884-aa64-ef9c646e2df1" />



---

## Connectivity Testing

### Testing Network Layer (ICMP - Ping)

From Kali Linux, tested connectivity to both targets:

Ping Ubuntu:
bash
ping -c 4 192.168.56.102


Results:
- Packets sent: 4
- Packets received: 4
- Packet loss: 0%

Screenshot: 
<img width="623" height="191" alt="Screenshot 2026-02-05 204121" src="https://github.com/user-attachments/assets/c3eaca86-11c6-4a52-9e45-55a5775319a5" />

---

Ping Metasploitable:
```bash
ping -c 4 
```

Results:
- Packets sent: 4
- Packets received: 4
- Packet loss: 0%

Screenshot:
<img width="688" height="196" alt="Screenshot 2026-02-05 204127" src="https://github.com/user-attachments/assets/be4e8023-0149-472f-adbb-f933007d372e" />


## Network Diagram
```
┌─────────────────────────────────────────────┐
│    VirtualBox Host-only Network (vboxnet0)  │
│            192.168.56.0/24                  │
└─────────────────────────────────────────────┘
                    │
    ┌───────────────┼───────────────┐
    │               │               │
    ▼               ▼               ▼
┌─────────┐   ┌──────────┐   ┌──────────────┐
│  Kali   │   │  Ubuntu  │   │Metasploitable│
│ [K-IP]  │   │ [U-IP]   │   │   [M-IP]     │
└─────────┘   └──────────┘   └──────────────┘
```
## What I Learned

### Networking Concepts Applied
1. Private IP Ranges:Used 192.168.x.x which is reserved for private networks (RFC 1918)
2. Subnet Masking:/24 means first 24 bits are network, last 8 bits are host (256 addresses)
3. Network Isolation: Host-only ensures no internet access, only inter-VM communication
4. ICMP Protocol: Ping uses ICMP (Internet Control Message Protocol) at Layer 3
5. ARP in Action: VMs used ARP to map IP addresses to MAC addresses

### Linux Commands Used
- ip addr - Modern way to view IP configuration (Kali, Ubuntu)
- ifconfig - Traditional IP configuration tool (Metasploitable - older system)
- ping- Test network connectivity using ICMP

### Skills from Pre Security
- Applied network fundamentals: IP addressing, subnetting
- Used OSI model knowledge: Layer 3 (Network), ICMP protocol
- Linux command line proficiency
- Network troubleshooting and verification

## Next Steps
With the network properly configured, I can now move to Phase 2:
1. Perform port scanning to discover services
2. Identify protocols running on each machine
3. Practice service enumeration
4. Apply web fundamentals to analyze HTTP services
