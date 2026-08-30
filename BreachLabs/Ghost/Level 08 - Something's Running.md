# BreachLabs: Ghost Track Level 8 - Something's Running

**Challenge:** Level 8 → 9  
**Date Completed:** August 30, 2026  
**Platform:** [BreachLabs](https://breachlab.org/)

---

## Summary

Something's Running introduces process forensics and the `/proc` filesystem. Although KAEL cleaned files and removed command-line arguments, running processes leave traces in the `/proc` virtual filesystem. The challenge requires examining process information through `/proc` to discover environment variables containing hidden credentials.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost9` user  
**Context:** Running processes contain accessible information in `/proc` filesystem  
**Constraint:** Command-line arguments cleaned; credentials stored in environment variables

---

## Initial Access

### SSH Connection

Connected using credentials obtained from Level 7:
```
Target: 204.168.229.209:2222
User: ghost8
Access Method: SSH
```

### Challenge Introduction

Challenge narrative stated:
- KAEL cleaned files and wiped logs
- Removed arguments from processes
- `/proc` remembers what shell forgets

---

## Process Enumeration

### Process List Analysis

Executed `ps -aux` to list all running processes:

**Key Processes Identified:**
- Multiple `python3` processes running various services
- `level8-daemon.py` running under both root and ghost8 (PIDs 39, 40)
- Multiple shell scripts and listeners

**Finding:** Process listing revealed `level8-daemon.py` running on PIDs 39 and 40.

---

## Binary Investigation

### Directory Enumeration

Navigated to `/usr/local/bin/` to examine executables:

```bash
ls -alps /usr/local/bin/
```

**Key File:**
- `level8-daemon.py` (152 bytes, readable by all users)

### Source Code Analysis

Examined `level8-daemon.py`:

```python
#!/usr/bin/env python3
# Ghost Protocol — background monitor service
import time
if __name__ == '__main__':
    while True:
        time.sleep(3600)
```

**Finding:** Source code showed simple daemon with no visible credentials or suspicious operations. Actual secrets must be elsewhere.

---

## /proc Filesystem Forensics

### Process Information Location

Examined `/proc/[PID]/` directories for running processes. For PID 39 (ghost8 daemon):

```bash
cd /proc/39
ls -alps
```

**Directory Contents:** Standard process information files including `environ`.

### Cmdline Examination

Checked command-line arguments for PID 40:

```bash
cat /proc/40/cmdline
```

**Output:**
```
python3/usr/local/bin/level8-daemon.py
```

**Finding:** Command-line contained only binary and script name; no arguments passed.

### Environment Variable Discovery

Examined `/proc/40/environ` file:

```bash
cat /proc/40/environ
```

**Output (formatted for readability):**
```
HOSTNAME=breachlab
PWD=/
HOME=/root
SHLVL=1
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
DEBIAN_FRONTEND=noninteractive
_=/usr/bin/env
ANALYST_KEY=[redacted]
USER=ghost8
LOGNAME=ghost8
```

**Key Finding:** `ANALYST_KEY` environment variable contained flag-formatted credential: `[redacted]`

---

## Credential Validation

### Password Confirmation

Tested extracted credential against ghost9 login. Successfully authenticated with password obtained from `/proc/[PID]/environ` file.

---

## Attack Chain Summary

```
SSH Connection (ghost8)
         ↓
Process Enumeration (ps -aux)
         ↓
Process Identification (level8-daemon.py PIDs 39, 40)
         ↓
Binary Location (/usr/local/bin/)
         ↓
Source Code Analysis (no visible secrets)
         ↓
/proc Filesystem Navigation (/proc/[PID]/)
         ↓
Cmdline Examination (no arguments)
         ↓
Environ File Access (/proc/[PID]/environ)
         ↓
Environment Variable Discovery (ANALYST_KEY)
         ↓
Credential Extraction and Validation
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **Process Enumeration** — Using `ps` to identify running services
2. **Binary Source Analysis** — Examining scripts to understand functionality
3. **/proc Filesystem** — Virtual filesystem containing process information
4. **Cmdline Inspection** — Examining command-line arguments in `/proc/[PID]/cmdline`
5. **Environment Variables** — Accessing `/proc/[PID]/environ` for secrets
6. **Process Forensics** — Information persistence despite file system cleanup

---

## Tools & Commands Used

```bash
# SSH access
ssh ghost8@204.168.229.209 -p 2222

# Process enumeration
ps -aux

# Directory navigation and listing
cd /usr/local/bin/
ls -alps

# File examination
cat level8-daemon.py
cd /proc/[PID]/
cat cmdline
cat environ
```

---

## Lessons Learned

1. **Process Information Persistence** — `/proc` filesystem retains process information even when files are cleaned from disk
2. **Environment Variable Exposure** — Credentials in environment variables are accessible via `/proc`
3. **Limited Cleanup** — Removing command-line arguments doesn't prevent access to environment variables
4. **Process Forensics Value** — Running processes expose information that static file analysis cannot reveal
5. **/proc Accessibility** — Process owner can read own `/proc/[PID]/environ` even with restricted permissions

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- Process Status: `ps(1)` man pages - https://man7.org/linux/man-pages/man1/ps.1.html
- /proc Filesystem: `proc(5)` man pages - https://man7.org/linux/man-pages/man5/proc.5.html

---

**Status:** ✅ Completed  
**Challenge Type:** Process Forensics / /proc Filesystem Analysis  
**Key Learning:** Running processes expose secrets through /proc even when files are cleaned
