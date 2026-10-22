# Nmap Home Lab Practice

Personal lab notes and scan reports from practicing network reconnaissance with **Nmap** against a deliberately vulnerable target (**Metasploitable 2**) inside an isolated **VirtualBox** home lab running **Kali Linux** as the attacker box.

This repo is a learning log, not a production toolkit. Everything here was run against machines I own, on an internal (host-only / NAT-only) virtual network with no route to the internet or any third-party system.

## Lab setup

| Component   | Role                                   |
|-------------|-----------------------------------------|
| Kali Linux  | Attacker machine — nmap, enumeration tools |
| Metasploitable 2 | Intentionally vulnerable target VM |
| VirtualBox  | Hypervisor, host-only network between the two VMs |

## Scope and ethics

- All targets are VMs I own and run locally.
- The lab network is isolated (host-only adapter) — no scans ever leave the virtual network.
- Techniques practiced here are for learning defensive/offensive security concepts and are **not** to be used against systems without explicit authorization.

## Repo structure

```
nmap-homelab-practice/
├── README.md
├── reports/
│   └── 2026-10-22-vsftpd-backdoor/
│       ├── scan-report.md      # write-up: command, findings, lessons learned
│       └── raw-output.txt      # raw nmap output for that session
└── scripts/
    └── (helper scripts, e.g. scan automation, parsing)
```

Each session gets its own `reports/<date-or-topic>/` folder with:
1. **scan-report.md** — what I ran, why, what I found, what it means, what I'd try next
2. **raw-output.txt** — the actual terminal output, for reference

## Progress log

- [x] TryHackMe Nmap room (host discovery) — completed
- [x] Home lab set up: Kali + Metasploitable 2 in VirtualBox
- [x] Full TCP port scan (`-sS -p- --min-rate 1000`) — 27 open ports found
- [x] Service/version detection (`-sV -sC`) on all open ports
- [x] Correlated findings to known exploits with `searchsploit --nmap`
- [x] Confirmed the vsftpd 2.3.4 backdoor (CVE-2011-2523) with Metasploit — root session obtained
- [x] Reviewed scan + exploit traffic in Wireshark
- [ ] Vulnerability scripts (`--script vuln`) as a second pass
- [ ] Remediation write-up per finding
- [ ] Cross-post write-up to LinkedIn

## Why this repo exists

Documenting hands-on practice to reinforce learning (active recall), build a portfolio of real lab work for internship/job applications, and share progress with the cybersecurity community on LinkedIn and GitHub.
