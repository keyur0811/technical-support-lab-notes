# Lab 08.01 — Virtualization Setup (DC01 + CLIENT01)

## Goal

Build a virtual lab environment using VirtualBox to simulate a corporate network.
This lab prepares the environment for practicing Active Directory, DNS, login troubleshooting, and remote support scenarios.

---

# Environment

| Component         | Details                    |
| ----------------- | -------------------------- |
| Host System       | Personal Laptop            |
| Hypervisor        | Oracle VirtualBox          |
| Extension Pack    | Installed                  |
| Server VM         | Windows Server 2022 (DC01) |
| Client VM         | Windows 10/11 (CLIENT01)   |
| Network Design    | NAT + Host-Only            |
| Host-Only Network | 192.168.56.1/24            |

---

# Network Design

Two network adapters were configured for each VM.

### Adapter 1 — NAT

Purpose:

* Provides internet access to the virtual machines
* Allows updates and software downloads

### Adapter 2 — Host-Only

Purpose:

* Creates a private internal network
* Allows DC01 and CLIENT01 to communicate like a corporate LAN

Host-only network configuration:

```
Network: 192.168.56.1/24
```

---

# Virtual Machines

## DC01 (Domain Controller Server)

| Setting | Value               |
| ------- | ------------------- |
| OS      | Windows Server 2022 |
| RAM     | 8 GB                |
| CPU     | 2–4 cores           |
| Disk    | 60–80 GB            |
| Network | NAT + Host-Only     |

---

## CLIENT01 (Domain Client)

| Setting | Value                   |
| ------- | ----------------------- |
| OS      | Windows 10 / Windows 11 |
| RAM     | 4–8 GB                  |
| CPU     | 2 cores                 |
| Disk    | 50–80 GB                |
| Network | NAT + Host-Only         |

---

# IP Configuration

Static IP addresses were configured on the Host-Only network.

| System   | Role        | IP Address    | Subnet Mask   | DNS           |
| -------- | ----------- | ------------- | ------------- | ------------- |
| DC01     | Server      | 192.168.56.10 | 255.255.255.0 | 127.0.0.1     |
| CLIENT01 | Workstation | 192.168.56.20 | 255.255.255.0 | 192.168.56.10 |

This DNS configuration allows the client to locate the domain controller once Active Directory is installed.

---

# Connectivity Testing

Connectivity between the virtual machines was tested using the `ping` command.

### Test from CLIENT01

```
ping 192.168.56.10
```

### Test from DC01

```
ping 192.168.56.20
```

Successful responses confirmed that both virtual machines were communicating correctly on the Host-Only network.

Example output:

```
Reply from 192.168.56.10: bytes=32 time<1ms TTL=128
```

---

# Issue Encountered — Ping Failure

## Description

During the initial connectivity test, ping requests between DC01 and CLIENT01 failed with the following result:

```
Request timed out
```

Both virtual machines were connected to the same Host-Only network and had correct static IP addresses.

---

# Investigation

The following troubleshooting steps were performed:

1. Verified IP configuration using:

```
ipconfig
```

2. Confirmed both machines were connected to the same VirtualBox Host-Only adapter (`vboxnet0`).

3. Re-tested connectivity using:

```
ping 192.168.56.10
ping 192.168.56.20
```

4. Temporarily disabled Windows Defender Firewall for testing:

```
netsh advfirewall set allprofiles state off
```

After disabling the firewall, ping requests succeeded.

---

# Root Cause

Windows Defender Firewall was blocking **ICMP Echo Requests (ping)** by default.

This security feature prevents unauthorized network scanning and device discovery.

---

# Resolution

Instead of leaving the firewall disabled, the correct solution was to enable the inbound ICMP rule.

Steps performed:

1. Open **Windows Defender Firewall with Advanced Security**
2. Navigate to **Inbound Rules**
3. Locate the rule:

```
File and Printer Sharing (Echo Request - ICMPv4-In)
```

4. Enable the rule.

This allows ping traffic while keeping the firewall enabled.

---

# Verification

Connectivity tests were repeated after enabling the ICMP rule.

```
ping 192.168.56.10
```

Result:

```
Reply from 192.168.56.10: bytes=32 time<1ms TTL=128
```

Both virtual machines were able to communicate successfully.

---

# Evidence

Screenshots stored in:

```
evidence/screenshots/
```

Examples:

* VirtualBox Host-Only network configuration
* DC01 system information
* CLIENT01 system information
* Ping failure output
* Firewall rule configuration
* Successful ping results

---

# Skills Demonstrated

* Virtual machine deployment
* Virtual network configuration
* Static IP configuration
* Network troubleshooting
* Firewall rule investigation
* Command-line diagnostics

---

# Next Lab

Next step in the lab environment:

```
08-Virtualization-VMs/02-domain-join-client
```

In the next lab:

* Install **Active Directory Domain Services**
* Promote DC01 to **Domain Controller**
* Create a domain (example: `supportlab.local`)
* Join CLIENT01 to the domain
