# SOC Case Study: Brute Force Login Attempt Investigation

## Project Overview

This case study shows how a SOC Level 1 analyst can investigate repeated failed login attempts on a Windows machine. The purpose of this project is to understand how brute force attacks appear in logs, how alerts can be reviewed, and how an analyst can decide whether the activity is suspicious or a false positive.

The case study is based on a simulated lab environment and is created for educational and defensive security learning.

## Scenario

A Windows endpoint generated multiple failed login alerts within a short time. The alerts showed repeated login attempts against one user account. After several failed attempts, one successful login was also observed.

This pattern may indicate a brute force attack where an attacker tries multiple passwords to gain access to a valid account.

## Objective

The main goals of this investigation are:

1. Identify the source of failed login attempts.
2. Check which user account was targeted.
3. Confirm whether any login was successful after repeated failures.
4. Review if the activity was normal user behavior or suspicious.
5. Recommend containment and prevention steps.

## Lab Environment

The lab was created using the following tools:

* Windows 10 test machine
* Windows Event Viewer
* Wazuh SIEM
* PowerShell
* Sample Windows security logs
* MITRE ATT&CK mapping

## Alert Summary

| Field                     | Details                        |
| ------------------------- | ------------------------------ |
| Alert Type                | Multiple Failed Login Attempts |
| Severity                  | Medium                         |
| Target System             | Windows Endpoint               |
| Target User               | testuser                       |
| Source IP                 | 192.168.1.45                   |
| Event ID                  | 4625                           |
| Time Window               | 5 minutes                      |
| Number of Failed Attempts | 12                             |
| Possible Threat           | Brute Force Attack             |

## Why This Alert Matters

Multiple failed login attempts in a short time can be a sign of password guessing. If the attacker finds the correct password, they may log in as a valid user and avoid some security controls.

This type of activity is important for SOC analysts because it can lead to unauthorized access, data theft, privilege abuse, or further movement inside the network.

## Investigation Steps

### Step 1: Review the Alert

The first step was to review the alert in Wazuh. The alert showed repeated failed login attempts from the same source IP address.

Important fields checked:

* Source IP address
* Target username
* Number of failed attempts
* Time of activity
* Authentication type
* Hostname
* Event ID

### Step 2: Check Windows Event ID 4625

Windows Event ID 4625 shows a failed login attempt. This event helps identify failed authentication activity.

The following details were reviewed:

* Account name
* Source network address
* Logon type
* Failure reason
* Workstation name
* Timestamp

Sample log details:

```text
Event ID: 4625
Account Name: testuser
Source Network Address: 192.168.1.45
Failure Reason: Unknown user name or bad password
Logon Type: 3
```

### Step 3: Check the Number of Attempts

The logs showed 12 failed login attempts within 5 minutes. This is not normal for a regular user and may indicate automated password guessing.

A single failed login can happen by mistake, but repeated failures from the same IP should be investigated.

### Step 4: Check for Successful Login

After reviewing failed login events, successful login events were checked using Windows Event ID 4624.

A successful login was found shortly after the failed attempts.

Sample log details:

```text
Event ID: 4624
Account Name: testuser
Source Network Address: 192.168.1.45
Logon Type: 3
```

This increased the risk level because the attacker may have guessed the correct password.

### Step 5: Check Source IP Address

The source IP address was reviewed to understand whether it belonged to a trusted internal machine or an unknown device.

The IP address `192.168.1.45` was not expected to access the target system. This made the activity suspicious.

### Step 6: Review User Activity After Login

After the successful login, the analyst checked for any suspicious actions.

The following items were reviewed:

* New process creation
* PowerShell usage
* New user creation
* File access activity
* Remote connection activity
* Antivirus alerts

No major malicious action was confirmed in this lab, but the successful login after repeated failures was still treated as suspicious.

## Detection Logic

The alert can be triggered using this simple logic:

```text
If one source IP generates more than 5 failed login attempts
against the same user account within 5 minutes,
generate a medium-severity brute force alert.
```

If a successful login happens after multiple failures, the severity should be increased.

```text
If multiple failed login attempts are followed by a successful login
from the same source IP,
Raise the alert severity to high.
```

## MITRE ATT&CK Mapping

| Tactic            | Technique      | Explanation                                                   |
| ----------------- | -------------- | ------------------------------------------------------------- |
| Credential Access | Brute Force    | The attacker attempted multiple passwords                     |
| Initial Access    | Valid Accounts | A successful login may mean the attacker used a valid account |
| Defense Evasion   | Valid Accounts | Valid accounts can make attacker activity look normal         |

## Indicators of Compromise

| IOC Type               | Value        |
| ---------------------- | ------------ |
| Source IP              | 192.168.1.45 |
| Target User            | testuser     |
| Failed Login Event     | 4625         |
| Successful Login Event | 4624         |
| Logon Type             | 3            |

## Analyst Findings

The investigation found that the user account `testuser` received multiple failed login attempts from the same source IP address. A successful login was also observed after these failed attempts.

This pattern is suspicious because it may indicate that the attacker guessed the correct password. Even though no major malicious action was confirmed after login, the event should not be ignored.

The alert should be escalated for further review.

## Recommended Actions

The following actions are recommended:

1. Disable or lock the affected user account temporarily.
2. Force a password reset for the targeted account.
3. Check whether the source IP belongs to a trusted device.
4. Review recent activity from the affected account.
5. Enable multi-factor authentication.
6. Block the source IP if it is not trusted.
7. Review other systems for similar login attempts.
8. Check if the same account was used on other machines.
9. Educate users about strong passwords.
10. Create a stronger detection rule for failed and successful login patterns.

## Containment Plan

If this alert appears in a real SOC environment, the analyst should first confirm whether the login attempt was expected. If not, the account should be locked or a password reset should be forced.

The source IP should be checked. If it is unknown or suspicious, network access should be blocked until the investigation is complete.

If a successful login is confirmed, the analyst should review the user’s activity after login to check for signs of further compromise.

## Lessons Learned

This case study shows that failed login attempts should not be reviewed alone. A SOC analyst should also check whether a successful login happened after repeated failures.

A brute force attack becomes more serious when the attacker gains access using a valid account. This is why correlation between failed and successful login events is important.

The project also shows the value of Windows Event IDs 4625 and 4624 in basic SOC investigations.

## Skills Demonstrated

This project demonstrates the following SOC skills:

* Windows log analysis
* Failed login investigation
* Brute force detection
* Alert triage
* IOC collection
* MITRE ATT&CK mapping
* Incident documentation
* Basic containment planning
* Security reporting

## Conclusion

The activity observed in this case study matches a possible brute force login attempt. The repeated failed login attempts followed by a successful login make the event suspicious.

A SOC analyst should escalate this case, reset the affected password, review user activity, and confirm whether the source IP is trusted.

This case study helped build a practical understanding of how login-based attacks appear in Windows logs and how a SOC Level 1 analyst can investigate them.
