# SSH Brute Force Detection in Splunk

## Project Overview
This home lab project simulates SSH credential abuse from a Kali Linux attacker system against an Ubuntu target and uses Splunk to ingest and analyze Linux authentication logs.

## Current Progress
- [x] Lab environment validated
- [x] SSH authentication logging confirmed
- [x] Splunk installed and configured
- [x] `/var/log/auth.log` ingested into Splunk
- [x] Initial SSH brute-force detection searches created

## Detection Goals
- Detect repeated failed SSH login attempts
- Identify source IPs responsible for brute-force activity
- Detect threshold-based authentication abuse
- Build dashboards and alerts for credential abuse monitoring