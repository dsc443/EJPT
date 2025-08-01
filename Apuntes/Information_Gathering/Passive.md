INFORMATION GATHERING:

Passive information gathering

-FOOTPRINTING

In the terminal, use the host command along with the URL to perform a DNS lookup (to find the IP address).

To gather information about a website, it’s useful to check the /robots.txt file. This file indicates which parts of the site the site owner doesn’t want search engines to index.

Another useful file is sitemap.xml. This file provides search engines with a structured map of the website to help them index it more efficiently.

Browser Plugins:

For Mozilla Firefox, the plugin BuiltWith is a technology profiler that shows what technologies or content management systems a website is running.

Another popular plugin is Wappalyzer, which also identifies technologies used by websites.


In the terminal, you can use the tool WhatWeb to identify technologies running on a website.

You can download an entire website using the tool available at httrack.com.




WHOIS ENUMERATION

This process involves identifying when a domain was registered, who owns it, and which registrar was used.

Running a Whois query in the terminal provides detailed information about the domain or website, such as registration date, owner contact details, registrar name, and domain status.

When querying an IP address or a range of IPs, Whois returns information including the network range, CIDR notation of the subnet, organization name, and contact information.


Common Tools and Commands:

Terminal Command:

whois example.com

Returns detailed domain registration info.

For IP addresses:

whois 192.168.1.1

Shows network allocation, ISP, and organization details.

Online Whois services:

whois.domaintools.com

who.is


Automated enumeration tools:

theHarvester: Gathers Whois info among other OSINT data.

Recon-ng: Modular framework with Whois lookup modules.




FOOTPRINTING WITH NETCRAFT.COM

Netcraft.com provides detailed information about a website, including its background, network details, SSL/TLS certificates, and web technologies used.

It shows information about IP delegation, certificate transparency logs, and name servers.

The tool can also identify if the site is vulnerable to SSL/TLS-related issues.

Important details include the validity period of the SSL/TLS certificate, who issued it, and the issuer’s country.

Web Trackers:
Netcraft reveals which analytics systems or trackers are currently active on the website.

Site Technology:
It identifies proxy servers in use on the server side and what technologies are running on the site.


Example of usage:

Visit https://www.netcraft.com/.

Enter the target domain in the search bar (e.g., example.com).

Review the report sections for SSL/TLS certificate details, network information, and technology profile.

Use this information to assess the site's infrastructure, security posture, and tracking technologies.




DNS RECONNAISSANCE (PASSIVE)

Passive DNS reconnaissance focuses on identifying DNS records related to a specific domain. This includes records such as mail servers, IP addresses, TXT records, and others.

The goal is to gain a better understanding of how the target website or system is configured and how its infrastructure is organized.

Common DNS Record Types:

NS (Name Server): The authoritative DNS servers for the domain.

A Record: Maps the domain to its IPv4 address.

AAAA Record: Maps the domain to its IPv6 address.

MX Record: Specifies the mail servers used for handling email for the domain.

TXT Record: Holds text data used for verification, diagnostics, and policies like SPF, DKIM, etc.


Terminal Tool: dnsrecon

Command format:

dnsrecon -d example.com

This command passively enumerates DNS records for the specified domain.


Web-based Tool: dnsdumpster.com

A free online resource that performs the same type of reconnaissance.

It provides a well-organized report including subdomains, DNS records, and host mapping.





DETECTING WEB APPLICATION FIREWALLS (WAF) WITH WAFW00F

A WAF (Web Application Firewall) is a security system that monitors and filters HTTP traffic between a web application and the internet. It is used to protect web applications from various attacks.

WAFW00F is a tool that helps detect the presence and type of WAF used by a website. It works by analyzing the HTTP responses and behaviors of the target website when specific requests are made.

Basic usage:

List all known WAFs:

wafw00f -l

Detect WAF:

wafw00f https://example.com

Verbose detection:

wafw00f https://example.com -a




SUBDOMAIN ENUMERATION WITH SUBLIST3R

Sublist3r is a tool designed to enumerate subdomains of websites using OSINT.

It helps penetration testers and bug bounty hunters collect and gather subdomains for the domain they are targeting.

Basic command:

sublist3r -d example.com

This scans for subdomains of example.com

To specify search engines:

sublist3r -d example.com -e Google,Yahoo

This command uses only Google and Yahoo for subdomain enumeration.

Sublist3r gathers results using multiple search engines like Google, Bing, Yahoo, Netcraft, etc.



GOOGLE DORKS

Google Dorks are advanced search queries used to find information that is not easily accessible through normal searches.

Examples:

site:example.com inurl:admin

Finds admin pages under example.com

intitle:"Index of"

Lists directory indexes exposed on the web

filetype:pdf site:*.example.com

Searches for PDF files under all subdomains of example.com

cache:example.com

Shows the cached version of the page

More dorks:

exploit-db.com/google-hacking-database



EMAIL HARVESTING WITH THEHARVESTER

theHarvester is a tool designed for gathering emails and domain/subdomain names from different public sources.

Command:

theHarvester -d example.com -b google,bing

This command searches for emails and subdomains related to example.com using Google and Bing.

Sources available:

google, yahoo, bing, baidu, dogpile, pgp, linkedin, twitter, googleplus, etc.