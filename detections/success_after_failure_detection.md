# Success After Failed SSH Attempts Detection
## Objective
Detect successful SSH logins that occur after multiple failed attempts from the same source.

## SPL Query
```spl
index=main sshd ("Failed password" OR "Accepted password")
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| rex "for (invalid user )?(?<user>\w+)"
| sort 0 _time
| streamstats count(eval(searchmatch("Failed password"))) as fail_count by src_ip, user
| where searchmatch("Accepted password") AND fail_count >= 3
```

## Validation Result
Example detection output:
- Source IP: <ATTACKER-IP>
- Target user: ubuntu
- Failed attempts before success: 10

This confirms detection of a successful SSH login following multiple failed attempts.