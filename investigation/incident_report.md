# SOC Investigation Report — Repeated Failed Logins

## Incident Summary

**Case ID:** SOC-LAB-001  
**Event Date:** October 11, 2026  
**Environment:** Windows 10 / Splunk Enterprise  
**Affected Account:** SOC-Lab  
**Classification:** Authorized security simulation  
**Status:** Closed — Expected test activity

### Summary

During a controlled authentication test, five failed login attempts were generated against a local Windows account named `SOC-Lab`.

The activity was collected through Windows Security Event Logs and investigated using Splunk Enterprise. An SPL detection query identified five failed logins within a five-minute window.

The investigation also included searches for successful authentication and account lockout events. No matching successful logins or account lockouts were identified in the respective searches.

The activity was confirmed to be part of an authorized lab simulation.

## Detection Details

| Field | Finding |
|---|---|
| Detection source | Windows Security Event Logs |
| SIEM | Splunk Enterprise |
| Event ID | 4625 |
| Target account | SOC-Lab |
| Failed attempts | 5 |
| Logon type | 2 — Interactive |
| Failure reason | Unknown username or bad password |
| Detection threshold | 5 failures within a fixed 5-minute interval |
| Source environment | Local Windows 10 workstation |

## Investigation Timeline

| Date / Time | Activity |
|---|---|
| October 11, 19:18:40 | First recorded failed authentication |
| October 11, 19:19:01 | Second failed authentication |
| October 11, 19:19:13 | Third failed authentication |
| October 11, 19:19:32 | Fourth failed authentication |
| October 11, 19:19:41 | Fifth failed authentication |

*Times reflect the timestamps displayed by Splunk.*

## Investigation Steps

### 1. Failed Login Analysis

Searched Windows Security logs for Event ID 4625 and verified that the five generated events targeted `SOC-Lab`.

The records showed Logon Type 2 and a failure reason indicating an invalid username or password.

### 2. Detection Development

Created an SPL query to identify five or more failed login attempts within a five-minute time bucket.

The initial query searched specifically for `SOC-Lab`. It was later improved by extracting the targeted username from raw Windows events, allowing failures to be grouped by individual account.

The improved detection successfully identified the test account.

### 3. Successful Login Investigation

Searched for Windows Event ID 4624 associated with `SOC-Lab`.

**Result:** No matching successful-login events were identified within the selected search window.

### 4. Account Lockout Investigation

Searched Windows Security logs for Event ID 4740.

**Result:** No account lockout events were found within the investigated search period.

## Assessment

The detection successfully identified repeated failed authentication attempts.

Although five failures within a short period may warrant investigation in a production environment, these events were deliberately generated using an authorized test account.

There was no evidence in the reviewed search results of a successful authentication or an account lockout associated with the simulation. The available evidence does not indicate a confirmed compromise.

## Limitations

The investigation was performed on a single Windows 10 workstation.

The detection uses fixed five-minute time buckets and relies on Windows Security auditing and Splunk log ingestion. Additional telemetry and context would be needed to evaluate similar activity in a production environment.

## Recommendations

For a production environment, I would recommend:

- Monitoring repeated authentication failures across user accounts.
- Correlating failed logins with subsequent successful authentication.
- Reviewing account lockout policies and authentication auditing.
- Investigating the originating host or IP address when available.
- Tuning detection thresholds to reduce false positives.

## Final Disposition

**Closed — Authorized test activity.**

The detection worked as expected, and the investigation documented the observed authentication failures and subsequent searches.

No incident response containment actions were required because the activity was intentionally generated in a controlled lab.
