# SSH Brute-Force Detection (Threshold-Based)

## Objective
Detect potential SSH brute-force attacks based on repeated failed login attempts from a single source.

## SPL Query
```spl
index=main sshd "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| bucket _time span=5m
| stats count as failed_attempts by _time, src_ip
| where failed_attempts >= 5
| sort - failed_attempts
```

## Detection Logic
- Monitor failed SSH authentication attempts
- Group activity into 5-minute time windows
- Count attempts per source IP
- Trigger when failures exceed threshold (≥5)

## Detection Value
- Identifies brute-force attack patterns
- Reduces noise compared to single-event alerts
- Suitable for alerting and dashboard visualization