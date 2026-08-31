# BreachLabs: Ghost Track Level 18 - Wrong User

**Challenge:** Level 18 → 19  
**Date Completed:** August 31, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

Wrong User introduces privilege escalation through SUID (set-user-ID) binaries. The challenge requires locating an executable with SUID bit set, owned by a higher-privilege user, and using it to access credentials beyond current user's permissions. Success demonstrates understanding of SUID permissions and privilege escalation vectors.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost19` user  
**Context:** SUID binary on system owned by ghost19, executable by ghost18  
**Constraint:** Must find and execute SUID binary to access ghost19 credentials

---

## Initial Access

### SSH Connection

Connected using password obtained from Level 17:
```
Target: 204.168.229.209:2222
User: ghost18
Access Method: SSH (password)
```

### Challenge Introduction

Challenge narrative stated:
- Program on system runs with another operative's privileges
- Find program, read permission bits
- Let it do the reading

---

## SUID Binary Discovery

### Filesystem Search

Scanned filesystem for SUID binaries owned by ghost19:

```bash
find / -type f -user ghost19 -perm -4000 2>/dev/null
```

**Search Parameters:**
- `-type f` — Regular files only
- `-user ghost19` — Owned by ghost19
- `-perm -4000` — SUID bit set (4000 octal = setuid)
- `2>/dev/null` — Suppress error messages

**Result:**
```
/usr/local/bin/readback
```

**Finding:** Single SUID binary discovered: `/usr/local/bin/readback`, owned by ghost19.

---

## Binary Analysis

### Permission Examination

Examined binary permissions in `/usr/local/bin/`:

```bash
ls -alps
```

**Readback Binary Details:**
```
-rwsr-x--- 1 ghost19 ghost18 819664 Jun 22 13:41 readback
```

**Permission Breakdown:**
- Owner: `ghost19`
- Group: `ghost18`
- Mode: `rwsr-x---`
- SUID Bit: Set (lowercase `s` in owner execute position)
- Size: 819,664 bytes

**Finding:** SUID bit enabled allows ghost18 to execute binary with ghost19's privileges.

---

## Privilege Escalation

### Binary Execution

Executed readback binary:

```bash
./readback
```

**Output:**
```
[REDACTED_FLAG]
```

**Finding:** Binary executed with ghost19 privileges and returned credential accessible only to ghost19.

---

## Attack Chain Summary

```
SSH Connection (ghost18, password auth)
         ↓
Challenge Context (find SUID binary, read with elevated privileges)
         ↓
SUID Binary Search (find / -user ghost19 -perm -4000)
         ↓
Binary Discovery (/usr/local/bin/readback)
         ↓
Permission Analysis (SUID bit confirmed)
         ↓
Ownership Verification (ghost19 owner, ghost18 executable)
         ↓
Binary Execution (./readback)
         ↓
Privilege Escalation (executes as ghost19)
         ↓
Credential Access (ghost19 data readable)
         ↓
Credential Extraction
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **SUID Bit** — Setuid bit (4000 octal) causes binary to execute with owner's privileges
2. **Privilege Escalation** — SUID binaries enable lower-privilege users to execute code as higher-privilege user
3. **Binary Ownership** — File owned by ghost19 but executable by ghost18
4. **Find Command Filtering** — Using find with multiple criteria to locate specific binaries
5. **Permission Bits** — Reading and interpreting octal permission notation
6. **Execution Context** — Process runs with ghost19 privileges, accessing ghost19 resources

---

## SUID Permission Details

**Permission String:** `rwsr-x---`

**Breakdown:**
- `rws` — Owner permissions: read, write, execute + SUID
- `r-x` — Group permissions: read, execute
- `---` — Other permissions: none

**Octal Notation:** `4750`
- `4` — SUID bit
- `7` — Owner (rwx = 7)
- `5` — Group (r-x = 5)
- `0` — Other (--- = 0)

**Execution Behavior:** When ghost18 executes readback, process runs with ghost19's effective UID, allowing access to ghost19-owned resources.

---

## Tools & Commands Used

```bash
# SSH access
ssh ghost18@204.168.229.209 -p 2222

# SUID binary discovery
find / -type f -user ghost19 -perm -4000 2>/dev/null

# Directory listing with permissions
ls -alps /usr/local/bin/

# Binary execution
./readback
```

---

## Lessons Learned

1. **SUID Vulnerability** — SUID binaries can be exploitation vectors if not properly designed
2. **Privilege Escalation Chains** — Finding binaries owned by target user enables privilege escalation
3. **Permission Analysis** — SUID bit presence indicates privileged execution
4. **Executable Discovery** — Systematic search identifies all SUID binaries on system
5. **Execution Context** — Understanding who owns process determines resource access
6. **Security Risk** — SUID binaries allow lower-privilege users to execute code as higher-privilege user

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- Find Command: `find(1)` man pages - https://man7.org/linux/man-pages/man1/find.1.html
- Permission Modification: `chmod(1)` man pages - https://man7.org/linux/man-pages/man1/chmod.1.html

---

**Status:** ✅ Completed  
**Challenge Type:** SUID Privilege Escalation  
**Key Learning:** Using SUID binaries to execute code with elevated privileges
