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
```bash
ip a
```

### step 2.2 - Verify SSH Service
command used:
```bash
sudo systemctl status ssh
```

### step 2.3 - verify Kali (attacker) to Ubuntu connectivity
command used:
```bash
ip -c 5 <UBUNTU-IP>
```

### Step 2.4 - Validate SSH Port Reachability
Command used:
```bash
nc -zv <UBUNTU-IP> 22
```

### Step 2.5 - Generate Failed SSH Authentication Event
Command used:
```bash
ssh fakeuser@<UBUNTU-IP>
```

### step 2.6 - sample auth log events
Observed issue:
- Raw `tail -n 20` output contained unrelated system noise (CRON, sudo, desktop events)
- Pivoted to filtered SSH-specific log review
commands used:
```bash
sudo grep sshd /var/log/auth.log | tail -n 30
```

## Phase 3 - Splunk Installation and Log Ingestion

### Step 3.1 - Validate Ubuntu Resources
Commands used:
```bash
free -h
df -h
nproc
```

### Step 3.2 - Download Splunk Enterprise
Source:
- Splunk Enterprise Linux (.deb) package
Purpose:
- Install Splunk locally on the Ubuntu target to ingest `/var/log/auth.log`

### Step 3.3 - Install Splunk Enterprise
Commands used:
```bash
cd ~/Downloads
sudo dpkg -i splunk-*.deb
```
If dependency error occured run the following:
```bash
sudo apt --fix-broken install -y
sudo dpkg -i splunk-*.deb
```
Verification:
```bash
ls /opt/splunk
```
### Step 3.4 - Initial Splunk Startup
Command used:
```bash
sudo /opt/splunk/bin/splunk start --run-as-root --accept-license
```
Result:
- Splunk started successfully
- Local administrator account created
- Credentials stored privately (not committed to GitHub)

### Step 3.5 - Access Splunk Web Interface
Access URL:
```text
http://localhost:8000
```

### Step 3.6 - Ingest Linux Authentication Logs into Splunk
Data source monitored:
```text
/var/log/auth.log
```
We now have:
- attacker activity
- Linux auth logs
- Splunk ingestion
- searchable security telemetry
That’s a proper detection engineering pipeline.

### Step 3.7 - Verify Log Ingestion in Splunk
Initial validation searches:
```spl
index=main sshd
```
Alternative searches used:
```spl
index=main sourcetype=linux_secure
index=main "/var/log/auth.log"
```

### Step 3.8 - Build First Detection Search
Detection goal:
- Identify failed SSH authentication attempts
- Captures brute-force and invalid-user authentication failures
- Forms the baseline for future threshold-based detections
SPL query:
```spl
index=main sshd "Failed password"
```

### Step 3.9 - Build Brute-Force Hunting Query
Detection goal:
- Identify source IPs responsible for repeated failed SSH authentication attempts
SPL query:
```spl
index=main sshd "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count by src_ip
| sort - count
```

### Step 3.10 - Build Threshold-Based Brute-Force Detection
Detection goal:
- Identify source IPs generating multiple failed SSH logins within a short time window
SPL query:
```spl
index=main sshd "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| bucket _time span=5m
| stats count by _time, src_ip
| where count >= 3
| sort - _time
```

## Phase 4 - Attack Simulation for Detection Tuning

### Step 4.1 - Validate Hydra Availability on Kali
Command used:
```bash
hydra -h | head
```
### Step 4.2 - Create Controlled SSH Password Wordlist
Created file:
```text
wordlists/ssh-passwords.txt
```
### Step 4.3 - Simulate Repeated Failed SSH Logins with Hydra
Purpose:
- Generate repeated failed SSH login attempts against the Ubuntu target
- Produce brute-force-style authentication telemetry for Splunk detection tuning
Command used:
```bash
hydra -l fakeuser -P wordlists/ssh-passwords.txt -t 2 -V ssh://<TARGET-IP>
```

### Step 4.4 - Validate Hydra-Generated SSH Failure Telemetry in Splunk
Validation searches:
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

### Step 4.5 - Create Multi-Username SSH Test List
Created file:
```text
wordlists/ssh-users.txt
```

### Step 4.6 - Simulate Multi-Username SSH Targeting with Hydra

Command used:
```bash
hydra -L wordlists/ssh-users.txt -p password -t 2 -V ssh://<TARGET-IP>
```

### Step 4.7 - Validate Multi-Username SSH Attack Telemetry in Splunk

Validation searches:
```spl
index=main sshd "Failed password"

index=main sshd "Failed password"
| rex "for (invalid user )?(?<target_user>\w+)"
| stats count by target_user
| sort - count

index=main sshd "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| rex "for (invalid user )?(?<target_user>\w+)"
| stats dc(target_user) as unique_users values(target_user) as users count by src_ip
| sort - unique_users
```

### Step 4.8 - Simulate Successful SSH Login After Failed Attempts
Purpose:
- Generate a success-after-failures authentication sequence
- Failed password attempts for a valid account
- Accepted password event
- Successful SSH session creation
Commands used:
```bash
hydra -l <VALID-USER> -P wordlists/ssh-passwords.txt -t 2 -V ssh://<TARGET-IP>

ssh ubuntu@<TARGET-IP>
```

