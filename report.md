# Home Network Security Audit Report

**Prepared by:** wumwumwum
**Date:** 20260719
**Network Scanned:** 192.168.1.0/24
**Tool(s) Used:** Nmap 7.98

---

## 1. Executive Summary

As an excersize to showcase my familiarity with using NMAp and as part of a regularly schedule, this audit served the following purpose:
- Routine hygiene
- New device onboarding
- Scan of my primary network (192.168.1.0/24 = 19 hosts)

> Summary: "A scan of the 192.168.1.0/24 network identified 18 active hosts. Two devices exposed unnecessary remote management ports to the LAN, and one IoT device was running an outdated firmware version with a known vulnerability. Recommended actions include disabling unused services and segmenting IoT devices onto a separate VLAN."

---

## 2. Scope and Methodology

### 2.1 Scope
- **Network range(s) scanned:**
> 192.168.1.0/24, 
- **Excluded hosts/ranges:** none
- **Authorization:** I confirm that this is my own home network and that I have explicit permission to scan it.

### 2.2 Methodology
I ran an initial scan to identify only the available hosts (i.e. sudo nmap -sN -A 192.168.1.0/24) and their versions.
I then completed a more complete scan of open ports per host (i.e. sudo nmap -sV -O 192.168.1.0/24).

```bash
# Host discovery
nmap -sn 192.168.1.0/24

# Full TCP port scan with service/version detection
nmap -sV -sC -p- -oA full_scan 192.168.1.0/24

# OS detection
nmap -O 192.168.1.0/24

# UDP scan of common ports
nmap -sU --top-ports 100 192.168.1.0/24
```

> Note: `-O` and SYN scans (`-sS`) required elevated/root privileges.

---

## 3. Network Inventory

Summary table of all discovered hosts.

| Host IP | Hostname | MAC Address | Vendor | Device Type | OS Guess |
|---|---|---|---|---|---|
| 192.168.1.x | router.local | B4:75:0E:B1:xx:xx | Netgear | Router | Linux-based |
| 192.168.1.x | Amazon Show | D8:BE:65:9E:xx:xx | Amazon | IoT | Android |
| 192.168.1.x | phone | 96:9E:04:50:xx:xx | Samsung | Phone | Android |
| 192.168.1.x | pi.hole| BC:24:11:31:xx:xx | Proxmox | Server | Linux-based |
| 192.168.1.x | custom OS | | Dell | Laptop | Linux-based |
| 192.168.1.1x | kali | E4:A4:71:B6:xx:xx | Proxmox | Desktop | Linux-based |
| 192.168.1.2x | | BC:24:11:4F:xx:xx | Proxmox | CTE | Linux-based |
| 192.168.1.5x | Amazon Show | 40:A2:DB:AF:xx:xx | Amazon | IoT | Android |
| 192.168.1.10x | Wazuh | BC:24:11:8F:xx:xx | Proxmox | Server | Linux-based | 
| 192.168.1.10x | lwIP | 84:E3:42:42:xx:xx | wlan01 | IoT | Linux-based |
| 192.168.1.11x | lwIP | 84:E3:42:42:xx:xx | wlan01 | IoT | Linux-based |
| 192.168.1.12x | Espressif | BC:DD:C2:A4:xx:xx | Espressif | IoT | Linux-based |
| 192.168.1.14x | Tailscale | BC:24:11:FA:xx:xx | Proxmox | Server | Linux-based |
| 192.168.1.15x | Ubuntu | BC:24:11:C9:xx:xx | Proxmox | Server | Linux-based |
| 192.168.1.16x | Ubuntu - Nagios | BC:24:11:98:xx:xx | Proxmox | Server | Linux-based | 
| 192.168.1.19x | iPhone | | Apple | Phone | Apple | 
| 192.168.1.20x | Proxmox | 6C:4B:90:B8:xx:xx | Proxmox | Server | Linux-based | 
| 192.168.1.20x | Proxmox | 00:23:24:EB:xx:xx | Proxmox | Server | Linux-based | 
| 192.168.1.21x | Splunk | BC:24:11:D3:xx:xx | Proxmox | Server | Linux-based |

---

## 4. Detailed Host Findings

Repeat this section per host of interest (especially anything with open ports beyond expected baseline).

### 4.1 Host: 192.168.1.x (Amazon Show)

**Open Ports:**
| Port | Protocol | State | Service | Version |
| 1080 | TCP | open | socks5 | no auth |
| 8009 | TCP | open | tcpwrapped | no auth |
| 8888 | TCP | open | tcpwrapped | no auth |

**Observations:**
- Port 1080 is running without authentication, which allows for a potential relay of traffic across the network.

**Risk Rating:** Medium

**Recommendation:**
- Review additional scans to identify if this was correctly identified.
- Is this a potential threat for a attack from an external source?

---

### 4.2 Host: 192.168.1.x (pi.hole)

**Open Ports:**

| Port | Protocol | State | Service | Version |
|---|---|---|---|---|
| 22 | TCP | open | ssh | OpenSSH |
| 53 | TCP | open | domain | dnsmasq |
| 80 | TCP | open | webdav | |
| 443 | TCP | open | ssl/webdav | |

**Observations:**
- PiHole is structured as expected and is operating with current versions of installed software.

**Risk Rating:**
- Low

**Recommendation:**
- Ensure patches are applied timely
---

## 5. Summary of Findings

| # | Finding | Affected Host(s) | Severity | Recommendation |
|---|---|---|---|---|
| 1 | Isolate IoT devices | 192.168.1.x, 192.168.1.5x | Medium | Create subnet |

---

## 6. Risk Rating Definitions

| Rating | Description |
|---|---|
| **Critical** | Actively exploitable, significant impact (e.g., remote code execution, default creds on exposed admin panel) |
| **High** | Exploitable with moderate effort, meaningful impact |
| **Medium** | Requires specific conditions or local access; moderate impact |
| **Low** | Minimal impact or requires significant effort/access to exploit |
| **Informational** | No direct risk, but worth noting for hygiene/best practice |

---

## 7. Recommendations

Prioritized action list:

1. **[High priority]** Patch/update [service/device] to address [CVE/issue].
2. **[Medium priority]** Segment IoT and guest devices onto a separate VLAN.
3. **[Medium priority]** Disable unused/unnecessary open ports and services.
4. **[Low priority]** Review router admin panel exposure and disable remote management if not needed.
5. **[Ongoing]** Schedule periodic re-scans (e.g., quarterly) to track network drift.

---

## 8. Appendix

### 8.1 Raw Scan Output
Link to or embed raw Nmap output files (e.g., `.nmap`, `.xml`, `.gnmap` from `-oA`). Consider storing these in a `/scans` folder in your repo rather than pasting large output inline.

```
### 8.2 Glossary
- **Host discovery** – Process of identifying which IP addresses have active devices.
- **Port state** – open / closed / filtered, indicating whether a port is accessible and how.
- **NSE (Nmap Scripting Engine)** – Extensible scripting system used for advanced detection (vuln checks, enumeration, etc.).

### 8.3 Disclaimer
This report documents a scan of a network the author owns/controls or is explicitly authorized to test. Scanning networks without authorization may be illegal.

---

## Suggested Repo Structure

```
