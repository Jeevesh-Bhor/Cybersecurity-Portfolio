# BreachLabs: Ghost Track Level 10 - Odd Token Out

**Challenge:** Level 10 → 11  
**Date Completed:** August 30, 2026  
**Platform:** [BreachLabs](https://breachlab.org/)

---

## Summary

Odd Token Out introduces deduplication and anomaly detection through sorting and uniqueness filtering. The challenge contains session token logs where most tokens are logged twice by two collectors, except for one token written during an outage that appears only once. Success requires identifying the anomalous single occurrence among duplicated entries.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost11` user  
**Context:** Session token log contains duplicated entries with one unique exception  
**Constraint:** Token appears only once; must be identified from large dataset without manual inspection

---

## Initial Access

### SSH Connection

Connected using credentials obtained from Level 9:
```
Target: 204.168.229.209:2222
User: ghost10
Access Method: SSH
```

### Challenge Introduction

Challenge narrative stated:
- Two collectors logged every token twice
- One token written during outage (not logged twice)
- Must find lone token, not by eye

---

## File Discovery

### Home Directory Contents

Located `session-tokens.log` file in home directory:

**File Properties:**
- Filename: `session-tokens.log`
- Size: 12,015 bytes
- Permissions: `-rw-r-----` (readable by user)
- Owner: ghost10

---

## Log File Analysis

### File Contents

Examined `session-tokens.log`:

```bash
cat session-tokens.log
```

**Output:** Large list of session tokens in alphanumeric format. Most tokens appeared multiple times throughout the file due to dual collector logging.

---

## Deduplication and Anomaly Detection

### Sorting and Uniqueness Filtering

Used combined `sort` and `uniq` commands to identify anomalous entry:

```bash
sort session-tokens.log | uniq -u
```

**Process:**
1. `sort` — Organized all tokens in sorted order, grouping identical tokens together
2. `uniq -u` — Filtered to show only unique entries (tokens appearing exactly once)

**Output:**
```
[REDACTED_FLAG]
```

**Finding:** Single token appeared only once in log file, indicating credential logged during outage when second collector was not operational.

---

## Attack Chain Summary

```
SSH Connection (ghost10)
         ↓
File Discovery (session-tokens.log)
         ↓
Log File Examination
         ↓
Token Duplication Pattern Recognition
         ↓
Sort Command Execution
         ↓
Uniq Filtering (show unique entries only)
         ↓
Anomalous Token Identification
         ↓
Credential Extraction and Validation
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **Data Deduplication** — Using `sort` to organize and group identical entries
2. **Uniqueness Filtering** — Using `uniq -u` to identify single occurrences
3. **Anomaly Detection** — Finding entries that deviate from expected pattern (single vs duplicated)
4. **Log Analysis** — Identifying abnormalities in collection logs
5. **Pipeline Operations** — Combining commands for efficient data processing
6. **Operational Anomalies** — Service outages create gaps in expected data patterns

---

## Tools & Commands Used

```bash
# SSH access
ssh ghost10@204.168.229.209 -p 2222

# Directory listing
ls -alps

# File examination
cat session-tokens.log

# Deduplication and filtering
sort session-tokens.log | uniq -u
```

---

## Lessons Learned

1. **Redundancy Patterns** — Duplicate collectors create expected data duplication
2. **Outage Detection** — Service interruptions leave gaps in expected patterns
3. **Uniq Effectiveness** — `uniq -u` efficiently identifies single occurrences in large datasets
4. **Sort Prerequisite** — `sort` must precede `uniq` for proper operation
5. **Automated Anomaly Detection** — Programmatic filtering more reliable than manual inspection
6. **Operational Logging** — Collection redundancy enables outage detection through pattern analysis

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- Sorting: `sort(1)` man pages - https://man7.org/linux/man-pages/man1/sort.1.html
- Uniqueness Filtering: `uniq(1)` man pages - https://man7.org/linux/man-pages/man1/uniq.1.html

---

**Status:** ✅ Completed  
**Challenge Type:** Log Analysis / Anomaly Detection  
**Key Learning:** Identifying anomalies through deduplication and filtering
