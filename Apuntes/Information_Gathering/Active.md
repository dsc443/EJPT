# Active Information Gathering

## Summary
Active information gathering uses *direct* queries and network interactions to enumerate targets: DNS interrogation, zone transfers, host discovery and active port scanning. It is more intrusive than passive techniques, so **obtain written authorization** before running these techniques against non-lab networks.

---

## Contents
1. DNS — Zone transfer & interrogation  
2. Host discovery (live hosts)  
3. Port scanning (Nmap)  
4. Workflow & suggestions  
5. Pitfalls, reliability & tuning  
6. Quick cheat sheet  

---

## 1) DNS — Zone transfer & interrogation

**Concept**  
A DNS zone transfer (AXFR) is the mechanism used to replicate DNS zones between authoritative servers. Misconfigured servers may allow an unauthorized AXFR and reveal internal hostnames and IPs.

**Why check it**  
A successful AXFR often provides a fast map of hosts, subdomains, mail servers and name servers—valuable for subsequent steps.

**Common DNS record types returned**
- `A` / `AAAA`: IPv4 / IPv6 addresses  
- `MX`: Mail exchangers  
- `NS`: Authoritative name servers  
- `CNAME`: Aliases  
- `TXT`: Text records (SPF, DKIM, verification strings)

**Tools & examples**
```bash
# Try AXFR against a specific authoritative NS
dig AXFR @ns1.example.com example.com

# List authoritative NS to try others
dig NS example.com +short

# Automated enumeration
dnsenum example.com

# Classic DNS recon / brute forcing
fierce -dns example.com
```

**Notes**
- If AXFR succeeds: store output securely and redact sensitive entries before sharing.  
- If AXFR fails: use subdomain enumeration, certificate transparency logs or NSEC/NSEC3 techniques.  
- Try all authoritative NS — permissions may differ per server.

---

## 2) Host discovery (live hosts)

**Objective**  
Identify live IPs in a target range so you focus port scans and reduce noise.

**Find your local IP**
```bash
ip addr show
# short form:
ip a
```

**Nmap ping-scan (fast sweep)**
```bash
sudo nmap -sn 192.168.1.0/24
# -sn: ping scan (no port scan), lists hosts that respond
```

**ARP-based local discovery — netdiscover**
```bash
sudo netdiscover -i eth0 -r 192.168.1.0/24
```
- ARP is reliable on local Ethernet networks even when ICMP is blocked.

**Notes**
- On cloud/hardened infra, ICMP and common probes may be filtered. Combine passive recon and DNS results.
- For large ranges, shard scans to avoid rate limits or triggering alerts.

---

## 3) Port scanning (Nmap)

**Principles**
- Start targeted and escalate: quick scans → full TCP scans → service/version detection → NSE scripts/OS detection.  
- Save outputs in machine-readable formats (`-oX`, `-oA`) for later parsing or import.

**Common commands & explanations**
```bash
# Quick / default scan
nmap <IP>

# Full TCP scan (ports 1-65535), skip host discovery
nmap -Pn -p- <IP>

# Aggressive discovery (service/version, default scripts, OS, fast)
nmap -Pn -sV -sC -O -F <IP>

# Full aggressive (all ports + scripts + OS)
nmap -Pn -p- -sV -sC -O <IP>

# UDP scan (slower, often unreliable)
nmap -Pn -sU -v <IP>

# Save results in multiple formats
nmap -Pn -F -sV -O -sC -oA scan_target <IP>

# Show reason for port states
nmap --reason -sV <IP>

# Quick top ports
nmap --top-ports 100 -sV <IP>
```

**Tuning & timing**
- Timing templates: `-T0`..`-T5` (`-T4` faster but noisy; `-T1/-T0` stealthier).  
- Control rate with `--min-rate` / `--max-rate` if needed.  
- Target specific UDP ports (53, 69, 161) rather than scanning all UDP ports.

**Interpretation**
- Cross-check service/version results; increase `--version-intensity` if needed.  
- Prioritize services with known vulnerabilities for further enumeration.

---

## 4) Workflow & recommended order
1. Passive recon: WHOIS, certificate transparency, OSINT for subdomains.  
2. DNS interrogation: `dig`, `dnsenum`, `fierce`, AXFR attempts.  
3. Host discovery: `nmap -sn`, `netdiscover` for LAN.  
4. Quick TCP scan: `--top-ports`, initial service list.  
5. Full TCP/UDP scans: `-p-`, `-sV`, `-sC`, `-O` as needed.  
6. Save outputs (`-oX`, `-oA`) and import into tooling (Metasploit, parsers).  
7. Analyze and plan service-specific enumeration or exploitation (with authorization).

---

## 5) Pitfalls, reliability & ethics
- **False negatives:** UDP scans and filtered ports commonly yield inconclusive results — re-scan or target specific ports.  
- **Blocking / detection:** IDS/IPS can trigger on aggressive scans; use lower timing (`-T1/-T2`) or split scans.  
- **Legality & ethics:** Obtain explicit authorization and scope; keep approvals and logs.  
- **Data handling:** Treat internal hostnames/IPs as sensitive; redact or encrypt when sharing.

---

## 6) Quick cheat sheet
```bash
# DNS
dig AXFR @ns1.example.com example.com
dnsenum example.com
fierce -dns example.com

# Host discovery
ip addr show
sudo nmap -sn 192.168.1.0/24
sudo netdiscover -i eth0 -r 192.168.1.0/24

# Port scanning
nmap <IP>
nmap -Pn -p- <IP>
nmap -Pn -p- -sV -sC -O <IP>
nmap -Pn -sU -v <IP>

# Save outputs
nmap -oA scan_prefix -Pn -sV -sC -O <IP>
```


**Security reminder:** Never run active scans outside authorized scope. Maintain approvals and handle discovered data securely.
