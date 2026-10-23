# Nmap Home Lab Practice

Personal practice log from setting up a home lab and working through the recon-to-exploitation process on Metasploitable 2.

## Setup

- Kali Linux and Metasploitable 2 running in VirtualBox, on the same internal network with static IPs
- Kali had a second adapter on NAT, used only for updates, kept separate from the lab network
- Took around 2-3 hours, mostly getting the networking set up correctly

## What's in here

- `reports/` - write-ups from each practice session: what I ran, what I found, what I learned
- `linkedin-post.md` - draft of a post about this project

## Progress

- Set up the home lab(virtual box,kali,metasploitable 2, metasploit)
- Ran a full port scan on the target - found 27 open ports(Used n-map with -sn flag)
- Ran version detection on all open ports (-sN and -sV)
- Used searchsploit to match services against known exploits(searchsploit)
- Confirmed the vsftpd 2.3.4 backdoor and got a root shell with Metasploit
- Reviewed the traffic in Wireshark to see what each step actually looked like on the wire
- use Metasploit framework to exploit the vulnurability

## What I Learned

- Scan broad first (`-sS -p-`), then go deep with `-sV -sC` only on what's open.
- A single outdated version banner was enough to flag the biggest risk on the host, before exploiting anything.
- `searchsploit --nmap <scan>.xml` matches open services to known exploits automatically — much faster than researching each one manually.
- Checked the actual traffic in Wireshark to confirm what Metasploit reported, instead of just trusting the tool.
- Next step: only went deep on one service — should repeat this on another, and add remediation notes, not just "how I broke it."

## Note on scope

Everything here was done against VMs I own, on a network isolated from my host machine and the internet.
