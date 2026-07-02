# Investigation Report: Brute Force Login Attempt

## Report Information

| Field | Details |
|---|---|
| Case ID | SOC-BF-001 |
| Report Type | Security Alert Investigation |
| Alert Category | Authentication Failure |
| Severity | High |
| Status | Escalated |
| Analyst | SOC Level 1 Analyst |
| Environment | Lab Simulation |
| Date | 03 July 2026 |

## Executive Summary

This report documents the investigation of repeated failed login attempts on a Windows endpoint. The alert was triggered after multiple failed authentication events were observed against the same user account within a short time period.

The investigation found 12 failed login attempts from the same source IP address targeting the user account `testuser`. A successful login was also observed shortly after the failed attempts. This pattern is suspicious because it may indicate a brute force login attempt where an attacker tried multiple passwords and eventually gained access.

The activity was treated as high risk and recommended for escalation.

## Alert Details

| Field | Details |
|---|---|
| Alert Name | Multiple Failed Login Attempts |
| Target Host | WIN-ENDPOINT-01 |
| Target User | testuser |
| Source IP | 192.168.1.45 |
| Failed Login Event ID | 4625 |
| Successful Login Event ID | 4624 |
| Number of Failed Attempts | 12 |
| Time Window | 5 minutes |
| Logon Type | 3 |
| Detection Source | Wazuh SIEM |
| Possible Attack Type | Brute Force |

## Reason for Investigation

The alert required investigation because multiple failed login attempts were detected from the same source IP address. Failed login attempts can happen due to a user typing the wrong password, but repeated failures in a short time can indicate password guessing or automated brute force activity.

The risk increased because a successful login was seen after the failed attempts.

## Timeline of Events

| Time | Event | Details |
|---|---|---|
| 22:23:10 | Failed login | User `testuser` failed login from `192.168.1.45` |
| 22:23:42 | Failed login | Same user and source IP |
| 22:24:16 | Failed login | Same pattern continued |
| 22:24:55 | Failed login | Authentication failure recorded |
| 22:25:21 | Failed login | Multiple failed attempts now visible |
| 22:25:51 | Failed login | Wazuh correlation started |
| 22:26:24 | Failed login | Same source IP and same username |
| 22:26:59 | Failed login | Suspicious pattern confirmed |
| 22:27:48 | Successful login | Login success observed after repeated failures |
| 22:28:14 | Alert raised | Wazuh generated brute force login alert |

## Evidence Reviewed

The following evidence was reviewed during the investigation:

- Wazuh SIEM alert dashboard
- Windows Security Event ID 4625 logs
- Windows Security Event ID 4624 logs
- Source IP address
- Target user account
- Login timestamp pattern
- Logon type
- Post-login activity indicators

## Windows Event Review

### Event ID 4625: Failed Login

Windows Event ID 4625 shows a failed login attempt.

Observed details:

```text
Event ID: 4625
Account Name: testuser
Source Network Address: 192.168.1.45
Failure Reason: Unknown user name or bad password
Logon Type: 3
Target Host: WIN-ENDPOINT-01
```

The same event appeared multiple times within a short time window.

### Event ID 4624: Successful Login

Windows Event ID 4624 shows a successful login.

Observed details:

```text
Event ID: 4624
Account Name: testuser
Source Network Address: 192.168.1.45
Logon Type: 3
Target Host: WIN-ENDPOINT-01
```

The successful login happened after repeated failed attempts, which increased the severity of the case.

## Analysis

The repeated failed login attempts suggest that someone attempted to guess the password for the `testuser` account. The activity came from the same source IP address, which makes it more suspicious than random failed login attempts.

The successful login after several failures may mean that the correct password was eventually entered. In a real SOC environment, this should be treated carefully because an attacker using a valid account can appear like a normal user.

No confirmed malware execution or privilege escalation was found in this simulated case, but the login pattern was enough to escalate the alert for further review.

## Possible Root Cause

The possible causes include:

1. A user repeatedly entered the wrong password.
2. A saved password on another system was outdated.
3. A script or service tried to authenticate with old credentials.
4. An attacker attempted brute force login.
5. A compromised internal machine attempted unauthorized access.

Based on the successful login after repeated failures, brute force activity is the most suspicious possibility.

## MITRE ATT&CK Mapping

| Tactic | Technique | ID | Reason |
|---|---|---|---|
| Credential Access | Brute Force | T1110 | Multiple password attempts were observed |
| Initial Access | Valid Accounts | T1078 | Successful login happened after failures |
| Defense Evasion | Valid Accounts | T1078 | Activity may look normal if attacker used valid credentials |

## Indicators of Compromise

| IOC Type | Value |
|---|---|
| Source IP | 192.168.1.45 |
| Target User | testuser |
| Target Host | WIN-ENDPOINT-01 |
| Failed Login Event ID | 4625 |
| Successful Login Event ID | 4624 |
| Logon Type | 3 |

## Risk Assessment

| Risk Factor | Rating | Reason |
|---|---|---|
| Repeated Failed Attempts | High | 12 failures in 5 minutes |
| Successful Login After Failures | High | May show password was guessed |
| Same Source IP | Medium | Indicates focused login attempts |
| Post-login Malicious Activity | Low | No major activity confirmed in lab |
| Overall Risk | High | Successful login after brute force pattern |

## Recommended Actions

The following actions are recommended:

1. Confirm whether the login was expected by the user.
2. Temporarily lock or disable the affected account if activity is not confirmed.
3. Force a password reset for `testuser`.
4. Check whether `192.168.1.45` belongs to a trusted device.
5. Review other logs from the same source IP.
6. Check whether the same account was used on other systems.
7. Review PowerShell and process execution logs after login.
8. Enable MFA for user accounts.
9. Create SIEM correlation for failed login followed by successful login.
10. Escalate to the SOC Level 2 analyst for deeper investigation.

## Containment Steps

If this was a real incident, the following containment steps should be performed:

- Lock the affected user account.
- Reset the password.
- Disconnect the suspicious source machine if needed.
- Block the source IP if it is not trusted.
- Review active sessions for the affected user.
- Check for signs of lateral movement.
- Monitor the account after password reset.

## Final Conclusion

The investigation identified a suspicious login pattern involving repeated failed login attempts followed by a successful login. This behavior is consistent with a possible brute force attack.

Although no confirmed malicious activity was found after the login in this lab scenario, the event should still be escalated because successful login after repeated failures can indicate unauthorized access.

The case should be marked as suspicious and escalated for further review.

## Lessons Learned

This case helped me understand how failed login alerts should be investigated in a SOC environment. I learned that failed login events should not be reviewed alone. It is important to check whether a successful login happened after the failed attempts.

I also learned how Windows Event IDs 4625 and 4624 can be used together to identify suspicious authentication activity.

## Disclaimer

This report is based on a simulated lab environment. No real user data, company data, or production logs are included. The purpose of this report is educational and defensive security learning only.
