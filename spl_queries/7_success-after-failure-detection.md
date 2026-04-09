# Success After Failed SSH Attempts Detection

## Objective
Identify successful SSH logins that were preceded by multiple failed attempts.

## Search 1 - Successful logins
```spl
index=main sshd "Accepted password"
```

## Search 2 - Combined failures and success
```spl
index=main sshd ("Failed password" OR "Accepted password")
```

##  Search 3 - Timeline view
```spl
index=main sshd ("Failed password" OR "Accepted password")
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| rex "for (invalid user )?(?<user>\w+)"
| table _time, src_ip, user, _raw
| sort _time
```

