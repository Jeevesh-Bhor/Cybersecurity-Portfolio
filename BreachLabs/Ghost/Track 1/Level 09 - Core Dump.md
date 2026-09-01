# BreachLabs: Ghost Track Level 9 - Core Dump

**Challenge:** Level 9 → 10  
**Date Completed:** August 30, 2026  
**Platform:** [BreachLabs](https://breachlab.org/)

---

## Summary

Core Dump introduces memory forensics through core dump files. When processes crash, core dumps preserve the process's memory state to disk, including secrets and environment variables that were live in memory. The challenge requires extracting readable strings from a core dump file and filtering through noise to locate credential tokens.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost10` user  
**Context:** Crashed agent process left core dump containing secrets  
**Constraint:** Core dump contains large amounts of noise; requires filtering to locate token

---

## Initial Access

### SSH Connection

Connected using credentials obtained from Level 8:
```
Target: 204.168.229.209:2222
User: ghost9
Access Method: SSH
```

### Challenge Introduction

Challenge narrative stated:
- Agent process crashed and left core dump
- Secrets live in memory hit disk verbatim
- Must pull token, not read raw bytes

---

## File Discovery

### Home Directory Contents

Located `ghost-agent.core` file in home directory:

**File Properties:**
- Filename: `ghost-agent.core`
- Size: 12,428 bytes
- Permissions: `-rw-r-----` (readable by user)
- Owner: ghost9

---

## Core Dump Analysis

### String Extraction

Used `strings` command to extract readable text from binary core dump:

```bash
strings ghost-agent.core
```

**Output:** Large volume of random alphanumeric data and noise mixed with readable text fragments.

### Credential Discovery in Raw Output

Within extensive noise output, identified readable credential data:

```
GHOST_REGION=eu-1
AGENT_BUILD=2.3.1
LD_LIBRARY_PATH=/opt/ghost/lib
GLIBC_2.34
AGENT_TOKEN=N01s3_Fl00r
HOME=/home/ghost-agent
PWD=/opt/ghost
```

**Key Finding:** `AGENT_TOKEN=N01s3_Fl00r` visible in raw strings output.

---

## Filtered Extraction Method

### Keyword-Based Filtering

For large core dumps with extensive noise, used `grep` to filter based on challenge context keyword "token" or any other keyword that might match the scenario:

**Attempt 1:**
```bash
strings ghost-agent.core | grep token
```
**Result:** No output (case-sensitive match failed)

**Attempt 2:**
```bash
strings ghost-agent.core | grep Token
```
**Result:** No output (incorrect case)

**Attempt 3:**
```bash
strings ghost-agent.core | grep TOKEN
```
**Result:**
```
AGENT_TOKEN=N01s3_Fl00r
```

**Finding:** Case-sensitive filtering required uppercase "TOKEN" to match environment variable.

---

## Attack Chain Summary

```
SSH Connection (ghost9)
         ↓
File Discovery (ghost-agent.core)
         ↓
Core Dump Recognition
         ↓
String Extraction (strings command)
         ↓
Noise Filtering Decision
         ↓
Keyword Identification ("token" from challenge description)
         ↓
Case-Sensitive Grep Filtering
         ↓
Credential Discovery (AGENT_TOKEN)
         ↓
Password Extraction and Validation
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **Core Dump Analysis** — Binary core dump files contain memory state at crash time
2. **String Extraction** — Using `strings` command to recover readable text from binary data
3. **Memory Forensics** — Secrets in process memory preserved in core dumps
4. **Grep Filtering** — Using case-sensitive pattern matching to locate specific data
5. **Keyword Heuristics** — Challenge narrative provides hints for filtering keywords
6. **Environment Variable Persistence** — Process environment variables recoverable from memory dumps

---

## Tools & Commands Used

```bash
# SSH access
ssh ghost9@204.168.229.209 -p 2222

# Directory listing
ls -alps

# Core dump analysis
strings ghost-agent.core

# Filtered extraction
strings ghost-agent.core | grep token
strings ghost-agent.core | grep Token
strings ghost-agent.core | grep TOKEN
```

---

## Lessons Learned

1. **Core Dumps Expose Secrets** — Process memory dumps preserve all environment variables and live data
2. **Strings Command Effectiveness** — Extracting readable text from binary data reveals embedded information
3. **Case Sensitivity Matters** — Grep filtering is case-sensitive; environment variable names must match exactly
4. **Challenge Context as Guide** — Narrative hints ("token") indicate filtering keywords to try
5. **Large Dataset Filtering** — Grep more efficient than manual inspection for large core dumps
6. **Memory Confidentiality** — Crashed processes leave sensitive data accessible on disk

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- String Extraction: `strings(1)` man pages - https://man7.org/linux/man-pages/man1/strings.1.html
- Pattern Matching: `grep(1)` man pages - https://man7.org/linux/man-pages/man1/grep.1.html
- File Type Detection: `file(1)` man pages - https://man7.org/linux/man-pages/man1/file.1.html

---

**Status:** ✅ Completed  
**Challenge Type:** Memory Forensics / Core Dump Analysis  
**Key Learning:** Core dumps preserve live process memory including credentials 
