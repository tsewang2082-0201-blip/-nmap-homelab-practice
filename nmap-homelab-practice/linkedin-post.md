Spent some time this week building a home lab (Kali Linux and Metasploitable 2 on an isolated VirtualBox network) to actually practice the recon-to-exploitation process, rather than just reading about it.

Here's what I did:

Ran a full port scan with Nmap and found 27 open ports on the target. Followed that with version detection to see exactly what was running on each one. Then fed the scan results into Searchsploit to check which of those services had known, documented exploits, instead of guessing.

One of them stood out: vsftpd 2.3.4, which has a known backdoor. I confirmed it with Metasploit and ended up with a root session on the target. Afterward, I went back through Wireshark to look at the actual traffic behind each step — the handshakes, the FTP exchange, the difference between an open and closed port at the packet level.

The main thing I took away from this: a single outdated version banner was enough to flag the biggest risk on the host, before any exploitation happened. The exploit just confirmed what good enumeration had already pointed to.

All of this was done on a VM I own, on a network isolated from the internet, purely for learning.

Full write-up with commands and findings is on GitHub: [link]

#cybersecurity #nmap #metasploit #infosec #homelab
