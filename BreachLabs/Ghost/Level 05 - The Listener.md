# BreachLabs: Ghost Track Level 5 - The Listener

**Challenge:** Level 5 → 6  
**Date Completed:** August 30, 2026  
**Platform:** [BreachLabs](https://breachlab.org/) 

---

## Summary

The Listener introduces network service enumeration and port scanning. Rather than accessing files on disk, KAEL implemented credential distribution through network services listening on multiple ports. The challenge requires identifying which ports are active, understanding their protocols, and sending correct authentication to retrieve credentials.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost6` user  
**Context:** KAEL deployed service-based credential storage on multiple ports instead of file-based storage  
**Constraint:** Locked down standard network tools; must use `nc` and `curl` to interact with services

---

## Initial Access

### SSH Connection

Connected using credentials obtained from Level 4:
```
Target: 204.168.229.209:2222
User: ghost5
Access Method: SSH
```

### Challenge Introduction

README file provided:
- KAEL left a service running on the box
- Two ports: one tells how to talk, other answers if correct word sent
- Standard tools (`ss`, `netstat`) are locked down
- `nc` and `curl` available for use
- Need to find listener, read message, answer correctly

---

## Port Discovery

### Netcat Port Scanning

Used netcat to scan all ports:
```bash
nc -zv localhost 1-65535 2>&1 | grep succeeded
```

**Identified Open Ports:**
- Port 22 (SSH)
- Port 30003
- Port 30100
- Port 30101
- Port 31339
- Port 34008
- Port 35722
- Port 41310
- Port 41311
- Port 41337
- Port 49213
- Port 52268

### Nmap Verification

Confirmed open ports using nmap (scan showed 9 open ports on 127.0.0.1).

---

## Service Enumeration

### Port 30100: Information Channel

Connected to port 30100:
```bash
nc localhost 30100
```

**Response:**
```
GHOST PROTOCOL — CHANNEL A
─────────────────────────────────────

This channel is informational only.

Authentication token: GHOST
Secure channel: port 30101

Send the token to receive your credential.
```

**Finding:** Port 30100 provided authentication token and directs to secure channel on port 30101.

### Port 30101: Secure Channel

Initial connection attempt:
```bash
nc localhost 30101
```

**Response:** "Invalid token. Connection logged."

**Second attempt with token:**
```bash
nc localhost 30101
AUTHENTICATE: GHOST
```

**Response:**
```
Credential: P0rts_N3v3r_L13
```

**Finding:** Port 30101 accepted token "GHOST" and returned credential with flag-formatted content.

### Port 31339: Gatekeeper Service

Connected to port 31339:
```bash
nc localhost 31339
```

**Response:**
```
Ghost Graduation Gatekeeper
===========================
Submit three shards in one line, pipe-separated, no spaces:
  SHARD1:<val>|SHARD2:<val>|SHARD3:<val>
```

**Finding:** Port expected three shards in specific format but credential was obtained from port 30101.

### Other Ports Investigated

- **Port 30003:** Empty response
- **Port 34008:** No response
- **Port 35722:** No response
- **Port 41310:** "ghost credential-broker v1.2" requesting "RETRIEVE <current-token>"
- **Port 41311:** Empty response (hangs)
- **Port 41337:** Bonus content referencing PHANTOM track (30 levels of Linux privesc)
- **Port 49213:** Single character response
- **Port 52268:** No response

### Curl Testing

Attempted HTTP protocol on various ports:
```bash
curl http://localhost:30003/
curl http://localhost:30100/
curl http://localhost:41310/
```

**Result:** Most returned HTTP/0.9 errors; some returned empty replies. Services used custom protocols, not HTTP.

---

## Attack Chain Summary

```
Port Discovery (netcat scan)
         ↓
Port Verification (nmap scan)
         ↓
Information Channel Access (port 30100)
         ↓
Token Extraction ("GHOST")
         ↓
Secure Channel Access (port 30101)
         ↓
Token Submission (AUTHENTICATE: GHOST)
         ↓
Credential Retrieval
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **Port Scanning** — Using netcat (`nc -zv`) and nmap to discover listening services
2. **Protocol Recognition** — Understanding custom protocols vs standard HTTP
3. **Service Interaction** — Using netcat to communicate with non-HTTP services
4. **Token-Based Authentication** — Multi-stage auth (info channel → token → secure channel)
5. **Network Enumeration** — Systematically testing ports to identify functionality

---

## Tools & Commands Used

```bash
# Port discovery
nc -zv localhost 1-65535 2>&1 | grep succeeded

# Port verification
nmap -p 1-65535 localhost

# Service interaction
nc localhost 30100
nc localhost 30101

# HTTP testing (returned errors)
curl http://localhost:30003/
curl http://localhost:41310/
```

---

## Lessons Learned

1. **Service-Based Secrets** — Credentials can be distributed through network services rather than files
2. **Protocol Diversity** — Not all services use HTTP; custom protocols require netcat interaction
3. **Multi-Stage Authentication** — Information channel provides token for secure channel access
4. **Port Significance** — README identified two ports; finding correct pair was essential
5. **Tool Constraints** — Locked-down system tools require alternative enumeration methods (netcat vs netstat)

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- Netcat: `nc(1)` man pages - https://manpages.debian.org/bookworm/netcat-openbsd/nc.1.en.html
- Nmap: `nmap(1)` man pages - https://man7.org/linux/man-pages/man1/nmap.1.html

---

**Status:** ✅ Completed  
**Challenge Type:** Port Scanning / Network Service Enumeration  
**Key Learning:** Custom protocol interaction and multi-stage authentication  
**Bonus:** Discovered PHANTOM track (Level 6+ series) containing 30 levels of Linux privilege escalation

---

**Note:** Port 31339 (Gatekeeper Service) remained unsolved; it requires three shards that may be obtained through other means in a future level.
