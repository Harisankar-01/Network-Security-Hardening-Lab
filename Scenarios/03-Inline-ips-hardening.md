# 03 - Inline IPS: Blocking RDP Brute Force in Real Time

**Objective:** Deploy Snort as a true inline IPS (not passive IDS) capable of actively dropping malicious traffic, and verify it can distinguish a brute-force attack from legitimate use of the same allowed service.

**Architecture:**
- Dedicated Ubuntu Server 24.04 VM with two network adapters, bridging OPNsense's LAN interface and Windows Server
- Snort run inline via AFPACKET DAQ:
```bash
sudo snort -Q --daq afpacket -i enp0s3:enp0s8 -c /etc/snort/snort.conf -A console
```

**Custom rule (final, working version):**
```bash
drop tcp any any -> 192.168.1.9 3389 (msg:“RDP Brute Force Attempt Detected - BLOCKED”; flags:S; detection_filter:track by_src, count 5, seconds 60; sid:1000002; rev:1;)
```


**Troubleshooting:**
1. First working rule used `flow:to_server,established`, which matched every packet within an already-open session, this caused a **false positive**: a single legitimate RDP login was dropped mid-session because it naturally exceeded the packet-count threshold. Fixed by changing to `flags:S`, so only new connection attempts (SYN packets) are counted, not ongoing session traffic.

**Test 1 — Legitimate use:**
```bash
xfreerdp /u:administrator /p:[password] /v:192.168.1.9
```
**Result:** Connects normally, no false drop.

**Test 2 — Brute force:**
```bash
hydra -l administrator -P passwords.txt rdp://192.168.1.9 -t4
```
**Result:** First few attempts proceed normally; after the 5th connection attempt within 60 seconds, Snort logs showed:
- “RDP Brute Force Attempt Detected - BLOCKED”
**Conclusion:** This is the core result of the project: an allowed service (RDP) remained reachable for legitimate use, but was actively protected against brute-force behavior by behavioral inspection, something port/IP-based firewall rules alone cannot do. The false-positive-to-fix cycle is itself a valuable finding: detection rules must distinguish between "new connection" and "ongoing session" to avoid breaking legitimate traffic.
