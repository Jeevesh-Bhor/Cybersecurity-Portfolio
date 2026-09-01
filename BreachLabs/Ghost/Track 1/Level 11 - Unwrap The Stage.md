# BreachLabs: Ghost Track Level 11 - Unwrap the Stage

**Challenge:** Level 11 → 12  
**Date Completed:** August 30, 2026  
**Platform:** [BreachLabs](https:/breachlab.org)

---

## Summary

Unwrap the Stage introduces multi-layer compression and archive extraction. The challenge uses nested compression formats (tar → xz → gzip) to conceal a credential, mimicking data loss prevention (DLP) evasion techniques. Success requires identifying each compression format and sequentially unpacking all layers.

---

## Challenge Objective

**Goal:** Retrieve password for `ghost12` user  
**Context:** Staging bundle wrapped in three different compression formats  
**Constraint:** Each layer uses different format; must identify and unpack sequentially

---

## Initial Access

### SSH Connection

Connected using credentials obtained from Level 10:
```
Target: 204.168.229.209:2222
User: ghost11
Access Method: SSH
```

### Challenge Introduction

Challenge narrative stated:
- Staging bundle wrapped three layers deep
- Each layer uses different format
- Bundle designed to slip past naive DLP (Data Loss Prevention)

---

## File Discovery

### Home Directory Contents

Located `stage.bin` file in home directory:

**File Properties:**
- Filename: `stage.bin`
- Size: 10,240 bytes
- Permissions: `-rw-r-----` (readable by user)
- Owner: ghost11

---

## Multi-Layer Extraction

### Layer 1: TAR Archive Identification

Used `file` command to identify first layer:

```bash
file stage.bin
```

**Output:**
```
stage.bin: POSIX tar archive (GNU)
```

**Finding:** File identified as TAR archive format.

### Layer 1: TAR Extraction

Extracted TAR archive:

```bash
tar -xvf stage.bin
```

**Output:**
```
payload.txt.gz.xz
```

**Finding:** TAR extraction revealed nested filename indicating multiple compression layers (`.gz.xz` extension).

### Layer 2: XZ Decompression

Decompressed XZ compression:

```bash
xz -d payload.txt.gz.xz
```

**Result:** Created `payload.txt.gz` file (48 bytes)

### Layer 3: GZIP Decompression

Decompressed GZIP compression:

```bash
gzip -d payload.txt.gz
```

**Result:** Created `payload.txt` file (16 bytes)

---

## Credential Discovery

### Final Layer Contents

Examined uncompressed payload:

```bash
cat payload.txt
```

**Output:**
```
[REDACTED_FLAG]
```

**Finding:** Final layer contained credential in flag format.

---

## Attack Chain Summary

```
SSH Connection (ghost11)
         ↓
File Discovery (stage.bin)
         ↓
File Type Identification (file command)
         ↓
Format Recognition (TAR archive)
         ↓
TAR Extraction (tar -xvf)
         ↓
Nested Filename Analysis (payload.txt.gz.xz)
         ↓
XZ Decompression (xz -d)
         ↓
GZIP Decompression (gzip -d)
         ↓
Payload Extraction
         ↓
Credential Discovery
         ↓
Challenge Complete
```

---

## Key Techniques & Concepts

1. **File Type Identification** — Using `file` command to determine archive format without extension
2. **TAR Archive Extraction** — Unpacking tar archives with `-xvf` flags
3. **XZ Decompression** — Removing XZ compression layer with `-d` flag
4. **GZIP Decompression** — Removing GZIP compression with `-d` flag
5. **Sequential Unpacking** — Removing compression layers in correct order (XZ → GZIP)
6. **Nested Filenames** — File extensions indicating multiple compression formats

---

## Compression Format Details

**Layer 1:** POSIX TAR archive (container format)
**Layer 2:** XZ compression (`.xz` extension)
**Layer 3:** GZIP compression (`.gz` extension)

---

## Tools & Commands Used

```bash
# SSH access
ssh ghost11@204.168.229.209 -p 2222

# Directory listing
ls -alps

# File type identification
file stage.bin

# TAR extraction
tar -xvf stage.bin

# XZ decompression
xz -d payload.txt.gz.xz

# GZIP decompression
gzip -d payload.txt.gz

# Payload examination
cat payload.txt
```

---

## Lessons Learned

1. **File Type Matters** — `file` command determines format regardless of extension
2. **Sequential Decompression** — Layers must be unpacked in reverse order (last applied → first removed)
3. **Naming Conventions** — Filenames indicate multiple compression formats stacked together
4. **DLP Evasion** — Multiple compression layers can obscure content from basic scanning
5. **Format Flexibility** — Different compression tools (tar, xz, gzip) work together in pipelines
6. **Archive Structure** — TAR contains compressed files; inner files use separate compression

---

## References

- BreachLabs: [Ghost Track Series](https://breachlab.org/tracks/ghost)
- File Type Detection: `file(1)` man pages - https://man7.org/linux/man-pages/man1/file.1.html
- TAR Archives: `tar(1)` man pages - https://man7.org/linux/man-pages/man1/tar.1.html
- GZIP Compression: `gzip(1)` man pages - https://manpages.debian.org/bookworm/gzip/gzip.1.en.html
- XZ Compression: `xz(1)` man pages - https://man7.org/linux/man-pages/man1/xz.1.html

---

**Status:** ✅ Completed  
**Challenge Type:** Archive Extraction / Multi-Layer Decompression  
**Key Learning:** Sequential unpacking of nested compression formats
