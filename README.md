# SSH Brute Force Detection using Splunk

## Overview
This project simulates SSH brute-force attacks in a controlled lab environment and builds detection logic using Splunk to identify credential abuse patterns.

The goal is to demonstrate practical detection engineering skills, including log analysis, attack simulation, and security monitoring.

---

## Lab Setup

- Attacker Machine: Kali Linux (VMware)
- Target Machine: Ubuntu (VMware)
- SIEM Tool: Splunk
- Logs Used: `/var/log/auth.log`

---

## Attack Simulation

The following attack scenarios were simulated:

- SSH failed login attempts
- Multi-username targeting (username enumeration)
- Brute-force attack using Hydra
- Successful login after multiple failed attempts

Tools used:
- Hydra
- SSH
- Netcat

---

## Data Ingestion

- Auth logs (`auth.log`) were ingested into Splunk
- SSH events (`sshd`) were extracted and analyzed

---

## Detection Engineering

### 1. Brute-Force Detection
Detects multiple failed login attempts from a single IP within a time window.

### 2. Top Attacking IPs
Identifies IPs generating the highest number of failed login attempts.

### 3. Username Enumeration Detection
Detects attempts across multiple usernames from a single source.

### 4. Success After Failures Detection
Identifies successful logins following multiple failed attempts (potential compromise).

---

## Dashboard

A Splunk dashboard was created with the following panels:

- Failed SSH Attempts Over Time
- Top Attacking IPs
- Top Targeted Usernames
- Successful Login After Multiple Failures

---

## Key Skills Demonstrated

- SIEM (Splunk)
- Log analysis (Linux auth logs)
- Detection engineering (SPL queries)
- Attack simulation (Hydra)
- Security monitoring and visualization

---

## Screenshots

Screenshots are available in the `screenshots/` directory.

---

## Future Improvements

- Add alerting for detections
- Integrate threat intelligence
- Expand to other protocols (FTP, RDP)

---

## Author
JOEL CHIRAYATH