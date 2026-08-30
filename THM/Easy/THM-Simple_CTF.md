# TryHackMe: Simple CTF

**Difficulty:** Easy  
**Date Completed:** January 2, 2026  
**Platform:** TryHackMe

---

## Summary

Simple CTF tests enumeration, vulnerability research, and privilege escalation. Features a known CMS vulnerability with SQL injection, weak credentials, and sudo misconfiguration.

---

## Enumeration

### Port Scanning

Discovered FTP (with anonymous access), HTTP, and SSH on non-standard port.

### FTP Anonymous Access

Anonymous FTP login revealed hints about system user and credential weakness.

### Web Server Enumeration

Directory enumeration discovered CMS installation. Research identified known vulnerability for this CMS version.

---

## Vulnerability Research & Exploitation

### CVE Identification

Researched CMS version and identified known SQL injection vulnerability. Located public exploit code.

### Exploitation Process

Used available exploit tool against CMS installation:
- Exploit extracted username from database
- Exploit output contained password hash

### Hash Cracking

Submitted hash to online cracking resources. Successfully retrieved plaintext password.

---

## Initial Access: SSH

### SSH Authentication

Used discovered credentials to authenticate on non-standard SSH port. Gained user shell access.

### User Flag

Located user flag in home directory.

---

## Privilege Escalation

### Sudo Capability Check

```bash
sudo -l
```

Discovered user could execute specific administrative tool with sudo privileges without password.

### Privilege Escalation Technique

Researched exploitation method for identified tool. Successfully spawned root shell using built-in command features.

### Root Flag

Retrieved root flag after privilege escalation.

---

## Attack Chain Summary

```
Enumeration
         ↓
FTP Access (Information Gathering)
         ↓
CMS Discovery
         ↓
CVE Research
         ↓
SQL Injection Exploitation
         ↓
Hash Cracking
         ↓
SSH Access
         ↓
User Flag
         ↓
Sudo Analysis
         ↓
Privilege Escalation
         ↓
Root Flag
```

---

## Key Techniques & Concepts

1. **Information Disclosure** — FTP can leak hints about system design
2. **CMS Exploitation** — Researching and using known CVEs
3. **Hash Cracking** — Rainbow tables and online resources
4. **Sudo Misconfiguration** — Dangerous privilege escalation vector
5. **Tool Escape Sequences** — Administrative tools often have built-in shell access

---

## Lessons Learned

1. **Keep Software Updated** — Known CVEs are trivially exploitable
2. **Credential Strength** — Weak passwords are quickly cracked
3. **Sudo Auditing** — Text editors and similar tools should never have sudo access
4. **Defense in Depth** — Multiple security failures created trivial exploitation chain

---

**Status:** ✅ Completed  
**Difficulty Assessment:** Easy  
**Key Learning:** CVE exploitation and sudo privilege escalation
