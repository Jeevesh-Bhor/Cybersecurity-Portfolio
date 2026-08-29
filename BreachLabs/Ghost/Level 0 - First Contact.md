# BreachLabs: Ghost Track Level 0 - First Contact

**Challenge:** Level 0 → 1  
**Date Completed:** August 29, 2026  
**Platform:** [BreachLabs](https://breachlab.org/tracks/ghost/i/0) 

---

## Summary

Ghost Track Level 0: First Contact is an introductory forensics and OSINT challenge focusing on information gathering from an abandoned workstation. The scenario presents an analyst's terminal left open with clues scattered throughout the filesystem. Success requires methodical directory exploration, file examination, and connecting disparate information fragments to retrieve required credentials.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost1` user  
**Context:** Previous analyst "KAEL" abandoned their workstation in a hurry, leaving terminal open with files intact  
**Constraint:** Find information without leaving traces

---

## Initial Access

### SSH Connection

Successfully established SSH connection to provided target using default ghost0 credentials:
```
Target: 204.168.229.209:2222
User: ghost0
Access Method: SSH key authentication
```

### Terminal Environment

Upon connection, received welcome banner and challenge instructions:
- Challenge framed as forensic analysis of abandoned workstation
- Hint provided to review Linux fundamentals (ls, cat, cd commands)
- Clear objective: locate password for ghost1 user

---

## Reconnaissance & Investigation

### Home Directory Analysis

Examined home directory contents using `ls -alps` (detailed listing including hidden files).

**Key Observation:** Directory structure revealed multiple subdirectories and configuration files. Initial scan identified no obvious password storage, but noted presence of `workspace/` directory as suspicious.

### README File Analysis

Located and examined `README` file in home directory:
```
ANALYST WORKSTATION — KAEL
Last active: 2026-03-28 02:47 UTC
Status: Abandoned

If you're reading this, you found my terminal. I left in a hurry. 
Didn't have time to clean up.

Nothing in this shell is hidden. It's just here. 
If you can't see it, you're not looking hard enough.

Don't leave traces.

— KAEL
```

**Analysis:** README provided narrative context and explicit hint: "Nothing in this shell is hidden. It's just here." This indicated password would be discoverable through standard navigation, not through obfuscation or advanced techniques.

---

## Directory Traversal & Credential Discovery

### Workspace Navigation

Changed to `workspace/` directory and examined contents.

**Directory Structure:**
```
~/workspace/
├── archive/          (subdirectory)
├── notes.txt         (file)
```

### Operational Notes Analysis

Retrieved and examined `notes.txt`:
```
OPERATIONAL NOTES — KAEL
========================
Target: internal network segment 10.4.x.x
Method: passive recon, no active scanning
Status: ongoing

Credentials filed separately in archive/. 
Do not store passwords in plaintext notes.
```

**Key Finding:** Notes explicitly referenced that credentials were stored separately in `archive/` subdirectory. This provided clear direction for next investigation step.

### Archive Directory Access

Navigated to `archive/` subdirectory and examined contents.

**Directory Contents:**
```
~/workspace/archive/
├── credentials (file)
```

### Credentials File Retrieval

Located `credentials` file containing the required password.

**File Access:** Used `cat` command to retrieve file contents and extract the password string.

---

## Challenge Completion

**Objective Achieved:** Successfully retrieved password for `ghost1` user

**Password Status:** [REDACTED - stored securely for ghost1 authentication]

---

## Attack Chain Summary

```
SSH Connection (ghost0)
         ↓
Home Directory Enumeration
         ↓
README File Analysis (contextual clues)
         ↓
Workspace Directory Navigation
         ↓
Operational Notes Examination (directional hint)
         ↓
Archive Directory Access
         ↓
Credentials File Discovery
         ↓
Password Retrieval
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **Filesystem Navigation** — Using `ls`, `cd`, `cat` for directory traversal and file examination
2. **Forensic Analysis** — Reconstructing analyst workflow from file artifacts
3. **Information Gathering** — Connecting contextual clues (README) to actionable findings
4. **OSINT Methodology** — Working with available information in logical progression
5. **Operational Security** — Understanding importance of "not leaving traces"

---

## Tools & Commands Used

```bash
# SSH connection
ssh ghost0@204.168.229.209 -p 2222

# Directory enumeration
ls -alps                    # Detailed listing with all files
cd <directory>              # Directory navigation

# File examination
cat <filename>              # Display file contents

# Information synthesis
# (reconstructing analyst's workflow from file locations and hints)
```

---

## Lessons Learned
 
1. **Filesystem as Evidence** — File placement and directory structure reveal user activity patterns
2. **Systematic Exploration Works** — Passwords not hidden through obfuscation, discovered through methodical navigation
3. **Credential Artifacts** — Credentials left in file artifacts despite security warnings against plaintext storage
4. **Clean Forensics** — Importance of investigating without contaminating or altering evidence
5. **Methodology Over Tools** — Patient enumeration more effective than advanced exploitation techniques

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- Linux Commands: `ls(1)`, `cat(1)`, `cd(1)` — See man pages linked in challenge
- Forensic Analysis Fundamentals: Filesystem artifact examination
- OSINT Methodology: Information gathering from available sources

---

**Status:** ✅ Completed  
**Challenge Type:** Forensics / OSINT / Beginner  
**Key Learning:** Systematic filesystem exploration and information correlation  
**Next:** Ghost Track Level 1 (to be documented)
