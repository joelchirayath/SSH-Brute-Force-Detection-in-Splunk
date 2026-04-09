# Success After Failures SSH Test

## Objective
Generate a realistic authentication sequence consisting of multiple failed SSH attempts followed by one successful login.

## Commands
```bash
hydra -l <VALID-USER> -P wordlists/ssh-passwords.txt -t 2 -V ssh://<TARGET-IP>

ssh ubunut@<TARGET-IP>
```