# Phase 1: Reconnaissance

Detailed documentation of the reconnaissance phase, including FTP enumeration, PCAP analysis, and web server discovery.

## Contents

1. **FTP Enumeration** - Initial access via anonymous FTP
2. **PCAP Analysis** - Packet capture inspection with Wireshark
3. **Web Server Enumeration** - Hidden directory and binary discovery
4. **Credential Enumeration** - Usernames and password identification

## Key Findings

- FTP accessible with anonymous credentials
- PCAP file contained hints about hidden directory: `/sup3rs3cr3tdirlol/`
- Binary executable (roflmao) contained address hint: `0x0856BF`
- Credentials file revealed usernames and password candidates

## Workflow

```
FTP Access → PCAP Discovery → Web Enumeration → Binary Analysis → Credential File
```
