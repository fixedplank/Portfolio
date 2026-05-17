Lab 02 — Active Directory Attack & Defence
Certifications applied: CompTIA Security+ · AZ-500
Focus: On-premises identity attack simulation and hardening
Status: ✅ Complete

Objective
Build a realistic Active Directory environment from scratch, execute common identity-based attacks used by real threat actors, and harden the environment against each attack vector — correlating findings to Windows Event IDs throughout.

Environment
ComponentDetailsHypervisorVirtualBoxDomain ControllerWindows Server 2022WorkstationWindows 10 (domain-joined)Attack toolsImpacket · RubeusReferenceTryHackMe AD labs

What I Built
1. Active Directory Lab Setup

Deployed Windows Server 2022 as a Domain Controller in VirtualBox
Configured a domain: lab.local
Joined a Windows 10 workstation to the domain
Created multiple user accounts with varying privilege levels
Configured service accounts with SPNs (deliberately vulnerable for Kerberoasting)

2. Attacks Executed
Password Spraying
Simulated a low-and-slow credential attack targeting multiple accounts with common passwords to avoid lockout thresholds.
bash# Using Impacket
python3 spray.py -U users.txt -p 'Password123' lab.local
Event IDs to monitor:

4625 — Failed logon
4648 — Logon attempt with explicit credentials


Kerberoasting
Requested Kerberos service tickets for accounts with SPNs and attempted offline cracking.
bash# Using Impacket GetUserSPNs
python3 GetUserSPNs.py lab.local/user:password -dc-ip 192.168.x.x -request
Event IDs to monitor:

4769 — Kerberos service ticket requested (encryption type 0x17 = RC4 = suspicious)


AS-REP Roasting
Targeted accounts with Kerberos pre-authentication disabled to obtain crackable hashes without any credentials.
bash# Using Impacket GetNPUsers
python3 GetNPUsers.py lab.local/ -usersfile users.txt -no-pass -dc-ip 192.168.x.x
Event IDs to monitor:

4768 — Kerberos authentication ticket requested


3. Hardening Applied
AttackHardening ActionPassword SprayingEnforced fine-grained password policy · enabled account lockout after 5 attemptsKerberoastingReplaced RC4 with AES256 for service account encryption · used strong 25+ char service account passwordsAS-REP RoastingEnabled Kerberos pre-authentication on all accounts · added vulnerable accounts to Protected Users groupGeneralDisabled unconstrained delegation · enabled Protected Users security group for privileged accounts

Key Findings

All three attacks succeeded against the default misconfigured environment
Kerberoasting extracted a crackable RC4 hash within seconds
AS-REP roasting worked against 2 of 5 test accounts that had pre-auth disabled
After hardening, all three attack vectors were fully mitigated
Event ID correlation confirmed detection was possible for all attacks with proper SIEM rules


Skills Demonstrated

Active Directory deployment and configuration
Kerberos authentication protocol understanding
Offensive tooling: Impacket, Rubeus
Windows Event ID correlation
Active Directory hardening
Fine-grained password policies
Protected Users security group
MITRE ATT&CK mapping: T1558 (Kerberoasting), T1110 (Password Spraying)
