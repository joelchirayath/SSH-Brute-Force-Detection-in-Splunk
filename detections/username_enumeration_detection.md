# Username Enumeration Detection

## Objective
Detect source IPs attempting SSH logins across multiple usernames.

## SPL Query
```spl
index=main sshd "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| rex "for (invalid user )?(?<target_user>\w+)"
| stats dc(target_user) as unique_users values(target_user) as targeted_users count as total_attempts by src_ip
| where unique_users >= 5
| sort - unique_users
```

## Detection Logic
- Extract source IP and targeted usernames
- Count distinct usernames per source
- Identify sources targeting multiple accounts (≥5)

## Detection Value
- Detects username enumeration and password spraying behavior
- Identifies attackers probing for valid accounts
- Useful for advanced threat detection