# SSH Brute Force Detection in Splunk

## Phase 1 - Attack Understanding

### Objective
Understand how SSH brute-force and password spraying attacks generate Linux authentication logs.

### Key Concepts
- SSH is used for remote Linux login.
- Brute force involves repeated password guessing.
- Password spraying involves trying one/few passwords across many usernames.
- Ubuntu logs SSH authentication events in `/var/log/auth.log`.

### Detection Goals
- Identify repeated failed logins from one IP
- Identify one IP targeting many usernames
- Detect successful logins after multiple failures

## Phase 2 - Lab Validation

### step 2.1 - Identify Ubuntu IP (target)
command used:
	ip a
### step 2.2 - Verify SSH Service
command used:
	sudo systemctl status ssh