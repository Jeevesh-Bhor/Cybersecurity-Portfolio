# BreachLabs: Ghost Track Level 14 - TLS Only

**Challenge:** Level 14 → 15  
**Date Completed:** August 31, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

TLS Only introduces encrypted service communication using TLS/SSL. The challenge mirrors the previous credential-broker service, but requires TLS encryption. Rather than plain netcat, the connection requires OpenSSL's `s_client` tool to establish encrypted communication and exchange credentials.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost15` user  
**Context:** Credential-exchange service on TCP 41311 using TLS encryption  
**Constraint:** Plain netcat cannot communicate with TLS endpoint; requires encryption-aware client

---

## Initial Access

### SSH Connection

Connected using password obtained from Level 13:
```
Target: 204.168.229.209:2222
User: ghost14
Access Method: SSH (password)
```

### Challenge Introduction

Challenge narrative stated:
- Same trade as credential-broker (Level 13)
- Control endpoint on TCP 41311 only speaks TLS
- Plain netcat will not work
- Must find CLI that speaks TLS

---

## TLS Service Connection

### OpenSSL s_client Connection

Connected to TLS-encrypted service:

```bash
openssl s_client -connect localhost:41311
```

**TLS Handshake Details:**
- Protocol: TLSv1.3
- Cipher: TLS_AES_256_GCM_SHA384
- Certificate: Self-signed (CN = ghost-internal)
- Certificate issued: June 22, 2026
- Certificate expires: June 19, 2036
- Server key: RSA, 2048 bits
- Verification: Self-signed certificate (expected verification error)

**Handshake Result:** Successfully established encrypted TLS connection

---

## Credential Exchange

### Service Prompt

After TLS handshake completed, service displayed:

```
Send the current level password:
```

**Finding:** Service ready to receive current token for exchange.

### Token Submission

Sent credential from previous level:

```
N3tc4t_D3l1v3r
```

### Service Response

Service validated token and returned next credential:

```
Correct! Next password: [REDACTED_FLAG]
```

**Finding:** TLS-encrypted exchange completed successfully; next token received.

---

## Attack Chain Summary

```
SSH Connection (ghost14, password auth)
         ↓
TLS Service Discovery (port 41311)
         ↓
OpenSSL s_client Connection
         ↓
TLS Handshake Execution
         ↓
Certificate Verification (self-signed acceptable)
         ↓
Encrypted Channel Established
         ↓
Service Prompt Reception
         ↓
Current Token Submission
         ↓
Token Validation
         ↓
Next Token Receipt
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **TLS/SSL Encryption** — Securing communication through encryption
2. **OpenSSL s_client** — Command-line TLS/SSL client for testing encrypted services
3. **Certificate Verification** — Understanding self-signed certificates and verification errors
4. **TLS Handshake** — Establishing secure connection before data exchange
5. **Self-Signed Certificates** — Certificates not issued by trusted CA but still usable for encryption
6. **Encrypted Protocol Communication** — Using same protocol over TLS instead of plain text
7. **Tool Selection** — Choosing appropriate client (openssl vs netcat) for service type

---

## TLS Connection Details

**Certificate Information:**
- Subject: CN = ghost-internal
- Issuer: CN = ghost-internal (self-signed)
- Public Key: RSA, 2048 bits
- Signature Algorithm: RSA-SHA256
- Validity: June 22, 2026 - June 19, 2036

**TLS Session:**
- Version: TLSv1.3
- Cipher Suite: TLS_AES_256_GCM_SHA384
- Server Temp Key: X25519, 253 bits
- Session Ticket Lifetime: 7200 seconds

**Verification:** Self-signed certificate (verification error code 18, expected)

---

## Comparison to Level 13

**Level 13 (Credential Broker):**
- Port: 41310
- Protocol: Plain text TCP
- Client: netcat (nc)
- Command: `nc localhost 41310`

**Level 14 (TLS Only):**
- Port: 41311
- Protocol: TLS-encrypted TCP
- Client: openssl s_client
- Command: `openssl s_client -connect localhost:41311`

**Similarity:** Same credential exchange protocol, different transport encryption

---

## Tools & Commands Used

```bash
# SSH access
ssh ghost14@204.168.229.209 -p 2222

# TLS service connection
openssl s_client -connect localhost:41311

# Manual interaction within TLS session
# Sent: N3tc4t_D3l1v3r
# Received: Correct! Next password: [REDACTED_FLAG]
```

---

## Lessons Learned

1. **Encryption Layer** — Services can implement same protocol with different encryption layers
2. **Tool Versatility** — Different tools required for plain vs encrypted communication
3. **Certificate Handling** — Self-signed certificates acceptable for testing/internal services
4. **TLS Handshake** — Encryption negotiation happens automatically before data exchange
5. **Service Robustness** — TLS protects credentials in transit but protocol logic remains same
6. **Error Tolerance** — Benign errors (self-signed cert) expected in testing environments

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- OpenSSL Manual: https://man.openbsd.org/openssl
- OpenSSL s_client: https://www.openssl.org/docs/man3.0/man1/openssl-s_client.html

---

**Status:** ✅ Completed  
**Challenge Type:** TLS/SSL Communication / Encrypted Service Interaction  
**Key Learning:** TLS encryption applied to same protocol as previous level 
