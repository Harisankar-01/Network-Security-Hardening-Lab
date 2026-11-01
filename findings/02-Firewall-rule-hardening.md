# 03 - Least-Privilege Hardening: Restricting RDP Source & Removing ICMP

**Objective:** Move from a working but broad allow-rule (any WAN host can reach RDP) to a hardened, least-privilege configuration, and remove unnecessary attack surface once testing was complete.

**Change 1 - Restrict RDP source to a single authorized host**
- Before: Source = `10.0.0.0/24` (any device on WAN)
- After: Source = `10.0.0.5/32` (Kali's exact IP only)

**Test from authorized source (Kali):**
```bash
xfreerdp /u:administrator /p:[password] /v:192.168.1.9
```
**Result:** Connected successfully.

**Test from a different device on the same WAN network (Windows 7):**
```bash
nmap -p 3389 192.168.1.9
```
**Result:** Port shown as closed/filtered, confirming the rule now enforces a specific authorized source rather than the entire subnet, a real demonstration of least-privilege access control.

**Change 2 - Remove ICMP allow-rule after testing**
ICMP was originally allowed to verify basic connectivity during setup. Once RDP connectivity was confirmed working, the rule was disabled.

**Test:**
```bash
ping 192.168.1.9   # from Kali
```
**Result:** No response, confirming ICMP/host-discovery is no longer possible from the WAN side, removing a reconnaissance vector that isn't needed for normal operation.

**Conclusion:** A working firewall rule is a starting point, not an end state. Narrowing access to only the specific source that needs it, and removing diagnostic allowances once they've served their purpose, reflects how a rule should actually be hardened before being considered production-ready.
**Change 3 - Remove SSH allow-rule after testing**
ssh was allowed for lab purpouse.the rule was also disabled

```bash
ping -p 22 192.168.1.9 #kali
```
**Result:** No response, confirming SSH is no longer open to connect from the WAN side, 
