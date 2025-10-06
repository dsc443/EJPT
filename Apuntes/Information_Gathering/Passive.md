# Passive Information Gathering
---

## Summary
Passive information gathering (OSINT) collects data about a target without direct interaction with its systems — using public sources, archives, certificate transparency, WHOIS, DNS history, search engines and third-party services. It is non-intrusive and should be the first step in any engagement.

---

## Contents
1. Footprinting & site discovery  
2. WHOIS enumeration  
3. DNS reconnaissance (passive)  
4. Technology and stack discovery (BuiltWith, Wappalyzer, WhatWeb)  
5. Subdomain enumeration & Google Dorks  
6. Email harvesting (theHarvester)  
7. WAF detection (WafW00f)  
8. Tools, commands & cheat sheet  

---

## 1) Footprinting & site discovery

**Concept**  
Footprinting gathers high-level information about a target: domains, hosts, site structure, sitemap and robots directives.

**Useful files on websites**
- `/robots.txt` — lists paths disallowed for crawlers; can reveal hidden directories.
- `/sitemap.xml` — structured map of publicly indexable URLs.

**Examples**
```bash
# View robots.txt
curl -s https://example.com/robots.txt

# Download sitemap.xml
curl -s https://example.com/sitemap.xml | xmllint --format -
```

**Full site download**
- `HTTrack` can mirror a site locally:
  - Website: https://www.httrack.com/

**Browser plugins**
- **BuiltWith** (Firefox/Chrome): technology profiler.  
- **Wappalyzer**: identifies frameworks, CMS, analytics, libraries.

**Terminal tool**
- `WhatWeb` — technology discovery:
```bash
whatweb https://example.com
```

---

## 2) WHOIS enumeration

**Concept**  
WHOIS reveals domain registration metadata: registrar, registration/expiry dates, registrant contacts, and sometimes technical contacts. For IP queries, WHOIS shows netblock allocation and organization.

**Commands**
```bash
# Domain WHOIS
whois example.com

# IP WHOIS
whois 192.0.2.1
```

**Web services**
- whois.domaintools.com  
- who.is

**Automated frameworks**
- `theHarvester` — gathers WHOIS and other OSINT data.  
- `Recon-ng` — modular framework with WHOIS and other OSINT modules.

**Notes**
- Some registrars redact personal data (GDPR/privacy protection). Try registrar-specific WHOIS or RDAP for structured data.

---

## 3) DNS reconnaissance (passive)

**Concept**  
Passive DNS collects historical and current DNS records from third-party sources (DNS archives, DNSDB), revealing subdomains, mail servers, name servers, and TXT records without querying the target's authoritative servers directly.

**Common record types**
- `NS`, `A`, `AAAA`, `MX`, `CNAME`, `TXT`

**Tools & services**
```bash
# dnsrecon (passive & active options)
dnsrecon -d example.com

# Online passive DNS / mapping
dnsdumpster.com
securitytrails.com (commercial)
virustotal.com (passive DNS via API)
```

**Notes**
- Passive DNS sources may show historical records and related domains. Use them to expand subdomain lists and identify third-party infrastructure.

---

## 4) Technology & stack discovery

**Browser-based**
- BuiltWith, Wappalyzer: quick overview of CMS, server, analytics and libraries.

**Command-line**
- `WhatWeb` — fingerprints web server and technologies.
```bash
whatweb https://example.com
```

**Netcraft**
- Web-based reports: SSL/TLS, hosting history, technologies, trackers.
- Visit https://www.netcraft.com/ and search the domain.

---

## 5) Subdomain enumeration & Google Dorks

**Subdomain enumeration**
- `Sublist3r` — OSINT-based subdomain discovery:
```bash
sublist3r -d example.com
# specify engines:
sublist3r -d example.com -e Google,Bing
```
- Also consider: `amass`, `assetfinder`, `crt.sh` (certificate transparency logs).

**Google Dorks**
- Use advanced search queries to find exposed content:
```
site:example.com inurl:admin
intitle:"Index of"
filetype:pdf site:*.example.com
cache:example.com
```
- Google Hacking Database: https://www.exploit-db.com/google-hacking-database

**Notes**
- Combine multiple sources: CT logs, DNS archives, search engines, and web archives (Wayback Machine) to build a comprehensive list.

---

## 6) Email harvesting (theHarvester)

**Purpose**
- Gather email addresses and related subdomains from public sources (search engines, PGP keys, social media).

**Example**
```bash
theHarvester -d example.com -b google,bing
```

**Sources**
- google, bing, yahoo, baidu, pgp, linkedin, twitter, pastebin, etc.

**Notes**
- Verify harvested emails (don't send unsolicited emails). Treat as sensitive data.

---

## 7) WAF detection (WAFW00F)

**Purpose**
- Detect the presence/type of Web Application Firewalls by analyzing HTTP responses.

**Commands**
```bash
# list known WAFs
wafw00f -l

# detect WAF
wafw00f https://example.com

# verbose
wafw00f https://example.com -a
```

**Notes**
- WAF detection helps plan later testing and evasion strategies (authorized only).

---

## 8) Tools, commands & cheat sheet

```bash
# Footprinting / site files
curl -s https://example.com/robots.txt
curl -s https://example.com/sitemap.xml

# Technology detection
whatweb https://example.com
# Browser: BuiltWith, Wappalyzer

# WHOIS
whois example.com
whois 192.0.2.1

# Passive DNS / subdomain mapping
dnsrecon -d example.com
dnsdumpster.com
sublist3r -d example.com
amass enum -d example.com
crt.sh

# Email harvesting
theHarvester -d example.com -b google,bing

# WAF detection
wafw00f https://example.com
```

---

## 9) Ethics, data handling & notes

- Passive recon is non-intrusive but can reveal sensitive operational details — **treat findings as confidential**.  
- Document sources and timestamps for every finding.  
- If you need to escalate to active techniques, obtain written authorization and update scope.  
- Respect robots.txt and terms of service when using scraping tools.

---
