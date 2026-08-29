# BreachLabs: Ghost Track Level 4 - Signal in the Noise

**Challenge:** Level 4 → 5  
**Date Completed:** August 29, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

Signal in the Noise introduces data filtering and pattern recognition using command-line tools. The challenge requires searching through hundreds of files with similar structure to locate a single entry with a different format. Success demonstrates understanding of grep for pattern matching and piping to efficiently process large datasets.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost5` user  
**Context:** Vault directory contains 500 log files with similar structure  
**Constraint:** One entry has different format than others and contains required credentials

---

## Initial Access

### SSH Connection

Connected using credentials obtained from Level 3:
```
Target: 204.168.229.209:2222
User: ghost4
Access Method: SSH
```

### Challenge Introduction

Challenge objective stated:
- Vault contains log entries
- One entry has different format than others
- Success requires identifying and accessing the anomalous entry

---

## Vault Directory Analysis

### Directory Contents Overview

Navigated to `vault/` directory and examined contents:

**File Structure:**
- 500 files named `record_{000}` through `record_{499}`
- Most files: 63 bytes in size
- Anomalies: 5-6 files with different sizes (42 and 48 bytes)

**Finding:** Size anomalies indicated that some files contained different data than standard entries.

### Manual Review Infeasibility

With 500 files to process and only 5-6 anomalies visible, manual examination of each file was impractical. A systematic search approach was required.

---

## Pattern Matching Strategy

### Grep-Based Search

Implemented systematic search using `grep` to identify different format entries.

**Search Attempt 1: FLAG Keyword**
```bash
cat ./* | grep FLAG
```
Output: No results

**Search Attempt 2: PASSWORD Keyword**
```bash
cat ./* | grep PASSWORD
```
Output: No results

**Search Attempt 3: CREDENTIAL Keyword**
```bash
cat ./* | grep CREDENTIAL
```
Output: Found match with flag-formatted credential

---

## Credential Discovery

### Standard Entry Format

Examined standard password entries for format comparison:

**Record_0477:**
```
[2026-03-28 02:47:13] password=Ofhkenr2wYH1qk1e
```

**Record_0404:**
```
[2026-03-28 02:47:13] password=olRUyqrQxbv4oZ0f
```

**Pattern:** Standard entries used timestamp and `password=` format.

### Anomalous Entry Format

**Record_0315:**
```
[CLASSIFIED] CREDENTIAL: {redacted}
```

**Finding:** This entry used `[CLASSIFIED]` prefix and `CREDENTIAL:` format instead of standard timestamp and password format.

---

## Attack Chain Summary

```
SSH Connection (ghost4)
         ↓
Vault Directory Navigation
         ↓
Directory Contents Analysis
         ↓
File Size Anomaly Detection
         ↓
Manual Review Infeasibility Assessment
         ↓
Grep-Based Search Strategy
         ↓
FLAG Keyword Search (no results)
         ↓
PASSWORD Keyword Search (no results)
         ↓
CREDENTIAL Keyword Search (match found)
         ↓
Format Comparison (standard vs anomalous)
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **File Aggregation** — Using `cat ./*` to process multiple files
2. **Pattern Matching** — grep searches for specific text patterns across files
3. **Piping** — Combining commands (`cat | grep`) for data filtering
4. **Anomaly Detection** — Identifying entries with different format than majority
5. **Data Volume Handling** — Using command-line tools to manage 500+ files efficiently

---

## Tools & Commands Used

```bash
# SSH access
ssh ghost4@204.168.229.209 -p 2222

# Directory navigation
cd vault/

# Directory listing
ls -alps

# File examination
cat ./*            # Concatenate all files in directory
grep FLAG          # Search for FLAG keyword
grep PASSWORD      # Search for PASSWORD keyword
grep CREDENTIAL    # Search for CREDENTIAL keyword

# Individual file inspection
cat record_0477
cat record_0404
cat record_0315
```

---

## Lessons Learned

1. **Signal vs Noise** — Anomalies can be identified through format differences rather than file size alone
2. **grep Effectiveness** — Simple pattern matching finds specific entries in large datasets
3. **Keyword Selection** — Correct search term (CREDENTIAL vs PASSWORD) determined success
4. **Piping Efficiency** — Combining commands processes large file volumes without manual iteration
5. **Format Recognition** — Different entry format indicated importance and authenticity

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- Pattern Matching: `grep(1)` man pages
- Piping: https://ryanstutorials.net/linuxtutorial/piping.php

---

**Status:** ✅ Completed  
**Challenge Type:** Data Filtering / Pattern Matching  
**Key Learning:** grep for identifying anomalies in large datasets
