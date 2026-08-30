# TryHackMe: Valley

**Difficulty:** Easy  
**Date Completed:** August 25, 2025  
**Platform:** TryHackMe

---

## Summary

Valley is a multi-stage exploitation challenge involving web enumeration, FTP access through credential discovery, packet capture analysis, and reverse shell execution through multiple access vectors.

---

## Enumeration

### Port Scanning

Discovered SSH and HTTP services. Further enumeration revealed multiple web directories.

### Web Directory Discovery

Located accessible directories including development notes, pricing pages, and hidden login interfaces. Development notes contained clues about alternate services and credentials.

---

## Credential Discovery

### Hidden Development Files

Found development notes referencing alternate services and ports. Notes contained partial credential information.

### FTP Access

Used discovered credentials to access FTP service on non-standard port. Successfully authenticated.

### Captured Files

Retrieved network packet capture files from FTP server.

### Packet Capture Analysis

Analyzed PCAP files using network analysis tools. Extracted additional credentials from captured HTTP traffic (specifically POST data).

---

## Multiple Access Vectors

### SSH Access

Used extracted credentials to establish SSH connection. Gained user shell access.

### Reverse Shell Execution

Executed reverse shell payload from user shell to establish secondary connection. Set up listener to receive connection.

---

## Post-Exploitation

### User Flag

Located and retrieved user flag.

### Privilege Escalation

Performed privilege escalation to root access.

### Root Flag

Retrieved root flag.

---

## Attack Chain Summary

```
Web Enumeration
         ↓
Development Notes Discovery
         ↓
FTP Access
         ↓
Packet Capture Retrieval
         ↓
Network Analysis
         ↓
Credential Extraction
         ↓
SSH Access
         ↓
Reverse Shell
         ↓
Privilege Escalation
         ↓
Flag Retrieval
```

---

## Key Techniques & Concepts

1. **Information Disclosure** — Development artifacts left in production
2. **Multiple Access Vectors** — FTP, SSH, reverse shells
3. **Packet Capture Analysis** — Wireshark analysis of network traffic
4. **Credential Extraction** — Finding credentials in network traffic
5. **Reverse Shell Payloads** — Python-based reverse shell execution

---

## Tools & Techniques Used

- Network enumeration tools
- FTP client access
- Wireshark for packet analysis
- Reverse shell payloads
- Network listeners

---

## Lessons Learned

1. **Development Artifacts** — Never leave development files in production environments
2. **Unencrypted Traffic** — Sensitive data in unencrypted HTTP can be captured
3. **FTP Dangers** — FTP should not be used for sensitive operations
4. **Network Security** — Monitor for unusual traffic patterns

---

**Status:** ✅ Completed  
**Difficulty Assessment:** Easy  
**Key Learning:** Multi-stage exploitation and packet analysis
