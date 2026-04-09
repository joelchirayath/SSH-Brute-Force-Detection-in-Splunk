## Search 1 - Basic file ingestion validation
```spl
index=main "/var/log/auth.log"
```

## Search 2 - Linux auth sourcetype check
```spl
index=main sourcetype=linux_secure
```

## Search 3 - SSH daemon events
```spl
index=main sshd
```