# SSH Brute-Force Hunting Query

## Objective
Identify source IPs generating repeated failed SSH login attempts.

## SPL Query
```spl
index=main sshd "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count by src_ip
| sort - count
```