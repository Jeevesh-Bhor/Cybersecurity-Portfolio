# BreachLabs: Ghost Track Level 19 - Piece By Piece

**Challenge:** Level 19 → 20  
**Date Completed:** August 31, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

Piece by Piece introduces automated credential assembly through scripting. The challenge requires connecting to a relay service that distributes password fragments indexed by number, then using a loop to systematically retrieve and reassemble all pieces. Success demonstrates understanding of bash loops, service interaction via netcat, and string concatenation.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost20` user  
**Context:** Service on port 30003 distributes credential one piece at a time by index  
**Constraint:** Must write a loop to collect and reassemble all pieces automatically

---

## Initial Access

### SSH Connection

Connected using password obtained from Level 18:
```
Target: 204.168.229.209:2222
User: ghost19
Access Method: SSH (password)
```

### Challenge Introduction

Challenge narrative stated:
- Service on port 30003 hands out credential one piece at a time, by index
- Don't fetch them by hand — write a loop to collect and reassemble
- Topic references: Bash loops (for loops, seq command)

---

## Service Discovery

### Relay Service Exploration

Connected to localhost port 30003 to understand service format:

```bash
nc localhost 30003
```

**Service Response:**
```
Format: <password> <index|count> (Level 19 relay service on port 30003.)
```

**Finding:** Service expects input format: `<password> <index|count>`

### Initial Credential Query

Tested service with credential string and index 0:

```bash
nc localhost 30003
[PASSWORD] 0
r
```

**Finding:** Service returned character `r` at index 0. Password appears to be `SU1D_Fl1p` (obtained from Level 18).

### Index Testing

Verified service returns sequential characters:

```bash
nc localhost 30003
[PASSWORD] 1
3
```

**Finding:** Index 1 returns character `3`. Service operates on index-based character retrieval.

---

## Automated Collection Script

### Script Creation

Created bash script to automate credential assembly:

```bash
#!/bin/bash
password="[PASSWORD]"
result=""
for i in $(seq 0 50); do
    char=$(printf '%s %s\n' "$password" "$i" | nc localhost 30003)
    if [ -z "$char" ]; then
        break
    fi
    echo "[$i] $char"
    result="${result}${char}"
done
echo "Credential: $result"
```

**Script Components:**
- `password="[PASSWORD]"` — Known password from previous level
- `result=""` — Initialize empty result string
- `for i in $(seq 0 50)` — Loop from index 0 to 50
- `char=$(printf '%s %s\n' "$password" "$i" | nc localhost 30003)` — Query service for character at index
- `if [ -z "$char" ]; then break` — Exit loop when no character returned
- `result="${result}${char}"` — Concatenate character to result
- Output each index and character for verification

### Script Execution

Made script executable and ran it:

```bash
chmod +x capture.sh
./capture.sh
```

**Output:**
```
Credential: [REDACTED_FLAG]
```

**Finding:** Loop successfully collected 26 characters from service and assembled complete credential.

---

## Attack Chain Summary

```
SSH Connection (ghost19, password auth)
         ↓
Challenge Context (service on port 30003, index-based credentials)
         ↓
Service Exploration (nc localhost 30003)
         ↓
Format Discovery (password + index required)
         ↓
Index Testing (confirmed character-by-character retrieval)
         ↓
Loop Script Creation (bash for loop, seq, nc)
         ↓
Automated Collection (iterate indices 0-50)
         ↓
String Concatenation (build result character by character)
         ↓
Credential Acquired
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **Bash For Loop** — `for i in $(seq 0 50)` iterates through index range
2. **Sequence Generation** — `seq` command generates numeric sequences
3. **Netcat (nc)** — Network utility connects to service and retrieves data
4. **Process Substitution** — Pipe password and index to nc command
5. **String Concatenation** — `result="${result}${char}"` builds output string
6. **Conditional Exit** — `if [ -z "$char" ]` breaks loop when empty response
7. **Variable Expansion** — Substitutes password and index into command
8. **Scriptable Service Interaction** — Automates manual query process

---

## Tools & Commands Used

```bash
# Netcat connection to service
nc localhost 30003

# Query service with password and index
printf '%s %s\n' "$password" "$i" | nc localhost 30003

# Sequence generation
seq 0 50

# Script execution
chmod +x capture.sh
./capture.sh
```

---

## Lessons Learned

1. **Service-Based Challenges** — Automated scripts more efficient than manual queries
2. **Loop Automation** — Bash loops enable systematic data collection
3. **String Building** — Concatenation assembles fragmented data
4. **Service Format Understanding** — Testing reveals expected input/output format
5. **Index-Based Retrieval** — Service designed for automated rather than manual access
6. **Scripting Efficiency** — Simple loop collects 26-piece credential in seconds

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- Bash Loops: https://tldp.org/LDP/Bash-Beginners-Guide/html/sect_09_01.html
- Bash Loops: https://tldp.org/LDP/abs/html/loops1.html
- Netcat: https://man.openbsd.org/nc

---

**Status:** ✅ Completed  
**Challenge Type:** Bash Scripting & Service Interaction  
**Key Learning:** Using bash loops to automate service queries and reassemble fragmented credentials
