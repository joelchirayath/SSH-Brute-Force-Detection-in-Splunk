# Failed SSH Login Detection

## Objective
Identify failed SSH password attempts from Linux authentication logs.

## SPL Query
```spl
index=main "sshd" "Failed password"
```