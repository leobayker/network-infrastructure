# Network Tables

Detailed IP addressing, VLAN plan, VPN tunnels, and switch port mapping for a multi-site enterprise network (11 sites, city-wide deployment).

> All data is anonymized: site names → site-01..site-11, real IPs replaced with RFC1918 placeholders. See [credentials.example](../credentials.example).

---

## 1. IP Addressing Table

### Core Router

| Device | Interface / VLAN | IP Address |
|---|---|---|
| Router-Core | int vlan 500 — uplink site-01 (P2P) | 10.100.0.1/30 |
| | int vlan 501 — uplink site-02 (P2P) | 10.100.0.5/30 |
| | int vlan 502 — uplink site-03 (P2P) | 10.100.0.9/30 |
| | int vlan 503 — uplink site-04 (P2P) | 10.100.0.13/30 |
| | int vlan 504 — uplink site-05 (P2P) | 10.100.0.17/30 |
| | int vlan 505 — uplink site-06 (P2P) | 10.100.0.21/30 |
| | int vlan 506 — uplink site-07 (P2P) | 10.100.0.25/30 |
| | int vlan 507 — uplink site-08 (P2P) | 10.100.0.29/30 |
| | int vlan 508 — uplink site-09 (P2P) | 10.100.0.33/30 |
| | int vlan 509 — uplink site-10 (P2P) | 10.100.0.37/30 |
| | int vlan 510 — uplink site-11 (P2P) | 10.100.0.41/30 |
| | int vlan 100 — Servers | 10.0.100.1/24 |
| | int vlan 110 — LAN_FW (FortiGate) | 10.0.110.1/24 |

### Site Routers

| Device | Interface / VLAN | IP Address |
|---|---|---|
| Router-site-01 | int vlan 500 — uplink (P2P) | 10.100.0.2/30 |
| | int vlan 10 — Data | 10.10.2.1/23 |
| | int vlan 20 — MGMT | 10.10.3.1/24 |
| | int vlan 30 — VoIP | 10.10.3.33/24 |
| | int vlan 40 — WiFi | 10.10.3.65/24 |
| Router-site-02 | int vlan 501 — uplink (P2P) | 10.100.0.6/30 |
| | int vlan 10 — Data | 10.10.4.1/23 |
| | int vlan 20 — MGMT | 10.10.5.1/24 |
| | int vlan 30 — VoIP | 10.10.5.33/24 |
| | int vlan 40 — WiFi | 10.10.5.65/24 |
| Router-site-03 | int vlan 502 — uplink (P2P) | 10.100.0.10/30 |
| | int vlan 10 — Data | 10.10.6.1/23 |
| | int vlan 20 — MGMT | 10.10.7.1/24 |
| | int vlan 30 — VoIP | 10.10.7.33/24 |
| | int vlan 40 — WiFi | 10.10.7.65/24 |
| Router-site-04..11 | Same pattern | See VLAN table below |

### Switches (VLAN 20 — MGMT)

| Device | IP Address |
|---|---|
| SW-site-01-1 (DGS) | 10.10.3.2/24 |
| SW-site-01-2 (DGS) | 10.10.3.3/24 |
| SW-site-01-3 (DGS) | 10.10.3.4/24 |
| SW-site-02-1 (DGS) | 10.10.5.2/24 |
| SW-site-02-2 (DGS) | 10.10.5.3/24 |
| SW-site-03-1 (DGS) | 10.10.7.2/24 |
| SW-site-04-1 (DGS) | 10.10.9.2/24 |
| SW-site-05-1 (DGS) | 10.10.11.2/24 |
| SW-site-06-1 (DGS) | 10.10.13.2/24 |
| SW-site-07-1 (DGS) | 10.10.15.2/24 |
| SW-site-07-2 (DGS) | 10.10.15.3/24 |
| SW-site-08-1 (DGS) | 10.10.17.2/24 |
| SW-site-09-1 (DGS) | 10.10.19.2/24 |
| SW-site-09-2 (DGS) | 10.10.19.3/24 |

### Infrastructure / IPMI

| Device | VLAN | IP Address |
|---|---|---|
| IPMI-Worker-1 | vlan 20 — MGMT | 10.0.100.11/24 |
| IPMI-Worker-2 | vlan 20 — MGMT | 10.0.100.12/24 |
| IPMI-Worker-3 | vlan 20 — MGMT | 10.0.100.13/24 |
| IPMI-Storage | vlan 20 — MGMT | 10.0.100.14/24 |
| IPMI-Veeam | vlan 20 — MGMT | 10.0.100.15/24 |
| IPMI-Proxmox-SIEM | vlan 20 — MGMT | 10.0.100.19/24 |
| FortiGate (primary) | vlan 110 — LAN_FW | 10.0.110.2/24 |
| FortiGate (backup) | vlan 110 — LAN_FW | 10.0.110.3/24 |
| SW-Core (DXS) | vlan 100 — Servers | 10.0.100.2/24 |

---

## 2. VLAN Plan

### Data VLANs (per site)

| VLAN ID | Name | Subnet | Purpose |
|---|---|---|---|
| 10 | Data | 10.10.X.0/23 | User workstations |
| 20 | MGMT | 10.10.X.0/24 | Network devices, servers |
| 30 | VoIP | 10.10.X.0/24 | VoIP handsets |
| 40 | WiFi | 10.10.X.0/24 | Wireless clients (CAPsMAN) |

### Infrastructure VLANs (core site)

| VLAN ID | Name | Subnet | Purpose |
|---|---|---|---|
| 100 | Servers | 10.0.100.0/24 | Server farm |
| 110 | LAN_FW | 10.0.110.0/24 | Core ↔ FortiGate LAN interface |

### Uplink VLANs (P2P /30 per site)

| VLAN ID | Site | Subnet | Core IP | Site IP |
|---|---|---|---|---|
| 500 | site-01 | 10.100.0.0/30 | 10.100.0.1 | 10.100.0.2 |
| 501 | site-02 | 10.100.0.4/30 | 10.100.0.5 | 10.100.0.6 |
| 502 | site-03 | 10.100.0.8/30 | 10.100.0.9 | 10.100.0.10 |
| 503 | site-04 | 10.100.0.12/30 | 10.100.0.13 | 10.100.0.14 |
| 504 | site-05 | 10.100.0.16/30 | 10.100.0.17 | 10.100.0.18 |
| 505 | site-06 | 10.100.0.20/30 | 10.100.0.21 | 10.100.0.22 |
| 506 | site-07 | 10.100.0.24/30 | 10.100.0.25 | 10.100.0.26 |
| 507 | site-08 | 10.100.0.28/30 | 10.100.0.29 | 10.100.0.30 |
| 508 | site-09 | 10.100.0.32/30 | 10.100.0.33 | 10.100.0.34 |
| 509 | site-10 | 10.100.0.36/30 | 10.100.0.37 | 10.100.0.38 |
| 510 | site-11 | 10.100.0.40/30 | 10.100.0.41 | 10.100.0.42 |

### Per-site subnet summary

| Site | Data (VLAN 10) | MGMT (VLAN 20) | VoIP (VLAN 30) | WiFi (VLAN 40) |
|---|---|---|---|---|
| site-01 | 10.10.2.0/23 | 10.10.3.0/24 | 10.10.3.32/24 | 10.10.3.64/24 |
| site-02 | 10.10.4.0/23 | 10.10.5.0/24 | 10.10.5.32/24 | 10.10.5.64/24 |
| site-03 | 10.10.6.0/23 | 10.10.7.0/24 | 10.10.7.32/24 | 10.10.7.64/24 |
| site-04 | 10.10.8.0/23 | 10.10.9.0/24 | 10.10.9.32/24 | 10.10.9.64/24 |
| site-05 | 10.10.10.0/23 | 10.10.11.0/24 | 10.10.11.32/24 | 10.10.11.64/24 |
| site-06 | 10.10.12.0/23 | 10.10.13.0/24 | 10.10.13.32/24 | 10.10.13.64/24 |
| site-07 | 10.10.14.0/23 | 10.10.15.0/24 | 10.10.15.32/24 | 10.10.15.64/24 |
| site-08 | 10.10.16.0/23 | 10.10.17.0/24 | 10.10.17.32/24 | 10.10.17.64/24 |
| site-09 | 10.10.18.0/23 | 10.10.19.0/24 | 10.10.19.32/24 | 10.10.19.64/24 |
| site-10 | 10.10.20.0/23 | 10.10.21.0/24 | 10.10.21.32/24 | 10.10.21.64/24 |
| site-11 | 10.10.22.0/23 | 10.10.23.0/24 | 10.10.23.32/24 | 10.10.23.64/24 |

---

## 3. WireGuard VPN Tunnels

Dual-ISP WireGuard with L2TP/IPSec fallback. Priority order: WG ISP1 (distance 3) → WG ISP2 (distance 5) → L2TP (distance 10).

| Tunnel | Client router | WG ISP1 client | WG ISP1 server | WG ISP2 client | WG ISP2 server |
|---|---|---|---|---|---|
| site-01 → core | Router-site-01 | 10.100.1.2/31 | 10.100.1.1/31 | — | — |
| site-02 → core | Router-site-02 | 10.100.1.4/31 | 10.100.1.3/31 | — | — |
| site-03 → core | Router-site-03 | 10.100.1.6/31 | 10.100.1.5/31 | — | — |
| site-04 → core | Router-site-04 | 10.100.1.8/31 | 10.100.1.7/31 | — | — |
| site-05 → core | Router-site-05 | 10.100.1.10/31 | 10.100.1.9/31 | — | — |
| site-06 → core | Router-site-06 | 10.100.1.12/31 | 10.100.1.11/31 | — | — |
| site-07 → core | Router-site-07 | 10.100.1.14/31 | 10.100.1.13/31 | 10.100.2.14/31 | 10.100.2.13/31 |
| site-08 → core | Router-site-08 | 10.100.1.16/31 | 10.100.1.15/31 | — | — |
| site-09 → core | Router-site-09 | 10.100.1.18/31 | 10.100.1.17/31 | — | — |
| site-10 → core | Router-site-10 | 10.100.1.20/31 | 10.100.1.19/31 | — | — |
| site-11 → core | Router-site-11 | 10.100.1.22/31 | 10.100.1.21/31 | — | — |

---

## 4. Switch Port VLAN Mapping

Example: site-09, DGS-3000 series (28-port). Same pattern applies to all sites.

| Port | VLAN | Mode | Notes |
|---|---|---|---|
| 1, 3–9, 11–16, 19–22 | VLAN 10 | access | User workstations |
| 2, 10, 17, 18 | VLAN 40 | access | AP uplink ports |
| 23 | VLAN 30 | access | VoIP handset |
| 24 | VLAN 10/20/30/40/500–510 | trunk | Uplink to Router-site-09 (native VLAN 20) |
| 25–48 | VLAN 10 | access | User workstations (52-port models) |
| 49–52 | VLAN 10/20/30/40 | trunk | Inter-switch uplinks (52-port models) |

---

*Full data available in [network-infrastructure-tables.xlsx](../network-infrastructure-tables.xlsx) (download for Excel view).*
