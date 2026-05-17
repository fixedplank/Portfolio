Lab 03 — Network Traffic Analysis & Firewall Lab
Certifications applied: CompTIA Network+ · CompTIA Security+
Focus: Packet analysis, IDS rule writing, perimeter defence
Status: ✅ Complete

Objective
Capture and analyse live network traffic to identify malicious patterns, deploy and configure a pfSense firewall with VLAN segmentation and VPN, and build custom Snort IDS rules to detect common attack signatures.

Environment
ComponentDetailsPacket analyserWiresharkFirewallpfSense (VM)IDSSnortScannerNmapHypervisorVirtualBoxNetworkHome network + isolated lab VMs

What I Did
1. Live Traffic Capture — Wireshark
Captured live traffic from my home network interface and identified the following:
DNS Exfiltration Pattern
Detected unusually long DNS query strings — a common data exfiltration technique where data is encoded in DNS subdomain labels.
suspicious-query.attacker-domain.com → 253 character subdomain
Cleartext Credentials in HTTP
Identified POST requests over HTTP (port 80) transmitting login credentials in plaintext — visible directly in Wireshark packet details.
ARP Spoofing
Detected duplicate ARP replies for the same IP from different MAC addresses — classic ARP poisoning indicator.

2. pfSense Firewall Deployment
Deployed pfSense as a virtual firewall with:

WAN interface — simulated external network
LAN interface — internal lab network
Firewall rules — explicit allow/deny rules for each interface
VLAN segmentation — separated lab traffic into isolated VLANs
Site-to-site IPsec VPN — configured encrypted tunnel between two VirtualBox networks


3. Snort IDS — Custom Rules
Installed Snort on pfSense and wrote custom detection rules:
bash# Detect Nmap SYN scan
alert tcp any any -> $HOME_NET any (msg:"Nmap SYN Scan Detected"; flags:S; threshold:type threshold, track by_src, count 20, seconds 1; sid:1000001;)

# Detect SMB enumeration
alert tcp any any -> $HOME_NET 445 (msg:"SMB Enumeration Attempt"; content:"|FF|SMB"; sid:1000002;)

# Detect ICMP flood
alert icmp any any -> $HOME_NET any (msg:"ICMP Flood Detected"; threshold:type threshold, track by_src, count 100, seconds 1; sid:1000003;)
Result: 0% false-negative rate on all tested signatures during controlled testing.

Key Findings
FindingSeverityRemediationCleartext HTTP credentialsHighForce HTTPS, implement HSTSDNS exfiltration patternHighDNS filtering, query length limitsARP spoofing detectedMediumEnable Dynamic ARP InspectionOpen ports on WANMediumRestrict pfSense WAN rules

Skills Demonstrated

Wireshark packet capture and protocol analysis
pfSense firewall deployment and rule configuration
VLAN design and segmentation
IPsec VPN configuration
Snort IDS rule writing
Nmap network scanning
Network attack pattern recognition
TCP/IP protocol analysis
