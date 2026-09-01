# BreachLabs: Ghost Track Level 12 - Harvested Key

**Challenge:** Level 12 → 13  
**Date Completed:** August 30, 2026  
**Platform:** [BreachLabs](https://breachlab.org)

---

## Summary

Harvested Key introduces SSH public key authentication as an alternative to password-based access. Rather than a password, the challenge requires locating a private SSH key harvested from a compromised jump host and using it to authenticate as the next level user. Success demonstrates understanding of SSH key-based authentication and private key usage.

---

## Challenge Objective

**Goal:** Log in as `ghost13` using SSH private key authentication  
**Context:** Private key obtained from compromised jump host (svc-deploy account)  
**Constraint:** Must use key-based auth; password method unavailable

---

## Initial Access

### SSH Connection

Connected using credentials obtained from Level 11:
```
Target: 204.168.229.209:2222
User: ghost12
Access Method: SSH (password)
```

### Challenge Introduction

Challenge narrative stated:
- No password for next account
- Private key pulled from jump host instead
- Ghost13 still trusts the harvested key

---

## File Discovery

### Home Directory Navigation

Located `loot/` directory in ghost12 home:

```bash
cd loot
ls -alps
```

**Directory Contents:**
- `NOTES.txt` — Context documentation (113 bytes)
- `id_ed25519` — Private key file (411 bytes)
- `id_ed25519.pub` — Public key file (100 bytes)

---

## Key Context Analysis

### Notes File

Examined `NOTES.txt`:

```
Pulled from jump-01:/home/svc-deploy/.ssh/ during the Sept op.
No passphrase set. ghost13 still trusts this key.
```

**Findings:**
- Key harvested from jump host (jump-01)
- Originally belonged to `svc-deploy` service account
- No passphrase protection on private key
- Ghost13 account configured to accept this key

---

## Private Key Examination

### Key File Contents

Examined `id_ed25519` private key:

```
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
QyNTUxOQAAACDKv4pzydCN63LdoPM1XTvkl+79ry9SZ2GH1t8lKOyjYAAAAJgHOhpDBzoa
QwAAAAtzc2gtZWQyNTUxOQAAACDKv4pzydCN63LdoPM1XTvkl+79ry9SZ2GH1t8lKOyjYA
AAAEDXoLsvSghHrxwRpGMrpVBPPGsSMDTw6ugPRM9eaBWstsq/inPJ0I3rct2g8zVdO+SX
7v2vL1JnYYfW3yUo7KNgAAAAEnN2Yy1kZXBsb3lAanVtcC0wMQECAw==
-----END OPENSSH PRIVATE KEY-----
```

**Key Specifications:**
- Format: OpenSSH private key format
- Algorithm: ED25519 (elliptic curve cryptography)
- Comment: `svc-deploy@jump-01`
- Passphrase: None (unencrypted)

---

## SSH Key-Based Authentication

### SSH Connection Using Private Key

Connected to ghost13 using harvested private key:

```bash
ssh -i id_ed25519 ghost13@204.168.229.209 -p 2222
```

**Authentication Method:**
- Private key (`id_ed25519`) for authentication
- Server matches key against ghost13's authorized_keys
- No password prompt required
- Direct shell access granted

---

## Credential Discovery

### Ghost13 Home Directory

Listed ghost13 home directory contents:

```bash
ls -alps
```

**Key File Located:**
- `flag` — Credential file (13 bytes, permissions: `-r--------`)

### Flag Extraction

Examined flag file:

```bash
cat flag
```

**Output:**
```
[REDACTED_FLAG]
```

**Finding:** Flag file contains credential for next level in flag format.

---

## Attack Chain Summary

```
SSH Connection (ghost12, password auth)
         ↓
Loot Directory Discovery
         ↓
Private Key File Identification (id_ed25519)
         ↓
Context Analysis (NOTES.txt)
         ↓
Key Specifications Review (OpenSSH, ED25519, unencrypted)
         ↓
SSH Key-Based Authentication
         │
         └─→ ssh -i <private_key> ghost13@host
         ↓
Ghost13 Access Granted
         ↓
Flag File Discovery
         ↓
Credential Extraction
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **SSH Key Harvesting** — Obtaining private keys from compromised systems
2. **Key-Based Authentication** — Using SSH private keys instead of passwords
3. **ED25519 Algorithm** — Modern elliptic curve cryptography for SSH
4. **OpenSSH Key Format** — Standard format for SSH private keys
5. **Unencrypted Keys** — Private keys without passphrase protection
6. **Key Trust** — Servers configured to accept specific public keys
7. **Privilege Escalation** — Using harvested keys to access higher-privilege accounts

---

## SSH Key Details

**Algorithm:** ED25519  
**Key Type:** Private key (secret)  
**Associated Public Key:** `id_ed25519.pub`  
**Passphrase:** None  
**Format:** OpenSSH private key format  
**Source Account:** `svc-deploy@jump-01`  
**Target Account:** `ghost13`  

---

## Tools & Commands Used

```bash
# Directory navigation
cd loot

# Directory listing
ls -alps

# File examination
cat NOTES.txt
cat id_ed25519
cat flag

# SSH key-based authentication
ssh -i id_ed25519 ghost13@204.168.229.209 -p 2222
```

---

## Lessons Learned

1. **Key Harvesting Impact** — Compromised service accounts expose all trusted systems
2. **Unencrypted Keys Risk** — Private keys without passphrase immediately usable by attackers
3. **Cross-System Trust** — Keys trusted across multiple systems enable lateral movement
4. **Service Account Compromise** — Service accounts often have broader access than user accounts
5. **Key-Based Auth Advantage** — More secure than passwords but vulnerable to key theft
6. **Lateral Movement** — Harvested keys provide direct access to trusted systems

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- SSH Protocol: https://man.openbsd.org/ssh
- SSH Key Generation: https://man.openbsd.org/ssh-keygen
- Public Key Authentication: https://www.ssh.com/academy/ssh/public-key-authentication

---

**Status:** ✅ Completed  
**Challenge Type:** SSH Key Authentication / Lateral Movement  
**Key Learning:** Harvested private keys enable direct lateral movement across systems
