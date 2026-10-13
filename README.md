# Windows Brute-Force Detection Using Splunk

## Overview

For this project, I set up Splunk Enterprise on a Windows 10 machine to monitor authentication activity and investigate repeated failed login attempts.

I wanted to get hands on experience working with a SIEM, analyzing Windows Security logs, and writing SPL queries to identify suspicious activity.

To test the detection, I created a local Windows account and intentionally generated several failed login attempts. I then used Splunk to find those events, build a detection query, and check whether any successful logins or account lockouts occurred.

## Tools Used

- **Splunk Enterprise** — Log collection, searching, and analysis
- **Windows 10** — Local lab environment
- **Windows Event Viewer** — Windows security event inspection
- **SPL (Search Processing Language)** — Detection queries
- **Command Prompt** — Simulating failed authentication attempts

## Lab Setup

I installed Splunk Enterprise locally and configured it to collect Windows Security Event Logs.

I created a standard Windows account named `SOC-Lab` specifically for testing. Using the `runas` command, I attempted to authenticate with an incorrect password five times.

The goal was to generate actual Windows authentication failures that I could investigate in Splunk instead of working with sample datasets.

## Detecting Failed Logins

I started by searching for Windows Event ID **4625**, which records failed logon attempts.

Splunk returned the five failed attempts I had generated against the `SOC-Lab` account.

I then wrote an SPL query to count failed logins within five-minute intervals. The initial query worked, but it searched specifically for `SOC-Lab`, which made it less useful for monitoring other accounts.

I improved the query by extracting the targeted username directly from the Windows event data. This allowed the detection to group failures by account rather than relying on a hardcoded username.

The final query identifies accounts with at least five failed login attempts within the same five-minute time bucket.

### Detection Query

The working SPL query is available in [`detections/brute_force_detection.spl`](detections/brute_force_detection.spl).

## Investigating the Activity

After detecting the failed logins, I checked two additional Windows events to better understand what happened.

| Event ID | Purpose | Result |
|---|---|---|
| 4625 | Identify failed logins | 5 failed attempts detected |
| 4624 | Check for successful logins | No matching events found |
| 4740 | Check for account lockouts | No events found in the searched period |

The failed logins were recorded as Logon Type 2 (interactive), with the failure reason indicating an incorrect username or password.

I also searched for successful logins associated with `SOC-Lab` to see whether authentication succeeded after the failed attempts. No matching events were returned.

The account lockout search also returned no events in the investigated period.

Since I generated the activity myself, I knew these were authorized test attempts rather than an actual attack. However, the same pattern could be worth investigating in a real environment, especially if repeated failures were followed by a successful login.

## Results

By the end of the project, I had:

- Configured Splunk to collect Windows Security logs
- Generated and identified five failed authentication attempts
- Written an SPL query to detect repeated login failures
- Improved the detection to work with different account names
- Investigated successful-login and account-lockout events
- Documented the results using screenshots and a written investigation

### Screenshots

**Failed login events**

![Failed authentication events](screenshots/01-failed-log
