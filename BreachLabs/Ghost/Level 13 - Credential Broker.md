# BreachLabs: Ghost Track Level 13 - Credential Broker

**Challenge:** Level 13 → 14  
**Date Completed:** August 31, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

Credential Broker introduces service-based credential exchange protocols. Rather than storing credentials in files, a daemon service on a specific port accepts the current token and exchanges it for the next token. Success requires discovering the service, understanding its protocol, and formulating the correct request using netcat.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost14` user  
**Context:** Credential-broker daemon running on TCP port 41310  
**Constraint:** Must connect to service, understand protocol, send correctly formatted request

---

## Initial Access

### SSH Connection

Connected using SSH key authentication obtained from Level 12:
```
Target: 204.168.229.209:2222
User: ghost13
Access Method: SSH key (id_ed25519)
```

### Challenge Introduction

Challenge narrative stated:
- Credential-broker daemon on TCP 41310
- Service trades next token for current token
- Must connect, read protocol specification, and ask correctly
- No client library available

---

## Port Discovery

### Port Enumeration

Performed port scan to locate credential-broker service:

```bash
nc -zv localhost 1-65535 2>&1 | grep succeeded
```

**Identified Open Ports:**
- Port 22 (SSH)
- Port 30003, 30100, 30101, 31339 (Various services)
- **Port 41310** (Target credential-broker)
- Port 41311, 41337 (Other services)
- Additional ports: 46960, 49213, 51846, 52128

**Finding:** Port 41310 confirmed open and accessible.

---

## Service Protocol Discovery

### Initial Connection

Connected to credential-broker service on port 41310:

```bash
nc localhost 41310
```

**Service Response:**
```
ghost credential-broker v1.2
usage: RETRIEVE <current-token>

DENIED. (Level 13 credential-broker, TCP 41310.)
Expected: RETRIEVE <current-token>. If you're at a different level you have the wrong port -- see your brief / scan local listeners.
```

**Protocol Analysis:**
- Service name: `ghost credential-broker v1.2`
- Required format: `RETRIEVE <current-token>`
- Current connection: Level 13 credential-broker
- Error indicates incorrect token or wrong port for user's level

---

## Token Acquisition

### Current Token Source

Retrieved current token from Level 12 flag file:

```
K3y_N0t_P4ss
```

**Finding:** Token from previous level serves as current token for this level's broker.

---

## Credential Exchange

### Formatted Request

Sent properly formatted request to credential-broker:

```bash
nc localhost 41310
RETRIEVE K3y_N0t_P4ss
```

**Service Response:**
```
ghost credential-broker v1.2
usage: RETRIEVE <current-token>
OK. Next token: [REDACTED_FLAG]
```

**Finding:** Service accepted token and returned next token in exchange. Format matched protocol specification exactly.

---

## Attack Chain Summary

```
SSH Connection (ghost13, key auth)
         ↓
Port Enumeration (netcat scan 1-65535)
         ↓
Credential-Broker Discovery (port 41310)
         ↓
Service Connection (netcat localhost 41310)
         ↓
Protocol Analysis (RETRIEVE <token> format)
         ↓
Current Token Identification (from Level 12 flag)
         ↓
Token Exchange Request
         ↓
Service Response (OK. Next token)
         ↓
Credential Extraction
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **Port Scanning** — Using netcat to enumerate open ports across range
2. **Service Discovery** — Identifying credential-broker on non-standard port
3. **Protocol Analysis** — Reading service responses to understand expected format
4. **Request Formatting** — Constructing requests matching service specification
5. **Token Exchange** — Trading current credential for next credential
6. **Credential Chaining** — Previous level's credential enables current level access
7. **Netcat Communication** — Using netcat for direct TCP service interaction

---

## Protocol Specification

**Service:** ghost credential-broker v1.2  
**Port:** 41310  
**Protocol:** Custom text-based  
**Request Format:** `RETRIEVE <current-token>`  
**Response:** `OK. Next token: <new-token>` (on success)  
**Error Response:** Service rejects request and explains expected format  

---

## Tools & Commands Used

```bash
# SSH access (key-based)
ssh -i id_ed25519 ghost13@204.168.229.209 -p 2222

# Port enumeration
nc -zv localhost 1-65535 2>&1 | grep succeeded

# Service connection
nc localhost 41310

# Token exchange (manual interaction)
# Connected to service and typed: RETRIEVE [REDACTED_FLAG]
```

---

## Lessons Learned

1. **Service-Based Credential Management** — Credentials distributed through daemon services
2. **Protocol Understanding** — Service responses provide format guidance
3. **Token Chaining** — Each level's credential enables next level authentication
4. **Netcat Effectiveness** — Direct service interaction without specialized clients
5. **Non-Standard Ports** — Credential services may run on non-HTTP ports
6. **Error Messages as Guides** — Denial messages explain expected format

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- Netcat: https://man.openbsd.org/nc
- HTTP/Protocol Client: https://man7.org/linux/man-pages/man1/curl.1.html

---

**Status:** ✅ Completed  
**Challenge Type:** Service Protocol / Credential Exchange  
**Key Learning:** Token-based credential exchange through daemon services
