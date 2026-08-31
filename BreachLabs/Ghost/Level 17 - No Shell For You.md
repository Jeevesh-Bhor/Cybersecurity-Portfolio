# BreachLabs: Ghost Track Level 17 - No Shell For You

**Challenge:** Level 17 → 18  
**Date Completed:** August 31, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

Commands Only introduces restricted shell access and non-interactive command execution. The challenge uses a relay account that disables interactive shell sessions but still permits execution of commands passed through SSH. Success requires understanding how to execute commands remotely without obtaining an interactive shell prompt.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost18` user  
**Context:** Relay account with disabled interactive shell  
**Constraint:** Cannot establish interactive shell session; must execute commands non-interactively

---

## Initial Access

### SSH Connection

Attempted to connect using password obtained from Level 16:
```
Target: 204.168.229.209:2222
User: ghost17
Access Method: SSH (password)
```

### Challenge Introduction

Challenge narrative stated:
- Automated relay account
- No interactive shell available
- Still runs one-off commands over SSH
- Must retrieve next operative's credentials without opening prompt

---

## Interactive Shell Attempt

### Initial Connection Failure

Attempted standard SSH connection:

```bash
ssh ghost17@204.168.229.209 -p 2222
```

**Result:**
```
(relay17: interactive sessions are disabled. Jobs run non-interactively.)
Connection to 204.168.229.209 closed.
```

**Finding:** Interactive shell access blocked. Connection immediately closed by relay account.

---

## Non-Interactive Command Execution

### SSH Command Syntax

Executed commands by passing them as arguments to SSH command:

```bash
ssh ghost17@204.168.229.209 -p 2222 "ls -alps"
```

**Output:**
```
total 52
8 drwx------ 1 ghost17 ghost17 4096 Aug 28 17:01 ./
8 drwxr-xr-x 1 root    root    4096 Jun 22 13:41 ../
0 -rw------- 1 ghost17 ghost17    0 Aug 28 14:26 .bash_history
8 -rw-r--r-- 1 ghost17 ghost17  220 Jan  6  2022 .bash_logout
8 -rw-r--r-- 1 ghost17 ghost17 3771 Jan  6  2022 .bashrc
4 drwx------ 2 ghost17 ghost17 4096 Aug 28 17:01 .cache/
8 -rw-r--r-- 1 ghost17 ghost17  807 Jan  6  2022 .profile
8 -r-------- 1 ghost17 ghost17   13 Jun 22 13:41 handoff
```

**Finding:** Commands passed in quotes execute successfully without interactive shell prompt.

### File Discovery

Located credential file in home directory:

**File:** `handoff`
- Permissions: `-r--------` (read-only, user access only)
- Size: 13 bytes
- Owner: ghost17

---

## Credential Retrieval

### File Access Command

Executed command to retrieve handoff file contents:

```bash
ssh ghost17@204.168.229.209 -p 2222 "cat handoff"
```

**Output:**
```
[REDACTED_FLAG]
```

**Finding:** Credential successfully retrieved through non-interactive command execution.

---

## Attack Chain Summary

```
SSH Connection Attempt
         ↓
Interactive Shell Denied (relay account restriction)
         ↓
Connection Closed
         ↓
Non-Interactive Command Syntax Discovery
         ↓
Command Execution via SSH Argument
         │
         ├─> ssh <host> "ls -alps"
         │
         └─> ssh <host> "cat handoff"
         ↓
Directory Listing (file discovery)
         ↓
Handoff File Location (13 bytes, restricted permissions)
         ↓
File Contents Retrieval
         ↓
Credential Extraction
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **SSH Remote Command Execution** — Passing commands as arguments to SSH for non-interactive execution
2. **Relay Account** — Accounts configured to run commands without interactive shell
3. **Restricted Shell Access** — Interactive sessions disabled while command execution enabled
4. **Command String Quoting** — Enclosing commands in quotes for proper argument passing
5. **Non-Interactive Access** — Retrieving information without establishing shell prompt
6. **Single Command Limitations** — Each SSH invocation executes one command and exits

---

## SSH Command Execution Details

**Standard Interactive Connection:**
```bash
ssh ghost17@204.168.229.209 -p 2222
# Result: Interactive shell denied, connection closed
```

**Non-Interactive Command Execution:**
```bash
ssh ghost17@204.168.229.209 -p 2222 "command"
# Result: Command executes, output returned, connection closes
```

**Key Difference:** Appending command in quotes bypasses shell restriction.

---

## Tools & Commands Used

```bash
# SSH interactive attempt (fails)
ssh ghost17@204.168.229.209 -p 2222

# SSH non-interactive command execution
ssh ghost17@204.168.229.209 -p 2222 "ls -alps"
ssh ghost17@204.168.229.209 -p 2222 "cat handoff"
```

---

## Lessons Learned

1. **Relay Accounts** — Service accounts designed for automated command execution
2. **Shell Restrictions** — Security controls can disable interactive access while enabling commands
3. **SSH Flexibility** — SSH supports both interactive and non-interactive command modes
4. **Syntax Importance** — Command placement and quoting determines execution mode
5. **Workaround Identification** — When interactive access denied, explore non-interactive alternatives
6. **Single-Use Access** — Relay accounts often execute one command per connection

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- SSH Man Pages: https://man.openbsd.org/ssh

---

**Status:** ✅ Completed  
**Challenge Type:** SSH Command Execution / Restricted Shell Access  
**Key Learning:** Non-interactive command execution for relay accounts  
