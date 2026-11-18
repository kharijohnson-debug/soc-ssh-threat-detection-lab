
SOC Incident Investigation Report

Case ID: SOC-LAB-001
Incident Type: Simulated SSH Authentication Attack
Environment: Ubuntu Server 24.04 LTS
Monitoring Platform: Grafana Cloud / Loki
Investigation Status: Completed — Authorized Lab Simulation

1. Executive Summary

This investigation documents repeated failed SSH authentication attempts detected within a controlled Ubuntu Linux security monitoring environment.

The objective was to simulate suspicious authentication activity, identify relevant security events, and validate an automated detection and notification workflow.

Using Grafana Alloy and Loki, authentication events were centrally collected and analyzed through LogQL queries. A Grafana-managed alert rule was configured to identify five or more failed SSH password events within a five-minute observation window.

Controlled authentication failures successfully triggered the detection rule, resulting in an automated email notification displaying a Firing status.

Investigation Outcome: The simulated activity was successfully detected and the automated notification workflow was validated. No external intrusion was established.

2. Environment and Detection Sources

Component

Purpose

Ubuntu Server 24.04 LTS

Monitored Linux host

UTM

Virtualization environment

OpenSSH

Authentication service

Grafana Alloy

Log collection and forwarding

Grafana Loki

Centralized log storage and querying

Grafana Cloud

Monitoring, visualization, and alerting

LogQL

Authentication event investigation

3. Detection Details

Alert Name: SOC Lab - Multiple Failed SSH Login Attempts

Detection Objective: Identify repeated failed SSH password attempts that may indicate credential-guessing activity.

LogQL Detection Query:

sum(count_over_time({instance="soc-lab"} |= "Failed password" [5m]))

Detection Parameters:

Observation window: Five minutes

Alert threshold: Greater than four failed-password events

Evaluation interval: One minute

Notification channel: Email

The alert was designed to identify elevated authentication failure activity and notify the designated recipient for further investigation.

4. Investigation Evidence

Evidence 1 — Security Monitoring Dashboard



The Grafana dashboard displayed 19 authentication failures within the selected reporting range, along with a time-series visualization and authentication log details.

This total represents the dashboard's selected time range and should not be interpreted as 19 failures within five minutes.

Evidence 2 — Failed SSH Authentication Logs



Loki displayed authentication events associated with the controlled test, including failed-password and invalid-user messages.

The observed test activity included:

Test username: fakeuser

Source IP address: 127.0.0.1

Authentication service: SSH

The loopback source address indicates that the displayed test attempts originated locally rather than from a confirmed external attacker.

Evidence 3 — LogQL Investigation



The following LogQL filter was used to isolate failed SSH password events:

{instance="soc-lab"} |= "Failed password"

The query returned matching authentication events, allowing the activity to be reviewed by timestamp and event type.

Evidence 4 — Automated Detection Rule



The configured alert rule evaluated failed-password event counts against a threshold of more than four events within five minutes.

Evidence 5 — Email Notification



An automated Grafana email notification was received with a Firing status, confirming successful notification during the controlled detection test.

5. Investigation Findings

Finding 1 — Repeated authentication failures

Multiple failed SSH password events were observed in the monitored environment.

Finding 2 — Invalid username activity

The authentication logs included attempts involving the intentionally invalid username fakeuser.

Finding 3 — Local test origin

The observed source address 127.0.0.1 corresponds to the local loopback interface, consistent with the authorized test procedure.

Finding 4 — Detection and alerting validation

The configured Grafana alert successfully identified the simulated activity and generated an email notification.

Finding 5 — No confirmed compromise

The available evidence establishes unsuccessful authentication attempts during a controlled simulation. It does not establish successful unauthorized access or system compromise.

6. Severity Assessment

Classification: Authorized Security Testing
Operational Severity: Informational — Controlled Lab Activity

The authentication pattern resembles behavior that could warrant investigation in a production environment. However, the observed activity was intentionally generated within an authorized lab.

In a production SOC, severity would depend on additional factors such as the source address, affected accounts, frequency of attempts, successful logins, and evidence of unauthorized access.

7. Recommended Analyst Response

If comparable activity were detected in a production environment, the recommended investigative steps would include:

Identify the source IP addresses associated with the failed authentication attempts.

Review authentication logs for successful logins following repeated failures.

Determine whether the targeted accounts are valid, privileged, or otherwise sensitive.

Correlate the activity with additional host and network telemetry.

Assess whether containment or escalation is warranted based on the available evidence.

Recommend SSH hardening measures such as key-based authentication, restricted administrative access, and appropriate login rate limiting.

These are recommended response procedures, not actions claimed to have been performed during this lab.

8. Conclusion

The investigation successfully validated a centralized SSH authentication monitoring and detection workflow.

Authentication logs were collected from the Ubuntu server, analyzed using LogQL, and used to test an automated Grafana alert rule. The controlled activity generated a security notification, demonstrating the operation of the detection and alerting pipeline.

The exercise provided practical experience in log analysis, detection engineering, security event validation, and evidence-based SOC reporting.

Final Disposition: Closed — Authorized simulation; detection and email notification successfully validated.
