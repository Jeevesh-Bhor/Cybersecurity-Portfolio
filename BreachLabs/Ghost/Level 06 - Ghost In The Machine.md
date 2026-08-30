# BreachLabs: Ghost Track Level 6 - Ghost in the Machine

**Challenge:** Level 6 → 7  
**Date Completed:** August 30, 2026  
**Platform:** [BreachLabs](https://breachlab.org/)

---

## Summary

Ghost in the Machine demonstrates information exposure through environment variables. Rather than storing secrets in files or on disk, KAEL stored encoded credentials in shell environment variables, believing the shell would not retain them. The challenge requires examining environment variables and decoding base64-encoded strings to retrieve the credential.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost7` user  
**Context:** Secrets stored in environment variables rather than on disk  
**Constraint:** Credentials are base64-encoded within environment variable values

---

## Initial Access

### SSH Connection

Connected using credentials obtained from Level 5:
```
Target: 204.168.229.209:2222
User: ghost6
Access Method: SSH
```

### Challenge Introduction

Challenge narrative stated:
- KAEL stopped writing secrets to disk after breach
- Believed shell would forget secrets
- Shell does not forget them

---

## Home Directory Analysis

### Command History

Checked shell history:
```bash
history
```

**Output:** Showed only 2 commands (ls -alps, history)

### Environment Variable Examination

Executed `env` command to examine environment variables:
```bash
env
```

---

## Environment Variable Analysis

### Variables Containing Encoded Data

Examined all environment variables returned by `env` command. Identified three variables containing base64-encoded strings:

**Variable 1: API_DIGEST**
```
API_DIGEST=M252X0wzNGtzXzN2M3J5dGgxbmc=
```

**Variable 2: TRACE_SALT**
```
TRACE_SALT=bW9uaXRvcmluZ19rZXlfZGVsdGE3
```

**Variable 3: RUNTIME_TOKEN**
```
RUNTIME_TOKEN=c3lzdGVtX3Rva2VuX2dhbW1hX3Yz
```

### Base64 Decoding

Used CyberChef to decode each base64 string:

**API_DIGEST Decoded:**
```
M252X0wzNGtzXzN2M3J5dGgxbmc= → 3nv_L34ks_3v3ryth1ng
```

**TRACE_SALT Decoded:**
```
bW9uaXRvcmluZ19rZXlfZGVsdGE3 → monitoring_key_delta7
```

**RUNTIME_TOKEN Decoded:**
```
c3lzdGVtX3Rva2VuX2dhbW1hX3Yz → system_token_gamma_v3
```

### Credential Identification

Tested decoded values against ghost7 login. Value decoded from `API_DIGEST` variable successfully authenticated as password for ghost7 user.

---

## Attack Chain Summary

```
SSH Connection (ghost6)
         ↓
History Examination (minimal output)
         ↓
Environment Variable Enumeration
         ↓
Base64-Encoded String Identification
         ↓
Base64 Decoding (CyberChef)
         ↓
Decoded Value Testing
         ↓
Credential Validation
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **Environment Variables** — Examining shell environment for stored data
2. **Base64 Encoding** — Recognizing and decoding base64-encoded strings
3. **Information Exposure** — Credentials exposed through environment rather than files
4. **Enumeration** — Systematic examination of all environment variables
5. **Credential Discovery** — Testing decoded values to identify correct password

---

## Tools & Commands Used

```bash
# SSH access
ssh ghost6@204.168.229.209 -p 2222

# History examination
history

# Environment variable enumeration
env

# Base64 decoding
# (performed using CyberChef tool)
```

---

## Lessons Learned

1. **Environment Variable Exposure** — Shell environment variables can store sensitive credentials and are easily accessible
2. **Encoding is Not Encryption** — Base64-encoded strings are trivially decoded; not secure for credential storage
3. **Shell History Persistence** — Shell does not forget environment variables or executed commands
4. **Plaintext Alternatives** — Avoiding file storage does not prevent credential exposure if environment is examined
5. **12-Factor Configuration** — Environment-based configuration requires careful handling of secrets

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- Environment Variables: `env(1)` man pages - https://man7.org/linux/man-pages/man1/env.1.html
- Base64 Encoding: `base64(1)` man pages - https://man7.org/linux/man-pages/man1/base64.1.html
- 12-Factor App Configuration: https://12factor.net/config

---

**Status:** ✅ Completed  
**Challenge Type:** Environment Variable Analysis / Base64 Decoding  
**Key Learning:** Credentials in shell environment are easily discoverable
