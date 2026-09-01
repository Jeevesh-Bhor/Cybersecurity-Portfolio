# BreachLabs: Ghost Track Level 15 - Ephemeral Port

**Challenge:** Level 15 → 16  
**Date Completed:** August 31, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

Ephemeral Port introduces port discovery within constrained ranges. The challenge requires scanning a specific port range (49000-49500) to locate a single TLS-encrypted service among closed ports, then connecting and exchanging credentials. Success demonstrates port scanning methodology and service discovery techniques.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost16` user  
**Context:** TLS service on single ephemeral port within 49000-49500 range  
**Constraint:** Most ports in range closed; must locate and connect to correct port

---

## Initial Access

### SSH Connection

Connected using password obtained from Level 14:
```
Target: 204.168.229.209:2222
User: ghost15
Access Method: SSH (password)
```

### Challenge Introduction

Challenge narrative stated:
- Service on ephemeral port in 49000-49500 range
- Service speaks TLS
- Rest of range is closed
- Must scan, find service, greet it

---

## Service Discovery

### Port Range Scanning

Scanned specified ephemeral port range to locate active service:

```bash
nc -zv localhost 49000-49500 2>&1 | grep succeeded
```

**Scan Output:**
```
Connection to localhost (127.0.0.1) 49213 port [tcp/*] succeeded!
```

### Ephemeral Port Identification

Located TLS service on ephemeral port within specified range:

**Service Port:** 49213

**Finding:** Single active service discovered in 49000-49500 range. Port scanning confirmed port 49213 as only open port in ephemeral range. Service accessible via OpenSSL s_client.

---

## TLS Connection

### OpenSSL s_client Connection

Connected to discovered service:

```bash
openssl s_client -connect localhost:49213
```

**TLS Handshake Details:**
- Protocol: TLSv1.3
- Cipher: TLS_AES_256_GCM_SHA384
- Certificate: Self-signed (CN = ghost-internal)
- Certificate issued: June 22, 2026
- Certificate expires: June 19, 2036
- Server key: RSA, 2048 bits

**Connection Status:** Encrypted TLS channel established successfully

---

## Credential Exchange

### Service Prompt

After TLS handshake:

```
Send the current level password:
```

### Token Submission

Sent credential from previous level:

```
TLS_0r_N0th1ng
```

### Service Response

Service validated and exchanged token:

```
Correct! Next password: [REDACTED_FLAG]
```

**Finding:** Credential exchange completed successfully over TLS connection.

---

## Attack Chain Summary

```
SSH Connection (ghost15, password auth)
         ↓
Ephemeral Port Range Definition (49000-49500)
         ↓
Netcat Port Range Scan (nc -zv localhost 49000-49500)
         ↓
Scan Output Filtering (grep succeeded)
         ↓
Active Port Identification (port 49213)
         ↓
OpenSSL s_client Connection (localhost:49213)
         ↓
TLS Handshake (TLSv1.3, self-signed certificate)
         ↓
Encrypted Channel Established
         ↓
Service Prompt Reception
         ↓
Current Token Submission (TLS_0r_N0th1ng)
         ↓
Token Validation
         ↓
Next Token Receipt
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **Ephemeral Port Range** — Dynamic ports typically assigned in 49000-65535 range
2. **Port Scanning** — Systematic enumeration of port range to locate services
3. **Service Discovery** — Identifying single active service among closed ports
4. **TLS Connection** — Establishing encrypted communication to discovered service
5. **Constrained Scanning** — Limiting scan scope to specified range reduces noise
6. **Service Identification** — TLS protocol signature distinguishes encrypted services

---

## Service Details

**Port:** 49213 (within 49000-49500 ephemeral range)  
**Protocol:** TLS/SSL  
**Protocol Version:** TLSv1.3  
**Cipher:** TLS_AES_256_GCM_SHA384  
**Certificate:** Self-signed, CN = ghost-internal  

---

## Comparison to Previous Levels

**Level 13 (Credential Broker):**
- Known port: 41310
- Plain text protocol
- No scanning required

**Level 14 (TLS Only):**
- Known port: 41311
- TLS-encrypted
- No scanning required

**Level 15 (Ephemeral Port):**
- Unknown port in range: 49000-49500
- TLS-encrypted
- Port scanning required
- Service discovery component added

---

## Tools & Commands Used

```bash
# SSH access
ssh ghost15@204.168.229.209 -p 2222

# Port range scanning
nc -zv localhost 49000-49500 2>&1 | grep succeeded

# TLS service connection
openssl s_client -connect localhost:49213

# Manual interaction within TLS session
# Sent: TLS_0r_N0th1ng
# Received: Correct! Next password: [REDACTED_FLAG]
```

---

## Lessons Learned

1. **Dynamic Port Assignment** — Services can run on ephemeral ports rather than fixed ports
2. **Range-Based Scanning** — Limiting scan scope improves efficiency
3. **Service Differentiation** — TLS vs plain-text services distinguishable during scanning
4. **Port Discovery First** — Must locate service before attempting connection
5. **Protocol Consistency** — Same TLS credential-exchange protocol as Level 14
6. **Scanning Necessity** — Port knowledge cannot be assumed; active discovery required

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- Nmap Manual: https://nmap.org/book/man.html
- Netcat Manual: https://man.openbsd.org/nc
- OpenSSL s_client: https://www.openssl.org/docs/man3.0/man1/openssl-s_client.html

---

**Status:** ✅ Completed  
**Challenge Type:** Port Discovery / Service Scanning  
**Key Learning:** Locating services through port enumeration in ephemeral range  
