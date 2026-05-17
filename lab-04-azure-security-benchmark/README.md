Lab 04 — Azure Security Benchmark & Defender for Cloud
Certifications applied: AZ-900 · AZ-500
Focus: Cloud misconfiguration detection, policy remediation, identity hardening
Status: ✅ Complete

Objective
Provision a deliberately misconfigured Azure environment, use Defender for Cloud to identify and triage security findings, apply Azure Policy aligned to the CIS Azure Foundations Benchmark, and harden identity access using PIM and RBAC.

Environment
ComponentDetailsPlatformMicrosoft AzureSecurity toolMicrosoft Defender for CloudBenchmarkCIS Azure Foundations Benchmark v2.0IdentityAzure Active Directory / Microsoft Entra IDIaCAzure Portal + Azure Policy

What I Did
1. Misconfigured Environment Setup
Provisioned an Azure environment with the following deliberate misconfigurations:

Storage accounts with public blob access enabled
VMs without disk encryption
Missing diagnostic logging on key resources
Over-privileged IAM role assignments
No MFA enforced on accounts
Key Vault with soft delete disabled


2. Defender for Cloud Assessment
Opened Defender for Cloud and triaged 25+ security recommendations across:
CategoryFindingsIdentity & Access8 findingsData & Storage7 findingsNetworking5 findingsCompute4 findingsKey Vault3 findings
Initial Secure Score: 42%

3. CIS Azure Foundations Benchmark — Azure Policy
Applied Azure Policy definitions aligned to CIS Azure Foundations Benchmark v2.0:

Assigned built-in CIS policy initiative to the subscription
Identified 14 non-compliant resources
Automated remediation for:

Enable soft delete on Key Vaults
Enforce HTTPS on storage accounts
Enable diagnostic logs on Azure services
Require encryption at rest on VMs




4. RBAC — Least Privilege
Audited existing role assignments and applied least privilege:
BeforeAfterUsers assigned Owner at subscription scopeScoped to resource group levelShared service account credentialsIndividual identities per servicePermanent admin accessJIT access via PIM

5. Privileged Identity Management (PIM)
Configured PIM for just-in-time admin access:

Converted permanent Global Admin assignments to eligible assignments
Set activation requirements: MFA + business justification
Configured 1-hour maximum activation window
Enabled approval workflow for Global Administrator activation


Results
MetricBeforeAfterSecure Score42%78%Critical findings120High findings133Non-compliant resources140Permanent admin assignments40

Skills Demonstrated

Microsoft Defender for Cloud
Azure Policy and initiative assignment
CIS Azure Foundations Benchmark v2.0
RBAC least privilege design
Privileged Identity Management (PIM)
Just-in-time access configuration
Azure misconfiguration remediation
Risk register documentation
