Lab 06 — Web Application Security: biknibottom.com
Certifications applied: CompTIA Network+ · CompTIA Security+ · Google Cybersecurity
Focus: Live e-commerce threat monitoring, WAF configuration, traffic analysis
Status: 🔄 In Progress

Objective
Use a self-owned and operated live Shopify e-commerce site (biknibottom.com) as a real-world security testing environment. Configure perimeter defences, simulate malicious traffic, analyse logs for threat indicators, and conduct authorised vulnerability scanning — mapping all findings to the OWASP Top 10.

Why This Lab is Different
Most cybersecurity home labs use isolated virtual machines or intentionally vulnerable applications. This lab uses a live, production e-commerce website that I own and operate. This means:

Real traffic from real users and bots worldwide
Real security events from Cloudflare WAF
Real attack attempts that occur organically
A genuine stake in the security of the environment


Environment
ComponentDetailsTargetbiknibottom.com (Shopify e-commerce)WAF / CDNCloudflare (free tier)AnalyticsGoogle Analytics 4ScannerNmap · NiktoSimulationPython · requests · LocustFrameworkOWASP Top 10

Lab Phases
Phase 1 — Cloudflare WAF Setup ✅

Pointed biknibottom.com DNS through Cloudflare
Configured custom firewall rules:

Rate limiting on /account/login — max 10 requests/minute
Block requests with suspicious user agents
Country-based access rules
Bot Fight Mode enabled


Enabled security event logging

Phase 2 — Google Analytics 4 Integration ✅

Connected GA4 to Shopify via native integration
Configured custom events for security-relevant behaviours:

Repeated failed checkout attempts
Unusual geographic traffic spikes
High-frequency page requests from single sessions



Phase 3 — Attack Traffic Simulation 🔄
Simulated the following attack patterns using Python:
Credential stuffing simulation:
pythonimport requests
import time

target = "https://biknibottom.com/account/login"
fake_credentials = [
    {"customer[email]": "test1@gmail.com", "customer[password]": "password123"},
    {"customer[email]": "admin@biknibottom.com", "customer[password]": "admin123"},
]
headers = {"User-Agent": "SecurityLabTest/1.0"}

for cred in fake_credentials:
    r = requests.post(target, data=cred, headers=headers)
    print(f"{cred['customer[email]']} → {r.status_code}")
    time.sleep(2)
Bot/crawler simulation:
pythonpages = [
    "https://biknibottom.com",
    "https://biknibottom.com/collections/all",
    "https://biknibottom.com/admin",  # admin probe
]
for page in pages:
    r = requests.get(page, headers={"User-Agent": "python-requests SecurityTest"})
    print(f"{page} → {r.status_code}")
Phase 4 — Log Analysis & IOC Extraction 🔄
Built a Python script to parse Cloudflare security event exports:
pythonimport csv
from collections import Counter

def analyse_cloudflare_log(filepath):
    ips, paths, blocked = [], [], []
    with open(filepath) as f:
        reader = csv.DictReader(f)
        for row in reader:
            ips.append(row.get('ClientIP', ''))
            paths.append(row.get('ClientRequestPath', ''))
            if row.get('EdgeResponseStatus') == '403':
                blocked.append(row.get('ClientIP', ''))

    print("Top IPs:", Counter(ips).most_common(5))
    print("Top paths:", Counter(paths).most_common(5))
    print("Blocked IPs:", set(blocked))

analyse_cloudflare_log("cloudflare_export.csv")
Phase 5 — Vulnerability Scanning 🔄
Authorised scanning of own domain:
bash# Nmap — open port discovery
nmap -sV -p 80,443,8080,8443 biknibottom.com

# Nikto — web vulnerability scan
nikto -h https://biknibottom.com
Phase 6 — OWASP Top 10 Remediation Report 🔄
Mapping all findings to OWASP Top 10 and documenting remediation steps.

OWASP Top 10 Coverage
OWASP CategoryTestedFindingRemediatedA01 Broken Access Control✅Admin path probe (403)✅A02 Cryptographic Failures✅HTTPS enforced via Cloudflare✅A05 Security Misconfiguration🔄In progress🔄A07 Auth Failures✅Rate limiting on login✅A09 Logging & Monitoring✅Cloudflare + GA4 active✅

Skills Demonstrated

Cloudflare WAF configuration and firewall rules
Google Analytics 4 for behavioural anomaly detection
Python scripting for traffic simulation and log analysis
Nmap and Nikto vulnerability scanning
OWASP Top 10 framework application
Security event log analysis
Real-world web application security testing
