# TryHackMe: RootMe

**Difficulty:** Easy  
**Date Completed:** August 29, 2025  
**Platform:** TryHackMe

---

## Summary

RootMe focuses on web shell upload exploitation and SUID binary privilege escalation. The box demonstrates common upload filter bypasses and the dangers of misconfigured binary permissions.

---

## Enumeration

### Port Scanning

Discovered SSH and HTTP services running. Further enumeration revealed specific web directories.

### Web Server Enumeration

Located directories including an upload functionality panel and accessible uploads directory.

---

## Initial Access: Web Shell Upload

### Upload Filter Bypass

Attempted direct PHP upload → Blocked by server filter

**Successful Approach:** Researched alternate PHP file extensions that bypass common filters. Successfully uploaded reverse shell using alternate extension.

### Reverse Shell Activation

Set up listener on attacking machine. Accessed uploaded file to trigger reverse connection and gain initial shell access.

---

## Privilege Escalation

### User Flag Retrieval

Located user flag through standard filesystem navigation.

### SUID Binary Enumeration

Used standard Linux commands to search for executables with dangerous permissions:
```bash
find / -user root -perm -4000 -type f 2>/dev/null
```

**Critical Finding:** Discovered a binary with SUID bit set that could be exploited.

### Privilege Escalation Technique

Researched and executed exploitation of the identified SUID binary to gain root shell access.

### Root Flag Retrieval

Successfully retrieved root flag after privilege escalation.

---

## Attack Chain Summary

```
Port Enumeration
         ↓
Web Directory Discovery
         ↓
Upload Filter Bypass
         ↓
Reverse Shell Activation
         ↓
User Flag
         ↓
SUID Binary Discovery
         ↓
Privilege Escalation
         ↓
Root Flag
```

---

## Key Techniques & Concepts

1. **File Upload Filters** — Understanding common filter bypasses
2. **Reverse Shell Payloads** — Establishing interactive access
3. **SUID Exploitation** — Leveraging binary permissions for privilege escalation
4. **Filesystem Enumeration** — Discovering exploitable binaries

---

## Lessons Learned

1. **Extension Whitelisting** — Single extension blocking is insufficient
2. **Secure File Upload** — Files should not be executable or web-accessible
3. **SUID Minimization** — Limit SUID binaries to absolutely necessary system tools
4. **Principle of Least Privilege** — Applications should run with minimal required permissions

---

**Status:** ✅ Completed  
**Difficulty Assessment:** Easy  
**Key Learning:** Upload bypasses and SUID exploitation
