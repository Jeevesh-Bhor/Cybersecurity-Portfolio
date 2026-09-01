# BreachLabs: Ghost Track Level 2 - In The Shadows

**Challenge:** Level 2 → 3  
**Date Completed:** August 29, 2026  
**Platform:** [BreachLabs](https://breachlab.org/)

---

## Summary

In The Shadows introduces the concept of hidden directory traversal and compartmentalized data storage. The challenge requires navigating nested directories and accessing hidden files (those prefixed with `.`) to locate credentials stored in deliberately obscured locations. Success demonstrates understanding of directory structure analysis and the significance of hidden files in Unix-like systems.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost3` user  
**Context:** KAEL compartmentalized investigation data across multiple directory levels and hidden files  
**Constraint:** Active leads deliberately hidden within investigation folder structure

---

## Initial Access

### SSH Connection

Connected using credentials obtained from Level 1:
```
Target: 204.168.229.209:2222
User: ghost2
Access Method: SSH
```

### Challenge Introduction

Challenge narrative stated:
- KAEL compartmentalized everything
- Investigation folder contains reports but not required data
- Active leads stored in less obvious locations
- Success requires understanding directory navigation and hidden files

---

## Home Directory Analysis

### Memo File

Examined `.memo` file in home directory:
```
NOTE TO SELF — KAEL
The work happens off the main paths.
Compartmentalization is the only real opsec.
If it's in plain sight, it's not worth finding.
```

**Key Finding:** Memo explicitly confirmed that important information exists outside obvious paths, validating the need to search beyond surface-level directories.

### Directory Structure

Home directory listing revealed `investigation/` folder as primary focus point for credential search.

---

## Investigation Directory Traversal

### Initial Investigation Folder Contents

Navigated to `investigation/` directory and examined contents:
```
Files:
- report.txt
- summary.txt

Subdirectories:
- .leads/ (hidden directory)
```

### Report File Analysis

```
INCIDENT REPORT — Q1 2026
Status: Classified
Prepared by: KAEL
Summary: Unauthorized access detected on segment C.
Response: Ongoing. Active leads compartmentalized.
Full details filed separately.
```

**Finding:** Report confirmed that active leads were compartmentalized and stored separately from visible files.

### Summary File Analysis

```
OPERATIONAL SUMMARY
===================
Operation: GHOST WATCH
Status: Active
All active source files have been compartmentalized
and moved to a separate location.
This document contains no credentials.
```

**Finding:** Summary explicitly stated that credentials exist in separate location, confirming need to access hidden `.leads/` directory.

---

## Hidden Directory Investigation

### .leads Directory Access

Navigated to `.leads/` subdirectory (hidden directory, indicated by leading `.`).

### Hidden Files Discovery

Directory contained three hidden files with source identifiers:
```
.source_alpha
.source_beta
.source_omega
```

### File Access and Data Retrieval

**File: `.source_alpha`**
```bash
cat ./.source_alpha
```
Output: Intermediate credential component

**File: `.source_beta`**
```bash
cat ./.source_beta
```
Output: Intermediate credential component

**File: `.source_omega`**
```bash
cat ./.source_omega
```
Output: Flag-formatted credential component

---

## Attack Chain Summary

```
SSH Connection (ghost2)
         ↓
Home Directory Enumeration
         ↓
.memo Analysis (compartmentalization hint)
         ↓
Investigation Directory Access
         ↓
Surface Files Analysis (report, summary)
         ↓
Confirmation of Compartmentalization
         ↓
Hidden Directory Navigation (.leads/)
         ↓
Hidden File Discovery (.source_*)
         ↓
Credential Component Retrieval
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **Hidden File Recognition** — Files prefixed with `.` are hidden from standard `ls` output
2. **Directory Traversal** — Nested directory structure requires systematic navigation
3. **Hidden Directory Access** — Directories can be hidden using same convention as files
4. **Compartmentalization** — Important data separated across directory hierarchy
5. **Path Navigation** — Proper `cd` commands required to access nested structures

---

## Tools & Commands Used

```bash
# SSH access
ssh ghost2@204.168.229.209 -p 2222

# Directory listing with hidden files
ls -alps

# Directory navigation
cd investigation/
cd ./.leads/

# File access
cat ./.memo
cat report.txt
cat summary.txt
cat ./.source_alpha
cat ./.source_beta
cat ./.source_omega
```

---

## Lessons Learned

1. **Hidden Files and Directories** — Default `ls` output omits hidden files; `-a` flag required to view
2. **Nested Directory Importance** — Compartmentalization achieved through directory hierarchy, not obfuscation
3. **Narrative Context Useful** — KAEL's notes and reports provided hints toward correct directory path
4. **Systematic Navigation** — Following directory structure leads to compartmentalized data
5. **Hidden Prefix Convention** — Leading `.` indicates hidden status in Unix-like systems

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- Directory Listing: `ls(1)` man pages
- Directory Navigation: `cd(1)` man pages
- Hidden Files: Unix file naming conventions

---

**Status:** ✅ Completed  
**Challenge Type:** Directory Traversal / Hidden Files  
**Key Learning:** Compartmentalization through directory hierarchy
