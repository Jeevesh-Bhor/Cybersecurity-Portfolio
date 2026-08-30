# TryHackMe: Lian-Yu

**Difficulty:** Easy  
**Date Completed:** August 20, 2025  
**Platform:** TryHackMe

---

## Summary

Lian-Yu is a box that combines web enumeration, file decoding, steganographic analysis, and SSH privilege escalation. The challenge requires identifying hidden directories, decoding credentials from encoded strings, extracting data from image files, and exploiting SSH access to escalate privileges.

---

## Enumeration

### Web Server Discovery

Initial scanning revealed a web server with minimal content on the root directory. Further enumeration discovered hidden web directories.

**Directory Enumeration:**
- Discovered hidden directory containing encoded credentials
- Found nested directory structure containing additional files
- Located encoded string requiring Base58 decoding

### Credential Discovery Approach

Identified an encoded string in a file. Researched and applied appropriate decoding method to reveal credentials for a system user.

---

## Initial Access: FTP

### FTP Connection

Successfully authenticated using decoded credentials from web enumeration.

**Key Finding:** FTP directory contained image files that warranted further analysis.

### Steganography Analysis

Analyzed image files for embedded data:
- One PNG file contained a passphrase extracted through steganographic analysis
- One JPG file contained hidden text data

These discoveries would be useful for accessing other services.

---

## SSH Access & Privilege Escalation

### SSH Login

Used credentials discovered through steganography to establish SSH connection. Gained user-level shell access.

### User Flag

Located and retrieved user flag through standard file access.

### Privilege Escalation

Escalated privileges to root access through standard Linux privilege escalation techniques.

### Root Flag

Retrieved root flag after successful privilege escalation.

---

## Attack Chain Summary

```
Web Enumeration
         ↓
Encoded Credential Discovery
         ↓
Credential Decoding (Base58)
         ↓
FTP Access
         ↓
Steganographic Data Extraction
         ↓
SSH Access with Extracted Credentials
         ↓
Privilege Escalation
         ↓
Flag Retrieval
```

---

## Key Techniques & Concepts

1. **Directory Enumeration** — Identifying hidden web paths through systematic fuzzing
2. **Encoding Recognition** — Identifying Base58 encoding and appropriate decoding
3. **Steganography** — Extracting hidden data embedded within image files (PNG, JPG)
4. **Credential Reuse** — Understanding that extracted credentials apply to multiple services
5. **Multi-stage Access** — Using FTP access to discover credentials for SSH escalation
6. **Service Enumeration** — Identifying available services and their access levels

---

## Tools & Techniques Used

**Enumeration:**
- Directory fuzzing tools
- Online decoding tools for Base58

**Steganography:**
- `steghide` or similar tools for extracting hidden data from images
- Image analysis techniques

**Access:**
- FTP client for remote file access
- SSH for secure shell access

**Privilege Escalation:**
- Standard Linux enumeration and exploitation techniques

---

## Lessons Learned

1. **Encoding vs Encryption** — Encoding methods like Base58 provide obfuscation only, not security
2. **Steganography in CTFs** — Unusual files (especially images) should be analyzed for hidden content
3. **Credential Persistence** — Credentials discovered in one service often apply to others
4. **Defense in Depth** — Multi-layered security requires multiple exploitation steps
5. **Methodology Matters** — Systematic enumeration across all services yields results

---

## References

- TryHackMe: [Lian-Yu](https://tryhackme.com/room/lianyu)
- Encoding/Decoding: Research appropriate encoding schemes
- Steghide Documentation: http://steghide.sourceforge.net/
- OWASP Directory Traversal: https://owasp.org/www-community/attacks/Directory_traversal_attack

---

**Status:** ✅ Completed  
**Difficulty Assessment:** Easy  
**Key Learning:** Multi-stage exploitation with steganography
