Active information gathering

DNS Zone Transfer & Interrogation

Zone transfer is a type of DNS transaction that involves copying the DNS records from a primary server to a secondary server. Attackers can exploit misconfigured DNS servers that allow unauthorized zone transfers.

Tool: dnsenum

Command:

dnsenum example.com

Tool: fierce

Command:

fierce -dns example.com

Tool: dig

Command:

dig axfr @ns1.example.com example.com

This attempts a full zone transfer

Common record types retrieved include:

A, AAAA, MX, NS, TXT, CNAME



HOST DISCOVERY

Before launching a port scan, it's crucial to identify live hosts within the target network.

Determine your own IP:

ip a s

Use Nmap for ping sweep:

sudo nmap -sn 192.168.1.0/24

This will list live hosts

Netdiscover is a tool for live host detection using ARP requests.

Command:

sudo netdiscover -i eth0 -r 192.168.1.0/24



PORT SCANNING WITH NMAP

Once live hosts are identified, use Nmap for deeper port scanning.

Basic scan:

nmap <IP>

Aggressive scan (includes version detection, script scanning, OS detection):

nmap -Pn -p- <IP>

nmap -Pn -F -sV -O -sC <IP>

UDP Scan:

nmap -Pn -sU -v <IP>

Save output:

nmap -Pn -F -sV -O -sC -oN scan.txt <IP>