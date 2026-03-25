# network-infrastructure

Enterprise network infrastructure design for a city-wide deployment across 11 sites in Kyiv.
Built from scratch: started with 3 locations and a flat network (1000+ users in a single broadcast domain),
scaled to a fully segmented multi-site architecture.

> All diagrams and configs are anonymized: site names, street addresses, IP addresses, ISP names, and VLAN IDs replaced with generic placeholders.

---

## Scale

| Parameter | Value |
|---|---|
| Sites | 11 city-wide locations |
| Users | 1000+ |
| VPN tunnels | 11 site-to-site (WireGuard + L2TP/IPSec IKEv2) |
| VLANs per site | 6–7 (Data, MGMT, VoIP, WiFi, BS, SKUD + uplink) |
| Core router | MikroTik CCR1009-7G-1C-1S+ |
| Branch routers | MikroTik CCR1009-7G-1C-1S+ × 11 |
| Switches | D-Link DGS-3000 / DGS-1210 at each site |
| Firewall | FortiGate 100F HA Cluster (Active-Passive) |
| Virtualization | VMware vSphere 3-node cluster + Proxmox VE |
| SIEM | Security Onion (centralized logs from all 11 sites) |
| Monitoring | Zabbix + Slack alerts |

---

## Architecture Overview

```
                         ┌──────────────────────────────┐
   ISP1 (PON) ──────────►│                              │
   ISP2 (Dedicated) ────►│   FortiGate 100F HA Cluster  │◄── Internet
                         │   (Active-Passive)            │
                         └──────────────┬───────────────┘
                                        │ VLAN trunk
                         ┌──────────────▼───────────────┐
                         │      Mikrotik_Core            │
                         │      CCR1009-7G-1C-1S+        │
                         │      RouterOS 7.x             │
                         └──┬───────────────────────┬───┘
                            │  SFP trunk (VLANs     │
                            │  500–510 per site)    │
              ┌─────────────┼───────────────────────┼──────────────┐
              │             │                       │              │
       ┌──────▼──────┐ ┌────▼────────┐       ┌─────▼──────┐ ┌────▼──────┐
       │  site-01    │ │  site-02    │  ...  │  site-10   │ │  site-11  │
       │ CCR1009     │ │  CCR1009    │       │  CCR1009   │ │  CCR1009  │
       └──────┬──────┘ └─────────────┘       └────────────┘ └─────┬─────┘
              │                                                     │
       D-Link DGS-3000                                       D-Link DGS-3000
       (per-site switches)                              + CAPsMAN WiFi (~20 APs)
```

**Tunnel redundancy (site-11 example):**
```
site-11 → WireGuard via ISP1  (distance 3, primary)
site-11 → WireGuard via ISP2  (distance 5, backup)
site-11 → L2TP/IPSec IKEv2   (distance 10, fallback)
```

---

## VLAN Design

### Core site VLANs
| VLAN ID | Name | Subnet | Purpose |
|---|---|---|---|
| 100 | Servers | 10.0.100.0/24 | Server farm |
| 110 | LAN_FW | 10.0.110.0/24 | Core ↔ FortiGate LAN |
| 500–510 | Site uplinks | — | One per branch site (provider trunk) |

### Branch site VLANs (per site)
| VLAN ID | Name | Subnet pattern | Purpose |
|---|---|---|---|
| 10 | Data | 10.10.X.0/23 | User workstations |
| 20 | MGMT | 10.10.X.0/24 | Network devices, servers |
| 30 | VoIP | 10.10.X.0/24 | IP phones |
| 40 | WiFi | 10.10.X.0/24 | Wireless clients |
| — | BS | 10.10.X.0/24 | Building systems |
| — | SKUD | 10.10.X.0/24 | Access control |

### Inter-site P2P subnets (IPSec)
`10.100.0.0/24` — split into /30 subnets, one per site:
- Core side: `.1` of each /30
- Site side: `.2` of each /30

---

## IP Addressing Scheme

| Block | Purpose |
|---|---|
| `10.0.100.0/24` | Server VLAN (core) |
| `10.0.110.0/24` | FortiGate LAN interface |
| `10.0.200.0/24` | Infrastructure (FortiAnalyzer, NTP, SIEM) |
| `10.10.0.0/23` | site-01 Data VLAN |
| `10.10.2.0/23` | site-02 Data VLAN |
| `10.10.4.0/23` | site-03 Data VLAN |
| `...` | (pattern: site-N = 10.10.(N*2-2).0/23) |
| `10.10.20.0/23` | site-11 Data VLAN |
| `10.100.0.0/24` | IPSec P2P /30 subnets |
| `172.16.200.0/24` | WireGuard tunnel addresses |
| `172.16.100.0/24` | L2TP/SSTP pool |

---

## Diagrams

| File | Description |
|---|---|
| [`diagrams/logical-diagram.xml`](diagrams/logical-diagram.xml) | Full logical topology: routing, VLANs, subnets per site |
| [`diagrams/physical-diagram.xml`](diagrams/physical-diagram.xml) | Physical connectivity: devices, ports, ISP uplinks |
| [`diagrams/its-diagram.xml`](diagrams/its-diagram.xml) | IT systems overview: server farm, virtualization, security |

Open with [app.diagrams.net](https://app.diagrams.net) → File → Open from device.

---

## Security Stack

| Layer | Technology |
|---|---|
| **Perimeter firewall** | FortiGate 100F HA Cluster (Active-Passive), FSSO, IPS, DNS Filtering, FortiMail, FortiToken 2FA |
| **Endpoint** | CrowdStrike EDR (GPO deployment across domain) |
| **MFA** | Duo Security (domain admins), FortiToken (VPN users) |
| **SIEM** | Security Onion on Proxmox VE — centralized Syslog from all 11 MikroTik routers + FortiGate + domain servers via Elastic Agent |
| **Monitoring** | Zabbix (custom templates, UPS, VMware) + Slack alerting |

---

## Related Repositories

- [`mikrotik-configs`](https://github.com/leobayker/mikrotik-configs) — RouterOS configs: core hub + branch site, backup scripts
- [`automation-scripts`](https://github.com/leobayker/automation-scripts) — Python/Netmiko mass config deployment across all sites
