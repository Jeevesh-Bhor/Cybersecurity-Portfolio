# BreachLabs: Ghost Track Level 1 - Name Game

**Challenge:** Level 1 → 2  
**Date Completed:** August 29, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

Name Game introduces file naming as a social engineering tactic. The challenge features deliberately confusing filenames designed to discourage casual investigation. Success requires understanding bash quoting mechanisms and proper file referencing syntax to access files with special characters, spaces, and command-like names.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost2` user  
**Context:** Previous analyst KAEL deliberately created obfuscated filenames to discourage investigation  
**Constraint:** Access files with problematic names using correct shell syntax

---

## Initial Access

### SSH Connection

Connected using credentials obtained from Level 0:
```
Target: 204.168.229.209:2222
User: ghost1
Access Method: SSH
```

### Challenge Introduction

Received narrative context:
- KAEL created intentionally problematic filenames
- Challenge framed as social engineering test — most analysts quit before attempting to access files
- Instructions directed to review MANIFEST and bash quoting documentation

---

## File Listing Analysis

### Directory Enumeration

Examined home directory with detailed listing:
```
Files with problematic names identified:
- File named: "-"
- File named: "--help"
- File named: "..."
- File named: "file name" (contains space)
```

**Challenge:** These filenames are problematic because:
- `-` is interpreted as option flag by many commands
- `--help` mimics command options
- Spaces require special handling
- Standard `cat filename` won't work for these cases

---

## MANIFEST Analysis

Retrieved and examined `MANIFEST` file:
```
NOTES — KAEL
────────────
I named my files to watch careless analysts
give up before they even read them.
Most people who poke around this directory
will quit before they open the first one.
— KAEL
```

**Key Finding:** MANIFEST confirmed that filename obfuscation was intentional, validating the hypothesis that challenging filenames contained important information.

---

## File Access Techniques

### Problem Files Resolution

Successfully accessed problematic filenames using proper bash quoting and path specification:

**File: `-`**
```bash
cat ./-
```
Output: Contains set of random alphanumeric characters

**File: `--help`**
```bash
cat ./--help
```
Output: Contains set of random alphanumeric characters

**File: `...`**
```bash
cat ./...
```
Output: Contains set of random alphanumeric characters

**File: `file name`**
```bash
cat ./'file name'
```
Output: Contains flag-formatted data (intermediate credential component)

---

## Bash Quoting Mechanisms

### Techniques Applied

1. **Path Prefix** — Using `./` prevents shell from interpreting `-` as option flag
   - `cat -` would attempt to read from stdin
   - `cat ./-` correctly references file named `-`

2. **Single Quotes** — Enclosing filename in single quotes prevents space interpretation
   - `cat file name` splits into multiple arguments
   - `cat 'file name'` treats entire string as single filename

3. **Double Quotes** — Alternative quoting mechanism for space-containing names
   - Also effective for preserving filename with spaces

### Challenge Progression

Each file accessed revealed intermediate data. These values appeared to chain together, suggesting multi-stage password retrieval across levels or credential composition.

---

## Attack Chain Summary

```
SSH Connection (ghost1)
         ↓
Directory Enumeration (identify problematic filenames)
         ↓
MANIFEST Analysis (confirms intentional obfuscation)
         ↓
Bash Documentation Review (quoting mechanisms)
         ↓
File Access via Path Prefix (./-)
         ↓
File Access via Options Prevention
         ↓
File Access via Single Quoting ('file name')
         ↓
Data Collection
         ↓
Credential Assembly
```

---

## Key Techniques & Concepts

1. **Shell Escaping** — Proper quoting prevents shell from misinterpreting special characters
2. **Path Specification** — Using `./` explicitly references current directory, preventing flag interpretation
3. **Quote Types** — Single quotes preserve literal strings; double quotes allow variable expansion
4. **Social Engineering** — Filename obfuscation used as discouragement tactic
5. **Credential Chaining** — Multiple files containing partial credential data requiring assembly

---

## Tools & Commands Used

```bash
# SSH access
ssh ghost1@204.168.229.209 -p 2222

# Directory enumeration
ls -alps

# File access with special names
cat ./-                    # Path prefix for hyphenated filename
cat ./--help              # Prevents option interpretation
cat ./'file name'         # Single quotes for space-containing names
cat "./file name"         # Double quotes alternative
```

---

## Lessons Learned

1. **Filename Escaping Critical** — Special characters in filenames require proper quoting to access
2. **Path Prefix Prevention** — Using `./` explicitly references files, preventing misinterpretation as flags
3. **Quote Mechanisms** — Understanding single vs double quotes essential for shell operations
4. **Persistence Matters** — Challenge designed to discourage but information remains accessible with correct technique
5. **Documentation Reference** — Bash quoting documentation proved essential for solving challenge

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- Bash Quoting: https://tldp.org/LDP/Bash-Beginners-Guide/html/sect_03_03.html
- Shell Syntax: https://ss64.com/bash/syntax-quoting.html
- File Access: `cat(1)` man pages

---

**Status:** ✅ Completed  
**Challenge Type:** Basic File System Commands  
**Key Learning:** Proper file referencing with special characters
