# Phase 5: Flag Capture

Final phase: Locating and capturing the challenge flag.

## Flag Location

- **Directory:** `/root/`
- **Filename:** `proof.txt`

## Retrieval Commands

```bash
cd /root
ls -la
cat proof.txt
```

## Flag Contents

```
Good job, you did it!
702a8c18d29c6f3ca0d99ef5712bfbdc
```

## Final Flag

```
702a8c18d29c6f3ca0d99ef5712bfbdc
```

## Challenge Status

✅ **COMPLETED**

---

**Time from Initial Access to Flag:** ~45-60 minutes  
**Attack Chain:** FTP → PCAP → Web Enum → SSH → PrivEsc → Root → Flag
