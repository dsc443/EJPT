# Nmap & Metasploit (MSF) — Clean Notes

Below are cleaned and organized notes covering Nmap usage, output formats, importing results into Metasploit, auxiliary modules, and post-exploitation pivoting.

## 1) What is Nmap?
- Nmap (Network Mapper) is a free, open-source network scanner for discovering hosts, open ports, and services on a network.
- It can enumerate service versions and detect the target operating system.
- Nmap output can be exported in formats that are importable into Metasploit for vulnerability analysis and exploitation.

## 2) Useful Nmap options (summary)
- `-Pn` — Skip host discovery (no ping); assume host is up and perform port scanning only.
- `-sV` — Service/version detection.
- `-O`  — Operating system detection.
- `-v`  — Increase verbosity (more terminal output).
- `--reason` — Show why a port is in a given state (e.g., RST, no-response).

## 3) Output formats
- `-oN <file>` — Normal output (terminal-style).
- `-oX <file>` — XML output (useful for Metasploit).
- `-oS <file>` — "Script kiddie" format.
- `-oG <file>` — Grepable output (for parsing).
- `-oA <prefix>` — Produce `-oN`, `-oX` and `-oG` simultaneously with the given prefix.

Example:
```
nmap -Pn -sV -O --reason -oA target_scan 10.4.22.173
```

## 4) Importing Nmap results into Metasploit
1. Start PostgreSQL (Metasploit’s DB):
```
service postgresql start
```
2. Launch msfconsole:
```
msfconsole
```
3. Check DB status:
```
db_status
```
4. (Optional) Create or switch to a workspace to organize results:
```
workspace -a Win2k12    # create 'Win2k12' workspace
workspace -a Nmap_MSX   # create 'Nmap_MSX' workspace
```
5. Import an Nmap XML file:
```
db_import /root/windows_server_2012.xml
```
6. Useful msfconsole queries:
```
hosts     # list imported hosts
services  # list imported services
```

## 5) Running Nmap from within Metasploit
Metasploit provides `db_nmap` which runs nmap and saves results to the Metasploit DB:
```
db_nmap -Pn -sV -O 10.4.22.173
```

## 6) Metasploit Auxiliary Modules
- Auxiliary modules perform tasks like scanning, discovery, and fuzzing (they are not exploit modules).
- They can do TCP and UDP port scans and enumerate services (FTP, SSH, HTTP, etc.).
- Useful during reconnaissance and in post-exploitation (e.g., scanning other subnets after gaining access).

## 7) Lab workflow (high level)
1. Use auxiliary modules to discover open ports on the first target.
2. Exploit a vulnerable service to obtain a foothold (meterpreter/shell).
3. From the foothold, pivot to another subnet (route/autoroute).
4. Use auxiliary modules to scan the second target/subnet.
5. Continue enumeration and exploitation as required.

## 8) Demo — Port scanning with Metasploit (practical steps)
- Preparation (start DB and msfconsole):
```
service postgresql start
msfconsole
```
- Create workspace:
```
workspace -a Port_Scan
```
- Search for port scanning modules:
```
search portscan
# choose a module from the results, e.g.:
use auxiliary/scanner/portscan/tcp
set RHOSTS 10.4.22.173
set PORTS 1-65535
run
```

## 9) Post-exploitation and pivoting (example flow)
1. After exploitation you get a meterpreter session:
```
# inside meterpreter
sysinfo
shell      # to open a system shell
# in shell:
 /bin/bash -i
```
2. Add routes to pivot (from meterpreter):
```
run autoroute -s 192.113.124.0/24   # add route to remote subnet
background                           # background the session and return to msfconsole
```
3. In msfconsole list sessions:
```
sessions -l
```
4. Use portscan modules that leverage the pivot (target the subnet you now have access to):
```
search portscan
use auxiliary/scanner/portscan/tcp
set RHOSTS 192.113.124.0/24
run
```

## 10) UDP scanning with auxiliary modules
- Search:
```
search udp_sweep
```
- Use and run (module parameters vary):
```
use <module_path_for_udp_sweep>
set RHOSTS 10.4.22.0/24
run
```

## Practical tips / final notes
- Save Nmap output in importable formats (`-oX` or `-oA`) if you plan to import into Metasploit.
- Only scan and exploit hosts/networks you are authorized to test.
- PostgreSQL must be running for Metasploit to store and index hosts/services.
- After gaining a foothold, autoroute/route modules let you scan hosts reachable only from the compromised host (pivoting).

## Quick command checklist
```
# Nmap
nmap -Pn -sV -O --reason -oA scan_target 10.4.22.173

# Metasploit (start)
service postgresql start
msfconsole
db_status

# Import Nmap XML
db_import /path/scan_target.xml
hosts
services

# Run Nmap inside Metasploit
db_nmap -Pn -sV -O 10.4.22.173

# Example autoroute & sessions (post-exploit)
# in meterpreter:
run autoroute -s 192.113.124.0/24
background

# in msfconsole:
sessions -l
use auxiliary/scanner/portscan/tcp
set RHOSTS 192.113.124.0/24
run
```
