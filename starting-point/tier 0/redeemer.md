# HTB: Redeemer — Writeup

**Difficulty:** Easy
**IP:** 10.129.74.1

---

## 1. Reconnaissance

### Initial Scan

Started with a SYN scan limited to the default top ports:

```bash
sudo nmap -sS -p 1-1000 -sV -v 10.129.74.1
```

This came back without finding the open service — the port in use falls outside the default 1–1000 range.

### Expanding the Port Range

Re-ran with a wider range to catch higher ports:

```bash
sudo nmap -sS -sV -p 1-10000 10.129.74.1
```

**Result:** found **port 6379/tcp open** — Redis.

> **Lesson learned:** nmap's default scan only checks ~1000 common ports. If a question asks generically "which TCP port is open" without naming a known service, don't assume it's in the default range — scan wider. Going forward, prefer:
>
> ```bash
> sudo nmap -sS -sV -p- --min-rate 5000 10.129.x.x
> ```
>
> `-p-` scans all 65535 ports; `--min-rate 5000` speeds it up for CTF-style targets.

### Flags Used

| Flag | Purpose |
|------|---------|
| `-sS` | SYN ("half-open") scan — sends SYN, reads SYN-ACK, sends RST without completing the handshake. Requires raw socket access, hence `sudo`. |
| `-sV` | Service/version detection on open ports |
| `-p` | Specify port range |
| `-v` | Verbose output |
| `-p-` | Scan all 65535 ports (use when port range is unknown) |
| `--min-rate` | Force minimum packet send rate to speed up large scans |

**Note:** when run with `sudo`, nmap defaults to a SYN scan anyway — `-sS` is mostly there for explicitness/clarity, not strictly required for privilege.

---

## 2. Service Enumeration — Redis (Port 6379)

Redis is an in-memory key-value data store. On this box it's exposed with **no authentication required** — a common and dangerous misconfiguration.

### Connecting

```bash
redis-cli -h 10.129.74.1
```

*(Install if missing: `sudo apt install redis-tools`)*

### Basic Enumeration Commands

```bash
INFO            # server info, Redis version, memory stats, uptime
KEYS *          # list all keys stored in the database
GET <keyname>   # retrieve the value of a specific key
```

---

## 3. Findings So Far

- **Open port:** `6379/tcp` (Redis)
- **Auth:** none required
- *(continue documenting: Redis version from `INFO`, keys found, credentials/flags retrieved, and path to user/root flag)*

---

## 4. Next Steps / To-Do

- [ ] Run `INFO` to confirm Redis version
- [ ] Run `KEYS *` to enumerate stored keys
- [ ] Retrieve credentials or flag values via `GET`
- [ ] Determine if Redis can be leveraged for further access (e.g., writing SSH keys via Redis if filesystem write access is possible)
- [ ] Document privilege escalation path to root

---

## Notes / Lessons Learned

- Default nmap scans miss ports outside the top ~1000 — always widen the range (`-p-`) when unsure.
- `sudo` + SYN scan is the default combo for full TCP scans; `-sS` is explicit but redundant under `sudo`.
- Exposed Redis instances without auth are a common easy-box foothold on HTB.