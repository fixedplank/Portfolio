Lab 01 — Microsoft Sentinel SIEM Homelab
Certifications applied: AZ-500 · CompTIA Security+
Focus: Cloud threat detection, KQL analytics, SIEM deployment
Status: ✅ Complete

Objective
Deploy Microsoft Sentinel as a fully functional SIEM in a free-tier Azure tenant, simulate real MITRE ATT&CK techniques against it, and build custom detection rules that generate actionable alerts.

Environment
ComponentDetailsPlatformMicrosoft Azure (free tier)SIEMMicrosoft SentinelEndpointWindows 10 VMIdentityAzure Active DirectoryAttack simulationAtomic Red Team

What I Built
1. Azure Tenant & Sentinel Deployment

Created a free-tier Azure tenant
Deployed a Log Analytics Workspace as the Sentinel data store
Enabled Microsoft Sentinel on top of the workspace

2. Data Connectors
Connected the following data sources to feed logs into Sentinel:

Windows Security Events — event logs from the Windows 10 VM
Azure Active Directory Sign-in Logs — authentication events
Defender for Cloud — security recommendations and alerts

3. Attack Simulation
Used Atomic Red Team to simulate the following MITRE ATT&CK techniques:

T1078 — Valid Accounts (simulated credential abuse)
T1110 — Brute Force (simulated password spray)

4. Custom KQL Detection Rules
Authored 8 custom analytic rules in KQL to detect the simulated techniques. Example rule:
kqlSecurityEvent
| where EventID == 4625
| summarize FailedAttempts = count() by Account, IpAddress, bin(TimeGenerated, 5m)
| where FailedAttempts > 5
| project TimeGenerated, Account, IpAddress, FailedAttempts
5. Workbooks
Built Workbooks to visualise:

Alert volume over time
Sign-in anomalies by location and user
Failed login heatmap by hour

6. Defender for Cloud Integration

Connected Defender for Cloud to Sentinel
Triaged security recommendations
Improved Secure Score from 42% to 78% by remediating flagged misconfigurations


Key Findings

Atomic Red Team successfully triggered 6 of 8 custom detection rules on first run
Two rules required KQL tuning to reduce false positives
Defender for Cloud identified 25+ misconfiguration findings across the tenant
Largest Secure Score improvements came from enabling MFA and restricting public network access on storage accounts


Skills Demonstrated

Microsoft Sentinel deployment and configuration
KQL query writing for threat detection
MITRE ATT&CK framework mapping
Data connector integration
Security workbook creation
Defender for Cloud misconfiguration remediation
Azure AD log analysis
