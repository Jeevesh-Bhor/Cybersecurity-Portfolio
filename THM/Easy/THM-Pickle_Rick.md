# TryHackMe: Pickle Rick

**Difficulty:** Easy  
**Date Completed:** September 1, 2025  
**Platform:** TryHackMe

---

## Summary

Pickle Rick is an easy-level web exploitation challenge testing source code analysis and command injection vulnerabilities. The challenge involves discovering credentials in web source code, exploiting command execution functionality, and locating multiple flag files throughout the system.

---

## Enumeration

### Web Application Discovery

Accessed web server displaying a login form. Examined HTML source code for sensitive information.

**Source Code Analysis:**
Found encoded credentials embedded in HTML comments. Successfully decoded to obtain login credentials.

### Web Application Login

Successfully authenticated to web dashboard containing a command execution interface.

---

## Initial Access: Command Injection

### Vulnerability Discovery

The application contained a command input field that appeared to execute system commands with minimal validation.

**Important Note:** Certain commands were intentionally disabled by the developers to increase difficulty.

### Command Execution

Executed allowed system commands to navigate the filesystem and locate flag files in various directories.

---

## Post-Exploitation

### Flag Discovery

Located three separate flag files distributed throughout the system:
- Flag 1: Located in user home directory
- Flag 2: Located through filesystem enumeration  
- Flag 3: Located in restricted directory (accessible due to application running with elevated privileges)

---

## Attack Chain Summary

```
Source Code Analysis
         ↓
Credential Discovery (HTML comments)
         ↓
Web Application Login
         ↓
Command Injection via Web Interface
         ↓
Filesystem Exploration
         ↓
Multiple Flag Retrieval
```

---

## Key Techniques & Concepts

1. **Source Code Analysis** — Examining HTML and page source for hardcoded information
2. **Encoding Recognition** — Identifying encoded data in page comments
3. **Command Injection** — Exploiting unsanitized command input fields
4. **Filesystem Enumeration** — Using command execution to explore system structure
5. **Privilege Awareness** — Understanding that web applications may run with elevated privileges

---

## Tools & Techniques Used

**Reconnaissance:**
- Browser developer tools (F12) for source inspection
- Decoding tools for encoded credentials

**Exploitation:**
- Web browser command interface
- Common Linux enumeration commands

---

## Lessons Learned

1. **Never Hardcode Credentials** — Embedding credentials anywhere in code (even encoded) is a critical vulnerability
2. **Input Validation is Essential** — Command input must be strictly validated and sanitized
3. **Source Code Review** — Simple browser tools reveal sensitive information in comments
4. **Defense in Depth** — Disabling some commands doesn't prevent exploitation if command injection exists

---

**Status:** ✅ Completed  
**Difficulty Assessment:** Easy  
**Key Learning:** Source code analysis and command injection
