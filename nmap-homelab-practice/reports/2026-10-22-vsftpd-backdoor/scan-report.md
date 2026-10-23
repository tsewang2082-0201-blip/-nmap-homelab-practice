# Scan Report — Recon & Exploitation of Metasploitable 2 (vsftpd 2.3.4 Backdoor)

**Date:** 2026-10-22
**Target:** Metasploitable 2 — 10.10.10.20 (internal/host-only network)
**Attacker machine:** Kali Linux — 10.10.10.10 (second adapter on NAT, for updates only)
**Tools:** Wireshark, Nmap 7.99, Searchsploit, Metasploit Framework 6.4

## objective

Practice the full recon-to-exploitation workflow against a known-vulnerable target in an isolated lab: discover the host, enumerate services, identify a known vulnerability, and confirm it with a real exploit — while also inspecting the underlying traffic in Wireshark to understand what each step looks like on the wire.

## Lab setup

- Kali and Metasploitable 2 placed on the same **internal** VirtualBox network with static IPs (10.10.10.10 / 10.10.10.20), isolated from the host and internet.
- A second adapter on Kali (NAT) used only for package updates — kept separate from the lab network.
- Setup took roughly 2–3 hours, mostly networking configuration.

##  Methodology

**a) Traffic baseline (Wireshark).**
Captured on `eth1` to see what discovery and scanning traffic actually looks like: an ARP request/reply resolving 10.10.10.20, then individual TCP handshakes for specific ports (e.g., port 20 closed → immediate RST/ACK; port 21 open → SYN/ACK then RST once the probe finished). This made the difference between an open, closed, and filtered port concrete at the packet level rather than just reading nmap's output.

**b) Host & port discovery (Nmap).**
```
sudo nmap -sS -p- --min-rate 1000 -oA portScan 10.10.10.20
```
A full-range SYN scan found the host up and **27 open TCP ports**, including several classic Metasploitable services: ftp (21), ssh (22), telnet (23), smtp (25), domain (53), http (80), netbios/microsoft-ds (139/445), several "r-services" (512–514), rmiregistry/ingreslock/nfs, mysql (3306), distccd (3632), postgresql (5432), vnc (5900), X11 (6000), irc (6667/6697), and a handful of unidentified high ports.

**c) Service/version enumeration.**
```
nmap -sV -sC -p <all 27 ports found above> -oA deepScan 10.10.10.20
```
Version detection and default scripts flagged several notable results — most importantly:
- **`vsftpd 2.3.4`** on port 21, with anonymous FTP login allowed. This exact version has a well-known backdoor (CVE-2011-2523).
- OpenSSH 4.7p1 (Debian), Postfix smtpd, ISC BIND 9.4.2, Apache httpd 2.2.8 — all old versions typical of this intentionally outdated image.

**d) Matching known exploits (Searchsploit).**
```
searchsploit --nmap deepScan.xml
```
Fed the scan's XML output straight into Searchsploit to cross-reference every detected service/version against the Exploit-DB index. This surfaced the vsftpd 2.3.4 backdoor module (among many other matches for mysql, postgresql, proftpd, etc.), confirming it as a known, exploitable issue rather than something to try blind.

**e) Exploitation (Metasploit).**
Used Metasploit's matching exploit module for the vsftpd 2.3.4 backdoor, pointed at the target and listener IPs. The module reported the vulnerable banner, confirmed the backdoor was present, and opened a Meterpreter session.

**f) Post-exploitation check.**
From the resulting session: `getuid` and `sysinfo` confirmed a session running as **root** on the target (`metasploitable.localdomain`, Ubuntu 8.04), and a dropped-to-shell `whoami` confirmed the same.

**g) Traffic review of the exploit.**
Went back to Wireshark to watch the FTP control channel during the exploit attempt — the `USER`/password exchange, a `500 OOPS:` server response, and the subsequent backdoor shell traffic on the attacker-controlled port — tying the Metasploit-reported result back to what was visible on the wire.

##  Findings summary

| Port | Service | Version | Notable issue |
|------|---------|---------|----------------|
| 21 | ftp | vsftpd 2.3.4 | Known backdoor (CVE-2011-2523); anonymous login allowed |
| 22 | ssh | OpenSSH 4.7p1 | Outdated, multiple known CVEs |
| 25 | smtp | Postfix | Weak/legacy SSLv2 ciphers offered |
| 53 | domain | ISC BIND 9.4.2 | Outdated |
| 80 | http | Apache 2.2.8 | Outdated |
| 3306 / 5432 | mysql / postgresql | — | Multiple known exploits per Searchsploit, not tested here |

Root-level code execution was achieved via the vsftpd backdoor, confirming the service is exploitable as flagged.

##  Lessons learned

- Seeing the TCP handshake at the packet level (SYN → SYN/ACK → RST for closed; full exchange for open) made nmap's state output much more intuitive than just trusting the tool.
- `searchsploit --nmap <file>.xml` is a fast way to triage a large open-port list into "which of these are actually known-exploitable" instead of researching each service by hand.
- Anonymous FTP + an old, backdoored version was the single highest-value finding on this host — a reminder that version/banner info alone can be enough to flag critical risk, before any exploitation is attempted.
- Keeping the lab fully isolated (internal network only, NAT adapter reserved for updates) made it easy to scan aggressively without any risk of touching real infrastructure.


---
*Practiced entirely in an isolated home lab against a VM I own. No scans or exploitation were run against third-party infrastructure.*
