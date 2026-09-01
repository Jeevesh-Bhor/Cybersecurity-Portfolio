# BreachLabs: Ghost Track Level 16 - Config Drift

**Challenge:** Level 16 → 17  
**Date Completed:** August 31, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

Config Drift introduces configuration change detection through file comparison. The challenge presents two snapshots of authorized credentials from consecutive days, with one line modified as a planted backdoor. Success requires using diff to identify the changed line among 100 similar entries rather than manual inspection.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost17` user  
**Context:** Two audit snapshots of service credentials (Monday vs Tuesday)  
**Constraint:** Single line changed between snapshots; must identify programmatically

---

## Initial Access

### SSH Connection

Connected using password obtained from Level 15:
```
Target: 204.168.229.209:2222
User: ghost16
Access Method: SSH (password)
```

### Challenge Introduction

Challenge narrative stated:
- Two daily snapshots of host's authorized credentials
- One line changed overnight (planted backdoor)
- Find drift without reading by eye
- Must use programmatic comparison

---

## File Discovery

### Home Directory Contents

Listed ghost16 home directory:

```bash
ls -alps
```

**Key Files Located:**
- `audit-mon.txt` — Monday credential snapshot (3000 bytes)
- `audit-tue.txt` — Tuesday credential snapshot (2993 bytes)

**Finding:** Size difference indicates content divergence between snapshots (7-byte difference).

---

## File Structure Analysis

### Monday Snapshot Contents

Examined `audit-mon.txt`:

**Format:** Service credential pairs (100 entries)

```
svc_0001:f38e9582a3108a2b3c3e
svc_0002:53e5ba5985adeea57565
...
svc_0042:ede8866d29e6eec0fb98
...
svc_0100:b89fdd1957c29002cf55
```

**Structure:** Each line contains service identifier and hex credential string.

### Tuesday Snapshot Contents

Examined `audit-tue.txt`:

**Format:** Service credential pairs (100 entries, same structure)

```
svc_0001:f38e9582a3108a2b3c3e
svc_0002:53e5ba5985adeea57565
...
svc_0042:[REDACTED_FLAG]
...
svc_0100:b89fdd1957c29002cf55
```

**Structure:** Identical format to Monday snapshot.

---

## Configuration Drift Detection

### Diff Comparison

Used `diff` command to identify changes:

```bash
diff audit-mon.txt audit-tue.txt
```

**Output:**
```
42c42
< svc_0042:ede8866d29e6eec0fb98
---
> svc_0042:[REDACTED_FLAG]
```

**Diff Interpretation:**
- Line 42: Changed (`42c42`)
- Removed: `svc_0042:ede8866d29e6eec0fb98` (Monday credential)
- Added: `svc_0042:[REDACTED_FLAG]` (Tuesday credential, backdoor)

**Finding:** Single configuration drift identified: svc_0042 credential modified between snapshots.

---

## Attack Chain Summary

```
SSH Connection (ghost16, password auth)
         ↓
Home Directory Navigation
         ↓
Audit File Discovery (audit-mon.txt, audit-tue.txt)
         ↓
File Size Analysis (3000 vs 2993 bytes)
         ↓
Structure Recognition (100 service entries each)
         ↓
Manual Inspection Infeasibility (100 entries, similar format)
         ↓
Diff Command Execution
         ↓
Output Parsing (line 42 changed)
         ↓
Credential Comparison (old vs new value)
         ↓
Backdoor Identification (svc_0042 modified)
         ↓
Credential Extraction
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **File Comparison** — Using `diff` to identify changes between files
2. **Configuration Audit** — Snapshots document system state at points in time
3. **Change Detection** — Identifying single modified line among 100 entries
4. **Drift Analysis** — Detecting unauthorized configuration changes
5. **Automated Detection** — Using tools instead of manual inspection
6. **Backdoor Identification** — Recognizing suspicious credential modifications

---

## Diff Output Format

**Notation:** `42c42` (line 42 changed in both files)

**Symbols:**
- `<` — Line present in first file (audit-mon.txt)
- `>` — Line present in second file (audit-tue.txt)
- `---` — Separator between old and new content

**Result:** Single change identified; all other 99 lines identical.

---

## Tools & Commands Used

```bash
# SSH access
ssh ghost16@204.168.229.209 -p 2222

# Directory listing
ls -alps

# File examination
cat audit-mon.txt
cat audit-tue.txt

# Configuration drift detection
diff audit-mon.txt audit-tue.txt
```

---

## Lessons Learned

1. **File Comparison Efficiency** — Diff faster and more reliable than manual scanning
2. **Configuration Baselines** — Snapshots enable change tracking
3. **Backdoor Patterns** — Credentials modified to add unauthorized access
4. **Scale Challenges** — 100 entries unmountable for manual inspection
5. **Automated Detection** — Programmatic tools essential for operational security
6. **Single Change Significance** — One modified line among hundreds indicates targeted modification

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- File Comparison: `diff(1)` man pages - https://man7.org/linux/man-pages/man1/diff.1.html
- Line-by-line Comparison: `comm(1)` man pages - https://man7.org/linux/man-pages/man1/comm.1.html

---

**Status:** ✅ Completed  
**Challenge Type:** Configuration Change Detection / Drift Analysis  
**Key Learning:** Using diff to identify unauthorized configuration changes  
