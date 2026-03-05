# Lab: Windows Server 2022 Domain Controller Setup

## Goal

Deploy a Windows Server 2022 virtual machine and configure it as a Domain Controller for an Active Directory lab environment used for technical support practice.

---

# Environment

| Component         | Details                    |
| ----------------- | -------------------------- |
| Hypervisor        | VirtualBox                 |
| Server VM         | Windows Server 2022 (DC01) |
| Client VM         | Windows 10/11 (CLIENT01)   |
| Network Type      | NAT + Host-Only            |
| Host-Only Network | 192.168.56.0/24            |

---

# Network Configuration

| System   | Role              | IP Address    | Adapter   |
| -------- | ----------------- | ------------- | --------- |
| DC01     | Domain Controller | 192.168.56.10 | Host-Only |
| CLIENT01 | Domain Client     | 192.168.56.20 | Host-Only |

Adapter configuration:

Adapter 1 → NAT (Internet Access)
Adapter 2 → Host-Only Adapter (Lab Network)

---

# Setup Steps

### 1. Create Virtual Machines

Created two virtual machines in VirtualBox:

• DC01 (Windows Server 2022)
• CLIENT01 (Windows 10)

Both machines were connected to:

```
Adapter 1 → NAT
Adapter 2 → Host-Only (vboxnet0)
```

---

### 2. Configure Static IP on Domain Controller

On **DC01**:

```
IP Address: 192.168.56.10
Subnet Mask: 255.255.255.0
DNS Server: 192.168.56.10
```

---

### 3. Configure Client Networking

On **CLIENT01**:

```
IP Address: 192.168.56.20
Subnet Mask: 255.255.255.0
DNS Server: 192.168.56.10
```

---

# Issue Encountered — VM Connectivity (Ping Failure)

## Description

During initial connectivity testing, **DC01 and CLIENT01 were unable to communicate using ping** even though both systems were configured on the same Host-Only network (`192.168.56.0/24`).

Both machines had valid static IP addresses and were connected to the same VirtualBox Host-Only adapter (`vboxnet0`). However, ping requests resulted in:

```
Request timed out
```

---

# Investigation

The following troubleshooting steps were performed:

### Step 1 — Verify Network Configuration

Checked IP configuration on both machines.

Command used:

```
ipconfig
```

Both systems displayed correct IP addresses.

---

### Step 2 — Verify Network Adapter Status

Command used:

```
ipconfig /all
```

Confirmed both VMs were connected to **Host-Only adapter vboxnet0**.

---

### Step 3 — Test Connectivity

Attempted ping tests.

```
ping 192.168.56.10
ping 192.168.56.20
```

Result:

```
Request timed out
```

---

### Step 4 — Firewall Testing

Temporarily disabled Windows Defender Firewall.

Command used:

```
netsh advfirewall set allprofiles state off
```

After disabling the firewall, ping tests succeeded.

---

# Root Cause

Windows Defender Firewall was **blocking ICMP Echo Requests (ping)**.

By default, Windows blocks incoming ICMP traffic to prevent unauthorized network discovery.

---

# Resolution

Instead of leaving the firewall disabled, the proper solution was to **enable the inbound ICMP firewall rule**.

Steps performed:

1. Open **Windows Defender Firewall with Advanced Security**
2. Navigate to **Inbound Rules**
3. Locate the rule:

```
File and Printer Sharing (Echo Request - ICMPv4-In)
```

4. Enable the rule.

---

# Verification

Connectivity tests were repeated.

Command:

```
ping 192.168.56.10
```

Result:

```
Reply from 192.168.56.10: bytes=32 time<1ms TTL=128
```

Both **DC01 and CLIENT01** were able to communicate successfully.

---

# Lessons Learned

• Windows Firewall can block diagnostic tools such as ping
• Firewall rules should be adjusted instead of disabling security
• Connectivity testing should always be performed before configuring services such as Active Directory

---

# Evidence

Screenshots stored in:

```
evidence/screenshots/
```

Example screenshots:

• VM network configuration
• Ping failure output
• Firewall rule configuration
• Successful ping response
