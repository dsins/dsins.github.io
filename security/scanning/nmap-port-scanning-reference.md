# Nmap: Port Scanning & Network Reconnaissance Reference

> **Legal reminder:** Only scan systems you own or have explicit written permission to test. Unauthorized scanning is illegal in most jurisdictions.

---

## Install

```bash
# Ubuntu / Debian
sudo apt install nmap -y

# macOS
brew install nmap

# Verify
nmap --version
```

---

## Scan Types - How They Work and When to Use Each

### TCP SYN Scan (`-sS`) - Default, recommended

Sends SYN → waits for SYN/ACK (open) or RST (closed). Never completes the 3-way handshake. Faster and stealthier than full connect - does not appear in the target application's connection logs (but does appear in firewall/IDS logs).

```bash
sudo nmap -sS 192.168.1.1
# Requires root/sudo - raw socket access needed
```

Port states:
- `open` - SYN/ACK received
- `closed` - RST received
- `filtered` - no response (firewall dropping packets)

### TCP Connect Scan (`-sT`) - No root required

Completes the full 3-way handshake. Slower, logged by the target application. Use when you don't have root or when scanning through a proxy.

```bash
nmap -sT 192.168.1.1
```

### UDP Scan (`-sU`)

Sends empty UDP packet → no response means `open|filtered`, ICMP Port Unreachable means `closed`. Slow because rate-limiting is common. Combine with `-sS` to cover both protocols.

```bash
sudo nmap -sU -sS 192.168.1.1
sudo nmap -sU --top-ports 20 192.168.1.1  # scan only top 20 UDP ports (much faster)
```

Common UDP services to check: DNS (53), DHCP (67/68), SNMP (161), NTP (123), TFTP (69).

### Other scan types

| Flag | Type | Notes |
|---|---|---|
| `-sN` | TCP Null | No flags set - evades some stateless firewalls |
| `-sF` | TCP FIN | FIN only - same evasion use case |
| `-sX` | Xmas | FIN+PSH+URG - same evasion use case |
| `-sA` | TCP ACK | Maps firewall rules, not open/closed ports |
| `-sn` | Ping scan (host discovery) | No port scan - just finds live hosts |

---

## Target Specification

```bash
nmap 192.168.1.1                  # single IP
nmap 192.168.1.1-20               # IP range
nmap 192.168.1.0/24               # entire subnet (256 hosts)
nmap 10.0.0.0/8                   # large network (use with caution - slow)
nmap scanme.nmap.org              # hostname
nmap -iL targets.txt              # read targets from file
nmap 192.168.1.1 192.168.1.2      # multiple targets
nmap --exclude 192.168.1.5        # exclude specific host
```

---

## Port Specification

```bash
nmap -p 22 192.168.1.1            # single port
nmap -p 22,80,443 192.168.1.1     # specific ports
nmap -p 1-1024 192.168.1.1        # port range
nmap -p- 192.168.1.1              # all 65535 ports (slow)
nmap --top-ports 100 192.168.1.1  # top 100 most common ports
nmap -F 192.168.1.1               # fast - top 100 ports (same as --top-ports 100)
```

---

## Service & OS Detection

```bash
# Service version detection
sudo nmap -sV 192.168.1.1
# Output example:
# 22/tcp  open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
# 80/tcp  open  http    Apache httpd 2.4.52 ((Ubuntu))
# 3306/tcp open mysql   MySQL 8.0.35

# OS detection (requires root)
sudo nmap -O 192.168.1.1
# Reports: "OS: Linux 4.15 - 5.8" or "Windows Server 2019"

# Aggressive scan: OS + version + scripts + traceroute
sudo nmap -A 192.168.1.1
# Equivalent to: -O -sV -sC --traceroute
```

> `-sV` intensity: `--version-intensity 0` (fastest, less accurate) to `9` (slowest, most thorough). Default is 7.

---

## Timing Templates

Controls speed/stealth tradeoff. Higher = faster but noisier and more likely to be blocked.

| Flag | Name | Use case |
|---|---|---|
| `-T0` | Paranoid | IDS evasion, very slow |
| `-T1` | Sneaky | IDS evasion |
| `-T2` | Polite | Reduces bandwidth, slower |
| `-T3` | Normal | Default |
| `-T4` | Aggressive | Fast - good for local networks |
| `-T5` | Insane | Very fast - may miss results |

```bash
sudo nmap -T4 -sS -sV 192.168.1.0/24  # good balance for internal network audit
```

---

## Nmap Scripting Engine (NSE)

NSE scripts run against discovered services and automate detection of vulnerabilities, misconfigurations, and information gathering. Scripts are in `/usr/share/nmap/scripts/`.

```bash
# Default scripts (safe, informational)
sudo nmap -sC 192.168.1.1
# Equivalent to: --script=default

# Run specific script
sudo nmap --script=http-title 192.168.1.1
sudo nmap --script=ssh-auth-methods 192.168.1.1

# Run all scripts in a category
sudo nmap --script=vuln 192.168.1.1        # known CVE checks
sudo nmap --script=auth 192.168.1.1        # default/empty credential checks
sudo nmap --script=discovery 192.168.1.1   # enumerate services further

# Combine script with version detection
sudo nmap -sV --script=banner 192.168.1.1  # grab service banners
```

### Useful scripts by scenario

```bash
# Check for SMB vulnerabilities (EternalBlue, MS17-010)
sudo nmap --script=smb-vuln-ms17-010 192.168.1.1

# Check HTTP headers and methods
sudo nmap --script=http-headers,http-methods -p 80,443 192.168.1.1

# Enumerate SNMP (community strings)
sudo nmap -sU --script=snmp-info -p 161 192.168.1.1

# Check for anonymous FTP login
sudo nmap --script=ftp-anon -p 21 192.168.1.1

# MySQL version + empty root password check
sudo nmap --script=mysql-empty-password -p 3306 192.168.1.1

# SSL/TLS cipher and cert info
sudo nmap --script=ssl-enum-ciphers -p 443 192.168.1.1
```

---

## Practical Compound Commands

```bash
# Full audit of a single host
sudo nmap -A -T4 -p- 192.168.1.1

# Fast subnet discovery (find live hosts only)
sudo nmap -sn 192.168.1.0/24

# Stealth scan with version detection on common ports
sudo nmap -sS -sV -T2 --top-ports 1000 192.168.1.1

# Web server fingerprint
sudo nmap -sV -p 80,443,8080,8443 --script=http-headers,http-title,http-server-header 192.168.1.1

# Full UDP + TCP sweep
sudo nmap -sS -sU -T4 --top-ports 200 192.168.1.1
```

---

## Output Formats

```bash
-oN output.txt      # Normal (human-readable)
-oX output.xml      # XML (parseable by tools like Metasploit, Zenmap)
-oG output.gnmap    # Grepable format
-oA output          # All three formats at once (output.nmap, output.xml, output.gnmap)
```

```bash
# Save and display simultaneously
sudo nmap -sV 192.168.1.1 -oA scan_results | tee /dev/tty

# Search grepable output for open ports
grep "open" output.gnmap
```

---

## Reading Output - What to Act On

```
PORT     STATE    SERVICE  VERSION
22/tcp   open     ssh      OpenSSH 7.2p2  ← old version, check CVEs
23/tcp   open     telnet               ← unencrypted, should be closed
80/tcp   open     http     Apache 2.2   ← EOL version, patch immediately
443/tcp  open     https    Apache 2.2
3306/tcp open     mysql                ← should NOT be exposed externally
8080/tcp filtered http                 ← firewall dropping - service exists but blocked
```

**Risk interpretation:**

| Finding | Risk | Action |
|---|---|---|
| Telnet (23), FTP (21) open | High | Disable - unencrypted protocols |
| SSH on port 22 with old version | Medium | Update OpenSSH, consider port change |
| MySQL/PostgreSQL externally exposed | High | Bind to 127.0.0.1 or firewall |
| RDP (3389) publicly open | High | Restrict to VPN/IP whitelist |
| Outdated service versions | Medium–High | Patch or replace |
| `filtered` ports | Low | Firewall working - verify intentional |
| Unexpected open ports | Variable | Investigate - may indicate compromise |

---

## Common Port Reference

| Port | Protocol | Notes |
|---|---|---|
| 21 | FTP | Unencrypted - prefer SFTP (22) |
| 22 | SSH / SFTP | Should be restricted by IP if possible |
| 23 | Telnet | Unencrypted - never expose |
| 25 | SMTP | Restrict to mail server IPs |
| 53 | DNS (UDP/TCP) | Should only be open on DNS servers |
| 80 | HTTP | OK if intentional, redirect to 443 |
| 443 | HTTPS | Should be open for web servers |
| 3306 | MySQL | Never expose publicly |
| 5432 | PostgreSQL | Never expose publicly |
| 3389 | RDP | Restrict to VPN/whitelist only |
| 6379 | Redis | Never expose - no auth by default |
| 27017 | MongoDB | Never expose - auth often disabled |

---

## Notes

- Nmap by default scans the 1,000 most common ports, not all 65,535. Use `-p-` for full coverage.
- `filtered` means packets are being dropped (firewall). The port may still have a service behind it.
- `open|filtered` on UDP means Nmap can't tell - no response could mean open or firewall.
- For scheduled audits, automate with cron + `-oA` and diff outputs to detect new open ports.
- Zenmap is the official Nmap GUI if you prefer visual output.
