# Phase 2 - Lab Validation Timeline

## Objective
Validate that SSH authentication events generated from Kali are logged on the Ubuntu target.

## Actions Performed
1. Identified Ubuntu target IP
2. Verified SSH service was running on Ubuntu
3. Confirmed Kali could ping Ubuntu
4. Confirmed TCP port 22 was reachable from Kali
5. Attempted SSH login using invalid username `fakeuser`
6. Confirmed failed SSH authentication events were recorded in `/var/log/auth.log`

## Result
Lab successfully generated credential abuse telemetry required for Splunk detection engineering.

## Security-Relevant Events Observed
- SSH connection attempts
- Invalid user enumeration
- Failed password events
- Authentication failure logging
- Pre-authentication connection closure