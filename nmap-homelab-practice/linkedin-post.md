Spent the week building out a home lab (Kali Linux + Metasploitable 2 on an isolated VirtualBox network) to practice the recon-to-exploitation workflow end to end, instead of just reading about it.

What I did:
🔍 Full TCP port scan with Nmap (-sS -p- --min-rate 1000) — found 27 open ports on the target
🧩 Service/version enumeration (-sV -sC) to fingerprint exactly what was running on each port
🗂️ Fed the scan results into Searchsploit (searchsploit --nmap) to cross-reference every service against known exploits — instead of guessing
🎯 Confirmed a known vsftpd 2.3.4 backdoor (CVE-2011-2523) and validated it with Metasploit — ended up with a root session on the target
📡 Went back through Wireshark to see the actual packets behind each step — the handshakes, the FTP control channel, the difference between an open and closed port on the wire

Biggest takeaway: a stale version banner on one service was enough to flag the single highest-risk finding on the box — exploitation just confirmed what good enumeration had already pointed to.

Everything here was run against a VM I own, on a network isolated from the internet and my host machine — purely for learning.

Full write-up (commands, findings, lessons learned) on GitHub: [link]

#cybersecurity #ethicalhacking #nmap #metasploit #infosec #homelab #pentesting
