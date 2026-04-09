# Hydra Invalid User Test

## Objective
Generate repeated failed SSH authentication attempts using a non-existent username.

## Command
```bash
hydra -l fakeuser -P wordlists/ssh-passwords.txt -t 2 -V ssh://<TARGET-IP>
```