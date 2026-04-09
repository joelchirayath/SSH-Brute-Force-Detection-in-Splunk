# Threshold-Based SSH Brute-Force Detection

## Objective
Detect repeated failed SSH login attempts from the same source IP within a short time window.

## SPL Query
```spl
index=main sshd "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| bucket _time span=5m
| stats count by _time, src_ip
| where count >= 3
| sort - _time
```
