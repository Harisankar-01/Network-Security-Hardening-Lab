# 01 - Network Segmentation Verification

**Objective:** Confirm that traffic between the attacker zone (Kali, WAN) and the protected zone (Windows Server, LAN) is forced through OPNsense.

**Setup:**
- Kali attached to Internal Network `NetSec_WAN` (10.0.0.0/24), static IP `10.0.0.5`
- Windows Server attached to a separate LAN segment (192.168.1.0/24), static IP `192.168.1.9`
- OPNsense interfaces: WAN `10.0.0.1`, LAN `192.168.1.1`

**Test 1 — Gateway reachability**
```bash
ping 10.0.0.1      # from Kali
ping 192.168.1.1   # from Windows Server
```
**Result:** Both succeeded, confirming each VM correctly reaches its own gateway.

**Test 2 — Cross-zone traffic before any rule exists**
```bash
ping 192.168.1.9   # from Kali, no firewall rule yet
```
**Result:** No response. Confirms OPNsense's default-deny behavior(auto generated rules) is active from the moment segmentation is in place, cross-zone traffic is blocked unless explicitly permitted.

**Conclusion:** Segmentation is correctly enforced. Unlike the SOC Lab (flat subnet, no barrier between attacker and target), cross-zone traffic here has no path except through OPNsense, and is blocked by default until explicitly allowed.
