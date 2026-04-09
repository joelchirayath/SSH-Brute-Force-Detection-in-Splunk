# Phase 4 - Hydra Validation Searches

## Search 1 - Hydra fakeuser failed logins
```spl
index=main sshd "Failed password" "fakeuser"

index=main sshd "Failed password" "fakeuser"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count by src_ip
| sort - count

index=main sshd "Failed password" "fakeuser"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| bucket _time span=5m
| stats count by _time, src_ip
| where count >= 3
| sort - _time
```

