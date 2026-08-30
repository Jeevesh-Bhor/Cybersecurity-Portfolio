# HackTheBox: SpookyPass

**Difficulty:** Easy  
**Platform:** HackTheBox (Offline Challenge)  
**Date Completed:** August 31, 2025

---

## Summary

SpookyPass is a reverse engineering challenge involving binary analysis and string extraction. Tests ability to identify hidden information within compiled executables.

---

## Challenge Approach

### File Extraction

Extracted archive containing binary executable.

### Binary Analysis

Executed binary expecting password requirement or similar protection mechanism.

### String Extraction Methodology

Used standard binary analysis technique to extract readable strings from executable. This approach reveals hardcoded data often overlooked during compilation.

### Credential Discovery

Identified password string within extracted content.

### Binary Execution

Provided discovered password to binary. Successful authentication retrieved flag.

---

## Attack Chain Summary

```
File Extraction
         ↓
Binary Analysis Planning
         ↓
String Extraction
         ↓
Credential Identification
         ↓
Binary Execution
         ↓
Flag Retrieval
```

---

## Key Techniques & Concepts

1. **String Extraction** — Standard binary analysis technique
2. **Binary Analysis** — Identifying executable behavior
3. **Obfuscation Analysis** — Understanding simple obfuscation bypass

---

## Lessons Learned

1. **Strings Not Secrets** — Hardcoded data in binaries is easily extractable
2. **Security Through Obscurity Fails** — Simple string storage provides no real protection
3. **Proper Obfuscation Required** — Real protection requires encryption or advanced obfuscation

---

**Status:** ✅ Completed  
**Difficulty Assessment:** Easy  
**Key Learning:** Basic binary analysis
