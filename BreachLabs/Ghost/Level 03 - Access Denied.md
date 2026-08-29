# BreachLabs: Ghost Track Level 3 - Access Denied

**Challenge:** Level 3 → 4  
**Date Completed:** August 29, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

Access Denied introduces file permissions and access control through group membership. The challenge requires navigating filesystem directories with restricted permissions and understanding that credentials are stored in locations accessible only to specific user groups. Success demonstrates comprehension of Unix file permissions, directory access restrictions, and the relationship between user identity and accessible resources.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost4` user  
**Context:** KAEL structured storage by access level using filesystem permissions  
**Constraint:** Credentials stored in restricted directories requiring proper group membership

---

## Initial Access

### SSH Connection

Connected using credentials obtained from Level 2:
```
Target: 204.168.229.209:2222
User: ghost3
Access Method: SSH
```

### Challenge Introduction

Challenge narrative stated:
- KAEL structured storage by access level
- Not everything is readable by everyone
- Success requires understanding user identity and accessible resources

---

## Home Directory Analysis

### Map File

Located and examined `map.txt` in home directory:
```
KAEL'S STORAGE LAYOUT
=====================
Recovered from workstation. Partially redacted.
  /var/intel/public/   — world readable
  /var/intel/ops/      — restricted
  /var/intel/archive/  — root only
Access follows the group scheme. The kernel will
tell you what you are, if you ask it.
— KAEL
```

**Key Finding:** Map explicitly documented that storage was organized by access level and that access was controlled by group membership scheme.

---

## Filesystem Navigation

### Intel Directory Structure

Navigated to `/var/intel/` and examined directory listing:
```
Directories:
  archive/      (permissions: drwx------)
  ops/          (permissions: drwxr-x---, owned by root:analysts)
  public/       (permissions: drwxr-xr-x)
```

**Finding:** Directory permissions revealed access restrictions. `ops/` directory was readable only by members of `analysts` group.

### Public Directory Contents

Accessed `/var/intel/public/` directory:
```
File: report_q1.txt
Content: Contains no operational credentials. Directs to restricted files for access group members.
```

**Finding:** Public directory contained no credentials, confirming that sensitive data was stored in restricted locations.

### Ops Directory Access

Successfully navigated to `/var/intel/ops/` directory despite "restricted" designation in map.

**Directory Contents:**
```
Files:
  access_codes.dat      (permissions: ----r------, owned by root:analysts)
  operative_list.txt    (permissions: ----r------, owned by root:analysts)
```

**Key Observation:** Files were readable (r permission) for group members despite restrictive overall permissions.

### Restricted File Contents

**File: `operative_list.txt`**
```
INDEX OF ACTIVE OPERATIONS
Classification: Analyst Only
See access_codes.dat for current credentials.
```

**File: `access_codes.dat`**
```bash
cat access_codes.dat
```
Output: Credential data with flag-formatted content

---

## Attack Chain Summary

```
SSH Connection (ghost3)
         ↓
Home Directory Enumeration
         ↓
map.txt Analysis (access level documentation)
         ↓
/var/intel/ Directory Navigation
         ↓
Directory Permissions Analysis
         ↓
Public Directory Access (no credentials)
         ↓
Ops Directory Access (group membership sufficient)
         ↓
Restricted Files Discovery
         ↓
Credential Retrieval from access_codes.dat
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **File Permissions** — Understanding rwx permissions and access restrictions
2. **Group Membership** — Access control through group-based permissions
3. **Directory Hierarchy** — Organizing restricted resources by access level
4. **Permission Notation** — Interpreting permission strings (----r-----)
5. **Access Control** — Using filesystem permissions to compartmentalize data

---

## Tools & Commands Used

```bash
# SSH access
ssh ghost3@204.168.229.209 -p 2222

# File and directory listing
ls -alps

# Directory navigation
cd /var/intel/
cd /var/intel/public/
cd /var/intel/ops/

# File access
cat map.txt
cat report_q1.txt
cat operative_list.txt
cat access_codes.dat
```

---

## Lessons Learned

1. **Group-Based Access Control** — Filesystem permissions restrict access by group membership, not just user identity
2. **Permission Compartmentalization** — KAEL used tiered directories (public/ops/archive) to control information access
3. **Readable vs Accessible** — Files may be readable (r) while overall directory access is restricted
4. **Directory Permissions Matter** — Directory permissions control who can access contents within
5. **Documentation as Clue** — Map.txt provided explicit guidance on access structure

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- File Permissions: `ls(1)` man pages
- User Identity: `id(1)` man pages
- Permission Modification: `chmod(1)` man pages

---

**Status:** ✅ Completed  
**Challenge Type:** Access Control / File Permissions  
**Key Learning:** Group-based access control and permission structure 
