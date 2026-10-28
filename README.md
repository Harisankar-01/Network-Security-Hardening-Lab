# Network Security Hardening Lab — Segmentation, Firewall Hardening & Inline IPS

A follow-up project to the <a href="https://github.com/Harisankar-01/SOC-Lab-Enterprise-Monitoring">SOC Lab project</a>, built specifically to close its biggest gap: the original SOC lab ran on a flat network where attacker to target traffic never touched the firewall. This project rebuilds the network with real segmentation, firewall rules to block malicious traffic, and an inline IDS/IPS that actively Detect, Alerts And blocks attacks.

**Environment:** VirtualBox, OPNsense Firewall,Snort IPS, Kali Linux, Windows Server 2022, Windows 7

---

## Core Objective

The <a href="https://github.com/Harisankar-01/SOC-Lab-Enterprise-Monitoring">SOC Lab project</a>, demonstrated that weak network configuration, a flat subnet with no real firewall enforcement, let an attacker reach and brute-force a target directly.This project fixes that root cause: proper network segmentation forces all traffic through a firewall, hardened rules enforce default-deny, and an inline intrusion detection & prevention system actively alerts & drops malicious traffic in real time, rather than only alerts.

---

## Architecture
## Logical Network Architecture

This lab is segmented into an untrusted WAN zone and a trusted LAN zone, with traffic heavily filtered at the perimeter by OPNsense and physically inspected inline by a Snort IPS bridge.

```mermaid
flowchart LR
    subgraph WAN [WAN Zone: 10.0.0.0/24 - Untrusted]
        KALI["Kali Linux (Attacker)<br/>IP: 10.0.0.5"]
    end

    subgraph Perimeter [Gateway Edge]
        FW["OPNsense Firewall<br/>WAN: 10.0.0.1<br/>LAN: 192.168.1.1"]
    end

    subgraph LAN [LAN Zone: 192.168.1.0/24 - Trusted]
        SNORT["Snort True IPS (Ubuntu)<br/>Inline Bridge (br0)<br/>IP: 192.168.1.20"]
        WIN["Windows Server 2022<br/>Target Endpoint<br/>IP: 192.168.1.9"]
    end

    KALI -- "Malicious Payloads" --> FW
    FW -- "NetSec_LAN" --> SNORT
    SNORT -- "snort_IDS_network" --> WIN

    style KALI fill:#ff4d4d,stroke:#333,stroke-width:2px,color:#000
    style FW fill:#ff9933,stroke:#333,stroke-width:2px,color:#000
    style SNORT fill:#ffff66,stroke:#333,stroke-width:2px,color:#000
    style WIN fill:#66ccff,stroke:#333,stroke-width:2px,color:#000
```
