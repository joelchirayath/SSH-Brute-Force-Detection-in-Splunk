# Top Attacking IPs Detection

## Objective
Identify source IPs generating the highest number of failed SSH login attempts.

## SPL Query
```spl
index=main sshd "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count as failed_attempts by src_ip
| sort - failed_attempts
```

## Detection Logic
- Extract source IP from SSH logs
- Count total failed login attempts per IP
- Rank results by highest activity

## Detection Value
- Highlights most active attacking sources
- Useful for triage and investigation
- Common dashboard visualization