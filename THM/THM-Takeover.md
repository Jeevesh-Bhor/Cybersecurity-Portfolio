# TryHackMe: Takeover

**Difficulty:** Easy  
**Date Completed:** August 21, 2025  
**Platform:** TryHackMe

---

## Summary

Takeover focuses on DNS enumeration and subdomain discovery. Requires modifying local network configuration and identifying information disclosure in SSL certificates.

---

## Enumeration

### Host Configuration

Modified local system configuration to resolve target domain locally.

### Subdomain Discovery

Enumerated subdomains for main domain using wordlists or DNS queries. Discovered multiple subdomains hosted on target.

---

## Exploitation

### Subdomain Investigation

Accessed discovered subdomains and examined content for vulnerabilities.

### SSL Certificate Analysis

Examined SSL certificate information accessible through HTTPS connection. Found sensitive information disclosed in certificate metadata.

---

## Flag Retrieval

Located flag in SSL certificate metadata or associated web content.

---

## Attack Chain Summary

```
Host File Configuration
         ↓
DNS/Subdomain Enumeration
         ↓
Subdomain Access
         ↓
Certificate Analysis
         ↓
Information Extraction
```

---

## Key Techniques & Concepts

1. **Host File Manipulation** — Local DNS resolution configuration
2. **Subdomain Enumeration** — Discovering additional hosting targets
3. **Certificate Analysis** — Information disclosure through SSL certificates
4. **HTTPS Inspection** — Examining encrypted connections for metadata

---

## Lessons Learned

1. **Avoid Certificate Secrets** — SSL certificates are public; never embed sensitive data
2. **Subdomain Discovery** — Always enumerate subdomains during reconnaissance
3. **Information Disclosure** — Public certificates can leak organizational information

---

**Status:** ✅ Completed  
**Difficulty Assessment:** Easy  
**Key Learning:** DNS enumeration and certificate analysis
