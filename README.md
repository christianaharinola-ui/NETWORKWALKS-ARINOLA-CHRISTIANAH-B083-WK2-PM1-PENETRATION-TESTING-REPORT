# 🛡️ Penetration Testing Report

## Footprinting & Network Scanning Phases

![Skill](https://img.shields.io/badge/Skill-Cybersecurity-red)
![Skill](https://img.shields.io/badge/Skill-Reconnaissance-red)
![Skill](https://img.shields.io/badge/Skill-Footprinting-red)
![Skill](https://img.shields.io/badge/Skill-Network%20Scanning-red)
![Tool](https://img.shields.io/badge/Tool-whois-blue)
![Tool](https://img.shields.io/badge/Tool-whatweb-blue)
![Tool](https://img.shields.io/badge/Tool-nslookup-blue)
![Tool](https://img.shields.io/badge/Tool-curl-blue)
![Tool](https://img.shields.io/badge/Tool-wafw00f-blue)
![Tool](https://img.shields.io/badge/Tool-dnsrecon-blue)
![Tool](https://img.shields.io/badge/Tool-Maltego-blue)
![Tool](https://img.shields.io/badge/Tool-theHarvester-blue)
![Tool](https://img.shields.io/badge/Tool-Zenmap-blue)
![Kali Linux](https://img.shields.io/badge/Kali-Linux-purple)
![NetworkWalks](https://img.shields.io/badge/NetworkWalks-grey)
![Ethical Hacking](https://img.shields.io/badge/Ethical%20Hacking-black)


## 1. Liability Disclaimer

I have performed these activities only on the systems & devices where I had secured written permission or the devices/systems that I own myself. All these materials are for education and research purposes only. Do not use anything from here to break the law. The instructor, the authors and Networkwalks are not responsible for what I do with this knowledge. Every action I take is my own responsibility. Misuse can lead to criminal charges, heavy fines, loss of my job and a permanent record. In most countries unauthorised access is a crime even when nothing is damaged.

## 2. Introduction

This report covers footprinting the networkwalks.com domain using multiple Kali Linux tools (W2-PM1), Google Hacking Database techniques (W2-PM2), Maltego (W2-PM3), and theHarvester (W2-PM4), along with scanning my own local network with Zenmap (W2-PM5). Together, these modules show how an attacker moves from gathering public information about a target, to mapping relationships between that target's infrastructure, to discovering live hosts on a network. It is the Week 2 part of my ongoing internship program at Networkwalks.

All commands were run in Kali Linux (footprinting, GHDB, theHarvester), on a Windows PC (Maltego, Zenmap). Every step below includes the exact command or method used, the result I observed, a screenshot as evidence, and a short note on why the finding matters from an attacker's point of view.

## 3. Tools Used

| Tool | Purpose |
|---|---|
| Kali Linux & Windows | Operating systems used for reconnaissance and scanning activities |
| WHOIS | Find domain registration details (owner, dates, name servers) |
| WhatWeb | Fingerprint web technologies (server, CMS, plugins, IP) |
| Nslookup | Resolve the domain name to its IP address using DNS |
| Curl -I | Read the HTTP response headers of the website |
| Wafw00f | Detect whether a Web Application Firewall protects the site |
| DNSRecon | Enumerate all DNS records (NS, MX, SPF, TXT, SRV) |
| Google Search / GHDB | Locate publicly indexed files and misconfigured resources via advanced search operators |
| Maltego (Community Edition) | Visualize relationships between a domain, its emails, and its infrastructure |
| theHarvester | Gather emails, subdomains and hosts related to a target organization from public sources |
| Zenmap (Nmap GUI) | Scan the local subnet to find live hosts, IPs and MAC addresses |
| Windows CMD | Local IP and MAC address identification |

## 4. Activities Performed

### 4.1 W2-PM1 — Footprinting & Reconnaissance with Multiple Kali Tools

**Target:** networkwalks.com (with permission)

This module covered six tasks, each using a different Kali Linux tool to gather a specific type of public information about the target domain.

**Task 1 — WHOIS lookup**
I ran `whois networkwalks.com` in a Kali Linux terminal to query the public domain registration record. The output showed the domain is registered through GoDaddy, was created on 06 November 2019 and expires 06 November 2027, and uses name servers NS6135.HOSTGATOR.COM and NS6136.HOSTGATOR.COM — revealing HostGator as the hosting provider without ever touching the live website.

<img width="1302" height="533" alt="who is" src="https://github.com/user-attachments/assets/2529b935-2031-41ba-957f-b4c7e5ad310d" />


**Task 2 — Web technology fingerprinting with WhatWeb**
I ran `whatweb networkwalks.com` to fingerprint the technologies running on the site. The output identified Apache as the web server, WordPress version 7.1.1 as the CMS, the WP Download Manager plugin (v3.3.58), the jQuery version in use, and the server's IP address (192.232.216.135) — the kind of detail an attacker would cross-reference against known vulnerability databases for that exact software version.

<img width="1492" height="792" alt="whatweb" src="https://github.com/user-attachments/assets/d023247a-abb3-4419-a236-9f7a9c697f02" />

**Task 3 — DNS resolution with Nslookup**
I ran `nslookup networkwalks.com` to resolve the domain name to its IP address using DNS. The result confirmed 192.232.216.135, matching what WhatWeb had already surfaced, giving a direct, confirmed target address for any further network-level scanning.

<img width="1355" height="658" alt="nslook" src="https://github.com/user-attachments/assets/5b685d25-a068-40db-94d9-41d3fb78d543" />

**Task 4 — HTTP header inspection with Curl**
I ran `curl -I https://networkwalks.com` to read the site's HTTP response headers without loading the full page. This revealed the Apache server banner, cookie and caching details, and a hidden WordPress REST API endpoint (`/wp-json/`) — information gathered without a single full page request.

<img width="1912" height="528" alt="curl" src="https://github.com/user-attachments/assets/a773daf4-a38a-4c12-814c-578008a304a7" />

**Task 5 — Web Application Firewall detection with Wafw00f**
I ran `wafw00f networkwalks.com` to check whether a firewall was protecting the site. It confirmed the site sits behind ModSecurity (SpiderLabs) WAF, telling an attacker that naive automated attack attempts would likely be blocked or logged.

<img width="1328" height="439" alt="wafwoof" src="https://github.com/user-attachments/assets/339be9be-9bee-4477-b334-0bbf05e5a1a2" />

**Task 6 — Full DNS enumeration with DNSRecon**
I ran `dnsrecon -d networkwalks.com` to enumerate the domain's complete DNS record set. It returned 8 records in total, including SOA/NS records confirming HostGator, an MX mail server record, SPF/TXT policy records, and cPanel-related SRV service records — the fullest single-tool picture of the domain's infrastructure gathered in this module.

<img width="1541" height="443" alt="dns" src="https://github.com/user-attachments/assets/8c159b5f-6e81-4c1c-9824-2b6474665aed" />

**Troubleshooting note:** before I could run any of these commands, my Kali VM lost internet connectivity after an earlier static IP configuration. I diagnosed this using a known fix for a DAD-timeout issue affecting newer Kali releases. 

### 4.2 W2-PM2 — Footprinting with GHDB (Google Hacking Database)

**Target:** Publicly indexed web content (via Google search operators)
This module covered two tasks using Google dorking — advanced search operators that surface specific, narrowly-targeted public content.

### 4.3 W2-PM3 — Footprinting with Maltego

**Task 1 — Install Maltego**
I downloaded and installed Maltego Community Edition (free tier) from the official maltego.com website onto my Windows host machine, completed the setup wizard, and registered a free Maltego account to activate the application. 

<img width="1763" height="997" alt="maltego start" src="https://github.com/user-attachments/assets/268c18a2-a924-42f4-8d1e-4887eab88c58" />

**Task 2 — Find email addresses related to networkwalks.com**
I added a Domain entity to the Maltego graph canvas and set its value to networkwalks.com. I then ran email-discovery transforms against it.

<img width="1761" height="949" alt="maltego rename" src="https://github.com/user-attachments/assets/d80f2748-6bd9-44be-922a-2f0704a22647" />

### 4.4 W2-PM4 — Footprinting with theHarvester

**Task 1 — theHarvester with Baidu source, limit 1000**

<img width="1877" height="572" alt="Harvester result" src="https://github.com/user-attachments/assets/7367ccac-955a-46e6-958d-8a33bbbdec5c" />

**Task 2 — theHarvester with all sources, limit 50**

<img width="1920" height="864" alt="Harvester, task 2" src="https://github.com/user-attachments/assets/54a039f3-c367-43f3-9297-730ec40d706a" />

### 4.5 W2-PM5 — Network Scanning with Zenmap

**Target:** My own local LAN network

**Task 1 — Install Zenmap**
I downloaded and installed Zenmap (the official GUI for Nmap) from the official nmap.org website onto my Windows PC, including the required Npcap component.

<img width="686" height="680" alt="zen entry" src="https://github.com/user-attachments/assets/87298eb4-f801-410b-88dd-68292f07c374" />

**Task 2 — Find local IP address and LAN subnet**
I opened Command Prompt and ran `ipconfig`, which showed my IPv4 address and subnet mask. Based on a /24 subnet mask, I determined my scanning target as `192.168.1.0/24`.

<img width="1073" height="647" alt="ip address" src="https://github.com/user-attachments/assets/a993061a-a5cd-40e4-8b89-3bf2f11c9549" />

**Task 3 — Find live hosts in the subnet**
I entered `192.168.1.0/24` as the target in Zenmap and selected the "Ping scan" profile, then ran the scan. The raw output was recorded.

<img width="1915" height="995" alt="hang up" src="https://github.com/user-attachments/assets/05961e7f-71da-4625-b653-6e16b10e5834" />


## 7. Conclusion

During Week 2 of my Cybersecurity & Ethical Hacking internship, I completed practical activities covering footprinting, reconnaissance, and network scanning across five project modules.

I learned how ordinary search engine operators can surface misconfigured or unintentionally public resources, and just as importantly. I learned how visualizing relationships between a domain, its emails and its infrastructure can reveal connections that are harder to spot in plain text output, even when working within the limits of a free-tier account.

Across all five modules, the exercises showed me that information gathering is a foundational part of cybersecurity work. Even before attempting to exploit a system, a security professional can learn a significant amount about an environment by carefully analyzing publicly available information and network responses.

I also learned that technical findings should be documented clearly. A good cybersecurity report should explain what was performed, what was discovered, what the observation means, what risk it may create, and what can be done to reduce that risk.

Finally, I learned that reconnaissance and scanning must always be performed within an authorized scope. 

## 👤 Author

Arinola Oluwadamilola Christianah
Cybersecurity Intern, Batch B083

LinkedIn: https://www.linkedin.com/in/christianaharinola/

## 📌 Project Information

**Program Name:** Cybersecurity program at Networkwalks | **Week:** 02 | **Repository:** GitHub
