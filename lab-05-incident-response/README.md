Lab 05 — Google Cybersecurity Capstone: Incident Response Simulation
Certifications applied: Google Cybersecurity Professional Certificate · CompTIA Security+
Focus: End-to-end incident response using NIST SP 800-61
Status: ✅ Complete

Objective
Simulate a phishing-led ransomware intrusion against a realistic environment, conduct a full end-to-end incident response exercise following NIST SP 800-61, and produce a professional incident report with IOCs, timeline, and lessons learned.

Environment
ComponentDetailsIR FrameworkNIST SP 800-61 Rev 2Packet analysisWireshark · tcpdumpLog analysisWindows Event ViewerScriptingPython 3DocumentationMarkdown · GitHub

Scenario

A phishing email was delivered to an employee containing a malicious invoice attachment. The user opened the file, triggering execution of a dropper that established C2 communication and began encrypting network shares. The SOC detected anomalous outbound traffic and elevated file modification activity 31 minutes after initial compromise.


NIST SP 800-61 Phases
Phase 1 — Preparation

Defined incident response roles and responsibilities
Documented asset inventory for the simulated environment
Established communication and escalation procedures

Phase 2 — Detection & Analysis
Packet capture analysis (Wireshark/tcpdump):

Identified DNS queries to suspicious domain: update-service[.]xyz
Detected HTTP POST requests to 185.220.x.x — known C2 IP range
Found large outbound data transfer (2.3GB) post-compromise

Windows Event Log analysis:
Event IDDescriptionFinding4688Process createdinvoice.exe spawned cmd.exe4663File accessMass .docx → .encrypted renames7045New service installedPersistence mechanism detected4625Failed logonLateral movement attempt
Python IOC Extractor:
Built a Python script to automate IOC extraction from log files:
pythonimport re
from collections import Counter

def extract_iocs(filepath):
    iocs = {"ips": [], "domains": [], "event_ids": []}
    ip_pattern = re.compile(r'\b(?:\d{1,3}\.){3}\d{1,3}\b')
    domain_pattern = re.compile(r'(?:[a-zA-Z0-9-]+\.)+[a-zA-Z]{2,}')

    with open(filepath, 'r', errors='ignore') as f:
        for line in f:
            iocs["ips"].extend(ip_pattern.findall(line))
            iocs["domains"].extend(domain_pattern.findall(line))
            if "EventID" in line:
                iocs["event_ids"].append(line.strip())

    print("Top IPs:", Counter(iocs["ips"]).most_common(5))
    print("Top Domains:", Counter(iocs["domains"]).most_common(5))

extract_iocs("sample_events.log")
Phase 3 — Containment
Short-term containment:

Isolated the affected workstation from the network
Blocked C2 IP range at the firewall
Disabled compromised user account

Long-term containment:

Reset all domain user credentials
Patched the vulnerability exploited by the dropper
Deployed updated AV signatures

Phase 4 — Eradication

Removed malicious files and registry persistence keys
Rebuilt the affected workstation from a clean image
Verified no remaining C2 communication

Phase 5 — Recovery

Restored encrypted files from backup
Monitored environment for 72 hours post-recovery
Confirmed no re-infection

Phase 6 — Lessons Learned

Email filtering — implement attachment sandboxing to detonate suspicious files before delivery
Macro execution — disable Office macros by Group Policy across all endpoints
Network segmentation — limit lateral movement by segmenting workstations from file servers
PowerShell logging — enable Script Block Logging (Event ID 4104) for better visibility
Backup verification — test restoration procedures quarterly to ensure backups are viable


Indicators of Compromise (IOCs)
TypeValueSourceIP185.220.x.xPCAP — C2 communicationDomainupdate-service[.]xyzDNS query logsFileinvoice.exeProcess creation logFile hash[SHA256 in screenshots]EndpointRegistry keyHKCU\Software\Microsoft\Windows\CurrentVersion\RunPersistence

Attack Timeline
TimeEventSource09:14Phishing email receivedEmail logs09:22User opened invoice.exeEndpoint09:23cmd.exe spawned by invoice.exeEvent ID 468809:31C2 beacon establishedPCAP09:45File encryption beginsEvent ID 466309:52SOC alert triggeredSIEM10:05Workstation isolatedIR team

Skills Demonstrated

NIST SP 800-61 incident response framework
Packet capture analysis (Wireshark, tcpdump)
Windows Event ID correlation
Python scripting for IOC extraction
Incident report writing
Attack timeline reconstruction
IOC documentation
