# HackTheBox: Flag Command

**Difficulty:** Easy  
**Date Completed:** August 23, 2025  
**Platform:** HackTheBox

---

## Summary

Flag Command demonstrates command injection vulnerabilities in web applications. The challenge involves identifying and exploiting a command execution interface with minimal input validation.

---

## Enumeration

### Port Scanning

Standard reconnaissance discovered web application accessible on target port.

### Web Application Discovery

Accessed web application containing command input field.

---

## Exploitation

### Command Injection

The web interface accepted command input with insufficient validation. Attempted command injection to test for vulnerability.

### Payload Delivery

Identified correct payload format that bypassed input filters and executed system commands.

---

## Flag Retrieval

Successfully executed commands and retrieved flag directly from system response.

---

## Attack Chain Summary

```
Web Application Discovery
         ↓
Command Execution Interface Identification
         ↓
Command Injection Testing
         ↓
Flag Retrieval
```

---

## Key Techniques & Concepts

1. **Command Injection** — Exploiting unsanitized command inputs
2. **Input Validation** — Understanding insufficient input filtering
3. **Payload Testing** — Iterating through payload variations

---

## Lessons Learned

1. **Input Validation Critical** — All user input must be validated and sanitized
2. **Principle of Least Privilege** — Applications should never execute arbitrary commands

---

**Status:** ✅ Completed  
**Difficulty Assessment:** Easy  
**Key Learning:** Command injection exploitation
