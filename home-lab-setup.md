# Home Lab Setup

**Author:** Moses Abraham (@mrabterminal-tech)  
**Last updated:** June 2026  
**Purpose:** Simulate attacker vs defender scenarios for SOC analyst skill development

---

## Environment

| Component | Details |
|-----------|---------|
| Host OS | macOS |
| Hypervisor | Oracle VirtualBox |
| Attacker VM | Kali Linux |
| Target VM | Windows 11 (Version 10.0.26200.8457) |
| Network mode | Host-Only / Internal Network |

---

## Lab 1 — NTFS Permissions Analysis (Week 1, Wednesday)

### Objective
Understand Windows NTFS permission structure and verify security boundaries using `icacls`.

### Commands run

```cmd
icacls C:\Windows\System32
echo test > C:\Windows\System32\test.txt
```

### Findings

**`C:\Windows\System32` permission output:**

```
C:\Windows\System32 NT SERVICE\TrustedInstaller:(F)
                    NT SERVICE\TrustedInstaller:(CI)(IO)(F)
                    NT AUTHORITY\SYSTEM:(M)
                    NT AUTHORITY\SYSTEM:(OI)(CI)(IO)(F)
                    BUILTIN\Administrators:(M)
                    BUILTIN\Administrators:(OI)(CI)(IO)(F)
                    BUILTIN\Users:(RX)
                    BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
                    CREATOR OWNER:(OI)(CI)(IO)(F)
```

### Analysis

| Principal | Permission | Security implication |
|-----------|-----------|----------------------|
| TrustedInstaller | Full Control (F) | Sole owner — only Windows Update can modify |
| SYSTEM | Modify (M) | OS-level access for runtime operations |
| Administrators | Modify (M) | Cannot change ownership without extra steps |
| Users | Read & Execute (RX) | Standard users cannot write — key security boundary |

### Test result
Attempted to write a file to `C:\Windows\System32` as a standard user:

```
Access Denied
```

**Conclusion:** NTFS permissions correctly block standard users from writing to System32. For malware to drop files here, an attacker would need to escalate to SYSTEM or TrustedInstaller — a high-severity privilege escalation event.

### SOC relevance
- Misconfigured NTFS permissions are a common privilege escalation path
- `icacls` output is used in forensic investigations to determine access scope
- Unexpected writes to System32 = immediate high-severity alert in a real environment

---

## Upcoming labs

- [ ] Lab 2 — Failed login detection (Event ID 4625)
- [ ] Lab 3 — User enumeration from Kali via Nmap
- [ ] Lab 4 — Packet capture with Wireshark
- [ ] Lab 5 — Simulated brute force + detection
- [ ] Lab 6 — Windows Firewall rules and blocking
