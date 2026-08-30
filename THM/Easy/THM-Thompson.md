# TryHackMe: Thompson

**Difficulty:** Easy  
**Date Completed:** August 22, 2025  
**Platform:** TryHackMe

---

## Summary

Thompson focuses on Apache Tomcat exploitation. Features default credentials, manager application abuse, and malicious WAR file deployment for remote code execution.

---

## Enumeration

### Port Scanning

Discovered Tomcat running on common port with AJP protocol available.

### Tomcat Server Discovery

Web server accessible on standard port. Located manager and host-manager applications.

### Credential Discovery

Attempted default Tomcat credentials against manager application. Successfully authenticated.

---

## Exploitation: WAR File Deployment

### Malicious Payload Generation

Used Metasploit framework to generate reverse shell payload in Java Web Archive format.

### CSRF Token Handling

Retrieved CSRF token from manager interface (required for deployment).

### WAR File Upload

Deployed malicious WAR file through manager interface using authenticated session.

### Shell Activation

Accessed deployed application URL to trigger reverse shell payload. Established interactive shell on target.

---

## Post-Exploitation

### User Flag

Located and retrieved user flag from filesystem.

### Privilege Escalation

Performed standard Linux privilege escalation enumeration and exploitation.

### Root Flag

Retrieved root flag after escalation.

---

## Attack Chain Summary

```
Port Enumeration (Tomcat)
         ↓
Manager Discovery
         ↓
Default Credential Testing
         ↓
Authentication Success
         ↓
Payload Generation
         ↓
CSRF Token Extraction
         ↓
WAR Deployment
         ↓
Reverse Shell Activation
         ↓
Flag Retrieval
```

---

## Key Techniques & Concepts

1. **Default Credentials** — Tomcat defaults remain unchanged in many deployments
2. **Tomcat Manager Abuse** — Using manager app for application deployment
3. **WAR File Deployment** — Java Web Archive format for malicious application delivery
4. **CSRF Token Handling** — Managing security tokens in automated deployment
5. **Reverse Shell Payloads** — Metasploit-generated JSP shells

---

## Lessons Learned

1. **Change Default Credentials** — Default Tomcat credentials are widely known
2. **Manager Application Security** — Restrict access to manager app through network controls
3. **WAR File Validation** — Never deploy untrusted WAR files
4. **Principle of Least Privilege** — Web applications should run with minimal permissions

---

**Status:** ✅ Completed  
**Difficulty Assessment:** Easy  
**Key Learning:** Tomcat exploitation and WAR deployment
