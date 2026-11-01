# 04 - Default Ruleset Detection: Nmap Scan

**Objective:** Determine whether Snort's out-of-the-box community ruleset provides any detection value beyond the custom brute-force rule.

**Test:**
```bash
nmap -sS -sV 192.168.1.9
```

**Result:** Snort's default ruleset flagged the scan under an "information leakage" classification (alert-only, not configured to drop in this lab).

**Observation:** No custom rule was needed for this, Snort's community signatures already cover common reconnaissance patterns like service/version probing. This confirms that a real IPS deployment benefits from both custom rules (tailored to specific scenarios, like the RDP brute-force rule) and the maintained community ruleset (broad baseline coverage for known reconnaissance and exploit patterns).

**Not yet implemented:** converting this specific alert to a `drop` action, currently logged only. Noted as a natural next step if extending this lab further: adding a `detection_filter`-based drop rule scoped to `$HOME_NET` broadly (not a single host) to actively block scan behavior network-wide, rather than relying on the default alert-only signature.

**Conclusion:** Default signature coverage and custom behavioral rules serve complementary purposes, out-of-the-box rules catch known patterns immediately with no setup, while custom rules close gaps specific to your own environment and threat model.
