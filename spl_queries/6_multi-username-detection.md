# Multi-Username SSH Targeting Analysis

## Search 1 - Raw failed SSH events
```spl
index=main sshd "Failed password"
```

## Search 2 - Count targeted usernames
```spl
index=main sshd "Failed password"
| rex "for (invalid user )?(?<target_user>\w+)"
| stats count by target_user
| sort - count
```

## Search 3 - Distinct usernames targeted by source IP
```spl
index=main sshd "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| rex "for (invalid user )?(?<target_user>\w+)"
| stats dc(target_user) as unique_users values(target_user) as users count by src_ip
| sort - unique_users
```

### Step 4.9 - Validate Success After Failures Pattern in Splunk

Validation searches:
```spl
index=main sshd "Accepted password"

index=main sshd ("Failed password" OR "Accepted password")

index=main sshd ("Failed password" OR "Accepted password")
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| rex "for (invalid user )?(?<user>\w+)"
| table _time, src_ip, user, _raw
| sort _time
```
Result:
-Successful SSH login confirmed after multiple failed attempts
-Full authentication timeline visible in Splunk

