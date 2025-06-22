# telstra-security-experience
Report from the Telstra Cybersecurity Virtual Experience.
# Incident Response & Mitigation of Spring4Shell (CVE-2022-22965) - A Virtual Cybersecurity Experience

## Overview

This report summarizes the incident response process and mitigation strategy developed during the [Telstra Cybersecurity Virtual Experience](https://www.theforage.com/simulations/telstra/cybersecurity-cyyo), focusing on a simulated [Spring4Shell (CVE-2022-22965)](https://nvd.nist.gov/vuln/detail/cve-2022-22965) exploitation incident. The objective was to identify the attack, develop a mitigation plan, implement a firewall rule, and document the incident in a post-mortem report. This report serves as a practical demonstration of incident response capabilities, critical for maintaining cybersecurity posture in modern IT environments.

## Incident Details

* **Incident Type:** Critical Malware Attack / Exploitation of Spring4Shell (CVE-2022-22965)
* **Target System:** NBN Connection (nbn.external.network)
* **Incident Start:** 2022-03-20T03:16:34Z
* **Incident Resolution:** 2022-03-20T05:16:34Z (2 hours after start)
* **Impact:** P1 Critical - Service unavailable, functionality impaired due to Remote Code Execution (RCE) and web shell deployment. The RCE capability allowed for unauthorized command execution and web shell deployment, compromising the server's integrity and rendering the service inoperable.
* **Teams Involved:** Telstra Security Operations, nbn Team, Networks Team.

## Attack Analysis & Mitigation Strategy

**Detection:** The incident was detected through firewall logs, showing high volumes of `POST` requests to a suspicious path, marked with `action: bypass`.

**Attack Pattern:** Analysis revealed the malicious traffic targeted `nbn.external.network` using specific HTTP headers (`c1=Runtime` and `c2=<%`) in conjunction with requests to `/tomcatwar.jsp`. This pattern confirmed the exploitation of CVE-2022-22965 (Spring4Shell). The attack was distributed, rendering IP-based blocking ineffective. The attack pattern was identified by correlating observed signatures with publicly available Proof-of-Concept (PoC) information for Spring4Shell.

**Root Cause:** The primary cause was the presence of a vulnerable Spring Framework (version 5.3.0) on the NBN Connection, which was successfully exploited.

**Mitigation Strategy:** Based on the identified patterns, two main firewall rules were proposed and implemented:
1.  Block `POST` requests simultaneously containing HTTP headers `c1=Runtime` and `c2=<%`.
2.  Block all requests targeting the URI path `/tomcatwar.jsp`.

## Technical Implementation (Firewall Rule Simulation)

A simulated firewall server (`firewall_server.py` in Python) was developed to implement the proposed rules. The code inspected incoming `POST` requests, checking for the presence and values of the `c1` and `c2` headers, as well as the request URI path. If a match was found (i.e., `c1='Runtime'` AND `c2='<%'`, OR `path='/tomcatwar.jsp'`), the server would respond with `403 Forbidden`, simulating a successful block. Testing with `test_requests.py` confirmed the rules effectively blocked the malicious traffic. The implementation was performed in a self-created, isolated virtual Kali Linux environment, to maintain maximum security, despite laboratory conditions. The implementation was performed using the Python `http.server` module to simulate the behavior of a network firewall.

## Lessons Learned

This virtual experience highlighted several key aspects of cybersecurity incident response:

* **Importance of Log Analysis:** Detailed log analysis is crucial for identifying sophisticated attack patterns that bypass traditional defenses.
* **Precision in Mitigation:** Generic blocking methods (like IP-based) are often insufficient against distributed or polymorphic attacks; hence, the focus on unique payload signatures proved critical for effective defense.
* **Collaboration:** Effective incident response requires seamless communication and collaboration between Security Operations, Infrastructure, and Network teams, highlighting the importance of clear communication channels and defined roles.
* **Proactive Vulnerability Management:** Regular patching and vulnerability assessments are vital to prevent such exploits.

---
*This report has been prepared as a summary of Telstra cybersecurity's virtual experience on The Forage.*
