# BreachLabs: Ghost Track Level 7 - Lost in Translation

**Challenge:** Level 7 → 8  
**Date Completed:** August 30, 2026  
**Platform:** [BreachLabs](https://breachlab.org/)

---

## Summary

Lost in Translation introduces multiple layers of encoding. The challenge requires examining a transmission file containing hexadecimal dump data, recognizing the encoded format, and successively decoding through multiple encoding layers to retrieve the final credential.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost8` user  
**Context:** Transmission file contains multi-layered encoding  
**Constraint:** Credential requires decoding from hex dump to base64 to plaintext

---

## Initial Access

### SSH Connection

Connected using credentials obtained from Level 6:
```
Target: 204.168.229.209:2222
User: ghost7
Access Method: SSH
```

### Challenge Introduction

Challenge narrative stated:
- KAEL said one layer was never enough
- Nothing sent was just one thing
- Transmission file isn't what it looks like

---

## File Discovery

### Transmission File Contents

Located `transmission.dat` file in home directory:

```bash
cat transmission.dat
```

**Output:**
```
00000000: 5244 4e6a 4d47 517a 587a 4279 5830 5178  RDNjMGQzXzByX0Qx
00000010: 4d77 3d3d 0a                             Mw==.
```

**Finding:** File contained hexadecimal dump format output, indicating hex-encoded data.

---

## Hex Dump Analysis

### Format Recognition

Output displayed:
- Hex offset (00000000, 00000010)
- Hexadecimal byte representation (5244 4e6a, etc.)
- ASCII character representation (RDNjMGQz, etc.)

### Hex Data Extraction

Extracted printable characters from hex dump:
- First line ASCII: `RDNjMGQzXzByX0Qx`
- Second line ASCII: `Mw==`

**Combined:** `RDNjMGQzXzByX0QxMw==`

---

## Multi-Layer Decoding

### Layer 1: Base64 Decoding

Identified combined string as base64-encoded data:
```
RDNjMGQzXzByX0QxMw==
```

Decoded using CyberChef:
```
RDNjMGQzXzByX0QxMw== → D3c0d3_0r_D13
```

### Credential Validation

Tested decoded value against ghost8 login. Successfully authenticated with password obtained from base64 decoding.

---

## Attack Chain Summary

```
SSH Connection (ghost7)
         ↓
File Discovery (transmission.dat)
         ↓
Hex Dump Recognition
         ↓
ASCII Character Extraction
         ↓
String Concatenation (two lines joined)
         ↓
Base64 String Identification
         ↓
Base64 Decoding
         ↓
Credential Validation
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **Hex Dump Format** — Recognizing xxd/hexdump output containing both hex and ASCII
2. **Character Extraction** — Recovering printable text from hex dump ASCII column
3. **Multi-Layer Encoding** — Understanding that credentials can use multiple encoding stages
4. **Base64 Decoding** — Decoding base64 strings from extracted hex dump data
5. **Data Reconstruction** — Joining fragmented encoded data to form complete string

---

## Tools & Commands Used

```bash
# SSH access
ssh ghost7@204.168.229.209 -p 2222

# File examination
ls -alps
cat transmission.dat

# Data processing
# (Hex dump interpretation and base64 decoding performed using CyberChef)
```

---

## Lessons Learned

1. **Hex Dump Output** — Hexadecimal dumps contain both hex representation and ASCII; ASCII column may contain encoded data
2. **Layered Encoding** — Credentials can be encoded multiple times (hex → base64 → plaintext)
3. **Format Recognition** — Identifying hex dump format signals need for decoding approach
4. **Data Fragmentation** — Multi-line hex dumps require concatenation before decoding
5. **Encoding Chains** — Each encoding layer must be decoded in reverse order

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- Hex Dump Format: `xxd(1)` man pages - https://manpages.debian.org/bookworm/xxd/xxd.1.en.html
- Base64 Encoding: `base64(1)` man pages - https://man7.org/linux/man-pages/man1/base64.1.html

---

**Status:** ✅ Completed  
**Challenge Type:** Hex Dump Analysis / Multi-Layer Decoding  
**Key Learning:** Credentials can use multiple sequential encoding layers
